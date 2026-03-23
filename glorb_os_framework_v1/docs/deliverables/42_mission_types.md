# 42 Mission Types

## Purpose

Mission types classify the fundamental nature of the work a mission is intended to accomplish. The mission type is the first input to the Router (see 20) and the first selection criterion in the Topology Compiler (see 21). It determines the default topology, the applicable output schema (see 40), and the set of Quality Gates (see 26) that are active.

Every mission in GLORB must be classified as one of the eight types below before any agent is spawned. If the mission type is unclear, the Router emits an escalation and requests human clarification rather than guessing.

## Mission Type Definitions

### 1. Exploration

**Definition:** An Exploration mission answers a question or maps an unknown domain where the answer is not known in advance and the principal value is the information gathered.

**Objective pattern:** "Find out what X is", "Understand how Y works", "Identify the options for Z", "Assess the current state of W."

**Typical topology (see 21):**
- Default: Researcher swarm plus Reducer
- Upgrade condition: High ambiguity — add a Strategist to frame the research questions before the swarm begins
- Scaling: Add domain-specific Researcher agents when the exploration spans multiple disciplines

**Typical deliverables (see 40):**
- Primary: Research Summary
- Secondary: Decision Brief (if the findings point to a clear next action)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before research begins — ensures the research question is specific enough to answer)
- Factual Confidence Gate (after findings are synthesized — ensures claims are sourced)
- Shipping Readiness Gate (before delivery)

**Risk profile:** Generally low reversibility risk — exploration produces information, not irreversible actions. Primary risks are wasted effort from poorly framed questions and hallucinated findings presented as facts. The Factual Confidence Gate is the primary defense.

**Example:** "We need to understand what vector database options exist for our use case, their tradeoffs, and which have been validated in production at our scale."

---

### 2. Decision

**Definition:** A Decision mission produces a recommendation and rationale for a specific choice that must be made, where options have been identified and the principal output is the recommendation with supporting analysis.

**Objective pattern:** "Choose between X and Y", "Determine whether to proceed with Z", "Recommend an approach to W given these constraints."

**Typical topology (see 21):**
- Default: Solo agent (Strategist)
- Upgrade condition: High risk — upgrade to Strategist plus Critic; enable the Disagreement Engine (see 25)
- Further upgrade condition: High stakes with conflicting stakeholder positions — Strategist plus Operator plus Verifier

**Typical deliverables (see 40):**
- Primary: Strategy Memo
- Secondary: Decision Brief (always produced as a companion record for traceability)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before analysis — ensures all options and criteria are defined)
- Factual Confidence Gate (after options analysis — ensures tradeoff claims are supported)
- Shipping Readiness Gate (before delivery)
- Architecture Coherence Gate is not typically active unless the decision is about a structural design

**Risk profile:** Medium. Decisions can be reversed but often carry switching costs. The primary risk is a poorly supported recommendation that is later invalidated when assumptions fail. The Sensitivity Analysis section of the Strategy Memo schema addresses this directly.

**Example:** "We must decide whether to build the integration layer in-house or adopt a third-party platform. Recommend one option with rationale given our 3-month timeline and team size."

---

### 3. Design

**Definition:** A Design mission produces an architecture, specification, or structural plan for a system, process, or artifact that will subsequently be built or implemented.

**Objective pattern:** "Design the architecture for X", "Specify how Y should work", "Define the structure of Z."

**Typical topology (see 21):**
- Default: Architect plus Builders plus Critic
- Upgrade condition: High complexity — add a Strategist before the Architect to resolve objectives before design begins
- Scaling: Add domain-specific Builder agents for components requiring specialized knowledge

**Typical deliverables (see 40):**
- Primary: Architecture Blueprint
- Secondary: Agent Spec (when the design includes agent definitions), Workflow Plan (when the design includes process specifications)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before design begins — ensures requirements and constraints are clear)
- Architecture Coherence Gate (after the Architect produces the design — Critic evaluates structural validity)
- Shipping Readiness Gate (before delivery)

