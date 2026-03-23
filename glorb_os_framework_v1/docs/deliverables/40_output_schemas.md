# 40 Output Schemas

## Purpose

Output schemas define the required structure of each deliverable type that GLORB produces. Every mission type (see 42 Mission Types) maps to an output schema. The Deliverable Compiler (see 41) selects the appropriate schema at compilation time, uses it to organize agent contributions into sections, and validates that every section has content before the deliverable is marked complete.

Schemas are not templates for prose — they are structural specifications. Each schema defines which sections must exist, what each section must contain, and which mission types produce it. The Shipping Readiness Gate (see 26 Quality Gates) evaluates the compiled deliverable against its schema before approving release.

## Schema Definitions

### 1. Strategy Memo

**Definition:** A concise, decision-oriented document that frames a strategic challenge, evaluates approaches, and makes a recommendation with rationale.

**Sections:**

| Section | Required Content |
|---|---|
| Executive Summary | One-paragraph summary of the recommendation and the key reason for it |
| Situation | Current state, constraints, and the specific decision or challenge being addressed |
| Options | Each viable approach considered, with a brief description and key characteristics |
| Evaluation Criteria | The dimensions used to compare options (cost, risk, timeline, strategic alignment, etc.) |
| Tradeoff Analysis | Structured comparison of options across the evaluation criteria |
| Recommendation | The recommended option, stated directly, with the primary rationale |
| Risk of Recommended Option | What could go wrong and what the mitigation strategy is |
| Sensitivity Analysis | Which assumptions, if changed, would alter the recommendation |
| Next Actions | Specific follow-up steps with responsible parties if known |

**Produced by:** Decision missions (primary), Negotiation missions (secondary), Synthesis missions when the output is directional rather than informational.

**Traceability requirement:** Each section must link to at least one Provenance Graph record. The Recommendation and Risk sections must link to the Disagreement Engine output if the Disagreement Engine was active.

---

### 2. Architecture Blueprint

**Definition:** A structured technical specification of a system, component, or design that defines what exists, how parts relate, and what constraints govern the design.

**Sections:**

| Section | Required Content |
|---|---|
| Design Objectives | What the architecture is intended to achieve, stated as verifiable criteria |
| Architecture Overview | High-level description of the design with a component diagram or structured narrative |
| Component Specifications | Per-component definition including role, responsibilities, inputs, and outputs |
| Interface Contracts | The explicit contracts between components — what each component accepts and produces |
| Dependency Map | Which components depend on which, rendered as a directed graph or equivalent |
| Constraint Compliance | How the design satisfies each active constraint from the Constraint Compiler (see 03) |
| Risk Assessment | Identified design risks, their likelihood, impact, and mitigation |
| Build Order | The recommended implementation sequence respecting dependency order |
| Open Design Questions | Issues not resolved in this version, with the impact of each open question |

**Produced by:** Design missions (primary), Build missions when a prior design phase has not been run (secondary).

**Traceability requirement:** Each Component Specification and Interface Contract must link to the Architect agent's contribution in the Provenance Graph. The Architecture Coherence Gate (see 26) result must be referenced in the front matter.

---

### 3. Agent Spec

**Definition:** A portable, runtime-agnostic specification for a single GLORB agent, defining its role, capability profile, tool access, memory scope, and governance profile.

**Sections:**

| Section | Required Content |
|---|---|
| Agent Name and Role | Canonical name and a one-sentence role description |
| Capability Profile | Scores across the capability dimensions defined in 11 Agent Capability Model |
| Prompt Definition | The agent's system prompt in GLORB portable format (no runtime-specific instructions) |
| Tool Access | List of GLORB-canonical tool names the agent is permitted to use |
| Memory Scope | Which memory layers the agent may read and write |
| Governance Profile | Authority level, autonomy level, reversibility requirements, escalation triggers |
| Input Contract | What the agent expects to receive (message types, required context fields) |
| Output Contract | What the agent produces (result format, required fields, provenance annotation requirements) |
| Reuse Notes | Conditions under which this agent is appropriate for reuse, and known limitations |

**Produced by:** Design missions that include agent design as a deliverable, Agent Foundry output (see 15) when a new agent is synthesized mid-mission.

**Traceability requirement:** The Prompt Definition must link to the design rationale in the Provenance Graph. If the spec was derived from a Reusable Memory template, the source template must be referenced.

---

### 4. Workflow Plan

**Definition:** A sequenced plan that maps a mission objective to a series of phases, specifying what work happens in each phase, which agents perform it, and where quality gates occur.

**Sections:**

| Section | Required Content |
|---|---|
| Plan Objective | The mission objective this plan addresses |
| Scope and Boundaries | What is in scope and explicitly out of scope |
| Phase Sequence | Ordered list of phases with descriptions of the work in each |
| Agent Assignments | Which agent roles are responsible for each phase |
| Dependencies | Which phases must complete before others can begin |
| Quality Gate Positions | Where in the sequence each Quality Gate (see 26) is inserted and which gate type |
| Parallel Branches | Where phases can execute concurrently and what convergence point they meet at |
| Risk and Contingencies | Key plan risks and the contingency action for each |
| Success Criteria | Measurable criteria that define when the plan is complete |

