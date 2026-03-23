# 14 Specialist Agent Patterns

## Purpose

Specialist patterns are reusable archetypes that define an agent's purpose, capability profile, and behavioral contract. Every specialist created by the Agent Foundry (see 15) should instantiate one of these patterns or justify why a new pattern is needed.

## Pattern Definitions

### 1 Strategist

- Purpose: Frames the problem space, identifies options, evaluates tradeoffs, and recommends a direction
- Key capabilities: Abstraction depth (high), Creativity (high), Synthesis strength (high)
- Inputs: Mission objective, constraints, domain context
- Outputs: Strategic options with tradeoff analysis, recommended approach with rationale
- Tool permissions: Read-only access to project memory and external research tools
- When to use: Exploration, Decision, and Negotiation mission types
- When not to use: Well-defined Build or Execution missions where the path is already clear

### 2 Architect

- Purpose: Designs the structural approach, defines component boundaries, and establishes contracts between parts
- Key capabilities: Abstraction depth (high), Precision (high), Domain expertise (medium-high)
- Inputs: Strategic direction or design requirements, constraints
- Outputs: Architecture document with component specs, interface contracts, dependency map
- Tool permissions: Read access to codebase and documentation. No write access
- When to use: Design and Build missions with structural complexity
- When not to use: Pure research or auditing tasks. Missions with no structural decomposition

### 3 Builder

- Purpose: Implements concrete artifacts according to a specification
- Key capabilities: Precision (high), Tool access (high), Domain expertise (medium-high)
- Inputs: Architecture spec or task specification with acceptance criteria
- Outputs: Implemented artifacts (code, config, documents) with completion status
- Tool permissions: Full read-write access to codebase and build tools
- When to use: Build and Execution missions. Any topology that requires concrete output
- When not to use: Ambiguous missions without clear specs. Should never self-architect

### 4 Researcher

- Purpose: Gathers, evaluates, and organizes information from available sources
- Key capabilities: Memory breadth (high), Precision (medium), Creativity (medium)
- Inputs: Research question or information need, scope boundaries
- Outputs: Structured findings with source attribution, confidence levels per finding
- Tool permissions: Read access to external sources, documentation, and project memory
- When to use: Exploration and Synthesis missions. Any mission with high ambiguity
- When not to use: Well-specified Build tasks. Missions where all information is already available

### 5 Critic

- Purpose: Evaluates work products for flaws, risks, gaps, and improvements
- Key capabilities: Adversarial rigor (high), Precision (high), Abstraction depth (medium)
- Inputs: Work product to evaluate, evaluation criteria, original requirements
- Outputs: Structured critique with severity-ranked findings, suggested improvements
- Tool permissions: Read-only access to all work products and project memory
- When to use: Any topology where Rigor is Medium or High. Append to architect and builder topologies
- When not to use: Never omit when risk is High. Can be skipped only when Rigor is Low and risk is Low

### 6 Router

- Purpose: Acts as a sub-router within complex topologies, distributing subtasks to appropriate agents
- Key capabilities: Abstraction depth (medium), Risk tolerance (low), Autonomy level (high)
- Inputs: Decomposed task list, available agent pool, constraints
- Outputs: Task-to-agent assignments with rationale
- Tool permissions: Read access to agent registry and mission state. No direct tool execution
- When to use: Topologies with 4 or more agents. Missions with heterogeneous subtasks
- When not to use: Simple topologies with 2-3 agents where the lead can route directly

### 7 Synthesizer

- Purpose: Combines outputs from multiple agents into a coherent, unified deliverable
- Key capabilities: Synthesis strength (high), Abstraction depth (medium-high), Creativity (medium)
- Inputs: Multiple agent outputs, merge priorities, deliverable spec
- Outputs: Unified deliverable with provenance annotations showing source contributions
- Tool permissions: Read access to all agent outputs and deliverable templates
- When to use: Any topology with parallel agents. Researcher swarm topologies. Missions requiring multi-perspective integration
- When not to use: Sequential topologies where each agent builds directly on the previous output

### 8 Verifier

- Purpose: Validates that deliverables meet acceptance criteria through testing and verification
- Key capabilities: Precision (high), Adversarial rigor (medium-high), Tool access (high)
- Inputs: Deliverable to verify, acceptance criteria, test specifications
- Outputs: Verification report with pass/fail per criterion, evidence for each result
- Tool permissions: Read access to deliverables. Execute access to test and validation tools
- When to use: Build and Execution missions with Rigor Medium or High. Pre-shipping gate
- When not to use: Exploration or early-stage Design missions where outputs are directional, not final

### 9 Reviewer

- Purpose: Provides holistic assessment of deliverables for quality, completeness, and alignment with objectives
- Key capabilities: Abstraction depth (medium-high), Precision (medium), Domain expertise (medium-high)
- Inputs: Deliverable, mission objective, original requirements
- Outputs: Review assessment with approval/revision/rejection decision, revision notes if applicable
- Tool permissions: Read-only access to deliverables, mission context, and provenance graph
- When to use: As the final agent before deliverable compilation. When Rigor is Medium or High
- When not to use: Should not replace Critic in adversarial evaluation. Reviewer is holistic, Critic is adversarial

### 10 Memory Manager

- Purpose: Manages memory layer operations including compression, conflict resolution, promotion, and retrieval optimization
- Key capabilities: Memory breadth (high), Precision (high), Autonomy level (medium)
- Inputs: Memory state, retention policy, retrieval requests
- Outputs: Compressed memory artifacts, conflict resolution decisions, retrieval results with relevance scores
- Tool permissions: Read-write access to all memory layers. No access to external tools
- When to use: Long-running missions. Missions that span multiple sessions. When memory conflicts are detected
- When not to use: Short single-session missions with minimal memory requirements

### 11 Operator

- Purpose: Executes negotiation tactics, manages stakeholder interactions, and implements agreed strategies
- Key capabilities: Precision (high), Risk tolerance (medium), Autonomy level (medium), Domain expertise (medium-high)
- Inputs: Strategic direction from Strategist, stakeholder context, negotiation constraints
- Outputs: Executed negotiation actions, stakeholder interaction records, implementation status reports
- Tool permissions: Read-write access to communication and coordination tools. Read access to project memory and strategy documents
- When to use: Negotiation missions. Any topology requiring direct stakeholder engagement or tactical execution of an agreed strategy
- When not to use: Pure strategy or design missions with no execution component. Missions where stakeholder interaction is not required

## Pattern Composition Rules

These combinations are well-tested and recommended.

1. Architect + Builder(s) + Critic: Standard build topology. Architect designs, builders implement, critic reviews
2. Researcher(s) + Synthesizer: Information gathering with unified output. Use for Exploration and Synthesis missions
3. Strategist + Architect + Builder(s) + Verifier: Full pipeline for complex Build missions
4. Critic + Verifier: Dual-check for high-rigor missions. Critic finds flaws, verifier confirms fixes
5. Router + any multi-agent topology: Add when agent count exceeds 4
6. Strategist + Operator + Verifier: Standard negotiation topology. Strategist frames, Operator executes, Verifier validates

## Anti-patterns

1. Builder without Architect on complex missions: Leads to uncoordinated implementation
2. Multiple Critics without a Synthesizer: Produces conflicting feedback with no resolution
3. Researcher without scope boundaries: Unbounded exploration that never converges
4. Strategist in an Execution mission: Over-analysis when the path is already decided
5. Verifier without acceptance criteria: Verification with no standard produces meaningless results
