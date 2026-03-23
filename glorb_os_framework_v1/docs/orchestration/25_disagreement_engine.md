# 25 Disagreement Engine

## Purpose

The Disagreement Engine is a structured adversarial process that challenges decisions, outputs, and assumptions to improve quality and catch errors before they reach the deliverable. It defines challenger types, the challenge protocol, resolution rules, and when disagreement blocks progression.

## Relationship to Review Formats

Each challenger type below corresponds to a structured review protocol defined in 44 Review Formats. The review formats specify the evaluation criteria, inputs, and output structure that challengers use when conducting their assessments.

## Example Challenger Types

1. Logic challenger
2. Factual challenger
3. Scope challenger
4. UX challenger
5. Strategic challenger
6. Implementation risk challenger

## Challenger Type Definitions

### 1 Logic Challenger

- Focus: Internal consistency and reasoning validity
- Challenge question: Does the conclusion follow from the premises? Are there logical fallacies, circular reasoning, or unsupported inferential leaps?
- Typical agent: Critic with High Adversarial Rigor
- Output: List of logical issues with specific references to the reasoning chain

### 2 Factual Challenger

- Focus: Accuracy and evidence quality
- Challenge question: Are the factual claims supported by evidence? Are sources reliable? Could the data be outdated or misinterpreted?
- Typical agent: Verifier or a dedicated Factual Challenger agent
- Output: Per-claim assessment with source verification status

### 3 Scope Challenger

- Focus: Boundary appropriateness
- Challenge question: Is this work within the defined scope? Does it address the right problem? Is anything important excluded?
- Typical agent: Reviewer or Critic
- Output: Scope alignment assessment mapping work product to mission objectives

### 4 UX Challenger

- Focus: Usability and human comprehension
- Challenge question: Can the target audience understand and act on this output? Is the structure intuitive? Are there accessibility issues?
- Typical agent: Reviewer with domain expertise in the target audience
- Output: Usability assessment with specific improvement suggestions

### 5 Strategic Challenger

- Focus: Alignment with broader objectives and long-term implications
- Challenge question: Does this approach serve the larger goal? What are the second-order effects? Are there strategic risks not being considered?
- Typical agent: Strategist acting in an adversarial role
- Output: Strategic risk assessment with alternative perspectives

### 6 Implementation Risk Challenger

- Focus: Feasibility and execution risk
- Challenge question: Can this be built as designed? What could go wrong during implementation? Are there hidden dependencies or resource constraints?
- Typical agent: Builder or Architect with adversarial framing
- Output: Risk inventory with likelihood and impact estimates

## Challenge Protocol

### Step 1: Challenge Initiation

A challenge is initiated when:

1. A Quality Gate evaluation triggers adversarial review (see 26)
2. The active Policy requires challenges on major decisions (see 04, Highest Rigor or Balanced)
3. The lead agent requests a challenge on a specific output
4. The human issues an INCREASE CRITIQUE command (see 09)

### Step 2: Challenge Format

Every challenge follows this structure:

1. **Target**: The specific output, decision, or assumption being challenged
2. **Challenger type**: Which type of challenge this is (from the list above)
3. **Claim**: The specific objection, stated clearly and precisely
4. **Evidence**: Supporting evidence for the objection (tool outputs, reasoning, precedent)
5. **Severity**: Blocking (must be resolved before progress), Significant (should be resolved), Advisory (improvement suggestion)
6. **Suggested resolution**: What the challenger recommends

### Step 3: Response

The challenged agent (the producer of the challenged work) responds:

1. **Accept**: The agent agrees with the challenge and commits to a specific revision
2. **Rebut**: The agent disagrees and provides counter-evidence or counter-reasoning
3. **Partially accept**: The agent accepts part of the challenge and rebuts the rest
4. **Escalate**: The agent cannot resolve the disagreement and requests adjudication

### Step 4: Adjudication

If the challenge is not resolved after one response cycle:

1. For Blocking severity: The lead agent reviews both positions and decides. If the lead cannot decide, escalate to human
2. For Significant severity: One additional challenge-response cycle is allowed. If still unresolved after 2 total cycles, the lead decides
3. For Advisory severity: The challenged agent's decision stands. The advisory is documented in the Provenance Graph

## Resolution Rules

| Situation | Resolution |
|---|---|
| Challenger provides stronger evidence | Challenge accepted. Work product is revised |
| Producer provides stronger evidence | Challenge rejected. Work product stands with documented defense |
| Evidence is equal | Lead agent decides based on risk: prefer the safer option |
| Both positions are valid | Document both as alternatives in the deliverable. Let the human choose |
| Challenge introduces new information | Return to the relevant Reasoning Engine function (see 02) with the new information |

## Iteration Limits

1. Maximum 2 challenge-response cycles per challenged item
2. Maximum 5 total challenges per Quality Gate evaluation
3. If the Disagreement Engine produces more than 3 unresolvable conflicts in a single mission, escalate all of them to human review as a batch

## When Disagreement Blocks Progress

A challenge blocks progress when:

1. Severity is Blocking and the challenge has not been accepted or adjudicated
2. Two challengers of different types identify the same fundamental issue
3. The challenge invalidates a locked assumption (see 09 LOCK ASSUMPTIONS). In this case, the human must decide whether to unlock the assumption

A challenge does NOT block progress when:

1. Severity is Advisory
2. Severity is Significant but the challenged agent has provided a reasoned rebuttal
3. The challenge has been adjudicated by the lead agent

## Disagreement Record

Every challenge-response cycle is recorded in the Provenance Graph (see 06) with:

1. Challenge details (target, type, claim, evidence, severity)
2. Response details (accept, rebut, partially accept, escalate)
3. Resolution (who decided, what the outcome was)
4. Impact on the work product (what changed, or documentation that nothing changed)
