# 23 Subagent Spawning

## Purpose

Subagent Spawning defines the rules for when and how a parent agent creates child agents, what context and permissions the child inherits, how the child reports results, and when the child is retired. It governs the hierarchical expansion of the topology during execution.

## Spawning Questions

1. Why is a subagent needed
2. What context should it inherit
3. What memory should it access
4. What tools should it use
5. Is it temporary or persistent
6. How does it report back
7. When is it retired

## Spawning Protocol

### Step 1: Justify

Before spawning, the parent agent must answer question 1 using the same criteria as the Agent Creation Framework (see 13).

**Spawn if**:
1. The task requires a distinct scope that the parent should not handle directly (scope separation)
2. The task can execute in parallel with the parent's other work (performance gain)
3. The task requires a different capability profile than the parent has (capability gap)
4. The task benefits from isolation (e.g., adversarial evaluation where the evaluator should not be the producer)

**Do not spawn if**:
1. The parent can complete the task directly within its scope and capability profile
2. The task is too small to justify the overhead of context transfer
3. The agent count limit from the active Policy (see 04) would be exceeded
4. The parent has already spawned the maximum allowed subagents (see Spawn Limits below)

### Step 2: Define Context Inheritance

The parent determines what context the subagent receives. Not all parent context should be passed.

| Context Type | Inherit? | Notes |
|---|---|---|
| Mission objective | Yes | Always. The subagent needs to understand the broader goal |
| Active constraints | Yes | Always. The subagent must operate within the same constraints |
| Parent's current task context | Selective | Only the portion relevant to the subagent's task |
| Parent's working memory | No | Parent's scratch work is private |
| Parent's session history | Selective | Only if the subagent needs historical context from the parent's prior tasks |
| Mission memory | Yes (read) | Subagent should have access to shared mission state |
| Project memory | Per governance | Only if the parent's memory scope includes project level |

### Step 3: Assign Permissions

Subagent permissions follow the inheritance rules from the Governance Model (see 08 Permission Inheritance).

1. Authority: Equal to or lower than parent's authority
2. Tool scope: Equal to or narrower than parent's tool scope
3. Memory scope: Equal to or narrower than parent's memory scope
4. Autonomy level: Equal to or lower than parent's autonomy level

### Step 4: Define Reporting Contract

How the subagent communicates results back to the parent.

1. **Report format**: The subagent produces a structured result matching its output contract (see 10 Agent Primitives)
2. **Report trigger**: On task completion, or at defined checkpoints if the task is long-running
3. **Report content**: Result, status (complete/partial/blocked), issues encountered, assumptions made
4. **Acknowledgment**: The parent acknowledges receipt. The subagent is not retired until the parent acknowledges

### Step 5: Set Retirement Conditions

1. **On completion**: The default. Subagent is retired after the parent acknowledges its result
2. **On parent retirement**: If the parent is retired, the subagent is also retired (see Orphan Handling below)
3. **On timeout**: If the subagent has not completed within a defined time or resource limit, it is retired and its partial results are returned
4. **On cancellation**: The parent may cancel a subagent if the task is no longer needed (e.g., another approach was chosen)

## Spawn Limits

| Active Policy | Max Subagents per Parent | Max Total Agents in Mission |
|---|---|---|
| Fastest acceptable | 2 | 3 |
| Lowest cost | 1 | 2 |
| Balanced | 3 | 5 |
| Highest rigor | No limit | No limit (size to mission) |
| High exploration | No limit | No limit |
| Human in loop required | 3 | No limit |
| Autonomous unless blocked | 3 | No limit |

## Memory Isolation vs Sharing

| Memory Layer | Isolation Model |
|---|---|
| Working memory | Fully isolated. Each subagent has its own working memory |
| Session memory | Isolated by default. Parent can request a summary via Status message (see 22) |
| Mission memory | Shared read access. Write access via lead agent only |
| Project memory | Shared read access per governance. Write access restricted |

## Orphan Handling

An orphan is a subagent whose parent has been retired, paused, or replaced while the subagent is still active.

1. **Detection**: The lead agent monitors for orphans by checking if each active subagent's parent is still active
2. **If parent was retired**: The orphan is reassigned to the lead agent. The lead decides whether to let the orphan complete or retire it
3. **If parent was paused**: The orphan continues to execute. Its results are held until the parent resumes
4. **If parent was replaced**: The replacement agent inherits the orphan. The orphan reports to the replacement agent
5. All orphan events are recorded in the Provenance Graph (see 06)

## Subagent Result Integration

When a subagent completes and returns its result to the parent:

1. The parent validates the result against the subagent's output contract
2. If the result is complete and valid, the parent incorporates it into its own work
3. If the result is partial, the parent evaluates whether the partial result is usable (see 07 Failure Recovery, Partial Failure Handling)
4. If the result is blocked, the parent either resolves the blocker itself, spawns a different subagent, or escalates
5. The subagent's contribution is recorded in the Provenance Graph with the parent as the incorporating agent
