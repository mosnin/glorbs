# 53 OpenClaw Adapter

## Purpose

The OpenClaw Adapter maps GLORB framework concepts to an open-source, API-driven agent runtime. OpenClaw represents a class of runtimes where agents are orchestrated via API calls, sessions are managed externally, and tools are exposed through a plugin or function-calling interface.

## Adapter Contract (per 51 Runtime Adapter Contract)

### Available Tools

1. Function-calling interface for external APIs and services
2. Code execution in sandboxed environments
3. File operations through API-mediated access
4. Web search and retrieval via plugins
5. Database query tools if configured

### File System Access Model

1. No direct filesystem access. All file operations go through the API layer
2. File read and write via tool calls that the runtime mediates
3. File access scope is defined by the runtime's configuration
4. Atomic operations are not guaranteed. The adapter must handle partial writes

### Session Model

1. Stateless by default. Each API call is independent
2. Session continuity is maintained via explicit context passing (message history or session IDs)
3. Long-running sessions require the adapter to manage context accumulation and compression
4. Multiple concurrent sessions are supported natively

### Memory Interface

| GLORB Memory Layer | OpenClaw Implementation |
|---|---|
| Working memory | The current API call's context (prompt + message history) |
| Session memory | Managed by the adapter. Accumulated message history passed to each call |
| Mission memory | External storage (database, file store, or vector store). Retrieved and injected by the adapter |
| Project memory | External persistent storage. Shared across mission sessions |
| Reusable memory | External persistent storage. Shared across projects |
| Archived memory | External persistent storage. Read-only, query on demand |

### Subagent Support

1. Multiple agents can run as independent API sessions
2. Parent-child relationships are managed by the adapter, not the runtime
3. Context inheritance requires explicit copying by the adapter
4. No nesting restrictions. Multi-level hierarchies are possible
5. Parallel agent execution is native (concurrent API calls)

### Approval Model

1. No built-in user approval. All calls execute immediately
2. The adapter must implement approval gates for sensitive actions
3. Approval can be implemented as a pre-execution hook that pauses and waits for human input

### Sandbox Model

1. Code execution runs in sandboxed containers (if the runtime provides them)
2. Network access is configurable per sandbox
3. File system in sandbox is isolated and ephemeral
4. Resource limits (CPU, memory, time) are enforced by the runtime

### Runtime Limits

1. Token limits per API call (varies by model and configuration)
2. Rate limits on API calls (varies by provider)
3. No built-in budget tracking. The adapter must track token usage
4. Concurrent session limits depend on the deployment

### Logging Model

1. API call logs (request/response pairs) serve as the execution log
2. Structured logging requires the adapter to extract and format
3. No built-in provenance. The adapter maps API logs to Provenance Graph records

## GLORB Concept Mapping

| GLORB Concept | OpenClaw Implementation |
|---|---|
| Lead agent | A dedicated orchestrator session that manages other sessions |
| Specialist agent | An independent API session with a role-specific system prompt |
| Agent spawning | Creating a new API session with appropriate context |
| Agent communication | Adapter routes messages between sessions |
| Topology | Adapter manages the session graph and routing |
| Quality gates | Adapter implements gate logic between session outputs |
| Merge and reduction | A dedicated synthesizer session receives outputs from parallel sessions |

## Key Differences from Claude Code

| Dimension | Claude Code | OpenClaw |
|---|---|---|
| Session persistence | Context window + files | Explicit context management |
| File access | Direct filesystem | API-mediated |
| Subagent nesting | Single level | Multi-level |
| Approval model | Built-in user prompts | Adapter must implement |
| Memory persistence | Files and git | External storage |
| Orchestration | Built into the tool | Adapter responsibility |

## Adapter Responsibilities

Because OpenClaw runtimes provide less built-in structure than Claude Code, the adapter must implement:

1. Session management (creation, context passing, cleanup)
2. Memory layer management (external storage reads and writes)
3. Approval gates (pre-execution hooks for sensitive actions)
4. Provenance logging (mapping API call logs to Provenance Graph records)
5. Token budget tracking
6. Agent-to-agent message routing
7. Topology enforcement (ensuring agents operate within their defined scope)
