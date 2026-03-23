# 52 Claude Code Adapter

## Purpose

The Claude Code Adapter maps GLORB framework concepts to Claude Code's native capabilities, defining how missions, agents, topologies, and memory operate within the Claude Code runtime. This is the primary adapter for repository-centric work.

## Typical Strengths

1. Strong repository context
2. File editing
3. Prompt driven project initialization
4. Good fit for framework driven repo work

## Adapter Contract (per 51 Runtime Adapter Contract)

### Available Tools

1. File read and write (Read, Edit, Write, Glob, Grep)
2. Shell execution (Bash)
3. Subagent spawning (Agent tool with specialized agent types)
4. Web search and fetch (WebSearch, WebFetch)
5. Notebook editing (NotebookEdit)
6. Task tracking (TodoWrite)
7. User interaction (AskUserQuestion)

### File System Access Model

1. Full read-write access to the working directory and subdirectories
2. Read access to system files where permissions allow
3. No access restrictions within the project scope
4. File operations are immediate and atomic per tool call

### Session Model

1. Single continuous session per invocation
2. Context window is the primary session boundary
3. Automatic context compression when approaching limits (prior messages are summarized)
4. No persistent state between separate invocations unless written to files

### Memory Interface

| GLORB Memory Layer | Claude Code Implementation |
|---|---|
| Working memory | The active context window. Current reasoning and intermediate state |
| Session memory | Context window history (subject to compression). TodoWrite for explicit tracking |
| Mission memory | CLAUDE.md and project files. Mission instance files in the repository |
| Project memory | Repository files, README.md, documentation directory. Persists across sessions via filesystem |
| Reusable memory | CLAUDE.md files (project-level and user-level). These survive across invocations |
| Archived memory | Git history. Prior commits preserve historical state |

### Subagent Support

1. Claude Code supports subagent spawning via the Agent tool
2. Available agent types: general-purpose, Explore, Plan, and custom types
3. Subagents receive a text prompt but do not inherit the parent's full context window
4. Subagents can read files, search code, and execute commands independently
5. Subagent results are returned as text to the parent agent
6. Multiple subagents can execute in parallel
7. Subagents cannot spawn their own subagents (single level of nesting)

### Approval Model

1. Tool calls may require user approval depending on the configured permission mode
2. Users can pre-approve categories of actions or approve individually
3. Denied tool calls cannot be re-attempted identically. The agent must adjust its approach
4. Hooks (user-configured shell commands) may execute automatically on certain events

### Sandbox Model

1. Commands execute in the user's shell environment
2. No built-in sandboxing beyond the user's OS permissions
3. Destructive operations should be confirmed with the user before execution
4. Network access is available but may be restricted by environment

### Runtime Limits

1. Context window is the primary constraint (automatically managed via compression)
2. Bash command timeout: 120 seconds default, configurable up to 600 seconds
3. No built-in token budget tracking (must be managed at the GLORB layer)
4. No built-in concurrent agent limit beyond what the Agent tool supports

### Logging Model

1. All tool calls and results are visible in the conversation
2. No separate structured log. The conversation is the log
3. Git commits serve as durable checkpoints
4. TodoWrite provides visible progress tracking

## GLORB Concept Mapping

### Agent Mapping

| GLORB Concept | Claude Code Implementation |
|---|---|
| Lead agent | The primary Claude Code session |
| Specialist agent | A subagent spawned via the Agent tool with a role-specific prompt |
| Agent spawning | Agent tool invocation with prompt containing the agent spec from 14 |
| Agent retirement | Subagent completes and returns result. No persistent state |
| Agent communication | Subagent results returned to parent. Parent incorporates into context |

### Topology Mapping

| GLORB Topology | Claude Code Implementation |
|---|---|
| Solo agent | Primary session handles everything directly |
| Planner and executor | Primary session plans, spawns executor subagent(s) |
| Architect plus builders | Primary session architects, spawns builder subagents in parallel |
| Researcher swarm plus reducer | Multiple Explore subagents in parallel, primary session synthesizes |
| Critic loop | Primary session produces, spawns critic subagent, incorporates feedback |

### Orchestration Mapping

| GLORB Concept | Claude Code Implementation |
|---|---|
| Parallel execution | Multiple Agent tool calls in a single response |
| Sequential execution | Agent tool calls in separate responses, each depending on prior results |
| Handoff | Subagent result text serves as the handoff artifact |
| Merge and reduction | Primary session acts as Synthesizer, combining subagent results |
| Quality gates | Primary session evaluates gate criteria before proceeding to next phase |
| Disagreement engine | Spawn a critic subagent to challenge the primary session's output |

## Limitations and Workarounds

| Limitation | Workaround |
|---|---|
| Single nesting level for subagents | Primary session acts as Router, decomposing complex topologies into flat subagent calls |
| No persistent memory between invocations | Write key decisions and state to CLAUDE.md or mission files in the repository |
| Context window pressure on long missions | Use aggressive memory compression: write findings to files, keep context focused on current task |
| No native token budget tracking | Track estimated usage via task complexity and agent count at the GLORB layer |
| Subagents do not share context | Include relevant context in each subagent's prompt. Extract shared context into files that subagents can read |
| No built-in structured logging | Use git commits as checkpoints. Write structured logs to files if needed |

## Best Practices for Claude Code Missions

1. Write mission state to files early and often. The filesystem is your most reliable memory layer
2. Use CLAUDE.md as the project memory anchor. It is read at the start of every session
3. Spawn subagents for independent, parallelizable work. Keep the primary session for coordination and synthesis
4. Prefer small, focused subagent tasks over large, complex ones. Subagents work best with clear scope
5. Use git commits as recovery checkpoints. If a mission fails, the last commit is the restore point
6. When context pressure is high, offload research to Explore subagents and keep synthesis in the primary session
