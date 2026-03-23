# 61 Observability

## Purpose

Observability defines how a running GLORB mission can be monitored, inspected, and debugged. It specifies what metrics are collected, what events are logged, what views are available, and how alerts are triggered.

## Observability Layers

### 1 Mission-level Observability

What is happening across the entire mission.

**Metrics:**

1. Mission progress: Percentage of Mission Graph objectives completed (see 27)
2. Agent count: Active, paused, retired
3. Phase: Current execution phase (planning, execution, review, synthesis)
4. Time elapsed since mission start
5. Estimated completion: Based on critical path analysis from Mission Graph
6. Error count: By severity (blocking, recoverable, warning)
7. Quality gate pass rate: Gates passed on first attempt vs total gate evaluations

**Views:**

1. **Mission dashboard**: Progress percentage, current phase, active agents, active blockers
2. **Critical path view**: The longest dependency chain with completion status per node
3. **Error summary**: Count and list of errors by category, with resolution status

### 2 Agent-level Observability

What each agent is doing.

**Metrics:**

1. Agent state: Current lifecycle state (see 12)
2. Tasks completed vs tasks assigned
3. Current task: Description, start time, estimated completion
4. Tool calls: Count, types, success/failure rate
5. Escalation count: How many times this agent has escalated
6. Evaluation scores: Latest per-dimension scores (see 16)

**Views:**

1. **Agent roster**: All agents with state, current task, and health indicator
2. **Agent detail**: Full history of an individual agent's actions, outputs, and evaluations

### 3 Orchestration-level Observability

How agents interact and coordinate.

**Metrics:**

1. Messages sent: Count by type (handoff, request, response, feedback, escalation, status)
2. Handoff latency: Time between an agent completing work and the next agent starting
3. Parallel utilization: Number of agents working simultaneously vs total active agents
4. Deadlock events: Count, duration, resolution method
5. Merge attempts: Count, success rate, average conflict count per merge

**Views:**

1. **Topology map**: Visual representation of the active topology showing agents, connections, and data flow
2. **Message log**: Chronological list of inter-agent messages with sender, receiver, type, and timestamp

### 4 Memory-level Observability

How memory is being used across the system.

**Metrics:**

1. Layer utilization: Size of content per memory layer
2. Retrieval count: Queries per layer
3. Compression events: Count and compression ratio achieved
4. Conflict count: Detected conflicts and their resolution status
5. Promotion/demotion events: Count of items moving between layers

**Views:**

1. **Memory map**: Per-layer size, item count, and most recent access
2. **Conflict log**: Active and resolved memory conflicts

### 5 Runtime-level Observability

How the runtime is performing.

**Metrics:**

1. Token usage: Consumed vs budget (if budget is set)
2. API call count: Total and per agent
3. Latency: Average response time per call
4. Error rate: Runtime errors as percentage of total calls
5. Runtime: Which runtime each agent is using

**Views:**

1. **Resource dashboard**: Token usage, API calls, costs, and runtime health
2. **Runtime comparison**: If multi-runtime, performance comparison across runtimes

## Event Log

All significant events are logged with a common schema:

1. Timestamp
2. Event type (from Provenance Graph event types, see 06, plus operational events below)
3. Source (agent, component, or runtime that produced the event)
4. Severity (info, warning, error)
5. Description
6. Related Provenance Graph record ID (if applicable)

### Operational Event Types (beyond Provenance)

1. **agent.spawned**: A new agent was created
2. **agent.state_change**: An agent changed lifecycle state
3. **tool.called**: A tool was invoked (with tool name and success/failure)
4. **memory.retrieved**: A memory retrieval query was executed
5. **memory.compressed**: Memory compression was performed
6. **runtime.fallback**: A runtime switch occurred
7. **budget.threshold**: Token budget crossed a threshold (50%, 75%, 90%)
8. **human.command**: A human command was received (with command type)

## Alerting

Alerts notify the human operator of conditions that require attention.

| Alert | Trigger | Priority |
|---|---|---|
| Mission blocked | A blocking error with no automated recovery path | High |
| Cascading failure | 3+ distinct failure classes in one mission | High |
| Budget warning | Token usage exceeds 75% of budget | Medium |
| Budget critical | Token usage exceeds 90% of budget | High |
| Agent stuck | An agent has been in_progress on a single task for more than 3x the average task duration | Medium |
| Deadlock detected | Mutual blocking between agents (see 22) | Medium |
| Repeated gate failure | Same Quality Gate fails 2+ times | Medium |
| Governance violation | An agent attempted an unauthorized action | High |

## Observability by Runtime

### Runtime-Specific Observability

#### Claude Code
- Metrics: Tracked via TodoWrite and inline status updates in the conversation
- Event log: The conversation itself serves as the event log
- Views: The human reads the conversation. Structured summaries can be written to files
- Alerts: Surfaced as direct messages to the human in the conversation
- Persistence: Written to files in the repository for cross-session visibility

#### OpenClaw
- Metrics: Collected from API call metadata (token counts, latency, error rates)
- Event log: Structured log derived from API request/response pairs. Adapter formats and stores
- Views: Adapter provides a dashboard endpoint or writes structured reports to external storage
- Alerts: Adapter sends notifications via configured channels (webhooks, email, messaging)
- Persistence: External logging service (e.g., structured log store, time-series database)

#### NanoClaw
- Metrics: Limited to per-call metadata (input tokens, output tokens, latency, success/failure)
- Event log: Call input/output pairs logged by the adapter (NanoClaw itself has no logging)
- Views: Aggregated by the lead agent's runtime. NanoClaw provides raw data only
- Alerts: Not applicable within NanoClaw. The lead agent's runtime handles alerting based on NanoClaw call results
- Persistence: The adapter logs call metadata. The lead agent persists meaningful results

## Adapter Observability Interface

### Abstract Observability Methods
All observability operations go through the runtime adapter (see 56 Adapter Interface Spec):

1. **log_event(event)**: Every operational event and provenance record is logged through the adapter. The adapter translates to its native logging mechanism. Events follow the schema defined in the Event Log section above.

2. **get_metrics(scope)**: Retrieves current metrics for a scope (mission, agent, runtime). The adapter collects metrics from its native monitoring and translates to the standard metric schema.

### Cross-Runtime Observability
In multi-runtime missions:
1. Each adapter logs events in the common schema (see Event Log above)
2. The lead agent's runtime aggregates events from all adapters into a unified view
3. Provenance records reference events across runtimes using the common record ID format (see 06)
4. Metrics are collected per-runtime and summed at the mission level for the mission dashboard
