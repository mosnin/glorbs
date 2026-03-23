# 13 Agent Creation Framework

## Purpose

The Agent Creation Framework provides the structured process for deciding whether a new agent should be created, specifying it, and validating it before instantiation. It prevents unnecessary agent proliferation and ensures every agent has a clear reason to exist.

## Creation Questions

1. Why does this agent need to exist
2. What problem does it solve
3. Why can the task not stay inside an existing agent
4. What are its exact boundaries
5. What is its output contract
6. What tools may it use
7. What memory may it access

## Creation Decision Process

### Step 1: Justify Existence

Answer questions 1-3. If any answer is weak, do not create the agent.

**Kill criteria** — do NOT create the agent if:

1. An existing agent in the topology already covers this scope (even partially). Expand the existing agent's scope instead
2. The task can be completed as a subtask of an existing agent without exceeding that agent's scope or capability profile
3. The task is a one-time action that does not require a distinct identity, memory, or tool set
4. Creating the agent would exceed the agent count limit set by the active Policy (see 04 Policy Engine)
5. The task is purely informational and can be answered from memory without agent-level orchestration

### Step 2: Check for Duplicates

Before creating, search the existing topology and agent registry for overlap.

1. Compare the proposed agent's scope to every active agent's scope. If overlap exceeds 50 percent, merge the scopes into the existing agent rather than creating a new one
2. Compare the proposed agent's capability profile to existing agents. If an existing agent matches on all High-priority dimensions, assign the task to that agent
3. Check Reusable Memory (see 30 Memory Layers) for previously successful agents with similar scope. If found, clone the existing spec and modify rather than creating from scratch

### Step 3: Specify the Agent

Using the Agent Primitives (see 10), fill in all required properties.

1. Choose a specialist pattern (see 14) if one fits. The pattern provides defaults for most properties
2. If no pattern fits, document why and specify all properties manually
3. Set the governance profile (see 08 Governance Model) appropriate to the agent's role
4. Define success criteria that are specific and verifiable at a Quality Gate (see 26)

### Step 4: Validate

Run the agent spec through the validation rules (see 10 Agent Primitives, Agent Validation).

1. If validation passes, the agent moves to Instantiated state (see 12 Agent Lifecycle)
2. If validation fails, return to Step 3 with the specific failures identified
3. Maximum 2 validation attempts. If the spec fails validation twice, escalate to the lead agent or human for review

## Creation Approval Flow

| Active Policy | Approval Required |
|---|---|
| Fastest acceptable | No approval. Auto-create if justified |
| Lowest cost | Lead agent approval. Must justify the token cost |
| Balanced | Lead agent approval |
| Highest rigor | Lead agent approval plus human notification |
| Human in loop required | Human approval required before instantiation |
| Autonomous unless blocked | No approval. Auto-create if justified |

## Agent Reuse Policy

Before creating a new agent, prefer these reuse strategies in order:

1. **Reassign**: Give the task to an existing idle agent with matching capabilities
2. **Extend**: Expand an existing agent's scope to include the new task (only if the expansion is modest)
3. **Clone**: Copy a proven agent spec from Reusable Memory and modify it for the current context
4. **Create**: Build a new agent from a specialist pattern or from scratch

## Creation Record

Every agent creation is recorded in the Provenance Graph (see 06) with:

1. The creation justification (answers to questions 1-3)
2. The duplicate check result
3. The specialist pattern used (if any)
4. The approval (who approved, or auto-approved under which policy)
5. Link to the agent's full spec
