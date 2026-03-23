# 43 Workflow Archetypes

## Purpose

Workflow archetypes are reusable patterns for multi-phase missions. Each archetype defines a sequence of stages, the agents involved in each stage, the handoff points between stages, and the deliverables produced. Where a mission type (see 42) classifies what a mission is doing, a workflow archetype specifies how the mission moves through its phases.

The Topology Compiler (see 21) references archetypes when constructing the execution plan in Step 5. Archetypes are stored in Reusable Memory (see 30 Layer 5) and are available as starting points for any mission. They may be modified to fit the specific mission; they are patterns, not rigid scripts.

For orchestration details referenced in each archetype — handoff protocol (see 22), merge and reduction (see 24), disagreement engine (see 25), quality gates (see 26) — consult the relevant docs.

## Archetype Definitions

---

### 1. Idea to Architecture

**Summary:** Transforms an unstructured idea or problem statement into a validated architecture ready for implementation planning.

**Applicable mission type:** Design (primary), with an Exploration phase if the problem space is ambiguous.

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Context Assessment | Evaluate the idea against known constraints. Identify ambiguities, missing requirements, and scope boundaries | Lead agent (Strategist) |
| 2. Research (conditional) | If the problem domain is unclear, run targeted research to gather facts before design begins | Researcher agent(s), triggered by Strategist |
| 3. Framing | Translate the idea into a structured design brief: objectives, constraints, success criteria, non-goals | Strategist |
| 4. Architecture Design | Produce the structural design: components, interfaces, dependencies, risk areas | Architect agent |
| 5. Critique | Challenge the design for coherence, completeness, and constraint compliance | Critic agent |
| 6. Revision (if needed) | Address critique findings and resubmit to Critic. Maximum 2 revision cycles | Architect agent |
| 7. Compilation | Assemble the Architecture Blueprint deliverable with full provenance | Deliverable Compiler (see 41) |

**Quality gates active:** Sufficient Context (after Stage 1), Architecture Coherence (after Stage 4/6), Shipping Readiness (before Stage 7 completes).

**Handoff points:**
- Stage 1 → 2: Strategist passes the ambiguity list to the Researcher via a Handoff message (see 22)
- Stage 2 → 3: Researcher returns findings to Strategist via Handoff. Strategist validates sufficient context before framing begins
- Stage 3 → 4: Strategist hands the design brief to the Architect. Architect must acknowledge receipt before proceeding
- Stage 4 → 5: Architect hands the design to Critic via Handoff. Includes the open design questions list
- Stage 5 → 4 or 7: Critic returns Feedback message. If pass, advance to compilation. If fail, return to Architect

**Deliverables produced:** Architecture Blueprint (primary), Research Summary (if Stage 2 is active).

---

### 2. Architecture to Build Plan

**Summary:** Translates a validated architecture into a concrete, sequenced build plan that Builders can execute without further design decisions.

**Applicable mission type:** Build (planning phase), Design when the output is a plan rather than an artifact.

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Architecture Review | Confirm the input architecture is complete and coherent before decomposing it | Lead agent (Architect or Planner) |
| 2. Component Decomposition | Break the architecture into buildable units. For each component, define the task, acceptance criteria, dependencies, and estimated complexity | Architect agent or Planner |
| 3. Dependency Mapping | Construct the build dependency graph. Identify which components can build in parallel and which must sequence | Planner |
| 4. Agent Assignment | Match each component to the appropriate Builder agent type based on capability requirements | Lead agent, referencing Agent Reuse Policy (see 13) |
| 5. Readiness Validation | For each component, verify the Implementation Readiness Gate (see 26) criteria: spec is complete, tools are available, dependencies are resolved | Lead agent |
| 6. Plan Assembly | Produce the Build Plan deliverable with phase sequence, agent assignments, parallel branches, and test strategy | Deliverable Compiler (see 41) |

**Quality gates active:** Sufficient Context (after Stage 1 — ensures architecture is complete enough to decompose), Implementation Readiness (Stage 5, per component), Shipping Readiness (before Stage 6 completes).

**Handoff points:**
- Stage 1 → 2: If the architecture fails review, it is returned to the Architect for revision before decomposition begins
- Stage 2 → 3: Component list passed from Architect to Planner via Handoff
- Stage 5 → 6: Any component that fails readiness validation is flagged. The plan is not assembled until all components pass or failures are documented with explicit escalation paths

**Deliverables produced:** Build Plan (primary), updated Architecture Blueprint (if architectural gaps are discovered during decomposition).

---

### 3. Research to Synthesis

**Summary:** Runs parallel research across multiple sources or domains, then merges findings into a single coherent synthesis output.

