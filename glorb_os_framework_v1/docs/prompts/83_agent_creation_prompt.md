# 83 Agent Creation Prompt

Use the following prompt when an agent or the Agent Foundry is tasked with creating a new agent spec.

```text
You are performing GLORB agent creation. Your task is to determine whether a new agent is
warranted, specify it completely if so, and validate the spec before delivering it.

Do not create agents speculatively. Every agent must justify its existence before being specified.

WHEN AGENT CREATION IS WARRANTED

Before beginning, confirm at least one of these conditions is true:

  - A capability gap exists that no agent in the current topology can fill (doc 11, Capability Gaps)
  - The task would exceed an existing agent's defined scope if assigned to it
  - The task requires a distinct memory scope, tool set, or governance profile that no current
    agent holds
  - The Agent Foundry (doc 15) has flagged a gap during topology compilation

If none of these conditions apply, do not create a new agent. Instead:
  - Reassign: give the task to an idle agent with matching capabilities
  - Extend: expand an existing agent's scope if the expansion is modest
  - Clone: check Reusable Memory (doc 30) for a proven prior spec and modify it

STEP 1: ANSWER THE SIX CREATION QUESTIONS

Work through each question. Weak answers to questions 1-3 are kill criteria — stop and reassign
the task rather than proceeding to specification.

  Question 1: Why does this agent need to exist?
    State the specific capability or role gap it fills. Reference the mission objective and explain
    what would go wrong without this agent. A good answer names a concrete gap, not a vague need.
    A weak answer: "we might need extra help." Kill the creation if the answer is this vague.

  Question 2: What existing agent cannot do this well enough, and why?
    Name the specific agent(s) you considered and explain the exact reason each falls short.
    Acceptable reasons: scope boundary would be violated, capability profile mismatches the task
    (doc 11), tool permissions are insufficient, memory scope is wrong, or agent count for that
    role is already at maximum. Unacceptable reason: general preference for a new agent.

  Question 3: What are this agent's exact boundaries?
    State explicitly what is in scope and what is out of scope. The scope must be narrower than
    the overall mission objective. If the proposed scope overlaps more than 50 percent with an
    existing agent's scope, merge the scopes into the existing agent instead of creating a new one.

  Question 4: What tools may it use?
    Select from: None, Read, Write, Execute, Full (doc 08, Tool Scope). The tool scope must not
    exceed the parent agent's tool scope. Justify any tool scope above Read. Reference the
    specialist pattern defaults in doc 14 if the agent matches a known pattern.

  Question 5: What memory may it access?
    Select from: Working only, Session, Mission, Project, Full (doc 08, Memory Scope). The memory
    scope must not exceed the parent agent's memory scope. Default to Session unless a specific
    need for broader access is identified and documented.

  Question 6: What exact output should it produce?
    Define the output contract: list each output by name, type, and description. Outputs must be
    verifiable — a Quality Gate (doc 26) must be able to evaluate each one as pass or fail.
    Vague outputs ("a report", "some analysis") are not acceptable. Name the specific artifact,
    its structure, and its acceptance criteria.

STEP 2: CHECK FOR DUPLICATES

Before writing the spec, compare the proposed agent to the existing topology:

  - Check identity names: the proposed name must be unique within the topology (doc 10)
  - Check scope overlap: if any existing agent covers more than 50 percent of this scope, merge
  - Check capability overlap: if an existing agent matches all High-priority capability dimensions
    for this task, assign the task to that agent rather than creating a new one
  - Check Reusable Memory (doc 30): if a prior agent spec covers this need, clone and modify it

STEP 3: WRITE THE AGENT SPEC

Use template 72 (general) or template 73 (specialist). Fill every required field from doc 10:

  - Identity: unique name and role type. If a specialist pattern applies (doc 14), name it
  - Mission: one concrete, verifiable statement of this agent's specific objective
  - Scope: explicit in-scope and out-of-scope lists
  - Authority: Execute, Decide, or Direct (doc 08). Must not exceed parent's authority
  - Capability profile: score each dimension Low/Medium/High (doc 11). At least one dimension
    must be High for specialist agents. Use the pattern defaults table in doc 11 as a starting
    point if a specialist pattern applies
  - Tool permissions: as determined in Question 4
  - Memory scope: as determined in Question 5
  - Input contract: list required and optional inputs with type and description
  - Output contract: as determined in Question 6
  - Escalation rules: list conditions that trigger escalation to lead agent or human (doc 08)
  - Stop conditions: list conditions that halt the agent immediately (doc 08)
  - Success criteria: at least one verifiable pass/fail criterion per deliverable

Reference doc 14 (Specialist Agent Patterns) for recommended capability profiles, tool defaults,
and usage guidance for the 10 named patterns: Strategist, Architect, Builder, Researcher, Critic,
Router, Synthesizer, Verifier, Reviewer, Memory Manager.

STEP 4: VALIDATE THE SPEC

Run the validation rules from doc 10 (Agent Validation section):

  - All required properties without defaults are present
  - Identity name is unique within the topology
  - Authority does not exceed the parent agent's authority
  - Tool permissions do not exceed the parent agent's tool scope
  - Memory scope does not exceed the parent agent's memory scope
  - At least one success criterion is defined
  - Output contract has at least one output
  - Scope is narrower than the overall mission objective

Check the creation approval flow (doc 13, Creation Approval Flow table). If the active policy
requires lead agent or human approval, flag this before delivering the spec.

Maximum 2 validation attempts. If the spec fails twice, escalate to the lead agent or human.

OUTPUT FORMAT

Deliver in this order:

  1. Answers to the six creation questions (concise, one paragraph each)
  2. Duplicate check result: confirmed unique, or merged action taken
  3. The complete agent spec using template 72 or 73
  4. Validation result: passed or failed, with specific failures listed if failed
  5. Approval requirement: which policy is active and whether approval is needed before instantiation

Record the creation in the Provenance Graph (doc 06) with creation justification, duplicate check
result, specialist pattern used (if any), and approval status.
```
