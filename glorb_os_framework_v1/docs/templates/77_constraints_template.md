# 77 Constraints Template

## Deadline
*The urgency level for completing this mission. Normalized by the Constraint Compiler (doc 03). Valid values: Low (no deadline or flexible), Medium (soft deadline, days to weeks), High (hard deadline, hours to days). Drives topology sizing and memory compression policy. Example: "Medium"*

## Budget
*The token and resource cost tolerance for this mission. Valid values: Low (strict cap, minimize usage), Medium (track but do not cap), High or Unlimited (no cap, size to mission complexity). Affects agent count limits and memory persistence. Example: "Medium"*

## Runtime Limits
*Any specific constraints on runtime selection or usage beyond what the budget and deadline fields capture. Include tool availability requirements, data residency restrictions, or compliance boundaries that exclude specific runtimes. Example: "Must use claude_code — repository file access required. No external API calls to non-approved endpoints."*

## Quality Bar
*The required quality level for this mission's outputs. Valid values: Low (best effort acceptable), Medium (should meet standards), High (must pass formal verification). Determines critique intensity, gate selection, and deliverable depth. Example: "High"*

## Risk Tolerance
*The acceptable level of risk for this mission's failure or side effects. Valid values: Low (failure has severe or irreversible consequences), Medium (failure has moderate cost), High (failure is acceptable and reversible). Drives Critic inclusion, Disagreement Engine activation, and human approval thresholds. Example: "Low"*

## Reversibility
*Whether the actions taken in this mission can be undone if something goes wrong. Valid values: Fully reversible, Partially reversible, Irreversible. Affects topology design and human approval requirements — irreversible missions require stricter gates. Example: "Partially reversible"*

## Legal Or Policy Constraints
*Any legal, regulatory, compliance, or organizational policy boundaries that the mission must not violate. These take highest precedence in conflict resolution (doc 03). Example: "No PII may be written to external storage. All outputs must comply with internal security review policy."*

## Human Approval Boundaries
*The specific actions or decisions that require explicit human approval before the mission proceeds. Valid values: Autonomous (no approval needed), Checkpoint at major decisions, Approve every significant action. Must be consistent with Risk tolerance — autonomous is not valid when Risk is High. Example: "Checkpoint at major decisions"*
