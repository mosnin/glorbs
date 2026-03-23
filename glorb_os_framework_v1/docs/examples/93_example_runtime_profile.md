# 93 Example Runtime Profile: Claude Code

This is the fully populated runtime profile for the Claude Code adapter. It is the selected runtime for `mission-build-user-auth-service` (see doc 90) and is used as the reference profile in the team topology (doc 92). It covers all fields defined in the Runtime Profile Template (doc 79) and the Capability Manifest schema (doc 57). For the adapter's full implementation contract, see doc 52.

---

## Runtime Name

`claude_code`

The primary repository-centric runtime in the GLORB framework. Selected by default for Build, Execution, Design, Decision, and Audit mission types (doc 55, Default Runtime Selection).

## Adapter Type

Claude Code Adapter (doc 52). Compliance level: 3. A compliance level 3 adapter satisfies all hard checks for solo, team, and full governance topologies (doc 57, Manifest Validation).

---

## Capability Manifest

The manifest is returned by the adapter's `query_capabilities()` method (doc 56). It is the authoritative source used by the Topology Compiler (doc 21) and Runtime Selection Policy (doc 55).

| Field | Value | Notes |
|---|---|---|
| `runtime_name` | `claude_code` | Unique runtime identifier |
| `compliance_level` | 3 | Supports all GLORB governance features |
| `session_model` | `stateful` | Single continuous session per invocation. Context window is the session boundary |
| `subagent_nesting_depth` | 1 | Subagents can be spawned, but subagents cannot spawn their own subagents |
| `max_concurrent_agents` | 8 | Maximum parallel subagents. Topology Compiler validates against this limit |
| `memory_layers_supported` | working, session, mission, project, reusable, archived | Full GLORB memory stack supported. See Memory Interface section below |
| `memory_persistence` | permanent | File-system backed. Survives session termination |
| `interruptible` | true | Supports `FREEZE`, `PAUSE`, `RESUME` commands. Agents can be paused mid-task |
| `approval_model` | `built_in` | Native user approval prompts for tool calls. No adapter implementation required |
| `tool_categories` | file_read, file_write, code_execution, web_search, web_fetch, structured_output | See Tool Model section |
| `max_context_size` | 200,000 tokens | Automatic compression applies when approaching limit |
| `parallel_execution` | true | Multiple Agent tool calls in a single response run simultaneously |
| `async_messaging` | false | Synchronous orchestration only. Async mode not needed (session is stateful) |
| `human_command_support` | FREEZE, FORK, ESCALATE, REPLACE, RECOMPILE, INCREASE_CRITIQUE, LOWER_RIGOR, LOCK_ASSUMPTIONS, COMPRESS_CONTEXT, REDUCE_COST, OVERRIDE, ABORT, REDIRECT, RESUME | See doc 09 for command definitions |
| `cost_model` | `per_token` | Cost accrues based on input and output tokens. GLORB layer must track estimated usage |
| `sandbox_model` | `os_level` | Commands execute in the user's shell. OS permissions are the isolation boundary |

---

## Tool Model

Claude Code exposes a rich native toolset. GLORB tool permission levels (doc 10: None, Read, Write, Execute, Full) map to the following tool subsets:

| GLORB Permission Level | Permitted Tools |
|---|---|
| None | No tool access |
| Read | `Read`, `Glob`, `Grep` |
| Write | `Read`, `Glob`, `Grep`, `Edit`, `Write`, `NotebookEdit` |
| Execute | `Read`, `Glob`, `Grep`, `Edit`, `Write`, `Bash`, `TodoWrite` |
| Full | All tools: `Read`, `Glob`, `Grep`, `Edit`, `Write`, `NotebookEdit`, `Bash`, `WebSearch`, `WebFetch`, `TodoWrite`, `Agent` |

Tool availability is declared in `tool_categories` above. All categories map to native tools rather than API-mediated proxies. File operations (`Read`, `Edit`, `Write`, `Glob`, `Grep`) are immediate and atomic per call. `Bash` commands time out after 120 seconds by default (configurable to 600 seconds).

---

## Session Model

Claude Code operates as a single stateful session per invocation.

| Property | Behavior |
|---|---|
| Session boundary | The active context window (200,000 tokens) |
| Session continuity | All prior messages in the conversation are in scope unless compressed |
| Context compression | Automatic. Prior messages are summarized when approaching the token limit. Compression is lossy: precise intermediate reasoning may be lost. Mitigate by writing key decisions to files early |
| Cross-session persistence | Not automatic. State that must survive beyond the session must be written to the filesystem. CLAUDE.md and mission instance files serve as the persistent anchor |
| Parallel subagents | Spawned via the `Agent` tool. Each subagent receives a fresh context window with only what is passed in its prompt. Subagents do not share the parent session's context |

