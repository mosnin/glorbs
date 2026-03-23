# 05 Memory Constitution

## Purpose

This document is the constitutional authority for memory in GLORB. It establishes the fundamental rules that govern how information is stored, accessed, promoted, compressed, and retired across all missions and runtimes. Every more-detailed memory document — the six-layer model (see 30), persistence rules (see 31), compression policy (see 32), conflict resolution protocol (see 33), and retrieval policy (see 34) — derives its authority from the constitutional rules defined here.

A constitution is not a policy. Policies (see 04 Policy Engine) can be adjusted per mission. The rules in this document cannot. They hold across all policies, all runtimes, and all mission types. When a proposed memory behavior conflicts with a constitutional rule, the constitution wins.

## Constitutional Rules

The following rules are always true in GLORB. No policy, override, or runtime limitation may violate them.

### Rule 1: Memory is governed, not passive

Memory is not a passive store that agents freely read and write. Every memory read and write is mediated by the governance model (see 08 Governance Model). An agent may only access the memory layers defined in its memory scope. An agent may only write to layers explicitly granted in its governance profile. The runtime adapter (see 56 Adapter Interface Spec) enforces these boundaries before executing any memory operation. Ungoverned memory access does not exist in GLORB; if a runtime cannot enforce memory scope, it cannot be used for missions that require memory isolation.

### Rule 2: Memory is layered, and layers have defined scopes

GLORB defines six memory layers: Working, Session, Mission, Project, Reusable, and Archived. Each layer has a defined scope — the boundary within which its content is meaningful and authoritative. Content at a given layer is authoritative within its scope and advisory outside it. No layer may be collapsed, merged, or bypassed by implementation convenience. A runtime that cannot natively support a required memory layer must simulate it through adapter-managed storage rather than substituting a different layer. See 30 Memory Layers for the complete layer definitions.

### Rule 3: Provenance must survive compression

Every memory item, at every layer, carries a provenance reference linking it to the Provenance Graph record (see 06) that records why it was created, which agent created it, and what decision it supported. When a memory item is compressed, summarized, or promoted to a higher layer, the provenance reference is preserved intact. A compressed memory item without provenance is invalid. See 32 Memory Compression for the Provenance Preservation strategy that enforces this rule.

### Rule 4: Conflicts must be detected and resolved, not silently overwritten

When two memory items disagree — because they come from different agents, different layers, or different points in time — the conflict must be detected, classified, and resolved through the conflict resolution protocol (see 33 Memory Conflict Resolution). Silent overwrites are not permitted. An item is not superseded until the resolution protocol marks it as such and records the resolution in the Provenance Graph. A stale item that has been silently overwritten is indistinguishable from an authoritative item, which is a correctness failure.

### Rule 5: Dangerous content must not persist beyond Working Memory

Memory items that contain credentials, API keys, passwords, personal identifying information, or raw restricted data from external systems must not be promoted beyond Working Memory. The Memory Manager (see 14 Specialist Agent Patterns) is responsible for detecting and purging such items. This rule supersedes all policy-level persistence directives. No policy, including Highest Rigor, may override this rule.

### Rule 6: Memory must serve the mission, not accumulate for its own sake

The purpose of memory is to make future work better-informed and more efficient. Accumulation without purpose — persisting items that will never be retrieved, retaining items beyond their useful lifetime, or promoting mission-specific details into cross-project layers — violates this rule. Persistence decisions follow the decision tree in 31 Memory Persistence Rules. Compression is mandatory when layers exceed size thresholds (see 32). Periodic review is mandatory for Reusable Memory. Memory that serves no current or anticipated future purpose is archived or discarded according to the retention rules in 31.

### Rule 7: Reusable Memory is human-curated