**Produced by:** Build missions (primary), Execution missions when explicit planning is required before action.

**Traceability requirement:** Agent assignments must link to the Topology Compiler (see 21) output in the Provenance Graph. The phase sequence must link to the Workflow Archetype (see 43) applied.

---

### 5. Audit Report

**Definition:** A systematic findings document that evaluates a subject against defined criteria, documents evidence for each finding, and produces recommendations for remediation.

**Sections:**

| Section | Required Content |
|---|---|
| Audit Scope | What was audited, the time period or version examined, and what was explicitly excluded |
| Audit Methodology | How the audit was conducted — what criteria were applied, what sources were examined, which agents performed evaluation |
| Findings Summary | Findings ranked by severity (Critical, High, Medium, Low, Informational) with counts at each level |
| Detailed Findings | Per-finding entry: title, severity, description, evidence, recommendation |
| Compliance Status | Overall pass/fail or percentage compliance against the stated evaluation criteria |
| Required Remediation Actions | Actions that must be taken, with priority order |
| Optional Improvements | Actions that would improve quality but are not required for compliance |
| Audit Trail | Reference to the Provenance Graph records for each finding |

**Produced by:** Audit missions (primary), Build missions at the review stage (secondary, as a subset of the Build deliverable).

**Traceability requirement:** Every finding must cite at least one evidence item from the Provenance Graph. The Factual Confidence Gate (see 26) result must appear in the front matter.

---

### 6. Decision Brief

**Definition:** A compact document that records a decision that has been made — the context, the options that were considered, the outcome, and the rationale — for future reference and traceability.

**Sections:**

| Section | Required Content |
|---|---|
| Decision Summary | The decision stated directly in one sentence |
| Decision Date and Decider | When the decision was made and which agent or human made it |
| Context | The situation that made this decision necessary |
| Options Considered | Each alternative that was evaluated, even if briefly |
| Criteria Applied | The basis on which options were evaluated |
| Outcome | The chosen option |
| Rationale | Why this option was chosen over the alternatives |
| Assumptions | The assumptions that, if false, would invalidate this decision |
| Revisit Trigger | Conditions that should prompt revisiting this decision |

**Produced by:** Decision missions (primary). Also produced as a sub-document within any mission where a significant decision is made and must be recorded for traceability. The Provenance Graph (see 06) may reference a Decision Brief as the record of a major milestone decision.

**Traceability requirement:** The Options Considered and Rationale sections must link to the Disagreement Engine output (see 25) if adversarial review occurred.

---

### 7. Research Summary

**Definition:** A structured synthesis of findings from a research or exploration process, organized by theme, with source attribution and confidence assessments.

**Sections:**

| Section | Required Content |
|---|---|
| Research Question | The specific question or topic this research addresses |
| Methodology | How research was conducted — what sources were accessed, which agents were used, what retrieval strategy was applied |
| Findings by Theme | Organized findings grouped thematically, with source attribution per finding |
| Source Summary | List of all sources accessed with provenance references |
| Confidence Assessments | Per-finding confidence level (High, Medium, Low) with rationale |
| Contradictions | Areas where sources or agents disagreed, and how contradictions were handled |
| Open Questions | Questions that the research did not resolve, ranked by importance |
| Recommended Next Steps | What should happen next given these findings |

**Produced by:** Exploration missions (primary), Synthesis missions when a research phase precedes synthesis, Design missions when a Researcher agent is active in the topology.

**Traceability requirement:** Every finding must link to the source record in the Provenance Graph. Confidence assessments must link to the Factual Confidence Gate result if one was run.

---

### 8. Build Plan

**Definition:** A detailed implementation specification that defines what is being built, the component-level tasks required, the build sequence, and the acceptance criteria for each component.

**Sections:**

| Section | Required Content |
|---|---|
| Build Objective | What the build is intended to produce, in specific and verifiable terms |
| Architecture Reference | Reference to the Architecture Blueprint (see schema 2 above) that this build implements, or an inline architecture summary if no prior design phase occurred |
| Component Inventory | List of all components to be built with a brief description of each |
| Task Breakdown | Per-component task list with acceptance criteria, estimated complexity, and dependencies |
| Build Sequence | The order in which components will be built, respecting dependencies |
| Agent Assignments | Which Builder agents are responsible for which components |
| Implementation Readiness | Per-component confirmation that the Implementation Readiness Gate (see 26) criteria are met |
| Test Strategy | How each component's output will be verified |
| Known Risks | Implementation risks and the mitigation for each |
| Deployment Notes | How the built output will be delivered and verified in its target environment |

**Produced by:** Build missions (primary). May also be produced as the planning output of an Architecture to Build Plan workflow archetype (see 43) before implementation begins.

**Traceability requirement:** Each Task Breakdown entry must link to the component spec in the Architecture Blueprint. The Implementation Readiness Gate (see 26) result must be referenced per component.
