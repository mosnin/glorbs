# 44 Review Formats

## Purpose

Review Formats define structured evaluation protocols for different types of review. Each format specifies what is reviewed, what criteria are applied, who performs the review, and what the output format is.

## Example Review Formats

1. Logic review
2. Risk review
3. UX review
4. Factual review
5. Scope review
6. Governance review

## Review Protocol (Common to All Formats)

### Step 1: Assign Reviewer

Based on the review format, assign an agent with the appropriate capability profile (see below). If the format aligns with a Disagreement Engine challenger type (see 25), the same agent may serve both roles.

### Step 2: Provide Review Inputs

The reviewer receives:

1. The work product to review
2. The review criteria specific to this format
3. The mission objective and constraints for context
4. Any prior review results on the same work product

### Step 3: Conduct Review

The reviewer evaluates the work product against each criterion and produces findings.

### Step 4: Produce Review Output

All review formats produce output with this common structure:

1. **Summary assessment**: One of Approved, Approved with revisions, Revisions required, Rejected
2. **Findings**: List of issues found, each with severity (High, Medium, Low) and specific location in the work product
3. **Recommendations**: Suggested actions for each finding
4. **Strengths**: What the work product does well (prevents review from being purely negative)

## Format Definitions

### 1 Logic Review

- What is reviewed: Reasoning chains, decision rationale, analytical arguments
- Reviewer: Critic with High Adversarial Rigor
- Criteria:
  - Conclusions follow from premises
  - No logical fallacies (circular reasoning, false dichotomy, appeal to authority without evidence)
  - Assumptions are stated explicitly
  - Counterarguments are addressed or acknowledged
  - Inferential leaps are identified and justified
- Pass threshold: No High severity logical issues. All assumptions documented
- Relates to: Logic Challenger (see 25), Critique reasoning function (see 02)

### 2 Risk Review

- What is reviewed: Plans, architectures, decisions, and any deliverable with forward-looking commitments
- Reviewer: Critic or Strategist with risk assessment focus
- Criteria:
  - Risks are identified and categorized (technical, operational, strategic, timeline)
  - Each risk has a likelihood estimate (High, Medium, Low) and impact estimate
  - Mitigation strategies are defined for High and Medium likelihood risks
  - Residual risks (after mitigation) are documented
  - No unacknowledged single points of failure
- Pass threshold: All High-impact risks have mitigation strategies. No unidentified High-likelihood risks
- Relates to: Implementation Risk Challenger (see 25)

### 3 UX Review

- What is reviewed: Any deliverable that will be consumed by end users or stakeholders (not just software UX)
- Reviewer: Reviewer with domain expertise in the target audience
- Criteria:
  - Structure is logical and navigable
  - Language is appropriate for the target audience (no unexplained jargon)
  - Key information is prominent, not buried
  - Actionable items are clearly identified
  - The deliverable is self-contained (can be understood without external reference)
- Pass threshold: No High severity usability issues. Target audience can act on the deliverable without clarification
- Relates to: UX Challenger (see 25)

### 4 Factual Review

- What is reviewed: Any deliverable containing factual claims, data, or references
- Reviewer: Verifier with High Precision or dedicated Factual Challenger
- Criteria:
  - Every factual claim has a source citation or is flagged as an assumption
  - Sources are reliable and current
  - Data is accurately represented (no misquotation, no out-of-context usage)
  - Statistical claims include methodology or caveats
  - No unsupported claims presented as established facts
- Pass threshold: All claims sourced or flagged. No fabricated citations. Aligns with Factual Confidence Gate (see 26)
- Relates to: Factual Challenger (see 25), Factual Confidence Gate (see 26)

### 5 Scope Review

- What is reviewed: Any work product, at any stage
- Reviewer: Reviewer or lead agent
- Criteria:
  - Work product addresses the assigned scope completely (no gaps)
  - Work product does not exceed the assigned scope (no scope creep)
  - All mission objectives are mapped to sections of the work product
  - Excluded items are explicitly noted as out of scope
- Pass threshold: Full coverage of assigned scope. No unacknowledged scope additions or omissions
- Relates to: Scope Challenger (see 25)

### 6 Governance Review

- What is reviewed: The mission execution itself (not a specific deliverable). Typically performed at mission completion
- Reviewer: Lead agent, with human review if Rigor is High
- Criteria:
  - All agents operated within their authority scope (see 08 Governance Model)
  - Tool permissions were respected (no unauthorized tool use)
  - Memory access boundaries were respected
  - Escalation rules were followed (agents escalated when they should have, did not escalate unnecessarily)
  - Policy was applied correctly throughout the mission
  - All human commands were executed as specified
- Pass threshold: No governance violations. If violations occurred, they were caught and corrected
- Relates to: Governance Model (see 08), Agent Evaluation (see 16)

## Review Assignment Matrix

| Review Format | When Required | When Optional |
|---|---|---|
| Logic review | Rigor High, any mission type | Rigor Medium on Decision and Strategy missions |
| Risk review | Risk High, any mission type | Any Build or Design mission |
| UX review | Deliverable targets external stakeholders | Any deliverable |
| Factual review | Low hallucination bias policy active | Rigor Medium or High |
| Scope review | Always required at Shipping Readiness Gate | At any Quality Gate |
| Governance review | Rigor High at mission completion | Any mission completion |

## Review Iteration

1. If the review result is "Revisions required" or "Rejected", the work product is returned to the producing agent with the review findings
2. The producing agent revises and resubmits
3. Maximum 2 review iterations per format per work product
4. If still not approved after 2 iterations, escalate to human review
5. Each review iteration is recorded in the Provenance Graph (see 06)
