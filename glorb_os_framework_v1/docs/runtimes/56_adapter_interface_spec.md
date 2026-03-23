# 56 Adapter Interface Spec

## Purpose

This document defines the formal interface that every runtime adapter must implement to plug into GLORB. Doc 51 lists the categories an adapter must address. This spec provides the concrete method signatures, data structures, and compliance levels that make that contract enforceable.

## Data Structures

### AgentStatus

- agent_id: string — runtime-specific agent identifier
- lifecycle_state: string — one of: initializing, running, paused, completed, failed
- current_task: string — description of the agent's current task
- progress_percentage: integer — 0 to 100, best-effort estimate
- last_active_timestamp: string — ISO 8601 timestamp of last activity

### CapabilityManifest

Defined in 57 Runtime Capability Manifest. Returned by `query_capabilities()`.

### DeliveryStatus

One of: delivered, queued, failed.

### MemoryResult

- content: string — the retrieved memory content
- layer: string — one of: working, session, mission, project, reusable, archived
- relevance_score: float — 0.0 to 1.0, ranking relevance to the query
- recency: string — ISO 8601 timestamp of when the content was persisted
- provenance_id: string — reference to the Provenance Graph record (see 06)

## Core Methods — Agent Lifecycle

### query_capabilities

- Parameters: none
- Returns: CapabilityManifest
- Description: Returns the runtime's capability manifest (see 57). Used to determine what the runtime supports before assigning agents or validating topologies.
- Called by: Topology Compiler, Runtime Selection Policy (see 55)

### spawn_agent

- Parameters:
  - agent_spec: object — a GLORB portable agent spec (see 10)
  - context: object — subset of mission and memory context the agent needs
- Returns: agent_id (string)
- Description: Creates an agent from a GLORB agent spec. The adapter translates the spec into runtime-native constructs. Returns a runtime-specific agent identifier used for all subsequent operations on this agent.
- Called by: lead agent, Topology Compiler (see 21)

### retire_agent

- Parameters:
  - agent_id: string
- Returns: status (object) — final agent state including outputs and lifecycle_state
- Description: Retires an agent and returns its final state. The adapter cleans up runtime resources. Any pending outputs are flushed.
- Called by: lead agent on task completion or REPLACE command (see 09)

### get_agent_status

- Parameters:
  - agent_id: string
- Returns: AgentStatus
- Description: Returns the current lifecycle state, current task, and progress. Stateless runtimes return status from the last completed call.
- Called by: lead agent for monitoring

### pause_agent

- Parameters:
  - agent_id: string
- Returns: state_snapshot (object) — serialized agent state sufficient for later resumption
- Description: Pauses an agent and returns a state snapshot. Stateless runtimes treat this as a no-op and return current context as the snapshot.
- Called by: FREEZE command (see 09)

### resume_agent

- Parameters:
  - agent_id: string
  - state_snapshot: object — snapshot returned by a prior pause_agent call
- Returns: status (string) — confirmation of resumed state
- Description: Resumes a previously paused agent from a state snapshot. Stateless runtimes treat this as equivalent to spawn_agent with the prior context.
- Called by: RESUME command (see 09)

## Communication Methods

### send_message

- Parameters:
  - from_agent: string — agent_id of the sender
  - to_agent: string — agent_id of the recipient
  - message: object — message payload following the Team Orchestration message format (see 22)
- Returns: DeliveryStatus
- Description: Sends an inter-agent message. Async runtimes may queue the message and return `queued`. The adapter handles cross-runtime routing when sender and recipient are on different runtimes.
- Called by: any agent

### receive_messages

- Parameters:
  - agent_id: string — agent_id of the recipient
  - filter: object — optional filter criteria (sender, message_type, timestamp range)
- Returns: messages (list of objects)
- Description: Retrieves pending messages for an agent. Stateless runtimes require the adapter to buffer messages externally and return them on the next call. Messages are returned in chronological order.
- Called by: agent at start of execution cycle

## Memory Methods

### retrieve_memory

- Parameters:
  - layer: string — one of: working, session, mission, project, reusable, archived
  - query: string — search query or key
  - scope: object — access boundaries per governance (agent permissions, data classification)
  - max_results: integer — maximum number of results to return