**Risk profile:** Medium. The design itself is low-risk to produce, but a flawed design produces high-cost downstream failures when builders implement it. The Architecture Coherence Gate is the primary control. Designs that skip this gate carry elevated implementation risk.

**Example:** "Design the multi-agent memory system for the GLORB framework, including layer definitions, promotion rules, and the adapter interface."

---

### 4. Build

**Definition:** A Build mission implements a specified artifact — code, document, configuration, prompt set, or other concrete output — based on a prior design or a specification provided as input.

**Objective pattern:** "Implement X based on the design in Y", "Build the Z module to spec", "Create the W artifacts as defined."

**Typical topology (see 21):**
- Default: Planner and Executor
- Upgrade condition: Complex build with multiple components — upgrade to Architect plus Builders plus Critic
- Scaling: Add parallel Builders for components with no blocking dependencies between them

**Typical deliverables (see 40):**
- Primary: Build Plan (produced at the start), then the built artifact itself
- Secondary: Audit Report (produced at the review stage to document verification results)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before building — ensures the specification is complete)
- Implementation Readiness Gate (per component — ensures each component is ready to build before the Builder begins)
- Factual Confidence Gate (after implementation — ensures claims in documentation are accurate)
- Shipping Readiness Gate (before delivery)

**Risk profile:** High. Build missions produce artifacts that will be used. Errors are operational, not just analytical. The Implementation Readiness Gate ensures Builders do not start on underspecified components. Reversibility must be assessed per artifact — code changes may be reversible; deployed configurations may not be.

**Example:** "Implement the five specialist agent specs defined in the Design deliverable from Mission 003. Each spec should be production-ready and stored in the agent registry."

---

### 5. Audit

**Definition:** An Audit mission evaluates an existing artifact, system, process, or decision against defined criteria and produces a structured findings report with recommendations.

**Objective pattern:** "Audit the X for compliance with Y", "Evaluate the quality of Z", "Review W against the defined acceptance criteria."

**Typical topology (see 21):**
- Default: Solo agent (Verifier)
- Upgrade condition: High rigor — add a Critic plus a Reviewer for independent evaluation
- Scaling: Add domain-specific Verifiers for audits that span multiple technical or domain areas

**Typical deliverables (see 40):**
- Primary: Audit Report
- Secondary: Decision Brief (when the audit reveals a decision that must be made about the audited artifact)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before audit — ensures the audit scope and criteria are defined)
- Factual Confidence Gate (after findings are produced — all findings must be evidenced)
- Shipping Readiness Gate (before delivery)

**Risk profile:** Low for the mission itself — evaluation is non-destructive. The risk profile of the findings may be high if the audit reveals critical issues. The Audit Report schema requires findings to be ranked by severity so recipients can prioritize remediation.

**Example:** "Audit the memory governance implementation against the Memory Constitution (doc 05) and identify any violations of constitutional rules."

---

### 6. Negotiation

**Definition:** A Negotiation mission resolves a conflict or disagreement between two or more positions, producing an agreed outcome or a structured record of where agreement was and was not reached.

**Objective pattern:** "Resolve the disagreement between X and Y on topic Z", "Find a workable compromise between these conflicting requirements", "Determine an acceptable position that satisfies both constraints."

**Typical topology (see 21):**
- Default: Strategist plus Operator plus Verifier
- Upgrade condition: High stakes or entrenched positions — add a Critic and enable the Disagreement Engine (see 25) explicitly
- Scaling: Add stakeholder-representative agents when positions are complex or numerous

**Typical deliverables (see 40):**
- Primary: Strategy Memo (framing the resolution)
- Secondary: Decision Brief (recording the outcome)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before negotiation — ensures all positions are accurately represented)
- Factual Confidence Gate (after position analysis — ensures claims about each position are accurate)
- Shipping Readiness Gate (before delivery)

