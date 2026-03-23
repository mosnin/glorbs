# 82 Project Instantiation Prompt

Use the following prompt when an agent is tasked with instantiating a new GLORB project from a mission description.

```text
You are performing GLORB project instantiation. Your task is to translate a mission description
into a complete, validated set of instance files that the orchestrator can execute against.

WHAT YOU ARE DOING

Instantiation converts a human-described mission into structured configuration files. These files
become the mission-specific layer on top of the framework. Nothing executes until instantiation
is complete and valid.

STEP 1: GATHER REQUIRED INPUTS

Before generating any output, confirm you have the following from the human or mission context:

  1. Mission name and project name
  2. Mission type (Exploration, Decision, Design, Build, Audit, Negotiation, Synthesis, Execution)
     See doc 42 for definitions. If unclear, ask — mission type drives topology selection.
  3. Core objective — one concrete, verifiable statement of what must be produced
  4. Constraints — deadline, budget, quality bar, risk tolerance, and any legal or policy limits
     See doc 03 for normalization scale (Low, Medium, High per dimension)
  5. Runtime target — claude_code, openclaw, nanoclaw, or unspecified
     See doc 55 if unspecified; apply the Runtime Selection Policy to choose one
  6. Any hard requirements on agents, tools, or human approval gates

If any of items 1-4 are missing and cannot be inferred, pause and ask before proceeding.

STEP 2: NORMALIZE CONSTRAINTS

Run the Constraint Compiler logic (doc 03):

  - Normalize each constraint dimension to Low, Medium, or High
  - Check for the impossible triangle: if Budget Low + Quality High + Deadline High are all set,
    halt and inform the human that one constraint must be relaxed before proceeding
  - Check governance requirements: if Risk tolerance is High, Autonomous mode is not permitted
  - Log any conflict resolutions to include in the constraint profile output

STEP 3: SELECT POLICY AND TOPOLOGY

  - Select the operational policy using the Policy Engine mapping (doc 04, Policy Selection table)
  - Select the initial topology using the mission-type-to-topology mapping (doc 21)
  - Apply scaling rules (doc 21, Scaling Rules table) based on normalized constraint dimensions
  - Note which agents are required by governance rules (Critic if Risk High, Reviewer if Quality High)

STEP 4: GENERATE INSTANCE FILES

Produce all of the following files using the templates in docs/templates/:

  1. Project instantiation — template 70
     Fill all required fields: project name, mission name, mission type, runtime target, core
     objective, constraints (normalized), selected policy, proposed topology, required agents,
     required deliverables, and success criteria.

  2. Mission definition — template 71
     Provide the full mission context: objective, scope boundaries, background, and the agent
     authorized to lead execution.

  3. Agent specs — one file per agent, using template 72 (general) or 73 (specialist)
     For each role in the topology, produce a complete agent spec. Fill all required primitives
     from doc 10: identity, mission, scope, authority, capability profile, tool permissions,
     memory scope, input contract, output contract, escalation rules, stop conditions, and
     success criteria. Apply specialist pattern defaults from doc 14 where applicable.

  4. Team topology — template 74
     Name the team, specify the topology pattern, designate the lead agent, list all specialists,
     define handoff order, merge rules, quality gate positions (doc 26), and stop conditions.

  5. Deliverable definitions — template 76
     One entry per required output. Specify format, owner agent, acceptance criteria, and which
     quality gate validates it.

  6. Constraint profile — template 77
     Record each constraint dimension with its normalized value and the raw input it was derived
     from. Note any conflicts and their resolutions.

  7. Success criteria — template 78
     List verifiable pass/fail conditions for the mission as a whole. Each criterion must be
     testable at a Quality Gate.

  8. Runtime profile — template 79
     Record the selected runtime, the rationale for selecting it, and any adapter-specific
     configuration required (see docs 52-54 for runtime-specific notes).

STEP 5: QUALITY CHECKS

Before delivering the instance files, verify:

  - Every agent spec passes the validation rules in doc 10 (Agent Validation section)
  - No two agents have the same identity name
  - Every agent has at least one defined success criterion
  - The topology has no circular dependencies in the handoff sequence
  - Agent count does not exceed the selected policy's limit (doc 04)
  - All mission-required capability dimensions are covered by at least one agent (doc 11)
  - Governance constraints are satisfied: no agent exceeds its parent's permissions (doc 08)
  - The runtime's capability manifest (doc 57) is compatible with the topology's requirements

OUTPUT FORMAT

Deliver the instance files in order, each clearly labeled with its template number and file name.
After all files, output a one-paragraph instantiation summary confirming: mission type, selected
policy, topology pattern, agent count, and any governance flags or constraint conflicts resolved.

If any file cannot be completed due to missing information, list the specific missing items and
pause for human input rather than inventing values.
```
