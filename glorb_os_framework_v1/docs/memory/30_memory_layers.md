# 30 Memory Layers

## Purpose

Memory Layers define the scoped, tiered storage model that governs how information persists, flows, and expires across the GLORB system. Each layer has a distinct scope, lifetime, access model, and role in supporting agent work.

## Layers

1. Working memory
2. Session memory
3. Mission memory
4. Project memory
5. Reusable memory
6. Archived memory

## Layer Definitions

### 1 Working Memory

- Scope: Single agent, single task
- Lifetime: Exists only while the agent is actively processing. Discarded when the agent completes its current task or is released
- Size guidance: Keep minimal. Only the information needed for the current reasoning step
- Access: Private to the owning agent. No other agent can read or write
- Contents: Current task context, intermediate reasoning, scratch calculations, draft outputs
- Promotion: Key decisions and final outputs promote to Session Memory on task completion. Intermediate reasoning is discarded unless Rigor is High (see 04 Policy Engine)

### 2 Session Memory

- Scope: Single agent, single session (one continuous execution context)
- Lifetime: Persists for the duration of the session. Compressed or discarded at session end based on Memory Policy
- Size guidance: Moderate. Accumulates across tasks within a session
- Access: Private to the owning agent. The lead agent may request a summary via a Status message (see 22 Team Orchestration)
- Contents: Task results, decisions made, tools used, errors encountered, handoff records
- Promotion: On session end, key findings and decisions promote to Mission Memory. Routine operational details are discarded or compressed (see 32 Memory Compression)

### 3 Mission Memory

- Scope: All agents participating in a single mission
- Lifetime: Persists for the full duration of the mission. Retained after mission completion based on Persistence Rules (see 31)
- Size guidance: Moderate to large. Contains the shared truth for the mission
- Access: Read access for all agents in the mission topology. Write access for the lead agent and the Memory Manager (see 14 Specialist Agent Patterns). Other agents contribute via Handoff messages that the lead incorporates
- Contents: Mission objective, constraints, topology decisions, agent assignments, Quality Gate results, Disagreement Engine outcomes, key decisions with rationale, deliverable drafts
- Promotion: On mission completion, reusable patterns, lessons learned, and domain knowledge promote to Project Memory. Mission-specific operational details are archived or discarded

### 4 Project Memory

- Scope: All missions within a single project
- Lifetime: Persists for the life of the project. Survives individual mission completion
- Size guidance: Large. Grows across missions. Requires periodic compression
- Access: Read access for all agents working on any mission in the project. Write access restricted to the Memory Manager or human operator
- Contents: Project objectives and constraints, cross-mission decisions, accumulated domain knowledge, agent performance history, reusable topology patterns that worked well, known pitfalls and anti-patterns discovered
- Promotion: Generalizable patterns and knowledge promote to Reusable Memory. Project-specific details remain at this layer

### 5 Reusable Memory

- Scope: Cross-project. Available to any mission on any project
- Lifetime: Persistent. No automatic expiry. Subject to periodic human review for relevance
- Size guidance: Curated and compact. Quality over quantity
- Access: Read access for all agents. Write access restricted to human operator
- Contents: Proven topology patterns, specialist agent templates that performed well, domain-agnostic best practices, framework-level lessons learned
- Promotion: None. This is the highest persistent layer. Content may be demoted to Archived if it becomes outdated

### 6 Archived Memory

- Scope: Historical record
- Lifetime: Indefinite. Retained for audit and reference
- Size guidance: Unbounded but compressed. Not actively indexed for retrieval
- Access: Read access on explicit request only. Not included in standard retrieval sweeps (see 34 Memory Retrieval Policy)
- Contents: Completed mission records, retired agent specs, historical decisions, superseded patterns, audit trails
- Promotion: None. Content may be restored to a higher layer if a human explicitly requests it

## Layer Promotion Rules

Promotion moves information from a lower layer to a higher (longer-lived) layer.

1. Working to Session: Automatic on task completion. Promote decisions and outputs. Discard scratch work
2. Session to Mission: Automatic on session end. Promote key findings, decisions, and errors. Compress or discard routine operations
3. Mission to Project: On mission completion. The Memory Manager or lead agent identifies reusable knowledge and promotes it. Requires human approval if the policy is Human In Loop Required (see 04)
4. Project to Reusable: Manual. Human operator reviews project learnings and promotes generalizable knowledge
5. Any layer to Archived: Automatic for completed missions. Manual for other content. Archiving compresses but does not delete

## Layer Demotion Rules

Demotion moves information from a higher layer to a lower (shorter-lived) layer or removes it.

1. Reusable to Archived: When patterns are superseded or no longer applicable. Human approval required
2. Project memory pruning: The Memory Manager may compress old project memory entries. Entries older than the most recent 3 missions are candidates for compression. Entries untouched for 5 or more missions are candidates for archival (see 31 Retention Periods). Compression and archival are independent operations: compression reduces size while retaining the item in Project Memory; archival moves the item to Archived Memory.
3. No layer may be deleted entirely without human approval when Risk Tolerance is High

## Cross-Layer Query

An agent may query across layers up to its access level. Queries follow the retrieval priority defined in 34 Memory Retrieval Policy. Lower layers (working, session) are searched first for recency, then higher layers (mission, project, reusable) for breadth.

## Adapter Memory Interface

### Unified Memory Access
All memory operations go through the runtime adapter's memory methods (see 56 Adapter Interface Spec). The control plane NEVER accesses storage directly. This ensures runtime agnosticism.

#### Read Operations
All agents query memory via retrieve_memory(layer, query, scope, max_results):
1. The adapter translates the GLORB layer name to its native storage mechanism
2. The adapter applies access control: the requesting agent's memory scope (see 08 Governance Model) is checked against the requested layer
3. The adapter executes the query against its storage backend
4. Results are returned in a standard format: content, layer, relevance_score, recency, provenance_id (see 56)

#### Write Operations
Agents write to memory via persist_memory(layer, key, content, metadata):
1. The adapter validates the agent's write permissions for the target layer
2. The adapter translates the write to its native storage mechanism
3. Metadata (provenance record ID, agent, timestamp) is stored alongside the content
4. The adapter returns success or failure status

#### Compression Operations
Memory compression is triggered via compress_memory(layer, strategy, scope):
1. The adapter applies the named compression strategy (see 32) to the specified layer
2. Provenance links are preserved through compression (see 32 Provenance Preservation)
3. The adapter returns the compression result (items compressed, space saved)

### Adapter Storage Backends
Each runtime adapter maps GLORB memory layers to native storage. The control plane does not need to know the storage mechanism.

| Runtime | Working | Session | Mission | Project | Reusable | Archived |
|---|---|---|---|---|---|---|
| Claude Code | Context window | Context + TodoWrite | Repository files | Repository + CLAUDE.md | CLAUDE.md (user-level) | Git history |
| OpenClaw | API call context | Message history (adapter-managed) | External DB/store | External persistent store | External persistent store | External persistent store |
| NanoClaw | Prompt content | Not supported (injected per-call) | Injected by adapter | Injected by adapter | Injected by adapter | Not practical |

### Memory on Stateless Runtimes
When the runtime is stateless (session_model: stateless, see 57):
1. Working memory is the prompt content for the current call. It is not persisted
2. Session memory does not exist. Each call is independent
3. Mission, project, and reusable memory must be injected into the prompt by the lead agent (on a stateful runtime) before dispatching the call
4. The lead agent is responsible for extracting important information from stateless agent results and persisting it to the appropriate layer via its own adapter
5. Stateless agents cannot write to memory layers directly. All persistence is mediated by the lead agent