**Risk profile:** Medium to high. The output of a Negotiation mission often drives downstream decisions. A negotiation that misrepresents one party's position or produces an unstable agreement creates compounding problems. The Disagreement Engine is frequently active in this mission type to stress-test proposed resolutions.

**Example:** "The engineering team requires 8 weeks and the product team has committed to a 4-week delivery. Negotiate a scoping and sequencing plan that is acceptable to both teams."

---

### 7. Synthesis

**Definition:** A Synthesis mission integrates outputs from multiple sources, agents, or prior missions into a unified, coherent whole that is more valuable than any individual input.

**Objective pattern:** "Synthesize the findings from X and Y", "Integrate these three research streams into a unified view", "Combine the outputs of prior missions into a single reference document."

**Typical topology (see 21):**
- Default: Researcher swarm plus Reducer
- Upgrade condition: Multiple domains with conflicting findings — add domain-specific Researchers and enable the Disagreement Engine (see 25) for conflict resolution
- Scaling: Scale the Researcher count to the number of distinct source streams

**Typical deliverables (see 40):**
- Primary: Research Summary (when the synthesis is informational)
- Secondary: Strategy Memo (when the synthesis leads to a recommendation)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before synthesis — ensures all source materials are accessible)
- Factual Confidence Gate (after synthesis — ensures the unified output does not introduce claims not present in the sources)
- Shipping Readiness Gate (before delivery)

**Risk profile:** Medium. The principal risk is that synthesis introduces distortions — overstating consensus, understating contradictions, or losing nuance from source materials. The Merge and Reduction process (see 24) and the Contradictions section of the Research Summary schema are the primary controls.

**Example:** "Synthesize the findings from the three exploration missions on distributed systems, cost modeling, and team capability into a unified technical strategy brief."

---

### 8. Execution

**Definition:** An Execution mission carries out a pre-specified plan, taking concrete actions and producing a record of what was done, what succeeded, what failed, and what the current state is.

**Objective pattern:** "Execute the build plan from Mission X", "Run the deployment sequence as defined", "Carry out the approved action sequence."

**Typical topology (see 21):**
- Default: Planner and Executor
- Upgrade condition: Parallel tasks — add multiple Executors for tasks with no blocking dependencies
- Upgrade condition: High risk actions — add a Verifier to confirm each step before and after execution

**Typical deliverables (see 40):**
- Primary: No dedicated schema — the deliverable is the record of actions taken, aligned to the Execution section structure in doc 41 (Deliverable Compiler)
- Secondary: Audit Report (for post-execution verification)

**Active Quality Gates (see 26):**
- Sufficient Context Gate (before execution — ensures the plan is complete and reversibility is understood)
- Implementation Readiness Gate (before each high-risk action — ensures the preconditions for the action are met)
- Shipping Readiness Gate (after execution is complete — ensures all steps were completed and issues were resolved)

**Risk profile:** The highest of all mission types when actions are irreversible. Every Execution mission must have its reversibility assessed before execution begins. Irreversible actions require explicit human approval unless the active Policy is Autonomous Unless Blocked with a pre-approved action class. Deviations from the plan must be logged and escalated rather than silently handled.

**Example:** "Execute the agent registry migration plan approved in Mission 012. Log all actions taken, all deviations encountered, and the final state of the registry."

## Mission Type Selection Reference

| If the primary output is... | Mission Type |
|---|---|
| Information or findings | Exploration |
| A recommendation between options | Decision |
| A structural plan or specification | Design |
| A concrete artifact | Build |
| An evaluation of something existing | Audit |
| A resolution of a conflict | Negotiation |
| An integration of multiple sources | Synthesis |
| Completion of a defined plan | Execution |

When a mission contains meaningful elements of multiple types, classify it by its primary output. Secondary types inform which additional Quality Gates and deliverable sections should be included.
