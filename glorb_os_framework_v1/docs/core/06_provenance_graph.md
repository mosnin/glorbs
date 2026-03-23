# 06 Provenance Graph

## Purpose

The Provenance Graph is the audit trail for every decision, contribution, and transformation that occurs during a mission. It enables traceability (who decided what and why), accountability (which agent introduced an assumption), and recovery (rebuild deliverables from preserved history).

## Provenance Should Capture

1. Which agents contributed
2. Which tools were used
3. Which assumptions were introduced
4. Which critiques modified the output
5. Which evidence informed the output
6. Which uncertainties remain unresolved

## Provenance Record Schema

Every event in the Provenance Graph is stored as a record with these fields:

1. **Record ID**: Unique identifier for this provenance entry
2. **Timestamp**: When the event occurred (relative to mission start)
3. **Event type**: One of contribution, decision, transformation, critique, escalation, gate_result, routing, failure, recovery, override
4. **Agent**: The agent that produced or triggered this event
5. **Phase**: The mission phase during which this occurred (planning, execution, review, synthesis)
6. **Input references**: List of Record IDs that served as inputs to this event
7. **Output references**: List of Record IDs that this event produced
8. **Content summary**: Brief description of what happened
9. **Evidence**: Sources, tool outputs, or reasoning chains that support this event
10. **Assumptions**: Any assumptions introduced by this event, with confidence levels
11. **Uncertainties**: Unresolved questions flagged by this event
12. **Status**: Active (current and valid), superseded (replaced by a later record), or rejected (invalidated by critique or gate failure)

## Event Types

### Contribution

An agent produces a work product or adds information to the mission. Records the agent, what was produced, and what inputs it drew from.

### Decision

A choice is made between alternatives. Records the options considered, the tradeoff analysis, the chosen option, and the rationale. Links to the Decision Support reasoning function (see 02).

### Transformation

An existing work product is modified. Records the before state (via input reference), the after state (via output reference), and what changed. Used during revision cycles after critique.

### Critique

An evaluation finding is recorded. Links to the Critique reasoning function (see 02) and the Disagreement Engine (see 25) if applicable. Records severity, finding, and whether the finding was addressed.

### Escalation

An agent escalates to a higher authority (lead agent or human). Records the blocker, what was attempted, and the escalation target.

### Gate Result

A Quality Gate (see 26) pass or fail. Records the gate name, evaluator, result, issues found, and retry count.

### Routing

A routing or re-routing decision. Records the Router inputs, scores, and selected output (see 20). For re-routing, links to the original routing record.

### Failure

A failure event. Records the failure class (see 07), the affected agent, and the context.

### Recovery

A recovery action taken in response to a failure. Links to the failure record. Records the strategy used and the outcome.

### Override

A human override of an automated decision. Records the original decision, the override, and the human's rationale.

## Provenance Queries

The Provenance Graph supports these query patterns:

1. **Trace forward**: Given a record, find all records that depend on it. Answers "what was built on this decision?"
2. **Trace backward**: Given a record, find all records it depends on. Answers "what led to this output?"
3. **Agent history**: All records produced by a specific agent. Answers "what did this agent do?"
4. **Assumption audit**: All records with non-empty assumptions. Answers "what assumptions are we carrying?"
5. **Uncertainty audit**: All records with non-empty uncertainties. Answers "what remains unresolved?"
6. **Decision log**: All decision-type records in chronological order. Answers "what choices were made?"
7. **Override log**: All override-type records. Answers "where did the human intervene?"
8. **Failure chain**: Given a failure record, trace to its recovery and the subsequent re-execution. Answers "how was this failure handled?"

## Provenance Attachment

Provenance attaches to deliverables at the section level. Each section of a compiled deliverable (see 41 Deliverable Compiler) carries a list of Record IDs that contributed to it. This enables per-section traceability without requiring the reader to traverse the full graph.

## Provenance Storage

1. Provenance records are stored in Mission Memory (see 30 Memory Layers) during execution
2. On mission completion, the full graph is archived to Archived Memory
3. Provenance is never compressed or summarized. It is the raw audit trail
4. If the active Policy is Highest Rigor or Human In Loop Required (see 04), the provenance graph must be included as an appendix to the deliverable

## Provenance Integrity

1. Records are append-only. Existing records cannot be modified, only superseded by new records
2. Every record must have at least one input reference (except the mission start record)
3. Circular references are prohibited. The graph must be a directed acyclic graph
4. Orphan records (records with no forward references and no attachment to a deliverable) are flagged during the Shipping Readiness Gate (see 26)
