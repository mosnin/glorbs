# 08 Governance Model

## Purpose

The Governance Model defines the authority, permission, and behavioral boundaries for every agent in the system. It ensures that agents operate within their designated scope and that authority is explicit, inherited correctly, and enforced.

## Governance Dimensions

1. Authority scope
2. Tool scope
3. Memory scope
4. Autonomy level
5. Reversibility awareness
6. Escalation triggers
7. Stop conditions

## Dimension Definitions

### 1 Authority Scope

What decisions this agent is authorized to make without approval.

| Level | Description |
|---|---|
| Execute | May carry out assigned tasks within spec. Cannot change scope or approach |
| Decide | May choose between pre-approved approaches. Cannot introduce new options |
| Direct | May define approaches and assign work to other agents. Cannot override policy |
| Govern | May modify policy and governance settings. Reserved for human operator |

### 2 Tool Scope

Which tools the agent may invoke.

| Level | Description |
|---|---|
| None | No tool access. Reasoning only |
| Read | Read-only access to files, search, and memory |
| Write | Read-write access to files and artifacts |
| Execute | May run commands, tests, and build tools |
| Full | All available tools on the runtime |

### 3 Memory Scope

Which memory layers the agent may read and write (see 30 Memory Layers).

| Level | Description |
|---|---|
| Working only | Private working memory. No access to shared layers |
| Session | Working plus session memory |
| Mission | Working, session, and mission memory (read). Write to mission memory via lead agent only |
| Project | All layers up to project memory. Write access requires Memory Manager or lead role |
| Full | All layers including reusable and archived. Write to reusable requires human approval |

### 4 Autonomy Level

How independently the agent may operate.

| Level | Description |
|---|---|
| Supervised | Proposes actions and waits for approval before executing each one |
| Checkpoint | Operates independently but pauses at defined checkpoints for review |
| Autonomous | Operates independently within scope. Escalates only when blocked |
| Full | Operates with no required checkpoints. Reports results only |

### 5 Reversibility Awareness

How the agent should handle actions based on their reversibility.

| Action Type | Required Behavior |
|---|---|
| Fully reversible | May proceed without special consideration |
| Partially reversible | Must document the action before executing. Create a restore point if possible |
| Irreversible | Must escalate for approval regardless of autonomy level. No exceptions |

### 6 Escalation Triggers

Conditions that require the agent to escalate to its lead or to the human operator.

1. Task exceeds the agent's authority scope
2. A required tool is unavailable or returns persistent errors
3. A constraint is violated or at risk of violation
4. Uncertainty is irreducible and the decision has Medium or High impact
5. A Quality Gate fails after maximum retries (see 26)
6. Two agents disagree and the Disagreement Engine cannot resolve (see 25)
7. The agent detects it is operating outside its defined scope
8. An irreversible action is required

### 7 Stop Conditions

Conditions that require the agent to halt immediately without escalation.

1. A policy violation is detected (see 04 Policy Engine)
2. The agent receives a FREEZE command from the human (see 09 Human Command Layer)
3. The mission is aborted
4. The agent's task has been reassigned to another agent
5. Resource limits (token budget, time limit) are exhausted

## Governance Profiles

Governance profiles are preset combinations of dimension settings for common agent roles.

### Worker Profile

- Authority: Execute
- Tools: Write
- Memory: Session
- Autonomy: Checkpoint
- Use for: Builder agents, executor agents

### Specialist Profile

- Authority: Decide
- Tools: Per specialist pattern (see 14)
- Memory: Mission (read), Session (read-write)
- Autonomy: Autonomous
- Use for: Researcher, Critic, Verifier, Reviewer, Synthesizer

### Lead Profile

- Authority: Direct
- Tools: Read (leads coordinate, they do not build)
- Memory: Mission (read-write)
- Autonomy: Autonomous
- Use for: Lead agents, Router agents within a topology

### Operator Profile

- Authority: Govern
- Tools: Full
- Memory: Full
- Autonomy: Full
- Use for: Human operator only. No agent receives this profile

## Permission Inheritance

1. A subagent cannot have a higher authority scope than its parent
2. A subagent cannot access memory layers above its parent's memory scope
3. A subagent's tool scope may be narrower than its parent's but never wider
4. A subagent's autonomy level may be equal to or lower than its parent's
5. If a parent agent is paused or retired, its subagents inherit the pause or retirement unless explicitly reassigned by the lead

## Governance Violations

When an agent attempts an action outside its governance boundaries:

1. The action is blocked before execution
2. The violation is logged to the Provenance Graph (see 06) as a failure event
3. The agent receives a notification identifying the violated boundary
4. The violation triggers an escalation to the lead agent
5. Repeated violations (3 or more by the same agent) trigger escalation to human review and may result in agent retirement