---

## Memory Interface

GLORB memory layers (doc 05) are realized through Claude Code's native capabilities.

| GLORB Memory Layer | Claude Code Implementation | Persistence |
|---|---|---|
| Working memory | The active context window. Current reasoning, intermediate outputs, in-flight calculations | Session only. Lost on compression or session end |
| Session memory | Context window history. Also: `TodoWrite` task list for explicit progress tracking | Session. Subject to compression |
| Mission memory | CLAUDE.md (project-level). Mission instance files written to the repository (e.g., `docs/examples/90_example_project_instance.md`) | Permanent (file-backed) |
| Project memory | Repository files: README.md, documentation directory, source files, configuration | Permanent (file-backed) |
| Reusable memory | CLAUDE.md (user-level and project-level). Framework docs in `docs/`. These survive across invocations | Permanent (file-backed) |
| Archived memory | Git history. Prior commits preserve historical state and are queryable via `Bash` + `git log` | Permanent (git-backed) |

Key implication: because working and session memory are volatile, mission-critical decisions and intermediate outputs must be written to files before the context compresses. This is the primary operational discipline for Claude Code missions.

---

## Strengths

### 1 Native Repository Context

Claude Code reads the full repository structure on session start. Agents have immediate access to all source files, configuration, documentation, and git history without any adapter-mediated proxying. For Build missions where the agent must understand an existing codebase before modifying it, this eliminates a major friction point present in API-orchestrated runtimes.

### 2 Unified File Read/Write/Execute

All three capability classes (read, write, execute) are native and tightly integrated. An agent can read a file, modify it, run tests against it, inspect the test output, and iterate — all within a single session, without crossing runtime boundaries. This makes Claude Code the strongest available runtime for Build and Execution missions.

### 3 Full GLORB Memory Stack

Claude Code is the only runtime in the framework that supports all six memory layers natively. Mission memory (CLAUDE.md + mission files), project memory (repository), and archived memory (git) are all available without adapter-managed external storage. This eliminates the memory synchronization complexity present in OpenClaw multi-runtime topologies.

### 4 Stateful Session with Interruption Support

The stateful session model allows agents to maintain accumulated context across a long sequence of steps. Combined with `interruptible: true`, the human operator can issue `FREEZE`, `REDIRECT`, and `RESUME` commands at any point. This makes Claude Code the preferred runtime when human oversight at intermediate checkpoints is required (e.g., Balanced policy with human checkpoints at mission start and final deliverable).

### 5 Built-in Approval Model

User approval prompts are native to the Claude Code interface. No adapter implementation is needed to gate destructive or sensitive operations. This satisfies the governance requirement (doc 08) for human oversight of irreversible actions without additional engineering.

### 6 Compliance Level 3

Claude Code is the only adapter in the framework with compliance level 3. It satisfies all hard topology validation checks for full governance topologies, including multi-agent teams with critique loops, quality gates, and provenance tracking. Lower compliance runtimes (OpenClaw: level 2, NanoClaw: level 1) are insufficient for full-governance missions without supplementary adapter logic.

---

## Limitations

### 1 Single Subagent Nesting Level

Subagents spawned via the `Agent` tool cannot themselves spawn subagents (`subagent_nesting_depth: 1`). Topologies that require hierarchical agent spawning (e.g., a Router spawning specialist teams that each spawn workers) cannot be executed natively. Workaround: the primary session acts as the Router, decomposing multi-level topologies into flat peer subagent calls. For missions requiring true multi-level nesting, use OpenClaw as the primary runtime.

### 2 No Persistent State Between Separate Invocations

Context from one Claude Code session does not automatically carry into a new invocation. A mission that spans multiple invocations must rely entirely on file-backed memory (CLAUDE.md, mission files). If the operator initiates a new session without first writing sufficient state to files, the new session starts cold. Mitigation: write mission state to files at every phase boundary and use CLAUDE.md as the persistent project memory anchor.

### 3 Context Window Pressure on Long Missions

A 200,000 token context window is large but finite. Long missions with many sequential steps, large file reads, or verbose tool outputs will eventually trigger automatic compression. Compression is lossy: prior agent reasoning, intermediate critiques, and in-flight architectural decisions may be collapsed into summaries. Mitigation: write key outputs to files aggressively. Prefer subagents for bounded research tasks to offload context from the primary session.

