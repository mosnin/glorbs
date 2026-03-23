# 07 Failure Recovery

## Purpose

Failure Recovery defines the mapping between failure classes and recovery strategies, including retry limits, escalation chains, and partial failure handling. Every failure is logged to the Provenance Graph (see 06) before recovery begins.

## Failure Classes

1. Invalid mission definition
2. Poor routing
3. Role overlap
4. Tool failure
5. Runtime mismatch
6. Memory conflict
7. Failed merge
8. Quality gate failure
9. Policy violation

## Recovery Strategies

1. Retry
2. Recompile topology
3. Spawn a specialist
4. Escalate to human review
5. Reduce scope
6. Switch runtime backend
7. Rebuild deliverable from preserved provenance

## Failure-to-Recovery Mapping

| Failure Class | Primary Strategy | Fallback Strategy | Max Retries | Escalation Trigger |
|---|---|---|---|---|
| Invalid mission definition | Escalate to human review | — | 0 | Immediate. Cannot proceed without valid mission |
| Poor routing | Recompile topology | Escalate to human review | 1 | Router produces Low confidence on re-route (see 20) |
| Role overlap | Recompile topology | Spawn a specialist to disambiguate | 1 | Overlap persists after topology recompilation |
| Tool failure | Retry | Switch runtime backend | 3 | Tool fails 3 consecutive times or is unavailable on all runtimes |
| Runtime mismatch | Switch runtime backend | Reduce scope to fit available runtime | 1 | No compatible runtime available |
| Memory conflict | Spawn Memory Manager specialist | Escalate to human review | 2 | Conflict involves contradictory facts with equal evidence weight |
| Failed merge | Recompile topology | Escalate to human review | 2 | Two merge attempts fail at the same convergence point (see 24) |
| Quality gate failure | Retry with feedback | Reduce scope | 2 | Same gate fails twice with the same root cause |
| Policy violation | Escalate to human review | — | 0 | Immediate. Policy violations cannot be auto-resolved |

## Recovery Protocols

### Retry

1. Log the failure with context (what failed, inputs, error details)
2. Wait one execution cycle before retrying (prevents hot loops)
3. Retry with the same inputs unless the failure suggests an input problem
4. If the failure includes a corrective suggestion (e.g., from a Quality Gate), apply the suggestion before retrying
5. Increment the retry counter. If the counter exceeds the Max Retries for this failure class, fall through to the Fallback Strategy
6. Each retry is recorded in the Provenance Graph as a distinct event

### Recompile Topology

1. The lead agent or Router re-evaluates the mission inputs with updated information (e.g., the failure context)
2. The Router runs a new pass through its decision matrix (see 20 Router) with the failure as an additional input
3. The new topology may add agents (e.g., a Critic was missing), remove agents (e.g., conflicting roles), or restructure the execution order
4. Agents already in progress are allowed to complete their current task before the topology change takes effect. Their outputs are preserved
5. The topology change is recorded in the Provenance Graph with before and after states

### Spawn a Specialist

1. The Agent Foundry (see 15) evaluates the gap that caused the failure
2. A specialist agent is created from the appropriate pattern (see 14 Specialist Agent Patterns)
3. The new agent receives context from Mission Memory (see 30 Memory Layers) including the failure that triggered its creation
4. The new agent is inserted into the active topology. The lead agent assigns it a specific task
5. The spawning event is recorded in the Provenance Graph

### Escalate to Human Review

1. The system composes an escalation summary including: what failed, what was attempted, what the options are
2. The escalation is presented to the human operator via the Human Command Layer (see 09)
3. Execution pauses at the failure point. Other independent work streams may continue
4. The human may respond with: a fix, a scope reduction, a policy override, or a mission abort
5. The human's decision is recorded in the Provenance Graph

### Reduce Scope

1. The lead agent identifies the portion of the mission objective that is blocked by the failure
2. The blocked portion is removed from the active mission scope
3. The remaining scope is re-evaluated for viability. If the remaining scope is less than 50 percent of the original, escalate to human review instead
4. The scope reduction is documented in the deliverable as a known limitation
5. The scope change is recorded in the Provenance Graph

### Switch Runtime Backend

1. Evaluate alternative runtimes against the Runtime Adapter Contract (see 51)
2. Select the best alternative that satisfies the mission's tool and capability requirements
3. Migrate active agent state to the new runtime. If state migration is not supported, restart affected agents with context from Mission Memory
4. The runtime switch is recorded in the Provenance Graph

### Rebuild Deliverable from Preserved Provenance

1. This is the strategy of last resort when a deliverable is corrupted or lost but the work that produced it is intact
2. The system traverses the Provenance Graph to reconstruct the chain of contributions
3. A Synthesizer agent (see 14) reassembles the deliverable from the preserved agent outputs, decisions, and Quality Gate results
4. The rebuilt deliverable is flagged as reconstructed and must pass the Shipping Readiness Gate (see 26) before delivery

## Partial Failure Handling

When some agents in a topology fail while others succeed:

1. Evaluate whether the successful outputs are independently useful (i.e., they address complete sub-objectives)
2. If yes: deliver the successful portions. Document the failed portions as incomplete. The deliverable is flagged as partial
3. If no: the successful outputs depend on the failed outputs. Trigger recovery for the failed agents first. Hold successful outputs until dependencies are resolved
4. Partial deliverables require human acknowledgment before delivery unless the Policy is Autonomous Unless Blocked (see 04)

## Cascading Failure Prevention

1. If 3 or more distinct failure classes occur within a single mission, halt all non-essential work streams and escalate to human review
2. A single agent failing does not halt the entire topology unless other agents have blocking dependencies on it
3. Recovery actions themselves can fail. If a recovery action fails, do not retry the recovery. Escalate to the next level (fallback strategy or human review)
4. Never retry a recovery strategy more than once. Recovery-of-recovery loops are prohibited
