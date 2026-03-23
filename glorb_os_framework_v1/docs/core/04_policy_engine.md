# 04 Policy Engine

## Purpose

The Policy Engine selects and enforces a coherent set of operational parameters that govern how the system behaves during a mission. A policy is a named configuration preset that adjusts topology, critique, memory, gates, and autonomy in concert.

## Common Policies

1. Fastest acceptable
2. Highest rigor
3. Lowest cost
4. Balanced
5. High exploration
6. Low hallucination bias
7. Human in loop required
8. Autonomous unless blocked

## Policy Parameter Tables

### Fastest Acceptable

| Parameter | Setting |
|---|---|
| Max agent count | 3 |
| Critique intensity | Self-review only, no dedicated critic |
| Quality gates | Shipping readiness gate only |
| Memory depth | Working memory and session memory only |
| Deliverable depth | Minimal, core output only |
| Disagreement engine | Disabled |
| Human checkpoints | None unless risk is High |
| Autonomy level | Full autonomy |

### Highest Rigor

| Parameter | Setting |
|---|---|
| Max agent count | No cap, size to mission |
| Critique intensity | Dedicated Critic plus Verifier. Minimum 2 review passes |
| Quality gates | All 5 gates required, sequential |
| Memory depth | Full provenance across all layers |
| Deliverable depth | Full with all sections, provenance annotations, verification report |
| Disagreement engine | Enabled with minimum 1 challenge per major decision |
| Human checkpoints | Required at architecture gate and shipping gate |
| Autonomy level | Restricted. Escalate on uncertainty |

### Lowest Cost

| Parameter | Setting |
|---|---|
| Max agent count | 2 |
| Critique intensity | Self-review only |
| Quality gates | Factual confidence gate only |
| Memory depth | Working memory only. No persistence |
| Deliverable depth | Minimal |
| Disagreement engine | Disabled |
| Human checkpoints | None |
| Autonomy level | Full autonomy |

### Balanced

| Parameter | Setting |
|---|---|
| Max agent count | 5 |
| Critique intensity | Single Critic, one review pass |
| Quality gates | Sufficient context, factual confidence, shipping readiness |
| Memory depth | Working, session, and mission memory |
| Deliverable depth | Standard with rationale section |
| Disagreement engine | Enabled for decisions with risk Medium or higher |
| Human checkpoints | At mission start and final deliverable |
| Autonomy level | Autonomous within defined scope |

### High Exploration

| Parameter | Setting |
|---|---|
| Max agent count | No cap |
| Critique intensity | Light. Critic provides suggestions, not blockers |
| Quality gates | Sufficient context gate only |
| Memory depth | Full breadth. Persist all research findings |
| Deliverable depth | Structured findings, not formal deliverable |
| Disagreement engine | Enabled. Encourage diverse perspectives |
| Human checkpoints | Optional check-ins, not gates |
| Autonomy level | High autonomy with broad scope |

### Low Hallucination Bias

| Parameter | Setting |
|---|---|
| Max agent count | 4 |
| Critique intensity | Dedicated Factual Challenger (see 25 Disagreement Engine) |
| Quality gates | Factual confidence gate required with strict threshold |
| Memory depth | Full provenance. Every claim must trace to a source |
| Deliverable depth | Standard with mandatory source citations |
| Disagreement engine | Enabled. Factual challenger always active |
| Human checkpoints | At factual confidence gate if confidence is below threshold |
| Autonomy level | Restricted on factual claims. Must cite or flag uncertainty |

### Human In Loop Required

| Parameter | Setting |
|---|---|
| Max agent count | No cap |
| Critique intensity | Per policy, but all critique results surfaced to human |
| Quality gates | All gates require human sign-off |
| Memory depth | Full provenance for audit |
| Deliverable depth | Full with decision log |
| Disagreement engine | Enabled. Unresolved disagreements escalate to human |
| Human checkpoints | Every phase transition, every major decision |
| Autonomy level | Minimal. Propose and wait for approval |

### Autonomous Unless Blocked

| Parameter | Setting |
|---|---|
| Max agent count | No cap |
| Critique intensity | Single Critic, auto-resolve unless blocked |
| Quality gates | All gates automated. Escalate only on failure |
| Memory depth | Standard. Compress aggressively between sessions |
| Deliverable depth | Standard |
| Disagreement engine | Enabled. Auto-resolve with evidence weighting. Escalate ties |
| Human checkpoints | None unless an automated gate fails or a blocker is hit |
| Autonomy level | Full autonomy until blocked, then escalate |

## Policy Selection

The Policy Engine selects a policy based on the Constraint Compiler output (see 03).

| Constraint Profile | Default Policy |
|---|---|
| Deadline High, Quality Low | Fastest acceptable |
| Risk High, Quality High | Highest rigor |
| Budget Low | Lowest cost |
| No strong constraints | Balanced |
| Ambiguity High, mission type Exploration | High exploration |
| Domain requires factual accuracy (medical, legal, financial) | Low hallucination bias |
| Human approval set to approve-all | Human in loop required |
| Human approval set to autonomous, Risk not High | Autonomous unless blocked |

## Policy Composition

Policies are not composable by default. If two policies are equally indicated by constraints, the Constraint Compiler precedence order (see 03 Constraint Conflicts) determines which wins.

Exception: Low hallucination bias may be layered on top of any other policy as an additive overlay. When layered, it adds the Factual Challenger and factual confidence gate requirements to the base policy without replacing other settings.

No other policy may be layered or composed. If constraints indicate two policies equally (for example, Deadline High and Risk High), the Constraint Compiler precedence order (see 03 Constraint Conflicts) determines which single policy is selected. The selected policy applies in full; the other is not partially applied. If the human operator needs behavior from multiple policies, they should use the OVERRIDE command (see 09) to adjust individual parameters after the base policy is selected.

## Policy Override

The human operator may override the selected policy at any time using the OVERRIDE command (see 09 Human Command Layer). Policy overrides are logged to the Provenance Graph.