**Applicable mission type:** Exploration, Synthesis, or the research phase of a Decision mission.

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Research Framing | Define the research questions, identify the source domains, and assign each domain to a Researcher | Lead agent (Strategist or Researcher lead) |
| 2. Parallel Research | Each Researcher independently investigates its assigned domain and produces structured findings | Multiple Researcher agents (parallel) |
| 3. Convergence | All Researcher outputs are collected at the Synthesizer. Scope coverage is verified | Synthesizer agent (see 24 Merge and Reduction) |
| 4. Conflict Detection | Contradictions between Researcher findings are identified and classified | Synthesizer agent, optionally Disagreement Engine (see 25) |
| 5. Synthesis | Findings are merged, contradictions resolved, and the unified output structured by theme | Synthesizer agent |
| 6. Confidence Assessment | Each finding is assigned a confidence level. Low-confidence findings are flagged | Verifier or Synthesizer |
| 7. Compilation | Research Summary or Strategy Memo assembled with provenance per finding | Deliverable Compiler (see 41) |

**Quality gates active:** Sufficient Context (after Stage 1 — research question must be specific), Factual Confidence (after Stage 6 — all findings must be sourced or flagged), Shipping Readiness (before Stage 7 completes).

**Handoff points:**
- Stage 1 → 2: Strategist distributes research assignments to Researcher agents via Task messages (see 22). Parallel execution begins
- Stage 2 → 3: Each Researcher sends a Handoff message to the Synthesizer when complete. The Synthesizer waits for all Handoffs before beginning Stage 3
- Stage 4 → 5: If the Disagreement Engine is active and produces an unresolvable conflict, the conflict is escalated to the lead agent before synthesis continues
- Stage 6 → 7: Synthesizer passes the confidence-annotated findings to the Deliverable Compiler

**Deliverables produced:** Research Summary (primary), Strategy Memo (if the synthesis produces a directional recommendation).

**Parallel execution note:** Stage 2 is the primary parallelism opportunity. The number of simultaneous Researcher agents is bounded by the runtime's max_concurrent_agents (see 57). If the number of research domains exceeds this limit, domains are batched and researched in sequential parallel sets.

---

### 4. Audit to Remediation

**Summary:** Evaluates an artifact or system against defined criteria, produces a findings report, and — if remediation is required — transitions into a build or execution workflow to address findings.

**Applicable mission type:** Audit (phases 1-3), transitioning to Build or Execution for phases 4-5.

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Scope Definition | Define what is being audited, what criteria apply, and what is out of scope | Lead agent (Verifier) |
| 2. Evidence Gathering | Collect and examine the subject of the audit against the criteria | Verifier agent(s), with tool access for inspection |
| 3. Findings Production | For each criterion, produce a finding: pass, fail, or informational. For failures, document evidence and recommended remediation | Verifier agent, Critic (if high rigor) |
| 4. Report Assembly | Compile the Audit Report with findings ranked by severity | Deliverable Compiler (see 41) |
| 5. Remediation Planning (conditional) | If Critical or High severity findings exist, produce a prioritized remediation plan | Planner or Architect agent |
| 6. Remediation Execution (conditional) | Execute the remediation actions per the plan | Builder or Executor agents |
| 7. Verification | Re-audit the remediated items to confirm findings are resolved | Verifier agent |

**Quality gates active:** Sufficient Context (after Stage 1 — audit scope must be defined), Factual Confidence (after Stage 3 — all findings must be evidenced), Shipping Readiness (after Stage 4, and again after Stage 7 if remediation was performed).

**Handoff points:**
- Stage 3 → 4: Verifier passes findings to Deliverable Compiler via Handoff. All findings must be in the required format before compilation begins
- Stage 4 → 5: This transition is conditional on the presence of Critical or High severity findings. If no findings meet this threshold, the archetype ends at Stage 4
- Stage 5 → 6: Remediation plan is approved by the lead agent or human (depending on Policy) before execution begins
- Stage 6 → 7: Executor hands off the completion record to Verifier. Verifier re-runs Stage 2 and 3 scoped to remediated items only

**Deliverables produced:** Audit Report (after Stage 4), Build Plan or Execution record (after Stage 5/6), updated Audit Report (after Stage 7 showing resolved findings).

---

### 5. Plan to Execution

**Summary:** Takes a pre-approved plan and executes it, with checkpoints to validate preconditions before high-risk actions and to record deviations.

**Applicable mission type:** Execution.

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Plan Validation | Confirm the input plan is complete, that all preconditions are met, and that reversibility has been assessed for each action | Lead agent (Planner) |
| 2. Pre-execution Checkpoint | For irreversible actions, request human confirmation if required by Policy | Lead agent, human operator |
| 3. Execution | Carry out the planned actions in sequence (or in parallel where the plan specifies). Log each action and its outcome | Executor agent(s) |
| 4. Deviation Handling | When an action produces an unexpected result, log the deviation, assess impact, and escalate if the deviation is material | Lead agent or Executor |
| 5. Completion Verification | Confirm all planned actions were completed and the target state matches the plan's success criteria | Verifier agent |
| 6. Record Compilation | Produce the execution record documenting all actions taken, outcomes, deviations, and final state | Deliverable Compiler (see 41) |

**Quality gates active:** Sufficient Context (after Stage 1 — plan must be complete), Implementation Readiness (before Stage 3 for each high-risk action), Shipping Readiness (after Stage 5).

