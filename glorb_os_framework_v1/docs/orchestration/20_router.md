# 20 Router

## Purpose

The Router is the primary decision engine that translates a mission into an execution strategy. It evaluates mission characteristics against a scoring model and selects the appropriate execution path: solo agent, specialist, team topology, or escalation.

## Router Inputs

1. Mission type
2. Complexity
3. Ambiguity
4. Risk
5. Reversibility
6. Time pressure
7. Cost policy
8. Required rigor

## Router Outputs

1. Single agent
2. Existing specialist
3. New specialist
4. Team topology
5. Escalation
6. Human review

## Input Scoring

Each input is scored on a 3-point scale before routing.

| Input | Low (1) | Medium (2) | High (3) |
|---|---|---|---|
| Complexity | Single concern, clear path | Multiple concerns, known patterns | Cross-cutting concerns, novel territory |
| Ambiguity | Requirements fully specified | Some interpretation needed | Requirements vague or contradictory |
| Risk | Fully reversible, no side effects | Partially reversible, limited blast radius | Irreversible, broad impact |
| Time pressure | No deadline | Soft deadline | Hard deadline, blocking others |
| Required rigor | Best effort acceptable | Should be reviewed | Must be verified and audited |

## Routing Decision Matrix

Sum the scores for Complexity, Ambiguity, and Risk to produce a Routing Score (3-9).

| Routing Score | Time Pressure | Output |
|---|---|---|
| 3-4 | Any | Single agent |
| 5 | Low | Existing specialist |
| 5 | Medium-High | Planner and executor |
| 6 | Low | Existing specialist with critic |
| 6 | Medium | Architect plus builders |
| 6 | High | Planner and executor (parallel builders) |
| 7 | Low | Architect plus builders plus critic |
| 7 | Medium | Researcher swarm plus reducer |
| 7 | High | Strategist plus operator plus verifier |
| 8-9 | Low-Medium | Full topology with disagreement engine |
| 8-9 | High | Escalation to human review |

## Rigor Overlay

After the matrix produces a candidate output, the Required Rigor score modifies it.

1. Rigor Low: No modification
2. Rigor Medium: Append a Reviewer agent to any topology with 2 or more agents
3. Rigor High: Append both Critic and Verifier agents. Enable the Disagreement Engine. Require at least one Quality Gate before deliverable compilation

## Cost Policy Override

The active Policy (see 04 Policy Engine) can override the matrix output.

1. Fastest acceptable: Downgrade team topologies to the smallest viable topology. Remove critic loops if Rigor is not High
2. Lowest cost: Cap agent count at 3. Prefer solo agent or planner-executor
3. Highest rigor: Upgrade any solo agent to specialist-with-critic. Never downgrade topology
4. Balanced: Use the matrix output without modification

## Routing Confidence

The Router assigns a confidence level to its decision.

1. High confidence: All inputs are scored, no ambiguity conflicts, matrix produces a single clear path
2. Medium confidence: One input is uncertain or two inputs conflict (e.g., high risk but low rigor). Router proceeds but flags the decision for review at the first Quality Gate
3. Low confidence: Two or more inputs are uncertain, or the mission type is novel. Router emits an Escalation output and requests human confirmation before proceeding

## Re-routing

Routing decisions can be revised during execution when:

1. A Quality Gate fails and the failure class maps to "Recompile topology" (see 07 Failure Recovery)
2. A specialist agent escalates because the task exceeds its scope
3. The human issues a REDIRECT or RESHAPE command (see 09 Human Command Layer)
4. The Disagreement Engine produces an unresolvable conflict after 2 challenge-response cycles

Re-routing triggers a new pass through this decision matrix with updated inputs. The Provenance Graph (see 06) records both the original and revised routing decisions.

## Runtime-Aware Routing

After the Routing Decision Matrix produces a candidate topology, the Router checks it against the available runtime's Capability Manifest (see 57 Runtime Capability Manifest).

### Runtime Compatibility Check

1. If the candidate topology requires subagent nesting deeper than the runtime supports (subagent_nesting_depth), downgrade to a flatter topology
2. If the candidate topology requires more concurrent agents than the runtime supports (max_concurrent_agents), serialize parallel branches or reduce agent count
3. If the candidate topology requires stateful agents but the runtime is stateless (session_model), switch to async orchestration mode (see 22) or select a different runtime
4. If the candidate topology requires human commands (FREEZE, RESUME) but the runtime does not support them (human_command_support), remove checkpoint-dependent workflow steps or escalate to human for runtime selection

### Router Input 9: Runtime Capability

The Router now accepts a 9th input: the Capability Manifest of the target runtime (or manifests, if multiple runtimes are available). This input is optional for backward compatibility. When absent, the Router assumes a Level 3 (Full) runtime.

### Routing Decision Matrix: Runtime Level Constraint

If the runtime is Level 1 (Minimal), the Router constrains output to Solo Agent or Escalation only. If Level 2 (Standard), all topologies are available. If Level 3 (Full), all topologies with full governance features.

## Router Anti-patterns

1. Routing every mission to a full team topology regardless of complexity
2. Routing high-risk missions to solo agents to save cost
3. Re-routing more than twice for the same mission without human review
4. Ignoring the cost policy when time pressure is low
