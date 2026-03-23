# 50 Runtime Abstraction Layer

## Purpose

The Runtime Abstraction Layer (RAL) is the boundary between GLORB's control plane and the execution environments that run agents. It enforces a clean separation: GLORB defines what agents do, the runtime determines how they do it. The RAL is not an optional integration layer — it is the mechanism by which GLORB achieves runtime agnosticism, forward compatibility, and the ability to route work to the most capable available runtime.

Without the RAL, every GLORB capability would be coupled to a specific execution backend. The topology compiler would produce Claude-specific instructions. Memory persistence would depend on TodoWrite syntax. Governance enforcement would require runtime-specific approval hooks. The RAL prevents this coupling by defining what the control plane may ask of runtimes (the contract) and what runtimes must implement to be eligible (the adapter requirements). See 51 Runtime Adapter Contract for the high-level categories; see 56 Adapter Interface Spec for the formal method signatures; see 57 Runtime Capability Manifest for the declaration mechanism.

## The Abstraction Boundary

The boundary between the control plane and the runtime is defined precisely. Crossing this boundary without going through the adapter is a violation of the abstraction and will produce runtime-specific behavior that breaks portability.

### GLORB Owns

These responsibilities belong exclusively to the control plane. No runtime may override or reinterpret them.

| Responsibility | Description |
|---|---|
| Control plane logic | Mission routing (see 20), topology compilation (see 21), quality gate evaluation (see 26), failure recovery (see 07) |
| Agent definitions | Portable agent specs (see 10) are runtime-agnostic. They describe roles, capabilities, governance profiles, and contracts — not runtime-native constructs |
| Mission compilation | The Router (see 20) and Topology Compiler (see 21) produce an execution plan in GLORB's portable format. No runtime-specific instructions appear in the plan |
| Governance enforcement | Authority scopes, tool permissions, memory scopes, and escalation triggers (see 08 Governance Model) are defined and enforced by the control plane, mediated by the adapter |
| Deliverable compilation | The Deliverable Compiler (see 41) assembles structured deliverables from agent outputs. Schema selection, section population, and provenance attachment are control-plane responsibilities |
| Memory policy | Layer definitions (see 30), persistence rules (see 31), compression triggers (see 32), conflict resolution (see 33), and retrieval policy (see 34) are control-plane rules. The adapter implements them in native storage |

### Runtime Owns

These responsibilities belong exclusively to the execution runtime. The control plane may not attempt to implement them directly.

| Responsibility | Description |
|---|---|
| Actual execution | The runtime executes agent instructions. The control plane does not directly invoke LLM APIs, execute shell commands, or call tools |
| Tool calls | Tool invocations are mediated by the adapter's `execute_tool()` method (see 56). The control plane uses GLORB-canonical tool names; the adapter translates to native tool calls |
| Session storage | The runtime determines how session state is stored — whether in context window, message history, files, or external stores. The control plane interacts with session state only through the adapter's memory methods |
| Sandboxing | The runtime enforces execution isolation. The control plane declares required isolation level; the runtime's sandbox_model in the Capability Manifest (see 57) declares what it provides |
| Runtime-specific approvals | When an action requires approval and the runtime has a built-in approval mechanism (approval_model: built_in in the manifest), the runtime handles the approval UI. The control plane calls `request_approval()` (see 56) and receives approved, denied, or timeout |
| Native runtime limits | Context window size, token budgets, rate limits, and concurrency ceilings are runtime concerns declared in the Capability Manifest and respected by the Topology Compiler during validation |

## The Adapter Pattern

Every runtime that connects to GLORB must implement an adapter. The adapter is the concrete implementation of the Runtime Abstraction Layer for a specific execution environment. It translates between GLORB's portable control-plane language and the runtime's native constructs.

### How Adaptation Works

When the control plane needs to spawn an agent, it calls `spawn_agent(agent_spec, context)` on the adapter (see 56). The adapter receives a portable GLORB agent spec — a structured definition containing role, prompt, tool permissions, memory scope, and governance profile. The adapter translates this into whatever the runtime requires: a Claude-style system prompt with tool declarations, an API call with parameter formatting, or an injection into a stateless prompt template. The control plane never knows or cares what happened inside the adapter. It receives back an agent_id and proceeds.

This same pattern applies to every operation:
- Memory reads and writes go through `retrieve_memory()` and `persist_memory()`. The adapter maps GLORB layer names to native storage (files, databases, prompt content — see 30 Memory Layers, Adapter Storage Backends table).
- Tool calls go through `execute_tool()`. The adapter maps GLORB-canonical tool names (web_search, file_read, etc.) to the runtime's native tool calling syntax.
- Approval requests go through `request_approval()`. If the runtime has a built-in approval UI, the adapter routes to it. If not, the adapter implements an external mechanism or logs and auto-approves with an audit record.
- Logging goes through `log_event()`. The adapter translates GLORB provenance events into native runtime logging while ensuring the data is available for the Provenance Graph (see 06).

### Adapter Compliance Levels

Adapters declare a compliance level (see 56 Adapter Interface Spec, Adapter Compliance Levels). The compliance level determines which topology types and GLORB features the adapter can support.

| Compliance Level | Minimum Methods | Supports |
|---|---|---|
| Level 1 — Minimal | query_capabilities, spawn_agent, retire_agent, execute_tool, list_available_tools, log_event | Solo agent topologies only. Suitable for isolated single-purpose tasks |
| Level 2 — Standard | All Level 1 methods plus: send_message, receive_messages, retrieve_memory, persist_memory, get_agent_status, get_metrics | Team topologies. Multi-agent coordination, memory sharing, monitoring |
| Level 3 — Full | All Level 2 methods plus: pause_agent, resume_agent, compress_memory, request_approval | All GLORB features including human commands (see 09), full governance enforcement, agent lifecycle control |

