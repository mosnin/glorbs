# 60 Error Taxonomy

## Purpose

The Error Taxonomy provides a unified classification system for all errors that can occur across the GLORB framework. It standardizes error identification, severity, and response, enabling consistent handling regardless of which layer or component produces the error.

## Error Categories

### 1 Mission Errors

Errors in the mission definition or objective.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| M-001 | Invalid mission definition | Mission spec is incomplete or malformed | Blocking | Reject. Escalate to human (see 07) |
| M-002 | Contradictory constraints | Two or more constraints cannot be simultaneously satisfied | Blocking | Constraint Compiler rejects with specific conflict (see 03) |
| M-003 | Impossible triangle | Budget Low + Quality High + Deadline High | Blocking | Human must relax one constraint |
| M-004 | Undefined mission type | Mission type does not match any known type (see 42) | Blocking | Escalate to human for classification |
| M-005 | Scope too broad | Mission objective cannot be decomposed into addressable sub-problems | Warning | Suggest scope reduction or decomposition |

### 2 Routing Errors

Errors in the routing or topology compilation process.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| R-001 | Low routing confidence | Router cannot determine a clear path | Warning | Flag for human confirmation (see 20) |
| R-002 | No viable topology | No topology satisfies all constraints | Blocking | Reduce constraints or escalate |
| R-003 | Agent count exceeded | Selected topology requires more agents than policy allows | Recoverable | Downgrade topology or increase limit |
| R-004 | Capability gap | No available agent covers a required dimension | Recoverable | Invoke Agent Foundry (see 15) |

### 3 Agent Errors

Errors in agent behavior or configuration.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| A-001 | Validation failure | Agent spec fails validation (see 10) | Blocking | Return to spec author for revision |
| A-002 | Scope violation | Agent operates outside defined scope | Warning | Block action, notify lead (see 08) |
| A-003 | Authority violation | Agent attempts action beyond authority level | Blocking | Block action, log violation, escalate |
| A-004 | Role overlap | Two agents have overlapping scope | Recoverable | Recompile topology to disambiguate (see 07) |
| A-005 | Agent unresponsive | Agent fails to produce output within expected timeframe | Recoverable | Retry, then replace (see 09 REPLACE) |
| A-006 | Repeated governance violations | Same agent violates governance 3+ times | Blocking | Retire agent, escalate to human (see 08) |

### 4 Tool Errors

Errors in tool execution.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| T-001 | Tool unavailable | Required tool is not available on the runtime | Recoverable | Switch runtime or reduce scope (see 07) |
| T-002 | Tool execution failure | Tool returns an error during execution | Recoverable | Retry up to 3 times, then escalate (see 07) |
| T-003 | Tool permission denied | Agent lacks permission to use the requested tool | Blocking | Block action, log violation |
| T-004 | Tool output invalid | Tool returns output that cannot be parsed or used | Recoverable | Retry with adjusted input. If persistent, switch tool or escalate |

### 5 Memory Errors

Errors in memory operations.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| ME-001 | Memory conflict | Contradictory items in memory (see 33) | Recoverable | Apply conflict resolution protocol |
| ME-002 | Memory access denied | Agent attempts to access a layer beyond its scope | Blocking | Block access, log violation |
| ME-003 | Memory corruption | Memory item is malformed or unreadable | Recoverable | Restore from provenance if possible, flag gap |
| ME-004 | Context budget exceeded | Retrieved memory exceeds the agent's working context capacity | Recoverable | Compress retrieved content (see 32) |

### 6 Quality Errors

Errors detected during quality evaluation.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| Q-001 | Gate failure | Work product fails a Quality Gate (see 26) | Recoverable | Return to producer with feedback. Retry up to 2 times |
| Q-002 | Repeated gate failure | Same gate fails 2+ times on same work product | Blocking | Escalate to human review |
| Q-003 | Unsupported factual claim | Factual Confidence Gate finds unsourced claims presented as facts | Recoverable | Return to producer for source attribution |
| Q-004 | Unresolvable disagreement | Disagreement Engine cannot resolve after 2 cycles (see 25) | Blocking | Escalate to human |

### 7 Runtime Errors

Errors in the runtime environment.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| RT-001 | Runtime unavailable | Selected runtime is unreachable | Recoverable | Fallback to next runtime in chain (see 55) |
| RT-002 | Token budget exhausted | Mission has consumed its token allocation | Blocking | Escalate to human. Options: increase budget, reduce scope, switch to cheaper runtime |
| RT-003 | Rate limit hit | Runtime rate limits prevent further execution | Recoverable | Wait and retry with backoff |
| RT-004 | Runtime mismatch | Agent requires capabilities the runtime cannot provide | Recoverable | Switch runtime (see 07) |

### 8 Orchestration Errors

Errors in coordination and execution flow.

| Error Code | Name | Description | Severity | Response |
|---|---|---|---|---|
| O-001 | Deadlock | Two or more agents are mutually blocked (see 22) | Recoverable | Lead agent breaks deadlock by assigning priority |
| O-002 | Merge failure | Merge and Reduction cannot produce a unified output (see 24) | Recoverable | Recompile topology, retry merge. Max 2 attempts |
| O-003 | Orphaned subagent | Subagent's parent was retired while subagent is active (see 23) | Recoverable | Reassign orphan to lead agent |
| O-004 | Cascading failure | 3+ distinct failure classes in one mission (see 07) | Blocking | Halt non-essential work, escalate to human |
| O-005 | Graph cycle detected | Mission Graph contains a circular dependency (see 27) | Blocking | Insert Synthesizer to break cycle, or escalate |

## Severity Levels

| Level | Definition | System Behavior |
|---|---|---|
| Blocking | Execution cannot continue past this point | Halt affected work stream. Escalate immediately |
| Recoverable | Execution can continue after applying a recovery strategy | Apply the mapped recovery strategy (see 07). Log the error |
| Warning | Execution can continue but something is suboptimal | Log the warning. Surface to lead agent. Continue execution |

## Error Logging

Every error is logged to the Provenance Graph (see 06) with:

1. Error code and name
2. Timestamp
3. Affected agent or component
4. Error context (what was happening when the error occurred)
5. Severity
6. Recovery action taken (or escalation performed)
7. Resolution status (resolved, escalated, unresolved)
