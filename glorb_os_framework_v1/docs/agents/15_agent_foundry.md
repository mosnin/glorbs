# 15 Agent Foundry

## Purpose

The Agent Foundry is the system component responsible for detecting capability gaps, creating agents to fill those gaps, and managing the agent creation lifecycle. It is the operational complement to the Agent Creation Framework (see 13), which defines the rules; the Foundry executes them.

## Foundry Functions

1. Detect capability gaps
2. Specify specialist scope
3. Assign tool permissions
4. Assign memory access
5. Define output contract
6. Define evaluation criteria
7. Decide whether the agent is temporary or persistent

## Gap Detection Algorithm

### When Gap Detection Runs

1. During initial topology compilation (see 21 Topology Compiler) when the Router output specifies a topology
2. When a Quality Gate fails and the failure root cause is a missing capability
3. When the Disagreement Engine (see 25) identifies a perspective that no current agent represents
4. When the human issues a RECOMPILE command (see 09 Human Command Layer)

### How Gaps Are Identified

1. Extract the required capability dimensions from the mission constraints (see 03 Constraint Compiler) and mission type (see 42 Mission Types)
2. For each required dimension at High level, check if at least one agent in the current topology has that dimension at High
3. For each required dimension at Medium level, check if at least one agent has that dimension at Medium or High
4. A gap exists when a required dimension has no agent covering it at the required level
5. A soft gap exists when a dimension is covered but only by one agent, and that agent is already assigned to other tasks (capacity gap rather than capability gap)

### Gap-to-Pattern Mapping

| Gap Type | Recommended Pattern |
|---|---|
| Missing High Abstraction Depth | Strategist or Architect |
| Missing High Precision | Builder or Verifier |
| Missing High Creativity | Strategist |
| Missing High Adversarial Rigor | Critic |
| Missing High Synthesis Strength | Synthesizer |
| Missing High Tool Access | Builder or Verifier |
| Missing High Memory Breadth | Researcher or Memory Manager |
| Missing High Domain Expertise | Researcher (with domain-specific prompt) |
| Capacity gap (agent overloaded) | Clone of the overloaded agent's pattern |

## Foundry Decision Tree

```
Gap detected
  |
  ├─ Can an existing agent cover the gap by adjusting scope?
  |    ├─ Yes -> Extend the existing agent (see 13 Agent Reuse Policy)
  |    └─ No -> Continue
  |
  ├─ Does a matching agent exist in Reusable Memory?
  |    ├─ Yes -> Clone and modify the stored spec
  |    └─ No -> Continue
  |
  ├─ Does a specialist pattern match the gap?
  |    ├─ Yes -> Create from the pattern (see 14)
  |    └─ No -> Continue
  |
  └─ Create a custom agent
       ├─ Specify all primitives manually (see 10)
       ├─ Document why no pattern fits
       └─ Subject to creation approval flow (see 13)
```

## Template vs Freeform Creation

### Template-based (Preferred)

1. Select a specialist pattern (see 14)
2. The pattern provides defaults for capability profile, tool permissions, and behavioral guidance
3. The Foundry customizes: mission-specific scope, input/output contracts, success criteria
4. Faster to validate, more predictable behavior

### Freeform

1. Used only when no pattern fits and the gap is documented
2. All Agent Primitives (see 10) must be explicitly specified
3. Requires lead agent or human approval regardless of policy
4. The Foundry should propose the closest matching pattern and document why it was rejected

## Temporary vs Persistent Agents

| Factor | Temporary | Persistent |
|---|---|---|
| Task scope | Single task or single phase | Multi-phase or cross-mission |
| Reuse potential | Low. Task is unique to this mission | High. Similar tasks will recur |
| Memory needs | Working and session only | Mission or project memory |
| Lifecycle | Instantiated -> Active -> Retired within one phase | May be Paused and Resumed across phases |
| Storage | Not stored after retirement | Spec stored in Reusable Memory for future cloning |

## Foundry Output

When the Foundry creates an agent, it produces:

1. A complete agent spec conforming to Agent Primitives (see 10)
2. A creation record for the Provenance Graph (see 06, see 13 Creation Record)
3. A gap report documenting what gap was filled and how
4. An insertion plan describing where the new agent fits in the topology and who it reports to