Reusable Memory (Layer 5) is the only memory layer that requires human approval for writes. Agents may identify candidates for promotion to Reusable Memory and flag them, but the write is not executed until a human operator approves it. This rule exists because Reusable Memory is the institutional knowledge of the system — available to all future missions on all projects. Content that enters Reusable Memory without quality review degrades the system's baseline for all subsequent work. Automatic promotion algorithms do not replace human judgment at this layer.

### Rule 8: Stateless runtimes must be compensated

When agents execute on a stateless runtime (see 57 Runtime Capability Manifest, session_model: stateless), they cannot natively persist session or mission memory. This limitation does not exempt stateless agents from constitutional rules — it requires compensation. The lead agent on a stateful runtime is responsible for injecting required memory context into stateless agent calls and extracting and persisting their outputs after completion. Stateless agents do not write directly to any memory layer; all persistence is mediated by the lead agent. See 30 Memory Layers, section on Memory on Stateless Runtimes, for the complete protocol.

## Layer Rights and Obligations

Each memory layer carries rights (what may be done with its contents) and obligations (what must be done by the agents that access it).

| Layer | Rights | Obligations |
|---|---|---|
| Working Memory | Private to the owning agent. Contents need not be structured. Scratch work is permitted | Discard on task completion unless a decision or output is identified for promotion. Never persist sensitive content |
| Session Memory | Private to the owning agent. Lead agent may request a summary via Status message | Compress and promote key findings to Mission Memory on session end. Discard routine operational details |
| Mission Memory | Read access for all agents in the topology. Write access only for the lead agent or Memory Manager | Reflect the shared truth of the mission. All writes must carry provenance. Conflicts with other layers must be resolved per 33 |
| Project Memory | Read access for all agents on any mission in the project. Write access restricted to Memory Manager or human operator | Compress entries older than 5 missions. Promote generalizable patterns to Reusable Memory candidates for human review |
| Reusable Memory | Read access for all agents on all projects and missions. Write access requires human approval | Human operator must review relevance annually. Outdated content must be demoted to Archived, not left in place |
| Archived Memory | Read access on explicit request only. Not indexed for standard retrieval | Retain indefinitely for audit. Content may be restored to a higher layer only by explicit human request |

## Constitutional Relationship to Detailed Memory Docs

This constitution establishes the rules. The following documents implement them:

| Document | Implements |
|---|---|
| 30 Memory Layers | Rule 2 — full definition of the six layers, their scopes, promotion rules, demotion rules, and adapter storage mappings |
| 31 Memory Persistence Rules | Rule 6 — the persistence decision tree and retention periods per layer and per policy |
| 32 Memory Compression | Rule 3 and Rule 6 — compression strategies, triggers, and the Provenance Preservation requirement |
| 33 Memory Conflict Resolution | Rule 4 — conflict detection, classification, resolution protocol, and cascade checking |
| 34 Memory Retrieval Policy | Rule 1 and Rule 2 — retrieval query format, search order, relevance scoring, and context budget management |

When a detailed rule in docs 30-34 conflicts with a rule in this constitution, the constitution supersedes it.

## Memory Governance Failures

The following conditions constitute a memory governance failure. A governance failure must be logged to the Provenance Graph (see 06) and escalated to the lead agent or human depending on severity.

| Failure | Severity | Required Action |
|---|---|---|
| Agent reads a memory layer outside its scope | High | Block the read. Log and escalate to lead agent |
| Agent writes to a memory layer outside its scope | High | Block the write. Log and escalate to lead agent |
| Sensitive content found in Session Memory or above | High | Purge immediately. Log the scope of potential exposure |
| Memory item exists without provenance reference | Medium | Halt further promotion of the item. Attempt to reconstruct provenance from the Provenance Graph. If irrecoverable, archive the item with a provenance-gap flag |
| Conflict detected but not resolved before next phase | Medium | Block phase transition. Initiate conflict resolution per 33 |
| Reusable Memory write executed without human approval | High | Revert the write. Log the agent responsible |
| Layer size threshold exceeded without triggering compression | Low | Trigger compression immediately. Log the condition |
