# GLORB Framework: Comprehensive Deep Dive Analysis

## Reference

For formal definitions of all terms used in this framework, see 62 Glossary. For the foundational design constraints, see 01 Core Principles.

## What GLORB Is

GLORB is a **runtime-agnostic control plane for AI agent systems** — an "operating system for agent architectures." Its core thesis:

> "Most agent systems are weak because they begin with execution. GLORB begins with architecture."

It is not code or a library. It is a **specification-driven governance and orchestration framework** expressed as structured documentation, YAML schemas, templates, and prompts. It sits above any execution backend and defines how multi-agent missions are modeled, staffed, governed, and delivered.

---

## Foundational Separation: Control Plane vs Runtime

### GLORB Owns (Control Plane)
- Mission typing and modeling
- Agent specification and creation
- Capability modeling
- Team topology design
- Routing logic
- Memory policy
- Governance policy
- Deliverable schemas
- Runtime selection policy

### Runtime Owns (Execution Plane)
- Session execution
- Tool calls
- Sandboxing and file system access
- Native runtime logging
- Backend-specific limits

This separation means you can swap Claude Code for OpenClaw or NanoClaw without changing the control plane.

---

## The 7 Layers

### 1. Core Layer (docs 00-09)

**10 Principles**: Architecture before execution, modularity over monolith, portable specs, mandatory governance, governed memory, deliverables matter, traceability matters, quality gates beat blind speed, runtime abstraction is a feature, reuse should compound.

**Reasoning Engine**: 8 structured reasoning functions (decomposition, abstraction management, constraint evaluation, tradeoff evaluation, uncertainty handling, synthesis, critique, decision support).

**Constraint Compiler**: Takes 9 inputs (deadline, budget, runtime limits, quality bar, risk tolerance, reversibility, legal boundaries, tool availability, human approval) and compiles them into topology, agent count, critique intensity, memory policy, deliverable depth, runtime selection, and oversight decisions.

**Policy Engine**: 8 named policies (fastest acceptable, highest rigor, lowest cost, balanced, high exploration, low hallucination bias, human-in-loop required, autonomous unless blocked).

**Memory Constitution**: 6 memory layers, 7 governance questions.

**Provenance Graph**: Tracks agent contributions, tools used, assumptions, critiques, evidence, and unresolved uncertainties.

**Failure Recovery**: 9 failure classes, 7 recovery strategies (retry, recompile topology, spawn specialist, escalate, reduce scope, switch runtime, rebuild from provenance).

**Governance Model**: 7 dimensions (authority scope, tool scope, memory scope, autonomy level, reversibility awareness, escalation triggers, stop conditions).

**Human Command Layer**: 10 commands (freeze branch, fork plan, escalate, replace agent, recompile topology, increase critique, lower rigor, lock assumptions, compress context, reduce cost mode).

### 2. Agent Layer (docs 10-16)

**Agent Primitives**: 12 required properties per agent (identity, mission, scope, authority, capability profile, tool permissions, memory scope, input/output contracts, escalation rules, stop conditions, success criteria).

**Capability Model**: 10 dimensions (abstraction depth, precision, creativity, risk tolerance, adversarial rigor, synthesis strength, tool access, autonomy level, memory breadth, domain expertise).

**Agent Lifecycle**: 9 states (Drafted, Instantiated, Active, Specialized, Forked, Paused, Merged, Retired, Archived).

**Agent Creation Framework**: 7 gating questions before any agent is created.

**Specialist Patterns**: 10 archetypes (Strategist, Architect, Builder, Researcher, Critic, Router, Synthesizer, Verifier, Reviewer, Memory Manager).

**Agent Foundry**: Factory pattern with 7 functions (detect gaps, specify scope, assign tools, assign memory, define output contract, define evaluation, decide persistence).

**Agent Evaluation**: 7 dimensions (output quality, scope discipline, tool correctness, hallucination resistance, merge usefulness, escalation correctness, cost efficiency).

