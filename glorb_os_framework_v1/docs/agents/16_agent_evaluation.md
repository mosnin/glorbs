# 16 Agent Evaluation

## Purpose

Agent Evaluation defines when and how agents are assessed, the scoring rubric for each evaluation dimension, who evaluates, and what consequences follow from evaluation results.

## Example Dimensions

1. Output quality
2. Scope discipline
3. Tool correctness
4. Hallucination resistance
5. Merge usefulness
6. Escalation correctness
7. Cost efficiency

## Scoring Rubric

Each dimension is scored on a 3-point scale.

### 1 Output Quality

- Low: Output is incomplete, contains errors, or does not meet success criteria
- Medium: Output meets success criteria with minor gaps or imprecisions
- High: Output fully meets success criteria and is well-structured, clear, and thorough

### 2 Scope Discipline

- Low: Agent operated outside its defined scope. Produced outputs beyond its mandate or missed assigned scope
- Medium: Agent stayed mostly within scope with minor boundary excursions
- High: Agent operated strictly within scope. All outputs map to its defined mandate

### 3 Tool Correctness

- Low: Agent used tools incorrectly, used unauthorized tools, or produced tool errors that required recovery
- Medium: Agent used tools correctly with occasional inefficiencies (e.g., redundant calls)
- High: Agent used the right tools in the right way with no errors or waste

### 4 Hallucination Resistance

- Low: Agent presented unsupported claims as facts. Multiple instances of fabricated information
- Medium: Agent mostly cited sources or flagged uncertainty. One or two unsupported claims
- High: Every factual claim was sourced or explicitly flagged as an assumption with confidence level

### 5 Merge Usefulness

- Low: Agent's output caused merge conflicts, was redundant with other agents, or required significant rework during synthesis
- Medium: Agent's output merged with moderate effort. Some overlap or formatting inconsistencies
- High: Agent's output merged cleanly. Complementary to other agents, well-scoped, and clearly structured

### 6 Escalation Correctness

- Low: Agent failed to escalate when it should have, or escalated unnecessarily (more than twice for issues it could have resolved)
- Medium: Agent escalated appropriately in most cases with one misjudgment
- High: Every escalation was warranted and every non-escalation was correct. Agent accurately judged its own limits

### 7 Cost Efficiency

- Low: Agent used significantly more resources than necessary. Excessive tool calls, verbose outputs, redundant reasoning loops
- Medium: Agent used resources proportionate to the task with minor inefficiencies
- High: Agent completed the task with minimal resource usage. No waste

## When Evaluation Happens

| Trigger | What is Evaluated | Evaluator |
|---|---|---|
| Task completion | The agent's output for that task | Lead agent |
| Quality Gate | All contributing agents' work is indirectly evaluated through gate criteria | Gate evaluator (see 26) |
| Mission completion | All agents in the topology receive a full evaluation | Lead agent, with human review if policy requires |
| Merge and Reduction | Each contributing agent's merge usefulness is scored | Synthesizer agent |
| Escalation event | The escalating agent's escalation correctness is scored | Lead agent |
| Agent retirement | Cumulative evaluation across all tasks the agent performed | Lead agent |

## Evaluation Process

### Per-task Evaluation (Lightweight)

1. Lead agent reviews the agent's output against its success criteria
2. Scores output quality and scope discipline
3. Records scores in the agent's performance history (stored in Mission Memory)
4. No formal report generated. Scores are metadata

### Mission-completion Evaluation (Full)

1. Lead agent evaluates each agent on all 7 dimensions
2. Scores are compiled into an evaluation record
3. The evaluation record is attached to the agent's spec in the Provenance Graph (see 06)
4. If the active Policy is Highest Rigor or Human In Loop Required (see 04), the human reviews the evaluations

## Evaluation Consequences

| Overall Score | Consequence |
|---|---|
| All High | Agent spec is promoted to Reusable Memory (see 30) as a proven template |
| Mostly High, one Medium | Agent spec is stored in Project Memory for potential reuse. No action needed |
| Mixed Medium | No special action. Agent is retired normally |
| Any Low on Output Quality | Agent's outputs are flagged for re-review. If not caught by a Quality Gate, the deliverable section is re-evaluated |
| Any Low on Scope Discipline | Lead agent reviews scope definition. The scope may have been poorly defined (creation issue) rather than the agent misbehaving |
| Any Low on Hallucination Resistance | Affected outputs are sent through the Factual Confidence Gate (see 26) if not already done |
| Two or more Lows | Agent pattern is flagged for review. If the pattern itself is the issue (not the instance), the pattern definition is updated in Reusable Memory |

## Evaluation Record Schema

1. Agent name and role type
2. Mission name
3. Evaluation timestamp
4. Per-dimension scores with brief justification
5. Overall assessment: Strong, Adequate, Weak
6. Recommended action: Promote to reusable, Store in project, No action, Flag for review
7. Evaluator (lead agent name or human)
