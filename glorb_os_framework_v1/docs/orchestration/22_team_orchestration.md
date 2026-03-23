# 22 Team Orchestration

## Purpose

Team Orchestration governs how agents within a topology communicate, hand off work, coordinate parallel execution, and resolve coordination failures. It defines the protocols that turn a collection of agents into a functioning team.

## Common Team Patterns

1. Leader and specialists
2. Planner and executor
3. Architect and builders
4. Researcher and synthesizer
5. Critic loop

## Communication Protocol

### Message Format

Every inter-agent message follows this structure:

1. From: Sending agent name and role
2. To: Receiving agent name and role
3. Type: One of handoff, request, response, feedback, escalation, status
4. Content: The substantive payload
5. Context: Relevant mission state, constraints, or prior decisions the receiver needs
6. Priority: Normal, urgent, or blocking
7. References: List of provenance IDs for prior work this message builds on

### Message Types

1. **Handoff**: Transfers ownership of a work product from one agent to another. Includes the artifact, completion status, and any open issues. The receiving agent acknowledges receipt before the sender is released
2. **Request**: Asks another agent to perform a specific action. Includes the ask, acceptance criteria, and deadline if applicable. Remains open until a Response is received
3. **Response**: Answers a Request. Includes the result, status (complete, partial, blocked), and any follow-up needs
4. **Feedback**: Delivers evaluation results from a Critic, Reviewer, or Quality Gate. Includes findings ranked by severity and suggested actions
5. **Escalation**: Signals that the sending agent cannot proceed. Includes the blocker description, what was attempted, and recommended next steps. Routes to the lead agent or human depending on severity
6. **Status**: Periodic progress update. Includes percentage complete, current phase, and any emerging risks

## Handoff Protocol

When one agent passes work to another:

1. The sending agent produces a Handoff message with the complete work product
2. The sending agent marks all open issues and assumptions in the handoff
3. The receiving agent validates it has sufficient context to proceed (mirrors the Sufficient Context Gate criteria, see 26)
4. If context is insufficient, the receiving agent sends a Request back to the sender for clarification
5. Once the receiving agent acknowledges, the handoff is recorded in the Provenance Graph (see 06)
6. The sending agent transitions to idle or proceeds to its next task

## Parallel Execution Rules

When multiple agents work concurrently:

1. **Independence check**: Before launching parallel agents, the lead verifies that their tasks have no blocking dependencies on each other
2. **Shared resource locking**: If two agents need to modify the same artifact, they execute sequentially on that artifact. The lead assigns an ordering
3. **Progress synchronization**: Parallel agents emit Status messages at defined intervals. The lead monitors for drift or blockers
4. **Convergence point**: Parallel work streams converge at a Synthesizer agent or merge step (see 24 Merge and Reduction). No downstream work begins until all parallel streams complete or are explicitly abandoned
5. **Partial failure**: If one parallel agent fails while others succeed, the lead evaluates whether the successful outputs are usable independently. If yes, proceed with partial results and log the gap. If no, trigger recovery (see 07)

## Sequential Execution Rules

When agents work in a pipeline:

1. Each agent waits for a Handoff from the previous agent before starting
2. Each agent validates the handoff before beginning work
3. If an agent produces output that invalidates a prior agent's work (e.g., the Critic rejects the Architect's design), the pipeline rewinds to the invalidated agent
4. Pipeline rewinds are limited to 2 per phase. After 2 rewinds, the lead escalates to human review

## Deadlock Detection

A deadlock occurs when two or more agents are each waiting for the other to act. The lead agent monitors for deadlock using these signals:

1. Two agents each have an open Request to the other with no Response for more than one execution cycle
2. An agent has been in a waiting state for more than 2 execution cycles without progress
3. A circular dependency is detected in the task graph

When deadlock is detected:

1. The lead agent identifies the cycle and logs it
2. The lead breaks the deadlock by assigning priority: the agent with the simpler unblocking action goes first
3. If the deadlock cannot be broken by reordering, the lead escalates to human review
4. The deadlock and its resolution are recorded in the Provenance Graph

## Load Balancing

When multiple agents of the same role type are available (e.g., multiple Builders):

1. Tasks are assigned to the agent with the fewest active tasks
2. If agents are equally loaded, assign based on capability match to the specific task
3. If an agent falls behind, the lead may reassign its pending tasks to a less-loaded agent
4. Agent reassignment requires a fresh Handoff; the new agent does not inherit in-progress state

## Team Lifecycle

1. **Formation**: The Topology Compiler (see 21) defines the team structure. Agents are spawned or assigned
2. **Execution**: Agents communicate via the protocols above. The lead coordinates
3. **Convergence**: All work streams complete. Outputs are merged (see 24)
4. **Review**: The merged output passes through Quality Gates (see 26)
5. **Dissolution**: On mission completion, agents are released. Session memory is preserved, working memory is discarded

## Async Orchestration Mode

An alternative orchestration protocol for stateless or async runtimes. When the standard synchronous protocols are not viable, async mode provides equivalent coordination guarantees through fire-and-collect patterns mediated by a stateful lead agent.

### When Async Mode Is Used

1. The runtime's Capability Manifest (see 57) has session_model: stateless
2. The runtime's Capability Manifest has async_messaging: true but interruptible: false
3. The Topology Compiler explicitly enables async mode for specific agents in a multi-runtime topology

### Async Message Protocol

Replace synchronous acknowledgment with fire-and-collect:

1. The lead agent (on a stateful runtime) dispatches tasks to stateless agents as independent calls via spawn_agent() (see 56)
2. Each stateless agent receives its full context in the spawn call (no incremental messages)
3. The stateless agent executes and returns its complete result. There is no mid-task communication
4. The lead agent collects results. No acknowledgment handshake is needed
5. If a stateless agent's result is not received within the timeout, the lead agent treats it as a failure and applies recovery (see 07)

### Async Handoff Protocol

Replace the synchronous 6-step handoff with:

1. The sending agent completes its work and writes the output to Mission Memory via persist_memory() (see 56)
2. The lead agent (stateful) detects the completion and dispatches the next agent with the stored output as input context
3. No direct agent-to-agent communication. The lead agent mediates all handoffs
4. This works even when the sending and receiving agents are on different runtimes

### Async Parallel Execution

1. The lead agent dispatches all parallel tasks simultaneously via multiple spawn_agent() calls
2. Results are collected as they arrive (no status polling)
3. The convergence point waits for all results or times out
4. Partial results from timed-out agents are handled per Partial Failure rules (see 07)

### Async Limitations

1. No mid-task intervention (FREEZE/RESUME have no effect on in-flight stateless agents)
2. No incremental status updates (agents complete fully or time out)
3. Critique loops require sequential round-trips (spawn critic, collect result, spawn revision, collect result)
4. Higher latency for sequential topologies (each handoff goes through the lead agent)

### Mixed-Mode Orchestration

In multi-runtime topologies, some agents may be synchronous and others async:

1. The lead agent tracks each agent's mode (sync or async) based on its runtime's manifest
2. Sync agents use the standard handoff protocol
3. Async agents use the async handoff protocol
4. The lead agent translates between modes at handoff boundaries