**Handoff points:**
- Stage 1 → 2: Lead agent passes the plan validation results to the human (or autonomous review step) before execution begins
- Stage 2 → 3: No agent begins execution until the pre-execution checkpoint is cleared
- Stage 3 → 4: Deviations are surfaced via Escalation messages (see 22) from Executor to Lead. Lead decides whether to adapt the plan or escalate further
- Stage 5 → 6: Verifier's completion report is handed to the Deliverable Compiler with final state confirmation

**Deliverables produced:** Execution record (no dedicated schema — uses the Execution structure from 41 Deliverable Compiler), Audit Report (if post-execution verification is required by Policy).

**Risk note:** This archetype has the highest operational risk of all archetypes. Stage 1 must assess whether any action in the plan is irreversible and flag those actions for enhanced review. The lead agent must not begin Stage 3 until irreversible action approvals are complete.

---

### 6. Build to Review

**Summary:** Implements a set of specified components and passes each through a structured review cycle before declaring the build complete.

**Applicable mission type:** Build.

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Build Preparation | Confirm the build specification is complete. Assign components to Builders. Verify Implementation Readiness Gate for each component | Lead agent (Architect or Planner) |
| 2. Implementation | Builders implement their assigned components. Working outputs are stored in Mission Memory | Builder agent(s), parallel where dependencies allow |
| 3. Component Review | Each completed component is reviewed by a Critic or Reviewer. Feedback is structured per the Disagreement Engine (see 25) | Critic or Reviewer agent |
| 4. Revision (if needed) | Builder addresses critique findings and resubmits. Maximum 2 revision cycles per component | Builder agent |
| 5. Integration | Completed components are integrated. Integration issues are identified and resolved | Lead agent or a dedicated Integration agent |
| 6. Final Verification | The integrated output is evaluated against the build objectives and acceptance criteria | Verifier agent |
| 7. Compilation | Assemble the Build deliverable or Build Plan with verification results | Deliverable Compiler (see 41) |

**Quality gates active:** Sufficient Context (after Stage 1), Implementation Readiness (per component, Stage 1), Factual Confidence (Stage 6 — documentation claims must be accurate), Shipping Readiness (before Stage 7 completes).

**Handoff points:**
- Stage 1 → 2: Each Builder receives a Handoff from the lead containing its component spec, acceptance criteria, and tool permissions
- Stage 2 → 3: Builder sends a Handoff to the Critic when the component is complete. Includes the implementation and any known issues
- Stage 3 → 4 or 5: Critic sends Feedback. If pass, component advances to integration. If fail, Feedback is returned to Builder
- Stage 5 → 6: Lead agent passes the integrated output to the Verifier via Handoff

**Deliverables produced:** Build Plan (if produced in Stage 1), the built artifact itself, Audit Report section (verification results from Stage 6).

**Parallel execution note:** Stage 2 is the primary parallelism opportunity. Builders for independent components execute simultaneously. The convergence point at Stage 5 waits for all Builder streams to complete.

---

### 7. Review to Revision

**Summary:** Takes an existing artifact that has received feedback, synthesizes the feedback, and manages the revision cycle through to an approved updated version.

**Applicable mission type:** Build (revision phase), Design (revision after critique), Audit (for re-review of remediated items).

**Stages:**

| Stage | Work | Agents Involved |
|---|---|---|
| 1. Feedback Synthesis | Collect all feedback on the artifact. Deduplicate, classify by severity, and produce a prioritized revision agenda | Lead agent or Synthesizer |
| 2. Revision Scoping | For each feedback item, determine whether to accept, partially accept, rebut, or escalate. Record the decision with rationale | Lead agent, original producing agent |
| 3. Revision | Apply accepted changes to the artifact. For each change, record what changed and why | Original producing agent (Builder, Architect, etc.) |
| 4. Change Verification | Confirm accepted changes were applied correctly. Confirm rebutted items are documented with rationale | Verifier or Critic agent |
| 5. Reviewer Sign-off | Final review of the revised artifact against the original feedback to confirm all items are addressed | Reviewer agent |
| 6. Compilation | Produce the revised artifact as a new versioned deliverable | Deliverable Compiler (see 41) |

**Quality gates active:** Sufficient Context (after Stage 1 — feedback must be complete), Factual Confidence (Stage 4 — change documentation must be accurate), Shipping Readiness (before Stage 6 completes).

**Handoff points:**
- Stage 1 → 2: Synthesizer passes the prioritized revision agenda to the lead and original producing agent via Handoff
- Stage 2 → 3: Revision scope decisions are recorded in Mission Memory before revision begins. This prevents scope drift during revision
- Stage 3 → 4: Producing agent sends the revised artifact to the Verifier via Handoff. Includes a change log documenting each modification
- Stage 4 → 5: Verifier passes the change-verified artifact to the Reviewer
- Stage 5 → 6: Reviewer's sign-off message is included in the Deliverable Compiler input

**Deliverables produced:** Revised version of the original artifact type (Architecture Blueprint, Build Plan, Agent Spec, etc.), with version number incremented and the revision record attached.

**Revision limit:** This archetype is limited to 2 full cycles for any single artifact. After 2 cycles without a Reviewer sign-off, the outstanding issues are escalated to human review rather than triggering a third revision cycle. This limit is enforced by the Quality Gate failure handling rules (see 26).
