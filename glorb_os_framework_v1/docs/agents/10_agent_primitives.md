# 10 Agent Primitives

## Purpose

Agent Primitives define the required and optional properties that every agent must declare before it can be instantiated. These properties form the agent's contract with the system and determine how it is governed, what it can do, and how it interacts with other agents.

## Every Agent Must Define

1. Identity
2. Mission
3. Scope
4. Authority
5. Capability profile
6. Tool permissions
7. Memory scope
8. Input contract
9. Output contract
10. Escalation rules
11. Stop conditions
12. Success criteria

## Property Definitions

### Required Properties

These must be specified for every agent. An agent cannot be instantiated without them.

#### 1 Identity

- Description: Unique name and role type
- Valid values: Name must be unique within the mission topology. Role type must reference a Specialist Pattern (see 14) or "general"
- Example: Name "alpha-architect", Role type "Architect"

#### 2 Mission

- Description: The specific objective this agent is responsible for achieving
- Valid values: A concrete, verifiable statement of what the agent should accomplish. Must not restate the overall mission objective without narrowing scope
- Example: "Design the module boundary structure and interface contracts for the authentication system"

#### 3 Scope

- Description: The boundaries of what this agent may and may not do
- Valid values: Explicit inclusion and exclusion list. What is in scope and what is explicitly out of scope
- Example: In scope: "Authentication module architecture." Out of scope: "Implementation, database schema, UI design"

#### 4 Authority

- Description: The governance authority level (see 08 Governance Model)
- Valid values: Execute, Decide, Direct. (Govern is reserved for human operator)
- Default: Execute

### Required Properties with Defaults

These must be present but have sensible defaults that can be used if not explicitly set.

#### 5 Capability Profile

- Description: Scores on the capability dimensions (see 11 Agent Capability Model)
- Valid values: Object with dimension names as keys and scores (Low, Medium, High) as values
- Default: All dimensions set to Medium
- Validation: At least one dimension must be High for specialist agents. General agents may use all-Medium

#### 6 Tool Permissions

- Description: Which tools this agent may use (see 08 Governance Model, Tool Scope)
- Valid values: None, Read, Write, Execute, Full
- Default: Read
- Validation: Must not exceed the parent agent's tool scope (see 08 Permission Inheritance)

#### 7 Memory Scope

- Description: Which memory layers this agent may access (see 08 Governance Model, Memory Scope)
- Valid values: Working only, Session, Mission, Project, Full
- Default: Session
- Validation: Must not exceed the parent agent's memory scope

#### 8 Input Contract

- Description: What this agent expects to receive before it begins work
- Valid values: List of named inputs with type (required or optional) and description
- Default: Single required input: "task_description" (string)
- Validation: At least one required input must be defined

#### 9 Output Contract

- Description: What this agent commits to producing when it completes
- Valid values: List of named outputs with type and description
- Default: Single output: "result" (string)
- Validation: At least one output must be defined. Outputs must be verifiable against success criteria

#### 10 Escalation Rules

- Description: Conditions that trigger escalation (see 08 Governance Model, Escalation Triggers)
- Valid values: List of conditions, each with a target (lead agent or human)
- Default: Escalate to lead agent when blocked. Escalate to human for irreversible actions

#### 11 Stop Conditions

- Description: Conditions that halt the agent immediately (see 08 Governance Model, Stop Conditions)
- Valid values: List of conditions
- Default: Stop on policy violation, FREEZE command, mission abort, task reassignment, resource exhaustion

#### 12 Success Criteria

- Description: How to determine if this agent accomplished its mission
- Valid values: List of verifiable criteria. Each criterion must be testable (pass/fail)
- Default: None. Must be explicitly defined for every agent
- Validation: At least one criterion must be defined. Criteria must be specific enough to evaluate at a Quality Gate (see 26)

## Optional Properties

These are not required but enhance agent behavior when present.

1. **Specialist pattern**: Reference to the pattern this agent instantiates (see 14). If set, the agent inherits defaults from the pattern
2. **Parent agent**: The agent that spawned this one. Governs permission inheritance
3. **Lifecycle state**: Current state (see 12 Agent Lifecycle). Set automatically by the system
4. **Performance history**: Evaluation scores from prior tasks (see 16 Agent Evaluation). Populated over time

## Agent Validation

An agent spec is rejected if:

1. Any required property without a default is missing
2. Identity name is not unique within the topology
3. Authority exceeds the parent agent's authority
4. Tool permissions exceed the parent agent's tool scope
5. Memory scope exceeds the parent agent's memory scope
6. No success criteria are defined
7. Output contract is empty
8. Scope is identical to the overall mission objective (agent must narrow scope)