### 3. Orchestration Layer (docs 20-27)

**Router**: 8 inputs to 6 possible outputs (single agent, existing specialist, new specialist, team topology, escalation, human review).

**Topology Compiler**: 5 patterns (solo agent, planner+executor, architect+builders+critic, researcher swarm+reducer, strategist+operator+verifier).

**Team Orchestration**: 5 patterns (leader+specialists, planner+executor, architect+builders, researcher+synthesizer, critic loop).

**Subagent Spawning**: 7 gating questions.

**Merge and Reduction**: 6 concerns (conflict detection, priority rules, evidence weighting, scope alignment, redundancy removal, final synthesis).

**Disagreement Engine**: 6 challenger types (logic, factual, scope, UX, strategic, implementation risk).

**Quality Gates**: 5 gates (sufficient context, architecture coherence, implementation readiness, factual confidence, shipping readiness).

**Mission Graph**: 8 node types (objectives, dependencies, assumptions, parallel branches, convergence points, quality gates, decision points, blockers).

### 4. Memory Layer (docs 30-34)

**6 Layers**: working, session, mission, project, reusable, archived.

**Persistence**: 5 gating questions. **Compression**: 5 target types. **Conflict Resolution**: 4 strategies. **Retrieval**: 4-level priority order.

### 5. Deliverables Layer (docs 40-44)

**8 Output Schema Types**, **mission-type-aware Deliverable Compiler**, **8 Mission Types**, **7 Workflow Archetypes**, **6 Review Formats**.

### 6. Runtime Adapters (docs 50-55)

**Claude Code**: Strong repo context and file editing. **OpenClaw** and **NanoClaw**: Defined by adapter questions. **Runtime Selection Policy**: 7 inputs drive the choice.

### 7. Templates, Prompts, Examples, Schemas

**10 Templates** (70 project instantiation, 71 mission, 72 agent, 73 specialist agent, 74 team topology, 75 workflow, 76 deliverable, 77 constraints, 78 success criteria, 79 runtime profile).

**5 Prompts** (80 kickoff system, 81 master execution, 82 project instantiation, 83 agent creation, 84 topology compilation).

**5 YAML Schemas** (portable agent spec, mission, team topology, deliverable, runtime profile).

**5 Instance Templates** (blank YAML forms to fill in per mission).

---

## Execution Flow

```
Human defines a mission
        |
        v
  Constraint Compiler — takes deadline, budget, risk, etc.
        |
        v
  Policy Engine — selects operational policy
        |
        v
  Router — analyzes mission type, complexity, ambiguity, risk
        |
        v
  Topology Compiler — selects team pattern
        |
        v
  Agent Foundry — creates/selects agents per portable_agent_spec
        |
        v
  Team Orchestration — wires agents with handoff/merge rules
        |
        v
  Quality Gates — checkpoints at key stages
        |
        v
  Disagreement Engine — challengers stress-test outputs
        |
        v
  Merge and Reduction — combines multi-agent outputs
        |
        v
  Deliverable Compiler — compiles final output per mission type
        |
        v
  Provenance Graph — records contributions and uncertainties
        |
        v
  Memory System — persists/compresses/archives per constitution
```

---

## Key Design Insights

1. **Meta-framework, not code.** Entirely declarative. Execution happens when an LLM reads these docs and follows the protocols.
2. **Governance is first-class.** Authority boundaries, escalation triggers, stop conditions, and human approval gates at every layer.
3. **Agent Foundry pattern.** Dynamic specialist creation based on detected capability gaps.
4. **Disagreement Engine.** Typed adversarial challengers stress-test outputs before quality gates.
5. **Governed memory.** 6-layer memory model with explicit persistence, compression, conflict resolution, and retrieval policies.
6. **Full portability.** YAML schemas are the interchange format — move missions between runtimes without rewriting the control plane.