The Topology Compiler (see 21) validates that the target runtime's compliance level is sufficient for the compiled topology type before execution begins (see 57 Manifest Validation, Hard Checks).

### Existing Adapters

| Adapter | Compliance Level | Session Model | Key Characteristics |
|---|---|---|---|
| Claude Code (see 52) | Level 3 | Stateful | Built-in approval, file-based memory persistence, native context compression, supports all Human Commands |
| OpenClaw (see 53) | Level 2 | Stateless | External approval required, adapter-managed memory, high concurrency ceiling (20 agents), deep subagent nesting (10 levels) |
| NanoClaw (see 54) | Level 1 | Stateless | Minimal surface: structured output only, no memory persistence, no inter-agent messaging, suited for high-volume stateless tasks within multi-runtime missions |

## The Capability Manifest

Every adapter exposes a Capability Manifest via `query_capabilities()` (see 57 Runtime Capability Manifest). The manifest is the runtime's declaration of what it can and cannot do. The control plane never assumes capability — it always queries the manifest.

The manifest is consumed by:
- The Topology Compiler (see 21), which validates compiled topologies against the manifest before execution
- The Runtime Selection Policy (see 55), which uses manifests to select the best available runtime for a mission's requirements
- The Capability Negotiation process (see 57), which identifies gaps and resolves them through graceful degradation or multi-runtime routing

Key manifest fields and what they govern:

| Field | Used By | If Missing/Insufficient |
|---|---|---|
| subagent_nesting_depth | Topology Compiler — nesting depth validation | Flatten topology to fit available depth |
| max_concurrent_agents | Topology Compiler — concurrency validation | Serialize parallel branches |
| memory_layers_supported | Topology Compiler — memory validation | Use adapter-mediated external storage or downgrade memory policy |
| session_model: stateless | All orchestration — determines sync vs async mode | Enable async orchestration mode (see 22) |
| compliance_level | Topology Compiler — feature eligibility | Restrict to topologies supported at the declared level |
| approval_model | Governance enforcement — approval routing | Implement external approval gate or log auto-approvals |

## Invariants

The following invariants must hold regardless of which runtime is active, which policy is applied, or which adapter is in use. If an adapter or runtime cannot maintain an invariant, it is not eligible to run missions that depend on the affected guarantee.

### Invariant 1: All memory operations are mediated

No control-plane component reads from or writes to storage directly. Every memory operation goes through the adapter's `retrieve_memory()` or `persist_memory()` methods. This invariant is what makes memory governance enforceable regardless of the underlying storage technology.

### Invariant 2: All tool calls respect governance scope

Before any tool is executed via `execute_tool()`, the agent's tool permissions (from its governance profile, see 08) are checked against the requested tool. A tool call outside the agent's scope is blocked, logged, and escalated. The adapter is responsible for this check — the control plane sets the governance profile, and the adapter enforces it at the tool boundary.

### Invariant 3: Agent permissions do not exceed parent permissions

When an agent spawns a subagent (see 23 Subagent Spawning), the subagent's governance profile is equal to or narrower than the parent's. This is enforced by the Topology Compiler during assignment and re-enforced at spawn time by the adapter. A subagent cannot gain authority by being spawned; authority only flows down, never up.

### Invariant 4: All significant events are logged

Every agent lifecycle event (spawn, task completion, retirement), memory operation, tool call, governance decision (approve, block, escalate), Quality Gate evaluation, and human command execution is logged to the Provenance Graph via `log_event()`. A runtime that silently drops events violates this invariant. If the runtime's native logging cannot satisfy this, the adapter must supplement it. The Provenance Graph (see 06) is only as complete as the adapter makes it.

### Invariant 5: Stateless runtimes receive complete context

When an agent executes on a stateless runtime, the adapter is responsible for injecting the complete context that agent needs — including mission state, relevant memory excerpts, and constraints — into the spawn call. The stateless agent does not "pull" context; context is pushed by the lead agent via the adapter. This invariant ensures that stateless agents behave as if they had access to the same shared state as stateful agents, mediated by the lead.

### Invariant 6: The control plane is never bypassed

Agents do not communicate directly with each other outside the adapter's `send_message()` and `receive_messages()` methods. Agents do not directly access runtime-native storage. Agents do not directly invoke runtime-native tool APIs. All coordination, storage, and tool access flows through the adapter interface. Any shortcut that bypasses the adapter also bypasses governance, provenance, and the memory model.

## Multi-Runtime Missions

GLORB supports missions where different agents execute on different runtimes. This occurs when a primary runtime lacks a capability required by a subset of agents (see 57 Capability Negotiation) or when the Runtime Selection Policy (see 55) routes specific agent types to specialized runtimes for cost or performance reasons.

In multi-runtime missions:
1. The Topology Compiler assigns each agent to a runtime and records the assignment in the execution plan
2. Each agent communicates through its own runtime's adapter
3. Inter-agent messages that cross runtime boundaries are routed through the adapters' `send_message()` and `receive_messages()` methods; the adapters handle cross-runtime delivery
4. Memory written by an agent on Runtime A and read by an agent on Runtime B must be in Mission Memory (see 30), which both adapters access via external storage or a shared persistence layer
5. Provenance records span both runtimes, linked by the provenance_id in the MemoryResult structure (see 56)

The lead agent in a multi-runtime mission is always on the runtime with the highest compliance level — typically a Level 3 stateful runtime — because it must coordinate all cross-runtime handoffs.
