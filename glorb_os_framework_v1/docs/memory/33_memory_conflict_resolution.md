# 33 Memory Conflict Resolution

## Purpose

Memory Conflict Resolution defines how the system detects and resolves contradictions between memory items across layers or agents. It prevents stale or incorrect information from silently influencing decisions.

## Resolution Options

1. Prefer newer memory
2. Prefer higher authority memory
3. Preserve both and mark conflict
4. Escalate when the conflict is decision relevant

## Conflict Detection

### When Detection Runs

1. During memory retrieval (see 34 Memory Retrieval Policy) when a query returns results from multiple layers or agents that cover the same topic
2. During memory promotion when an item being promoted contradicts an existing item in the target layer
3. During merge and reduction (see 24) when agent outputs reference contradictory memory items
4. On demand when the Memory Manager (see 14) runs a consistency check

### How Conflicts Are Identified

1. **Direct contradiction**: Two memory items make opposing factual claims about the same subject (e.g., "Service X uses PostgreSQL" vs "Service X uses MySQL")
2. **Stale override**: A newer memory item updates information that an older item established, but the older item has not been superseded (e.g., a constraint was changed but an earlier decision based on the old constraint is still active)
3. **Authority conflict**: Two items from agents with different authority levels disagree on a decision or approach
4. **Cross-layer inconsistency**: A project-level memory item contradicts a mission-level item (e.g., a project-level pattern says "always use REST" but the mission decided to use GraphQL)

## Resolution Protocol

### Step 1: Classify the Conflict

| Conflict Type | Classification |
|---|---|
| Direct contradiction on facts | Factual conflict |
| Stale information vs updated information | Temporal conflict |
| Different authority levels disagree | Authority conflict |
| Different layers disagree | Layer conflict |

### Step 2: Assess Decision Relevance

Is the conflict relevant to a current or upcoming decision?

1. **Yes, actively relevant**: The conflict affects work currently in progress or an imminent Quality Gate. Resolution is urgent
2. **Potentially relevant**: The conflict could affect future work but is not blocking anything now. Resolve before the next phase
3. **Not relevant**: The conflict is between historical items that do not affect current work. Document and defer

### Step 3: Apply Resolution Rules

| Conflict Type | Decision Relevance | Resolution |
|---|---|---|
| Factual | Active | Investigate. Check sources for both items. Prefer the item with stronger evidence (see 24 Evidence Weighting). If equal, escalate to lead agent |
| Factual | Not active | Prefer newer memory. Mark the older item as superseded |
| Temporal | Any | Prefer newer memory. Mark the older item as superseded. Cascade: check if any decisions were made based on the now-superseded item |
| Authority | Active | Prefer higher authority memory. A human decision overrides an agent decision. A lead agent decision overrides a worker agent decision |
| Authority | Not active | Prefer higher authority. Document the override |
| Layer | Active | Project memory overrides mission memory on project-level concerns. Mission memory overrides project memory on mission-specific concerns. If ambiguous, escalate to lead agent |
| Layer | Not active | Document the inconsistency. Resolve during next memory promotion cycle |

### Step 4: Cascade Check

After resolving a conflict:

1. Identify all memory items and decisions that depended on the now-superseded item
2. For each dependent item, evaluate whether the resolution changes its validity
3. If a dependent decision is invalidated, flag it for re-evaluation
4. If a dependent deliverable section is invalidated, trigger re-review at the relevant Quality Gate (see 26)

## Conflict Escalation

Escalate to human review when:

1. Both conflicting items have strong evidence and equal authority
2. The conflict affects an irreversible decision
3. Resolution would require overriding a locked assumption (see 09 LOCK ASSUMPTIONS)
4. The conflict cascade affects more than 3 dependent items

## Conflict Record

Every conflict and its resolution is recorded in the Provenance Graph (see 06) with:

1. The two conflicting memory items (references)
2. Conflict type and classification
3. Decision relevance assessment
4. Resolution applied
5. Cascade impact (list of dependent items re-evaluated)
6. Resolver (which agent or human resolved it)
