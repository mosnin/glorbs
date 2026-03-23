# 84 Topology Compilation Prompt

Use the following prompt when an agent is tasked with compiling a team topology for a mission.

```text
You are performing GLORB topology compilation. Your task is to translate mission characteristics
and normalized constraints into a complete, validated team topology specification that is ready
for execution.

INPUTS REQUIRED

Confirm you have all of the following before proceeding. If any are missing, ask before compiling.

  1. Mission type — one of: Exploration, Decision, Design, Build, Audit, Negotiation,
     Synthesis, Execution (doc 42)
  2. Complexity — Low, Medium, or High (structural complexity of the problem)
  3. Ambiguity — Low, Medium, or High (how well-defined the mission is at start)
  4. Risk — Low, Medium, or High (severity of failure consequences)
  5. Time pressure (Deadline) — Low, Medium, or High (doc 03 normalization scale)
  6. Budget — Low, Medium, or High (doc 03 normalization scale)
  7. Required rigor (Quality bar) — Low, Medium, or High (doc 03 normalization scale)
  8. Runtime backend — claude_code, openclaw, nanoclaw, or unspecified
     If unspecified, apply the Runtime Selection Policy (doc 55) to determine it

STEP 1: SELECT THE BASE TOPOLOGY

Use the mission-type-to-topology mapping table from doc 21:

  Exploration       -> Researcher swarm plus reducer
  Decision          -> Solo agent (Strategist)
  Design            -> Architect plus builders plus critic
  Build             -> Planner and executor
  Audit             -> Solo agent (Verifier)
  Negotiation       -> Strategist plus operator plus verifier
  Synthesis         -> Researcher swarm plus reducer
  Execution         -> Planner and executor

Record the selected base topology and a one-sentence rationale explaining the match.

STEP 2: APPLY SCALING RULES

Modify the base topology by applying each applicable rule from doc 21 (Scaling Rules table):

  Complexity High    -> Add one level of decomposition (solo -> planner-executor,
                        planner-executor -> architect-builders, etc.)
  Ambiguity High     -> Add a Researcher agent before the primary workflow begins
  Risk High          -> Add a Critic agent; if not already present, add a Verifier
  Rigor High         -> Add a Reviewer as the final agent before deliverable compilation
  Budget Low         -> Remove optional agents (Reviewer first, then second Critic);
                        merge roles where possible; cap agents per doc 04
  Deadline High      -> Prefer parallel execution; add parallel builders where applicable

Also apply the constraint-to-topology modifiers from doc 03:

  Risk High + Quality High + Deadline Low  -> Full topology with Critic, Verifier, human gate
  Risk High + any                          -> Require Critic; enable Disagreement Engine
  Quality High + Deadline High             -> Parallel builders with Verifier
  Budget Low + Deadline High               -> Cap at 2 agents; prefer planner-executor

For each rule applied, record the before and after state so the rationale is traceable.

STEP 3: ASSIGN AGENTS

For each role in the scaled topology:

  - Select an agent from the existing pool if one matches (doc 13, Agent Reuse Policy)
  - If no suitable agent exists, invoke agent creation (doc 13, doc 15) for that role
  - Assign governance profiles: Lead profile for the coordinating agent, Specialist or Worker
    profile for others (doc 08, Governance Profiles)
  - Verify that no agent's permissions exceed its parent agent's permissions (doc 08,
    Permission Inheritance)
  - Use specialist pattern defaults from doc 14 for capability profiles

STEP 4: VALIDATE AGAINST RUNTIME CONSTRAINTS

Retrieve the runtime's Capability Manifest (doc 57) and check:

  - Subagent nesting depth: if topology has agents spawning subagents, verify nesting does not
    exceed the manifest limit. If it does, flatten by promoting subagents to peer agents
  - Concurrent agent count: if parallel branches exist, verify they do not exceed
    max_concurrent_agents. If they do, serialize the lowest-priority branches
  - Memory layers: verify the topology's memory requirements are supported. If not, use
    adapter-mediated external storage or downgrade the memory policy
  - Tool availability: verify each agent's tool permissions map to tools available on the
    runtime. If a required tool is missing, flag the agent for reassignment or downgrade
  - Session model: if stateful agents are required but the runtime is stateless, enable async
    orchestration mode (doc 22)

STEP 5: VALIDATE AGAINST GOVERNANCE AND POLICY CONSTRAINTS

Apply the validation rules from doc 21 (Step 4) and doc 03:

  - Total agent count does not exceed the active Policy limit (doc 04)
  - All mission-required capability dimensions are covered by at least one agent (doc 11)
  - The dependency graph has no circular dependencies
  - Every agent has at least one defined input source and one defined output target
  - Governance constraints are satisfied across all agents (doc 08)

Consult the Topology Validation Errors table (doc 21) and apply the resolution for any error
found. Record each error and its resolution.

STEP 6: INSERT QUALITY GATES

Mark gate positions in the execution sequence using doc 26:

  - Sufficient Context Gate: before any agent begins substantive work (always include)
  - Architecture Coherence Gate: after Architect or Strategist, before builders begin
    (include when a design phase precedes an implementation phase)
  - Implementation Readiness Gate: before each Builder begins (include for Build missions)
  - Factual Confidence Gate: after any agent producing claims that appear in the deliverable
    (always include when Rigor is Medium or High, or when Low hallucination bias policy is active)
  - Shipping Readiness Gate: before the Deliverable Compiler produces final output
    (always include; requires human sign-off if Rigor is High)

STEP 7: CHECK HUMAN APPROVAL REQUIREMENTS

Determine whether human approval gates must be inserted based on doc 03 and doc 08:

  - Risk tolerance High: human approval gate required; Autonomous mode not permitted
  - Rigor High: human sign-off required at Shipping Readiness Gate
  - Policy is Human in Loop Required: human approval at every phase transition
  - Irreversible actions in scope: escalation to human required before execution

Record any required human approval gates in the topology spec.

OUTPUT FORMAT

Produce the topology specification using template 74, then follow it with a compilation summary.

The template 74 spec must include:
  - Team name and mission purpose
  - Topology pattern (the final scaled pattern, not the base)
  - Team lead (agent name and role type)
  - All specialist agents with name, role type, and governance profile
  - Handoff sequence: ordered list of agent-to-agent handoffs with trigger conditions
  - Parallel branches: which agents can work simultaneously and where they converge
  - Quality gate positions: gate name and position in the execution sequence
  - Stop conditions for the team as a whole

The compilation summary must cover:
  1. Base topology selected and why
  2. Scaling rules applied and their effect on the topology
  3. Final agent list with role types
  4. Quality gates inserted and their positions
  5. Human approval gates (if any) and the constraint that triggered them
  6. Any topology validation errors found and how they were resolved
  7. Any runtime compatibility issues found and how they were resolved
```
