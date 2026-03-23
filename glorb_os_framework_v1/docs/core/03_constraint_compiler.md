# 03 Constraint Compiler

## Purpose

The Constraint Compiler translates raw mission constraints into concrete parameters that shape topology selection, agent configuration, and execution policy. It is the bridge between what the human specifies and how the system configures itself.

## Constraint Inputs

1. Deadline
2. Budget
3. Runtime limits
4. Quality bar
5. Risk tolerance
6. Reversibility
7. Legal or policy boundaries
8. Tool availability
9. Human approval requirements

## Compiler Outputs

Constraints should influence:

1. Topology choice
2. Agent count
3. Critique intensity
4. Memory policy
5. Deliverable depth
6. Runtime selection
7. Human oversight requirements

## Constraint Normalization

Each raw constraint is normalized to a standard scale before compilation.

| Constraint | Low | Medium | High |
|---|---|---|---|
| Deadline | No deadline or flexible | Soft deadline, days to weeks | Hard deadline, hours to days |
| Budget | Unlimited or unconcerned | Moderate, track but do not cap | Strict cap, minimize token usage |
| Quality bar | Best effort acceptable | Should meet standards | Must pass formal verification |
| Risk tolerance | Failure is acceptable and reversible | Failure has moderate cost | Failure has severe or irreversible consequences |
| Human approval | Autonomous, report results only | Checkpoint at major decisions | Approve every significant action |

## Constraint-to-Output Mapping

### Topology Choice

| Risk Tolerance | Quality Bar | Deadline | Recommended Topology Modifier |
|---|---|---|---|
| Low risk | Low quality | Any | Solo agent or planner-executor |
| Low risk | High quality | Low pressure | Add Reviewer |
| High risk | Any | Any | Require Critic. Enable Disagreement Engine |
| Any | High quality | High pressure | Parallel builders with Verifier |
| High risk | High quality | Low pressure | Full topology with Critic, Verifier, and human gate |

### Agent Count

1. Budget Low + Deadline High: Cap at 2 agents. Prefer planner-executor
2. Budget Low + Deadline Low: Cap at 3 agents. Allow critic loop
3. Budget Medium: Allow up to 5 agents
4. Budget High or Unlimited: No agent cap. Size to mission complexity

### Critique Intensity

1. Quality bar Low + Risk tolerance Low: No dedicated critic. Self-review only
2. Quality bar Medium or Risk tolerance Medium: Single critic agent with one review pass
3. Quality bar High or Risk tolerance High: Dedicated critic with Disagreement Engine. Minimum 2 review passes
4. Quality bar High and Risk tolerance High: Critic plus Verifier. Disagreement Engine required. Human approval gate before final deliverable

### Memory Policy

1. Deadline High: Aggressive compression. Keep only decisions and deliverables in mission memory
2. Budget Low: Minimal memory persistence. Working and session memory only
3. Quality bar High: Full provenance. Persist all reasoning chains and decision rationale
4. Risk tolerance High: Full provenance plus audit trail. No memory may be discarded without human approval

### Deliverable Depth

1. Quality bar Low + Deadline High: Minimal deliverable. Core output only, no supporting documentation
2. Quality bar Medium: Standard deliverable with rationale section
3. Quality bar High: Full deliverable with all sections populated, provenance annotations, and verification report

### Runtime Selection

1. Tool availability constrains runtime choice. If required tools are unavailable on a runtime, that runtime is excluded
2. Budget Low: Prefer the lowest-cost runtime that satisfies tool requirements
3. Deadline High: Prefer the runtime with lowest latency
4. Legal or policy boundaries: Exclude runtimes that violate data residency or compliance requirements

## Constraint Conflicts

When constraints contradict each other, resolve using this precedence order:

1. Legal or policy boundaries (always win, never overridden)
2. Risk tolerance (safety over speed or cost)
3. Quality bar (correctness over speed)
4. Budget (cost over speed)
5. Deadline (speed is the lowest priority unless it is the only constraint)

When a conflict is detected, the Constraint Compiler logs the conflict and the resolution to the Provenance Graph (see 06) and emits a warning to the human operator.

## Default Values

When a constraint is not specified, these defaults apply:

1. Deadline: Low (no deadline)
2. Budget: Medium (track but do not cap)
3. Quality bar: Medium (should meet standards)
4. Risk tolerance: Medium (moderate cost of failure)
5. Reversibility: Assume partially reversible unless stated otherwise
6. Human approval: Checkpoint at major decisions
7. Tool availability: All tools available on selected runtime
8. Legal or policy boundaries: None unless specified

## Constraint Validation

The compiler rejects a mission with an error if:

1. Risk tolerance is High and Human approval is set to Autonomous
2. Legal boundaries are specified but no compliant runtime is available
3. Budget is Low and Quality bar is High and Deadline is High (impossible triangle, human must relax one)
