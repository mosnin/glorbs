# 09 Human Command Layer

## Purpose

The Human Command Layer defines the commands available to the human operator for controlling, adjusting, and intervening in mission execution. Each command has a defined syntax, trigger behavior, and constraints on when it may be issued.

## Useful Human Commands

1. Freeze this branch
2. Fork this plan
3. Escalate this decision
4. Replace this agent
5. Recompile topology
6. Increase critique
7. Lower rigor
8. Lock assumptions
9. Compress context
10. Reduce cost mode

## Command Definitions

### 1 FREEZE

- Syntax: FREEZE [target]
- Target: A specific agent, a work stream, or the entire mission
- Effect: The target immediately halts. No further actions are taken. State is preserved. Other work streams continue unless the entire mission is frozen
- When to use: When the human detects a problem and needs to pause for inspection
- Restrictions: Can be issued at any time. No restrictions
- Undo: RESUME [target] restarts the frozen target from its preserved state

### 2 FORK

- Syntax: FORK [target] [variant description]
- Target: A plan, approach, or work stream
- Effect: Creates a parallel copy of the target with the specified variation. Both the original and the fork execute simultaneously. Results are compared at the next convergence point (see 24 Merge and Reduction)
- When to use: When the human wants to explore an alternative approach without abandoning the current one
- Restrictions: Cannot fork more than 3 active variants of the same target. Budget and agent count constraints still apply to each fork

### 3 ESCALATE

- Syntax: ESCALATE [decision or issue]
- Target: A specific decision point, disagreement, or quality concern
- Effect: The specified item is immediately surfaced to the human with full context including the Provenance Graph trail. Execution at that decision point pauses. Other work streams continue
- When to use: When the human wants to take direct control of a decision
- Restrictions: Can be issued at any time. No restrictions

### 4 REPLACE

- Syntax: REPLACE [agent] WITH [agent spec or pattern]
- Target: A specific agent in the active topology
- Effect: The target agent is retired. A new agent is created from the specified pattern (see 14 Specialist Agent Patterns) or spec. The new agent receives the retired agent's pending tasks and relevant context from Mission Memory (see 30). The replacement is recorded in the Provenance Graph
- When to use: When an agent is underperforming or the wrong pattern was selected
- Restrictions: Can only be issued between tasks, not mid-task. The retiring agent must complete or abandon its current task first

### 5 RECOMPILE

- Syntax: RECOMPILE [reason]
- Target: The active topology
- Effect: The Router (see 20) re-evaluates the mission with current state and the human's stated reason as additional input. A new topology is compiled. Active agents complete their current tasks. The new topology takes effect after current tasks finish
- When to use: When the current topology is not working and a structural change is needed
- Restrictions: Maximum 2 recompilations per mission without explicit human justification for a third

### 6 INCREASE CRITIQUE

- Syntax: INCREASE CRITIQUE [scope]
- Target: The entire mission or a specific work stream
- Effect: Critique intensity is raised by one level (see 03 Constraint Compiler, Critique Intensity). If already at maximum, a second Critic agent is added. The Disagreement Engine is enabled if not already active
- When to use: When the human suspects outputs are not being sufficiently challenged
- Restrictions: Can be issued at any time. Subject to agent count limits from the active Policy

### 7 LOWER RIGOR

- Syntax: LOWER RIGOR [scope]
- Target: The entire mission or a specific work stream
- Effect: Quality gates are relaxed. Required rigor drops by one level. Optional gates become skipped. Critique intensity drops by one level. This is a Policy override and is logged to the Provenance Graph
- When to use: When time pressure increases or the human determines the current rigor level is excessive for the task
- Restrictions: Cannot lower rigor below minimum for the mission's risk level. If Risk is High, Rigor cannot drop below Medium

### 8 LOCK ASSUMPTIONS

- Syntax: LOCK ASSUMPTIONS
- Target: All current assumptions in the Provenance Graph
- Effect: All assumptions currently marked in the Provenance Graph are frozen. Agents must treat them as facts for the remainder of the mission. No agent may challenge locked assumptions through the Disagreement Engine
- When to use: When the human has reviewed assumptions and wants to prevent further debate on settled questions
- Restrictions: Only the human operator can lock assumptions. Locked assumptions can be unlocked by issuing UNLOCK ASSUMPTIONS

### 9 COMPRESS CONTEXT

- Syntax: COMPRESS CONTEXT [scope]
- Target: A specific agent, a work stream, or the entire mission
- Effect: Triggers immediate memory compression (see 32 Memory Compression) on the target. Working memory is reduced to essentials. Session memory is summarized. Detailed history is moved to Mission Memory or Archived Memory
- When to use: When context pressure is limiting agent performance or when budget is tight
- Restrictions: Can be issued at any time. Cannot compress memory that is currently being read by an active agent

### 10 REDUCE COST

- Syntax: REDUCE COST
- Target: The entire mission
- Effect: Switches the active Policy to Lowest Cost (see 04 Policy Engine). Agent count is capped at 2. Critique intensity drops to self-review. Memory depth is reduced. This override is logged to the Provenance Graph
- When to use: When budget is running out or the mission scope has been reduced
- Restrictions: Cannot be applied if Risk is High and no Critic agent would remain. In that case, agent count is capped at 3 (including one Critic)

## Additional Control Commands

### OVERRIDE

- Syntax: OVERRIDE [gate or decision] WITH [directive]
- Effect: Force-passes a Quality Gate or overrides an automated decision. Logged to Provenance Graph with the human's rationale
- Restrictions: Can be issued at any time. The human accepts responsibility for consequences

### ABORT

- Syntax: ABORT [reason]
- Effect: Terminates the mission immediately. All agents are retired. Current work products are preserved in their current state. The mission is marked as aborted in the Provenance Graph with the human's reason
- Restrictions: Irreversible. Cannot be undone

### REDIRECT

- Syntax: REDIRECT [new objective or modified scope]
- Effect: Modifies the mission objective mid-execution. Triggers a re-evaluation by the Constraint Compiler (see 03) and potentially a topology recompilation (see 20 Router)
- Restrictions: Can only be issued at phase boundaries or quality gates, not mid-task

### RESUME

- Syntax: RESUME [target]
- Effect: Unfreezes a previously frozen target. Execution resumes from the preserved state
- Restrictions: Can only resume targets that were frozen by FREEZE

## Command Timing

| Command | Mid-task | Between tasks | At gates | Any time |
|---|---|---|---|---|
| FREEZE | Yes | Yes | Yes | Yes |
| FORK | No | Yes | Yes | No |
| ESCALATE | Yes | Yes | Yes | Yes |
| REPLACE | No | Yes | Yes | No |
| RECOMPILE | No | Yes | Yes | No |
| INCREASE CRITIQUE | Yes | Yes | Yes | Yes |
| LOWER RIGOR | Yes | Yes | Yes | Yes |
| LOCK ASSUMPTIONS | Yes | Yes | Yes | Yes |
| COMPRESS CONTEXT | No | Yes | Yes | No |
| REDUCE COST | No | Yes | Yes | No |
| OVERRIDE | No | No | Yes | No |
| ABORT | Yes | Yes | Yes | Yes |
| REDIRECT | No | No | Yes | No |
| RESUME | Yes | Yes | Yes | Yes |
