# 62 Glossary

## Purpose

Formal definitions of terms used throughout the GLORB framework. Terms are listed alphabetically.

## Terms

**Agent**: An autonomous unit of work within GLORB. Has a defined identity, scope, capability profile, and governance boundaries. Agents are the execution units that perform tasks within a topology. See 10 Agent Primitives.

**Agent Foundry**: The system component that detects capability gaps and creates new agents to fill them. See 15 Agent Foundry.

**Archived Memory**: The lowest-access memory layer. Stores historical records for audit and reference. Not included in standard retrieval sweeps. See 30 Memory Layers.

**Authority Scope**: The level of decision-making power granted to an agent. Ranges from Execute (carry out assigned tasks) to Govern (modify policy, reserved for humans). See 08 Governance Model.

**Capability Dimension**: A measurable axis of agent ability, such as Precision, Creativity, or Adversarial Rigor. Scored as Low, Medium, or High. See 11 Agent Capability Model.

**Capability Gap**: A situation where a mission requires a capability dimension at a level that no available agent provides. Triggers the Agent Foundry. See 15 Agent Foundry.

**Challenge**: A structured objection raised by the Disagreement Engine against a decision, output, or assumption. Includes the claim, evidence, severity, and suggested resolution. See 25 Disagreement Engine.

**Compilation**: The process of translating abstract specifications into concrete, executable configurations. Used in Constraint Compiler (constraints to parameters), Topology Compiler (routing decision to team structure), and Deliverable Compiler (agent outputs to final deliverable).

**Compression**: The process of reducing memory size while preserving essential value. Strategies include key extraction, summarization, pattern extraction, and deduplication. See 32 Memory Compression.

**Constraint**: A boundary condition on mission execution, such as deadline, budget, quality bar, or risk tolerance. Constraints are normalized and compiled into parameters that shape the execution. See 03 Constraint Compiler.

**Convergence Point**: A node in the Mission Graph where parallel work streams merge. Requires all contributing branches to complete before downstream work begins. See 27 Mission Graph.

**Control Plane**: The layer of GLORB that defines structure, governance, and orchestration logic. Distinct from the runtime, which handles actual execution. See 00 System Overview.

**Critique**: An adversarial evaluation of a work product for flaws, risks, and improvements. A reasoning function (see 02) and an agent role (see 14 Specialist Agent Patterns).

**Deadlock**: A state where two or more agents are mutually blocked, each waiting for the other. Detected and resolved by the lead agent. See 22 Team Orchestration.

**Deliverable**: The final output of a mission, assembled by the Deliverable Compiler from agent work products. Structure varies by mission type. See 41 Deliverable Compiler.

**Disagreement Engine**: A structured adversarial process that challenges decisions and outputs to improve quality. Uses challenger types such as Logic, Factual, and Scope challengers. See 25 Disagreement Engine.

**Escalation**: The process of transferring a decision or problem to a higher authority (lead agent or human) when an agent cannot proceed. See 08 Governance Model.

**Evidence Weighting**: The process of comparing the evidentiary support behind competing claims during merge or disagreement resolution. Verified data ranks highest, unsourced assertions are rejected. See 24 Merge and Reduction.

**Governance Profile**: A preset combination of authority, tool, memory, and autonomy settings for common agent roles. Profiles include Worker, Specialist, Lead, and Operator. See 08 Governance Model.

**Handoff**: The transfer of work ownership from one agent to another. Includes the artifact, completion status, and open issues. See 22 Team Orchestration.

**Lead Agent**: The coordinating agent in a topology that receives the mission, distributes tasks, monitors progress, and manages inter-agent coordination. Has the Lead governance profile.

**Memory Layer**: One of six tiered storage scopes in GLORB: Working, Session, Mission, Project, Reusable, Archived. Each has distinct lifetime, access rules, and promotion paths. See 30 Memory Layers.

**Memory Promotion**: Moving information from a lower (shorter-lived) memory layer to a higher (longer-lived) one. Typically occurs at session or mission boundaries. See 30 Memory Layers.

**Merge**: The process of combining outputs from multiple agents into a single coherent result. Handled by a Synthesizer agent using the merge algorithm. See 24 Merge and Reduction.

**Mission**: A discrete unit of work with a defined objective, constraints, and expected deliverables. The top-level task that GLORB orchestrates. See 42 Mission Types.

**Mission Graph**: A directed acyclic graph representing the execution plan for a mission. Contains objectives, dependencies, parallel branches, convergence points, quality gates, and blockers. See 27 Mission Graph.

**Mission Memory**: The shared memory layer accessible to all agents in a mission. Persists for the mission duration. See 30 Memory Layers.

**Policy**: A named configuration preset that governs system behavior during a mission. Adjusts agent count, critique intensity, quality gates, memory depth, and autonomy in concert. See 04 Policy Engine.

**Provenance**: The traceable history of how a decision, output, or artifact was produced. Recorded as a directed acyclic graph of events. See 06 Provenance Graph.

**Quality Gate**: An evaluation checkpoint that work products must pass before progressing. Five gates are defined: Sufficient Context, Architecture Coherence, Implementation Readiness, Factual Confidence, and Shipping Readiness. See 26 Quality Gates.

**Reasoning Function**: One of eight structured cognitive operations in the Reasoning Engine: Decomposition, Abstraction Management, Constraint Evaluation, Tradeoff Evaluation, Uncertainty Handling, Synthesis, Critique, Decision Support. See 02 Reasoning Engine.

**Reduction**: Synonym for merge, emphasizing the act of reducing multiple outputs into one. See 24 Merge and Reduction.

**Routing**: The process of evaluating mission characteristics and selecting an execution strategy (solo agent, specialist, team topology, or escalation). See 20 Router.

**Runtime**: The environment where agents actually execute. GLORB defines three adapter types: Claude Code, OpenClaw, and NanoClaw. The control plane is runtime-agnostic. See 50 Runtime Abstraction Layer.

**Specialist Pattern**: A reusable agent archetype that defines purpose, capability profile, and behavioral contract. Ten patterns are defined: Strategist, Architect, Builder, Researcher, Critic, Router, Synthesizer, Verifier, Reviewer, Memory Manager. See 14 Specialist Agent Patterns.

**Topology**: The structure of agents and their relationships within a mission. Defines who does the work and how they coordinate. Compiled by the Topology Compiler from the Router's output. See 21 Topology Compiler.

**Topology Compiler**: The component that translates a Router decision into a concrete team structure with named agents, defined relationships, and an execution plan. See 21 Topology Compiler.

**Working Memory**: The most ephemeral memory layer. Private to a single agent, single task. Discarded on task completion. See 30 Memory Layers.
