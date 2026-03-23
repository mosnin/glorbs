# 12 Agent Lifecycle

## Purpose

The Agent Lifecycle defines the states an agent can occupy, the valid transitions between states, what triggers each transition, and the behaviors available in each state.

## Lifecycle States

1. Drafted
2. Instantiated
3. Active
4. Specialized
5. Forked
6. Paused
7. Merged
8. Retired
9. Archived

## State Definitions

### 1 Drafted

The agent spec has been written but not yet validated or instantiated.

- Available actions: Edit spec, validate spec
- Cannot: Execute tasks, access tools, access memory
- Entry: Agent Creation Framework (see 13) produces a spec
- Exit: Spec passes validation (see 10 Agent Primitives, Agent Validation) -> Instantiated. Spec fails validation -> remains Drafted

### 2 Instantiated

The agent spec has been validated and the agent is ready to receive tasks.

- Available actions: Receive task assignments, read its own spec
- Cannot: Execute tasks, use tools (until activated)
- Entry: From Drafted after validation passes
- Exit: Receives first task assignment -> Active. Mission is aborted before activation -> Retired

### 3 Active

The agent is executing tasks within its scope.

- Available actions: All actions permitted by its governance profile (see 08). Execute tasks, use tools, access memory, send and receive messages (see 22), escalate
- Cannot: Modify its own spec, exceed its authority scope
- Entry: From Instantiated on task assignment. From Paused on RESUME command (see 09)
- Exit: Task requires specialization -> Specialized. FREEZE command -> Paused. Task completed and no more tasks pending -> Retired. Fork requested -> Forked. Merged with another agent -> Merged

### 4 Specialized

The agent has narrowed its scope to focus on a specific sub-problem discovered during execution.

- Available actions: Same as Active but restricted to the narrowed scope
- Cannot: Operate outside the specialized scope. Return to its original broader scope without recompilation
- Entry: From Active when the agent or lead determines a narrower focus is needed
- Exit: Specialized task complete -> Active (original scope) or Retired. FREEZE -> Paused

### 5 Forked

The agent has been duplicated to explore an alternative approach (typically via the FORK command, see 09).

- Available actions: Same as Active. Operates on the alternative approach
- Cannot: Modify the original agent's work. The fork is independent
- Entry: From Active when a FORK command is issued
- Exit: Fork produces the preferred result -> the fork becomes the primary, the original is Retired. Fork is abandoned -> Retired. Fork results are merged with the original -> Merged

### 6 Paused

The agent is halted and preserving state. No execution occurs.

- Available actions: None. State is preserved for resumption
- Cannot: Execute any task, use tools, send messages
- Entry: From Active or Specialized on FREEZE command. From Active when waiting for a blocking dependency
- Exit: RESUME command -> Active or Specialized (returns to pre-pause state). Mission abort -> Retired

### 7 Merged

The agent's output has been combined with another agent's output, and this agent is no longer needed as a separate entity.

- Available actions: None. The agent is consumed
- Cannot: Execute further tasks
- Entry: From Active or Forked when the Merge and Reduction process (see 24) combines this agent's output with another's
- Exit: -> Archived (automatic)

### 8 Retired

The agent has completed its work or been removed from the topology. It is no longer active but its record is preserved.

- Available actions: None
- Cannot: Execute tasks, be reactivated (a new agent must be created instead)
- Entry: From Active on task completion with no pending tasks. From any state on mission abort. From Active on REPLACE command (see 09). From Forked when the fork is abandoned
- Exit: -> Archived (automatic after mission completion)

### 9 Archived

The agent's full specification, performance history, and contribution record are stored for future reference.

- Available actions: May be queried for historical reference. May serve as a template for future agent creation
- Cannot: Be reactivated
- Entry: From Retired or Merged after mission completion
- Exit: None. Terminal state

## State Transition Diagram

```
Drafted -> Instantiated -> Active -> Specialized -> Active
                              |          |              |
                              v          v              v
                           Forked    Paused          Paused
                              |          |              |
                              v          v              v
                           Merged    Active          Active
                              |
                              v
                           Retired -> Archived
                              ^
                              |
               Active, Specialized, Forked, Paused, Instantiated
```

## Lifecycle Event Hooks

Each state transition emits an event that can trigger system actions.

| Transition | Event | System Action |
|---|---|---|
| Drafted -> Instantiated | agent.validated | Agent is added to the available pool |
| Instantiated -> Active | agent.activated | Provenance Graph records the activation |
| Active -> Paused | agent.paused | Dependent agents are notified of the pause |
| Paused -> Active | agent.resumed | Dependent agents are notified of resumption |
| Active -> Retired | agent.retired | Agent's pending tasks are reassigned or flagged. Memory is promoted per layer rules (see 30) |
| Any -> Archived | agent.archived | Agent record is moved to Archived Memory |
| Active -> Forked | agent.forked | Fork is recorded in Provenance Graph with link to original |
