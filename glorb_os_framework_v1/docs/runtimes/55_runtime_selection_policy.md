# 55 Runtime Selection Policy

## Purpose

The Runtime Selection Policy defines how the system chooses which runtime to use for each agent or sub-task within a mission. It maps mission requirements to runtime capabilities and handles multi-runtime coordination.

## Selection Inputs

1. Tool requirements
2. Sandboxing needs
3. File editing needs
4. Session persistence needs
5. Speed needs
6. Cost constraints
7. Safety requirements

## Input-to-Runtime Decision Matrix

| Requirement | Claude Code | OpenClaw | NanoClaw |
|---|---|---|---|
| File read/write | Native | API-mediated | Not supported |
| Code execution | Native (Bash) | Sandboxed | Not supported |
| Web search | Supported | Plugin-based | Not supported |
| Subagent nesting | Single level | Multi-level | None |
| Session persistence | File-based | Adapter-managed | None |
| Speed | Moderate | Moderate | Fast |
| Cost per call | Higher | Variable | Lowest |
| Context window | Large | Variable | Small |
| Approval model | Built-in | Adapter-provided | None needed |
| Sandbox isolation | OS-level | Container-based | Full (no side effects) |

## Selection Algorithm

### Step 1: Determine Hard Requirements

For each agent in the topology (as compiled by 21 Topology Compiler), identify non-negotiable requirements by consulting the target runtime's Capability Manifest (see 57):

1. If the agent needs file read/write access -> exclude NanoClaw
2. If the agent needs code execution -> exclude NanoClaw
3. If the agent needs multi-level subagent nesting -> exclude Claude Code
4. If the agent needs full sandbox isolation -> prefer NanoClaw or OpenClaw with containers
5. If legal or policy boundaries restrict data residency -> exclude runtimes that violate the boundary

### Step 2: Apply Soft Preferences

Among runtimes that satisfy hard requirements, rank by soft preferences:

| Preference | Ranking |
|---|---|
| Cost Low constraint | NanoClaw > OpenClaw > Claude Code |
| Speed High constraint | NanoClaw > Claude Code > OpenClaw |
| Rigor High constraint | Claude Code > OpenClaw > NanoClaw |
| Session persistence needed | Claude Code > OpenClaw > NanoClaw |
| Repository-centric work | Claude Code > OpenClaw > NanoClaw |

### Step 3: Select

1. If one runtime satisfies all hard requirements and ranks highest on soft preferences, select it
2. If trade-offs exist, prefer the runtime that satisfies the highest-priority constraint (using the Constraint Compiler precedence order, see 03)
3. If the mission has agents with different requirements, use a multi-runtime topology (see below)

## Multi-Runtime Missions

A single mission can span multiple runtimes when different agents have incompatible requirements.

### Architecture

1. The lead agent runs on the primary runtime (typically Claude Code for repository work, OpenClaw for API-orchestrated work)
2. Specialist agents run on the runtime best suited to their task
3. The adapter layer handles cross-runtime communication

### Cross-Runtime Communication

1. Agent outputs are serialized as text (the universal exchange format)
2. The adapter on each runtime is responsible for receiving inputs and formatting them for its runtime's interface
3. Memory synchronization: Mission Memory is stored externally and accessed by adapters on all runtimes
4. Provenance Graph: A single graph spans all runtimes. Each adapter logs events using the common record schema (see 06)

### Runtime Routing Table

The lead agent maintains a runtime routing table:

| Agent | Runtime | Reason |
|---|---|---|
| (populated per mission) | | |

This table is produced during topology compilation (see 21) and recorded in the Provenance Graph.

## Runtime Fallback Chain

When the selected runtime is unavailable or fails:

| Primary | Fallback 1 | Fallback 2 | Notes |
|---|---|---|---|
| Claude Code | OpenClaw | Reduce scope | OpenClaw requires adapter to manage file operations |
| OpenClaw | Claude Code | NanoClaw (if task is narrow) | Claude Code may have nesting limitations |
| NanoClaw | OpenClaw | Claude Code | Upgrade to a more capable runtime |

Fallback triggers:

1. Runtime is unreachable (network failure, service outage)
2. Runtime exceeds resource limits (token budget, rate limits)
3. Runtime cannot provide a required tool that was expected to be available

When a fallback is triggered:

1. Log the failure and fallback to the Provenance Graph
2. Migrate agent context to the fallback runtime (via the adapter)
3. If state migration is not possible, restart the agent with context from Mission Memory
4. Notify the lead agent of the runtime change

## Default Runtime Selection

When no specific constraints drive the choice:

| Mission Type | Default Runtime | Rationale |
|---|---|---|
| Build | Claude Code | File access and code execution needed |
| Execution | Claude Code | File access and code execution needed |
| Design | Claude Code | Repository context valuable |
| Exploration | OpenClaw | Broad tool access, multi-level nesting useful |
| Decision | Claude Code | Single-session, repository context |
| Audit | Claude Code | File access for reviewing artifacts |
| Synthesis | OpenClaw | Multi-source, benefits from parallel sessions |
| Negotiation | OpenClaw | Multi-perspective, benefits from isolated sessions |

## Router Integration

### Integration with Router
The Runtime Selection Policy runs BEFORE the Router (see 20) makes its final decision, not after. The flow is:

1. The Constraint Compiler (see 03) produces normalized constraints including tool requirements, safety requirements, and cost constraints
2. The Runtime Selection Policy evaluates available runtimes against these constraints and produces a ranked list of viable runtimes with their Capability Manifests (see 57)
3. The Router receives the top-ranked runtime's Capability Manifest as its 9th input (see 20 Runtime-Aware Routing)
4. The Router produces a topology that is compatible with the selected runtime
5. The Topology Compiler validates the topology against the runtime manifest (see 21 Runtime Capability Validation)

This ordering prevents the system from producing topologies that are impossible on the available runtime.

### Runtime Pre-selection
If the human specifies a runtime in the mission definition, steps 1-2 are skipped. The specified runtime's manifest is passed directly to the Router. If the specified runtime cannot satisfy the mission's constraints, the Constraint Compiler flags a validation error (see 03).

### Multi-Runtime Pre-selection
If the mission will span multiple runtimes:
1. The primary runtime is selected using the standard algorithm
2. For each agent role in the expected topology, the Policy identifies the optimal runtime
3. The Router receives all manifests and the Policy's per-role runtime recommendations
4. The Topology Compiler assigns each agent to a runtime and validates per-agent compatibility