- Returns: results (list of MemoryResult)
- Description: Queries a memory layer and returns ranked results. The adapter translates GLORB memory requests to runtime-native storage. Scope enforcement is mandatory.
- Called by: any agent via Retrieval Policy (see 34)

### persist_memory

- Parameters:
  - layer: string — one of: working, session, mission, project, reusable, archived
  - key: string — storage key or identifier
  - content: string — the content to persist
  - metadata: object — includes provenance_record_id, agent_id, timestamp
- Returns: status (string) — confirmation of persistence
- Description: Writes content to a memory layer. Metadata must include provenance information for auditability.
- Called by: Memory Manager or lead agent

### compress_memory

- Parameters:
  - layer: string — the memory layer to compress
  - strategy: string — named compression strategy (see 32)
  - scope: object — defines which content within the layer to compress
- Returns: status (string) — confirmation of compression
- Description: Triggers compression on a memory layer using the named strategy. The adapter translates to runtime-native summarization or pruning.
- Called by: COMPRESS CONTEXT command (see 09) or automatic triggers

## Tool Methods

### execute_tool

- Parameters:
  - agent_id: string — the agent requesting tool execution
  - tool_name: string — GLORB-canonical tool name
  - params: object — tool-specific parameters
- Returns: result (object) — tool execution result including output and status
- Description: Executes a tool on behalf of an agent. The adapter translates GLORB tool requests to runtime-native tool calls. Tool permissions are checked before execution.
- Called by: any agent with tool permissions

### list_available_tools

- Parameters: none
- Returns: tool_list (list of objects) — each entry includes tool_name, category, and parameter schema
- Description: Returns the tools available on this runtime. Used during topology validation to verify that required tools are available.
- Called by: Topology Compiler during validation

## Observability Methods

### log_event

- Parameters:
  - event: object — provenance or operational event following the record schema (see 06, 61)
- Returns: status (string)
- Description: Logs a provenance or operational event. The adapter translates to runtime-native logging while maintaining compatibility with the GLORB Provenance Graph.
- Called by: all components

### get_metrics

- Parameters:
  - scope: string — one of: mission, agent, runtime
- Returns: metrics (object) — current metrics for the specified scope
- Description: Returns operational metrics including token usage, call counts, latency, and error rates. Scope determines granularity.
- Called by: observability layer

## Approval Methods

### request_approval

- Parameters:
  - agent_id: string — the agent requesting approval
  - action_description: string — human-readable description of the proposed action
  - context: object — relevant context for the approver (governance rule triggered, risk level)
- Returns: string — one of: approved, denied, timeout
- Description: Requests human approval for a sensitive action. Runtimes with built-in approval use their native mechanism. Runtimes without built-in approval must implement an external approval mechanism or auto-approve with logging.
- Called by: governance enforcement

## Adapter Compliance Levels

### Level 1 — Minimal

Required methods:

1. query_capabilities
2. spawn_agent
3. retire_agent
4. execute_tool
5. list_available_tools
6. log_event

Supports solo agent topologies only. Sufficient for single-purpose, isolated tasks.

### Level 2 — Standard

Required methods: all Level 1 methods plus:

1. send_message
2. receive_messages
3. retrieve_memory
4. persist_memory
5. get_agent_status
6. get_metrics

Supports team topologies. Enables multi-agent coordination, memory sharing, and monitoring.

### Level 3 — Full

Required methods: all Level 2 methods plus:

1. pause_agent
2. resume_agent
3. compress_memory
4. request_approval

Supports all GLORB features including human commands (see 09), full governance enforcement, and agent lifecycle control.

## Existing Adapter Compliance

| Adapter | Compliance Level | Notes |
|---|---|---|
| Claude Code (see 52) | Level 3 | Full support. Built-in approval model, file-based memory persistence, context compression via native summarization |
| OpenClaw (see 53) | Level 2 | Standard support. Adapter must implement approval gates externally. Upgradeable to Level 3 by adding approval hooks and compression logic |
| NanoClaw (see 54) | Level 1 | Minimal support. Stateless, no inter-agent messaging, no memory persistence. Suited for solo agent topologies within multi-runtime missions |
