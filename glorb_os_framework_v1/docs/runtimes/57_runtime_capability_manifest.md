# 57 Runtime Capability Manifest

## Purpose

The capability manifest is a structured declaration returned by every adapter's `query_capabilities()` method (see 56 Adapter Interface Spec). It tells the GLORB control plane exactly what a runtime can and cannot do. The Topology Compiler, Runtime Selection Policy (see 55), and capability negotiation logic all consume this manifest to make routing and validation decisions.

Without manifests, runtime selection relies on hardcoded assumptions. With manifests, GLORB can dynamically validate topologies, select runtimes, and negotiate capability gaps at mission compile time.

## Manifest Schema

| Field | Type | Description |
|---|---|---|
| runtime_name | string | Unique identifier for the runtime (e.g., claude_code, openclaw, nanoclaw) |
| compliance_level | integer (1, 2, or 3) | Adapter compliance level per 56 Adapter Interface Spec |
| session_model | string | One of: stateful, stateless |
| subagent_nesting_depth | integer | Maximum nesting depth. 0 = no subagents, 1 = single level, N = multi-level |
| max_concurrent_agents | integer | Maximum agents that can run simultaneously on this runtime |
| memory_layers_supported | list of strings | Which GLORB memory layers this runtime supports (working, session, mission, project, reusable, archived) |
| memory_persistence | string | One of: none, session, mission, permanent |
| interruptible | boolean | Whether agents can be paused mid-task via pause_agent |
| approval_model | string | One of: built_in, adapter_provided, none |
| tool_categories | list of strings | Available tool categories: file_read, file_write, code_execution, web_search, web_fetch, structured_output |
| max_context_size | integer | Maximum context size in tokens or equivalent |
| parallel_execution | boolean | Whether multiple agents can execute in parallel |
| async_messaging | boolean | Whether the runtime supports queued message delivery between agents |
| human_command_support | list of strings | Supported commands from 09 Human Commands |
| cost_model | string | One of: per_token, per_call, per_session, flat |
| sandbox_model | string | One of: none, os_level, container, full_isolation |

## Claude Code Manifest

| Field | Value |
|---|---|
| runtime_name | claude_code |
| compliance_level | 3 |
| session_model | stateful |
| subagent_nesting_depth | 1 |
| max_concurrent_agents | 8 |
| memory_layers_supported | working, session, mission, project, reusable, archived |
| memory_persistence | permanent |
| interruptible | true |
| approval_model | built_in |
| tool_categories | file_read, file_write, code_execution, web_search, web_fetch, structured_output |
| max_context_size | 200000 |
| parallel_execution | true |
| async_messaging | false |
| human_command_support | PAUSE, RESUME, REDIRECT, REPLACE, FREEZE, EXPAND SCOPE, REDUCE SCOPE, COMPRESS CONTEXT, STATUS |
| cost_model | per_token |
| sandbox_model | os_level |

## OpenClaw Manifest

| Field | Value |
|---|---|
| runtime_name | openclaw |
| compliance_level | 2 |
| session_model | stateless |
| subagent_nesting_depth | 10 |
| max_concurrent_agents | 20 |
| memory_layers_supported | working, session, mission, project, reusable, archived |
| memory_persistence | mission |
| interruptible | false |
| approval_model | adapter_provided |
| tool_categories | file_read, file_write, code_execution, web_search, web_fetch, structured_output |
| max_context_size | 128000 |
| parallel_execution | true |
| async_messaging | true |
| human_command_support | PAUSE, REDIRECT, REPLACE, STATUS |
| cost_model | per_token |
| sandbox_model | container |

## NanoClaw Manifest

| Field | Value |
|---|---|
| runtime_name | nanoclaw |
| compliance_level | 1 |
| session_model | stateless |
| subagent_nesting_depth | 0 |
| max_concurrent_agents | 50 |
| memory_layers_supported | working |
| memory_persistence | none |
| interruptible | false |
| approval_model | none |
| tool_categories | structured_output |
| max_context_size | 16000 |
| parallel_execution | true |
| async_messaging | false |
| human_command_support | STATUS |
| cost_model | per_call |
| sandbox_model | full_isolation |

## Manifest Validation

The Topology Compiler (see 21) uses manifests to validate topologies before execution. Validation checks include:

### Hard Checks — Topology is Rejected on Failure

1. Tool availability — every tool required by the topology's agents exists in the runtime's tool_categories
2. Subagent depth — the topology's nesting depth does not exceed subagent_nesting_depth
3. Agent count — the topology's agent count does not exceed max_concurrent_agents
4. Memory layers — every memory layer the mission reads or writes is in memory_layers_supported
5. Compliance level — the runtime's compliance_level meets the minimum required by the topology type (solo = 1, team = 2, full governance = 3)

### Soft Checks — Warnings Logged, Execution Proceeds

1. Context pressure — estimated context usage approaches max_context_size
2. Cost projection — estimated cost exceeds mission budget soft limits
3. Persistence gap — mission expects memory_persistence longer than the runtime provides
4. Approval gap — mission requires approval but runtime uses adapter_provided (not built_in)
5. Async gap — topology assumes async_messaging but runtime does not support it

Validation results are recorded in the Provenance Graph (see 06).

## Capability Negotiation

When a mission requires capabilities that the primary runtime does not have, the system negotiates using the following strategy.

### Step 1 — Identify the Gap

Compare mission requirements against the primary runtime's manifest. Gaps fall into categories:

1. Missing tools — a required tool_category is absent
2. Insufficient depth — required nesting exceeds subagent_nesting_depth
3. Missing memory — a required memory layer is not supported
4. Missing governance — required compliance_level exceeds the runtime's level

### Step 2 — Attempt Graceful Degradation

For each gap, check if the mission can proceed with reduced capability:

1. Missing tools — can the agent complete its task without the missing tool? If yes, log the degradation and proceed
2. Insufficient depth — can the topology be flattened to fit the runtime's depth limit? The Topology Compiler attempts automatic flattening (see 21)
3. Missing memory — can the adapter simulate the memory layer externally? If yes, use adapter-managed memory with a performance warning

### Step 3 — Split Across Runtimes

If degradation is not acceptable:

1. Identify which agents require the missing capability
2. Select a secondary runtime whose manifest satisfies the requirement (using the Runtime Selection Policy, see 55)
3. Route those agents to the secondary runtime
4. The adapter layer handles cross-runtime communication (see 55 Cross-Runtime Communication)
5. Record the multi-runtime topology in the Provenance Graph

### Step 4 — Fail Explicitly

If no combination of runtimes satisfies the mission requirements:

1. Return a validation failure with specific capability gaps listed
2. Suggest the minimum runtime additions needed to satisfy the mission
3. Do not attempt execution with known unsatisfiable requirements