### 4 No Native Token Budget Tracking

Claude Code does not expose a real-time token counter to the agent. GLORB's budget governance (doc 04, Budget constraint) cannot be enforced by polling a runtime API. Token usage must be estimated at the GLORB layer based on task complexity and agent count. Actual cost is only known after the session ends.

### 5 No Async Messaging Between Agents

`async_messaging: false`. The synchronous orchestration model means critique loops require sequential round-trips mediated by the primary session. There is no message queue or event bus between agents. In practice, this is acceptable for the typical topology sizes Claude Code handles (2–5 agents), but it limits throughput for larger parallel workloads compared to OpenClaw.

### 6 OS-Level Sandbox Only

Claude Code executes Bash commands in the user's shell environment with OS-level isolation only. There is no container boundary between the agent and the host system. This is acceptable for trusted development environments but is not appropriate for missions where the agent will execute untrusted or destructive code. For those cases, prefer OpenClaw (container sandbox) or NanoClaw (full isolation).

---

## Selection Rationale

Claude Code was selected for `mission-build-user-auth-service` because:

1. **Hard requirements satisfied**: The mission requires file read/write (implement Python source files), code execution (run pytest, ruff), and session persistence (multi-phase architecture → build → verify pipeline). NanoClaw fails on all three. OpenClaw satisfies them but adds adapter complexity for file operations.

2. **Soft preferences favor Claude Code**: Quality bar is Medium-High (Rigor High preference → Claude Code > OpenClaw > NanoClaw per doc 55). The mission is repository-centric (Repository-centric preference → Claude Code > OpenClaw). Session persistence is required (Session persistence preference → Claude Code > OpenClaw).

3. **No disqualifying constraints**: The mission does not require multi-level subagent nesting (which would require OpenClaw), full container isolation for untrusted code (which would require OpenClaw or NanoClaw), or maximum cost minimization (which would prefer NanoClaw).

4. **Full governance required**: The Balanced policy activates three quality gates and a Critic agent. Compliance level 3 is needed. Claude Code is the only available runtime at compliance level 3.

Runtime selection followed the algorithm in doc 55 (Step 1: Hard requirements → Step 2: Soft preferences → Step 3: Select). No trade-offs were required.

---

## Fallback Runtime

**OpenClaw** (Primary fallback per doc 55, Runtime Fallback Chain)

Fallback triggers:
- Claude Code is unreachable (network failure, service outage)
- Claude Code exceeds resource limits (rate limits, token budget)
- A required tool is unexpectedly unavailable

On fallback to OpenClaw:

1. Log the failure and the fallback decision to the Provenance Graph (doc 06)
2. File operations are no longer native. The OpenClaw adapter must mediate all file reads and writes via its API layer. This adds latency and removes atomicity guarantees
3. Session state cannot be migrated directly. The mission must restart from the last file-backed checkpoint (CLAUDE.md + mission instance files)
4. Subagent nesting depth increases from 1 to multi-level, which adds capability but requires topology recompilation if the flat subagent model was relied upon
5. The human operator is notified of the runtime change before execution continues

Secondary fallback: Reduce scope. If OpenClaw is also unavailable, evaluate whether the mission can be decomposed into smaller sub-missions that can execute within the available runtime constraints.

---

## Configuration Notes

### CLAUDE.md as Mission Anchor

Write the project name, mission name, active phase, and the paths of all mission instance files to CLAUDE.md at session start. This ensures any new session can orient itself without requiring the operator to reconstruct context manually.

### Git Commits as Recovery Checkpoints

Commit at each phase boundary: after architecture is complete, after each builder completes, after verification. Each commit is a named recovery point. If a session fails mid-phase, restart from the last commit and re-run only the current phase.

### Subagent Prompt Design

Each subagent prompt must be self-contained. Because subagents do not inherit the parent session's context, include: the agent's role and purpose, the specific task, the acceptance criteria, all required input data or file paths, and the expected output format. Do not assume the subagent has read any prior conversation turn.

### Context Management During Build Phase

The Build phase is the most context-intensive: the primary session holds the architecture specification, the Critic report, both builder task briefs, and the accumulated conversation. Before dispatching builders, write all architecture outputs to files. Provide builders only the file paths, not the full content in the prompt. This frees context window capacity for the Verification phase synthesis.

### Tool Call Timeouts

Default Bash timeout is 120 seconds. Test suites with database setup may exceed this. Configure the `pytest` invocation for `verifier-coverage` with a timeout parameter. If a test run exceeds 120 seconds, set the Bash timeout to 300 seconds for the verification task brief.
