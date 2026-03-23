# 01 Core Principles

## Purpose

These ten principles define the constraints that every GLORB design decision must satisfy. They are not aspirations — they are the boundaries within which the framework operates. When a proposed change conflicts with a principle, the principle wins unless the change is justified as a deliberate exception. Read these principles before designing any new component, selecting a topology, or extending the framework.

## Principles

### 1. Architecture before execution

Plan the mission structure, agent topology, and deliverable schema before any agent begins substantive work. Every mission in GLORB passes through the Router (see 20) and Topology Compiler (see 21) before agents are spawned. This is not optional overhead — it is the mechanism by which the system avoids the most common failure mode of agentic systems: agents that act on ambiguous objectives and produce work that must be discarded. A mission that begins without a compiled topology and a validated context assessment (see 26 Sufficient Context Gate) has not started in GLORB's sense; it has begun guessing.

### 2. Modularity over monolith

Decompose capabilities into discrete, well-scoped agents rather than building one agent that does everything. Each agent in GLORB is defined by a portable spec (see doc 10 Agent Spec) that declares its role, tool access, memory scope, and governance profile. This boundary-drawing is not an implementation detail — it is what makes topologies reusable, agents replaceable, and systems auditable. A monolithic agent that handles research, reasoning, writing, and critique cannot be governed, cannot be swapped at runtime, and cannot be evaluated in isolation. Modularity is what makes the rest of the framework possible.

### 3. Portable specifications

Agent definitions, topology patterns, memory policies, and deliverable schemas are expressed in runtime-agnostic formats. A GLORB agent spec does not contain instructions that are specific to Claude Code, OpenClaw, or any other runtime. The Runtime Abstraction Layer (see 50) is responsible for translating portable specs into runtime-native constructs. This portability is what allows the same mission design to execute on different runtimes without redesign, and what allows capability gaps to be handled through runtime selection (see 55) rather than bespoke per-runtime engineering.

### 4. Governance is mandatory

Every agent operates under an explicit governance profile that defines its authority scope, tool scope, memory scope, autonomy level, reversibility requirements, escalation triggers, and stop conditions (see 08 Governance Model). There is no ungoverned execution in GLORB. Governance profiles are assigned at topology compilation time and enforced by the runtime adapter. An agent that attempts an action outside its governance boundaries is blocked before execution, logged to the Provenance Graph (see 06), and triggers an escalation. The governance model is not a suggestion layer applied after the fact — it is a structural constraint that determines what agents may do before they are ever dispatched.

### 5. Memory must be governed

Memory is not a passive store. Every read and write to any memory layer passes through the governance model (see 08 Memory Scope) and the retrieval policy (see 34). Agents may only access the layers they are permitted to read, and may only write to layers explicitly granted to them. The Memory Constitution (see 05) establishes the constitutional rules that govern memory across all layers. The six-layer model (see 30), persistence rules (see 31), compression policy (see 32), conflict resolution protocol (see 33), and retrieval policy (see 34) collectively ensure that memory grows usefully, decays appropriately, and does not silently corrupt decisions. Ungoverned memory accumulation is a form of technical debt that compounds across missions — governed memory compounds as reusable knowledge instead.

### 6. Deliverables matter

The final output of a mission is a structured, versioned deliverable compiled by the Deliverable Compiler (see 41), not the raw conversation output of the last agent to run. Every mission type maps to a deliverable schema (see 40) with defined sections, traceability requirements, and quality criteria. This matters because a deliverable that a human cannot read, reference, or verify is not a deliverable — it is noise. The compilation step enforces structure, attaches provenance, and ensures that every section traces to an agent contribution. Deliverables survive the mission and carry the mission's value forward; agent conversations do not.

### 7. Traceability matters

Every decision, contribution, assumption, critique, and override in a mission is recorded in the Provenance Graph (see 06). This is not post-hoc documentation — it is built into the execution flow as a structural requirement. Agents record provenance as they produce work. Quality Gates evaluate against provenance. The Deliverable Compiler attaches provenance to each deliverable section. The purpose of traceability is not compliance — it is trust. A system whose decisions cannot be traced cannot be corrected, audited, or improved. Traceability is the foundation on which quality control, conflict resolution (see 33), and failure recovery (see 07) are built.

### 8. Quality gates beat blind speed

Moving fast by skipping evaluation is a debt that compounds. GLORB inserts Quality Gates (see 26) at defined points in every mission to evaluate work products before they advance. The gate sequence — Sufficient Context, Architecture Coherence, Implementation Readiness, Factual Confidence, Shipping Readiness — represents the minimum quality surface for a mission. Which gates are active is governed by the active Policy (see 04). Even the fastest acceptable policy includes a Shipping Readiness gate. Gates introduce latency, but they also prevent the higher-latency outcome of a delivered result that must be revised from scratch. A gate failure with structured feedback is cheaper than a delivered failure discovered by the recipient.

### 9. Runtime abstraction is a feature

The GLORB control plane is separated from the execution runtime by the Runtime Abstraction Layer (see 50) and the Adapter Interface (see 56). This separation is not an architectural formality — it is a deliberate feature that enables runtime selection, multi-runtime topologies, and forward compatibility. The control plane defines what agents do; the runtime determines how they do it. This means that a mission designed today can execute on a runtime that does not yet exist, that agents can be reassigned to more capable runtimes at topology compile time, and that capability gaps (see 57 Capability Negotiation) can be resolved through routing rather than through redesign. The abstraction layer is what prevents GLORB from being a thin wrapper around any single provider.

### 10. Reuse should compound

Each completed mission should leave the system more capable than it found it. GLORB achieves this through its memory promotion rules (see 30), which elevate proven topology patterns, validated agent specs, and domain knowledge from mission-level to project-level to reusable-level storage. The Reusable Memory layer (see 30 Layer 5) holds cross-project knowledge accessible to all future missions. The Agent Reuse Policy (see doc 13) prefers existing agents over newly created ones. Pattern extraction during compression (see 32) converts specific successes into generalizable templates. Reuse is not merely efficiency — it is how the system develops institutional knowledge that makes each subsequent mission faster, more reliable, and more likely to start from a proven foundation rather than a blank slate.
