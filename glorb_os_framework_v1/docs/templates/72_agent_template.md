# 72 Agent Template

## Name
*Unique identifier for this agent within the mission topology. Must be unique across all agents in the current mission. Example: "alpha-architect"*

## Role Type
*The specialist pattern this agent instantiates, or "general" for a non-specialist. Valid values: Strategist, Architect, Builder, Researcher, Critic, Router, Synthesizer, Verifier, Reviewer, Memory Manager, general. Example: "Architect"*

## Purpose
*The specific objective this agent is responsible for achieving within the mission. Must narrow scope to this agent's mandate, not restate the overall mission objective. Example: "Design the module boundary structure and interface contracts for the authentication system"*

## Scope
*Explicit inclusion and exclusion list defining what this agent may and may not do. Both in-scope and out-of-scope boundaries are required. Example: "In scope: authentication module architecture. Out of scope: implementation, database schema, UI design"*

## Capability Profile
*Scores on the capability dimensions from doc 11. Each dimension is scored Low, Medium, or High. At least one dimension must be High for specialist agents. Example: "Abstraction depth: High, Precision: High, Creativity: Medium, Risk tolerance: Low, Adversarial rigor: Medium, Synthesis strength: Medium, Tool access: Low, Autonomy level: Medium, Memory breadth: Medium, Domain expertise: High"*

## Tool Permissions
*Which tools this agent may use. Valid values: None, Read, Write, Execute, Full. Must not exceed the parent agent's tool scope. Example: "Read"*

## Memory Scope
*Which memory layers this agent may access. Valid values: Working only, Session, Mission, Project, Full. Must not exceed the parent agent's memory scope. Example: "Session"*

## Input Contract
*List of named inputs this agent expects before it begins work, with type (required or optional) and description. At least one required input must be defined. Example: "task_description (required, string): The design requirements and constraints to work from"*

## Output Contract
*List of named outputs this agent commits to producing on completion, with type and description. At least one output must be defined and must be verifiable against success criteria. Example: "architecture_doc (required, document): Component specs, interface contracts, and dependency map"*

## Escalation Rules
*Conditions that trigger escalation, each with the target (lead agent or human). Default: escalate to lead agent when blocked; escalate to human for irreversible actions. Example: "Blocked on ambiguous requirements -> escalate to lead agent. Proposed change is irreversible -> escalate to human"*

## Stop Conditions
*Conditions that halt this agent immediately. Default: policy violation, FREEZE command, mission abort, task reassignment, resource exhaustion. Example: "Scope violation detected, FREEZE command received, budget exhausted"*

## Success Criteria
*Verifiable pass/fail conditions that determine if this agent accomplished its mission. Each criterion must be testable at a Quality Gate. At least one criterion required. Example: "All component interfaces defined with input/output contracts. No circular dependencies in the dependency map. Design satisfies stated constraints."*
