# 21 Topology Compiler

## Purpose

The Topology Compiler translates a Router decision (see 20) into a concrete team structure with named agents, defined relationships, and an execution plan. It selects a topology pattern, scales it to the mission, validates it against constraints, and produces a ready-to-execute team specification.

## Example Topologies

1. Solo agent
2. Planner and executor
3. Architect plus builders plus critic
4. Researcher swarm plus reducer
5. Strategist plus operator plus verifier

## Topology Selection Criteria

### Mission Type to Topology Mapping

| Mission Type (see 42) | Primary Topology | When to Upgrade |
|---|---|---|
| Exploration | Researcher swarm plus reducer | High ambiguity: add Strategist to frame research |
| Decision | Solo agent (Strategist) | High risk: upgrade to Strategist plus Critic |
| Design | Architect plus builders plus critic | High complexity: add Strategist before Architect |
| Build | Planner and executor | Complex build: upgrade to Architect plus builders plus critic |
| Audit | Solo agent (Verifier) | High rigor: add Critic plus Reviewer |
| Negotiation | Strategist plus operator plus verifier | High stakes: add Critic and Disagreement Engine |
| Synthesis | Researcher swarm plus reducer | Multiple domains: add domain-specific Researchers |
| Execution | Planner and executor | Parallel tasks: add multiple executors |

### Scaling Rules

How to size the topology based on mission characteristics.

| Factor | Effect on Topology |
|---|---|
| Complexity High | Add one level of decomposition. E.g., solo -> planner-executor, planner-executor -> architect-builders |
| Ambiguity High | Add a Researcher agent to gather information before the primary workflow begins |
| Risk High | Add a Critic agent. If not already present, add a Verifier |
| Rigor High | Add a Reviewer as the final agent before deliverable compilation |
| Budget Low | Remove optional agents (Reviewer, second Critic). Merge roles where possible |
| Deadline High | Prefer parallel execution. Add builders that can work simultaneously |

### Agent Count by Topology

| Topology | Minimum Agents | Typical | Maximum |
|---|---|---|---|
| Solo agent | 1 | 1 | 1 |
| Planner and executor | 2 | 2-3 | 4 (planner + 3 parallel executors) |
| Architect plus builders plus critic | 3 | 4-5 | 7 (architect + 4 builders + critic + reviewer) |
| Researcher swarm plus reducer | 3 | 4-6 | 8 (6 researchers + synthesizer + reviewer) |
| Strategist plus operator plus verifier | 3 | 3-4 | 5 (strategist + 2 operators + verifier + reviewer) |

## Topology Compilation Process

### Step 1: Select Pattern

1. Start with the Router's output topology (see 20 Routing Decision Matrix)
2. Cross-reference with the Mission Type mapping above
3. If they agree, use that topology. If they disagree, prefer the Router's output (it accounts for more inputs)

### Step 2: Assign Agents

1. For each role in the topology, select or create an agent
2. Prefer existing agents from the available pool (see 13 Agent Reuse Policy)
3. If no suitable agent exists, invoke the Agent Foundry (see 15)
4. Assign governance profiles (see 08 Governance Model) based on role: Lead profile for the coordinating agent, Specialist or Worker for others

### Step 3: Define Relationships

1. Designate the lead agent (the coordinating agent that receives the mission and distributes tasks)
2. Define reporting lines: which agents report to which
3. Define handoff sequences: the order in which agents pass work (see 22 Team Orchestration)
4. Identify parallel branches: which agents can work simultaneously
5. Identify convergence points: where parallel work merges (see 24 Merge and Reduction)

### Step 3.5: Runtime Capability Validation

1. Retrieve the target runtime's Capability Manifest (see 57) via the adapter's query_capabilities() method (see 56 Adapter Interface Spec)
2. Check subagent nesting: if the topology has agents spawning subagents, verify nesting depth does not exceed the manifest's subagent_nesting_depth. If it does, flatten the topology by promoting subagents to peer agents coordinated by the lead
3. Check concurrency: if the topology has parallel branches, verify the number of simultaneous agents does not exceed max_concurrent_agents. If it does, serialize some branches
4. Check memory: verify the topology's memory requirements (per policy, see 04) are satisfied by memory_layers_supported. If mission memory is required but not supported, use adapter-mediated external storage or downgrade memory policy
5. Check tools: verify each agent's tool permissions (see 10) map to tools available on the runtime (via list_available_tools(), see 56). If a required tool is missing, flag the agent for runtime reassignment or capability downgrade
6. Check session model: if the topology requires stateful agents (checkpoint autonomy, pause/resume) but the runtime is stateless, enable async orchestration mode (see 22) for those agents

### Step 4: Validate

1. Check that total agent count does not exceed the Policy limit (see 04)
2. Check that all mission-required capability dimensions are covered (see 11 Agent Capability Model, see 15 Gap Detection)
3. Check that the topology has no circular dependencies
4. Check that every agent has at least one defined input source and one defined output target
5. Check that governance constraints are satisfied (no agent exceeds its parent's permissions, see 08)

### Step 5: Produce Execution Plan

1. Generate the ordered execution sequence respecting dependencies
2. Mark Quality Gate positions in the sequence (see 26)
3. Estimate the number of execution phases (sequential handoffs)
4. Record the topology in the Provenance Graph (see 06)

## Operational Prompts

For the step-by-step prompt that guides an orchestrator through this compilation process, see 84 Topology Compilation Prompt. The compiled topology is recorded using template 74 Team Topology Template.

## Dynamic Topology Modification

The topology can be modified during execution under these conditions:

1. **Agent addition**: The Foundry detects a capability gap mid-execution. A new agent is created and inserted. Requires lead agent approval
2. **Agent removal**: An agent is underperforming or redundant. The lead retires the agent and reassigns tasks. Requires that no other agent has a blocking dependency on the removed agent
3. **Role change**: An agent's scope is expanded or narrowed. This is a lightweight modification that does not require full recompilation
4. **Full recompilation**: Triggered by RECOMPILE command (see 09) or by a failure recovery strategy (see 07). The Router runs a new pass and a new topology is compiled from scratch. Active agents complete their current tasks first

## Topology Validation Errors

| Error | Resolution |
|---|---|
| Agent count exceeds policy limit | Remove the lowest-priority optional agent (Reviewer first, then second Critic) |
| Capability gap detected | Invoke the Agent Foundry to create or extend an agent |
| Circular dependency | Restructure the handoff sequence. Insert a Synthesizer to break the cycle |
| Agent with no input source | Assign the lead agent as the input source |
| Agent with no output target | Assign the lead agent as the output target or connect to the Deliverable Compiler |
| Governance violation | Reduce the violating agent's permissions to comply with inheritance rules |
| Subagent nesting exceeds runtime limit | Flatten topology: promote subagents to peers |
| Concurrent agent count exceeds runtime limit | Serialize parallel branches |
| Required memory layer not supported | Use adapter-mediated external storage |
| Required tool not available on runtime | Reassign agent to compatible runtime or remove tool dependency |
| Stateful topology on stateless runtime | Enable async orchestration mode |
