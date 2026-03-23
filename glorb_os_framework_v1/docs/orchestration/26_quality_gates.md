# 26 Quality Gates

## Purpose

Quality Gates are evaluation checkpoints that work products must pass before progressing to the next phase. Each gate defines what is evaluated, who evaluates it, pass/fail criteria, and what happens on failure.

## Example Gates

1. Sufficient context gate
2. Architecture coherence gate
3. Implementation readiness gate
4. Factual confidence gate
5. Shipping readiness gate

## Gate Ordering

Gates are evaluated in sequence. A work product cannot advance to a later gate without passing all prior gates.

```
Sufficient Context -> Architecture Coherence -> Implementation Readiness -> Factual Confidence -> Shipping Readiness
```

Not all gates are required for every mission. The active Policy (see 04 Policy Engine) determines which gates are enabled.

## Gate Definitions

### 1 Sufficient Context Gate

- When: Before any agent begins substantive work
- Evaluator: Lead agent or Router
- Criteria:
  - Mission objective is unambiguous or ambiguities are explicitly flagged
  - All required inputs are available or their absence is acknowledged
  - Constraints are normalized and non-contradictory (see 03 Constraint Compiler validation)
  - At least one viable approach has been identified
- Pass: All criteria met
- Fail: Missing inputs or unresolved ambiguities. Action: Escalate to human for clarification or spawn a Researcher to fill gaps
- Output: Context assessment with list of resolved and unresolved items

### 2 Architecture Coherence Gate

- When: After the Architect or Strategist produces a structural plan, before builders begin
- Evaluator: Critic agent
- Criteria:
  - All components in the design are necessary (no speculative additions)
  - Component interfaces are defined with input/output contracts
  - Dependencies between components form a directed acyclic graph (no circular dependencies)
  - The design satisfies the stated constraints (deadline, budget, quality bar)
  - Risk areas are identified and have mitigation strategies
- Pass: All criteria met, or remaining issues are Low severity and documented
- Fail: Any High severity issue. Action: Return to Architect for revision. If revision fails twice, escalate to human
- Output: Coherence assessment with per-component evaluation

### 3 Implementation Readiness Gate

- When: Before a Builder begins implementation of a component
- Evaluator: Lead agent or Architect
- Criteria:
  - The component spec includes acceptance criteria
  - Required tools and dependencies are available on the selected runtime
  - The implementation scope is bounded (clear start and stop conditions)
  - No unresolved blocking dependencies on other components
- Pass: All criteria met
- Fail: Missing spec or unresolved dependencies. Action: Return to Architect to complete spec. If dependencies are blocked, re-route affected components
- Output: Readiness checklist with per-item status

### 4 Factual Confidence Gate

- When: After any agent produces claims, recommendations, or analysis that will appear in the deliverable
- Evaluator: Verifier agent or Factual Challenger (see 25 Disagreement Engine)
- Criteria:
  - Every factual claim traces to a source (tool output, document, or explicit reasoning chain)
  - Claims without sources are flagged as assumptions with confidence level (high, medium, low)
  - No unsupported claims are presented as facts
  - Contradictions between sources are identified and resolved or flagged
- Pass: All claims sourced or explicitly flagged as assumptions. No unresolved contradictions
- Fail: Unsupported claims presented as facts, or unresolved contradictions. Action: Return to the producing agent for source attribution. If sources cannot be found, flag claims as assumptions or remove them
- Output: Confidence report with per-claim assessment

### 5 Shipping Readiness Gate

- When: After all work is complete, before the Deliverable Compiler (see 41) produces the final output
- Evaluator: Reviewer agent. If Rigor is High, also requires human sign-off
- Criteria:
  - All prior gates have passed
  - The deliverable addresses the original mission objective
  - All acceptance criteria from the mission spec are met
  - No open High severity issues from Critic or Disagreement Engine
  - Provenance graph is complete (all decisions traceable)
- Pass: All criteria met. Deliverable is compiled and delivered
- Fail: Open issues or unmet criteria. Action: Return to the responsible agent for resolution. If the issue cannot be resolved, document it as a known limitation in the deliverable
- Output: Ship/no-ship decision with rationale

## Gate Failure Handling

When a gate fails:

1. The failure is logged to the Provenance Graph with the gate name, failure reason, and evaluator
2. The failure class is matched to a Recovery Strategy (see 07 Failure Recovery)
3. The work product is returned to the responsible agent with structured feedback
4. A retry counter is incremented. Maximum 2 retries per gate per work product
5. If retries are exhausted, the failure escalates to human review

## Gate Override

The human operator may force-pass a gate using the FORCE command (see 09 Human Command Layer). Force-passes are logged to the Provenance Graph with the human's rationale. Force-passing does not reset the retry counter for subsequent gates.

## Gate Metrics

Each gate evaluation records:

1. Gate name
2. Evaluator agent
3. Pass or fail
4. Time spent in evaluation
5. Number of attempts
6. Issues found (count and severity)
7. Override status (if force-passed)
