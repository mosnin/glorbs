# 31 Memory Persistence Rules

## Purpose

Persistence Rules govern the decisions about what memory to keep, what to compress, what to discard, and what to archive. They translate the abstract memory layer definitions (see 30) into concrete retention logic.

## Persistence Questions

1. Is it reusable
2. Is it project specific
3. Is it time sensitive
4. Is it dangerous to persist
5. Does it justify long term storage

## Persistence Decision Tree

For each memory item at the end of a session or mission, evaluate:

```
Is it a decision with rationale?
  ├─ Yes -> Persist in Mission Memory. If generalizable, promote to Project Memory
  └─ No -> Continue

Is it a factual finding with source attribution?
  ├─ Yes -> Persist in Mission Memory
  └─ No -> Continue

Is it a reusable pattern (topology, agent spec, workflow)?
  ├─ Yes -> Persist in Project Memory. If proven across 2+ missions, promote to Reusable Memory
  └─ No -> Continue

Is it intermediate reasoning or scratch work?
  ├─ Yes, and Rigor is High -> Compress and persist in Mission Memory for audit
  ├─ Yes, and Rigor is not High -> Discard
  └─ No -> Continue

Is it operational metadata (tool calls, timing, errors)?
  ├─ Yes, and there was a failure -> Persist in Mission Memory (supports post-mortem)
  ├─ Yes, no failure -> Discard or compress to summary statistics
  └─ No -> Continue

Is it a deliverable draft or section?
  ├─ Yes, final version -> Persist in Mission Memory
  ├─ Yes, superseded draft -> Archive or discard based on Rigor level
  └─ No -> Continue

Does it contain sensitive information (credentials, personal data)?
  ├─ Yes -> Do NOT persist beyond Working Memory. Flag for immediate cleanup
  └─ No -> Apply default retention for its layer
```

## Retention Periods

| Memory Layer | Default Retention | Override Condition |
|---|---|---|
| Working memory | Discarded on task completion | Rigor High: compress and promote key items to Session |
| Session memory | Discarded on session end | Rigor High: compress and promote to Mission |
| Mission memory | Retained for mission duration + 1 subsequent mission | Rigor High: retained indefinitely in Archive |
| Project memory | Retained for project lifetime | Periodic review: entries untouched for 5+ missions are candidates for archival |
| Reusable memory | Retained indefinitely | Human review annually for relevance |
| Archived memory | Retained indefinitely | No automatic expiry |

## Policy-Driven Persistence

The active Policy (see 04 Policy Engine) adjusts persistence behavior.

| Policy | Persistence Behavior |
|---|---|
| Fastest acceptable | Aggressive discard. Keep only deliverables and blocking decisions |
| Highest rigor | Full persistence. Keep everything including intermediate reasoning |
| Lowest cost | Minimal persistence. Working memory only. Discard session memory |
| Balanced | Standard persistence per decision tree |
| High exploration | Broad persistence. Keep all research findings even if not used in deliverable |
| Low hallucination bias | Persist all source attributions and claim-evidence links |
| Human in loop required | Full persistence for audit. Nothing discarded without human approval |
| Autonomous unless blocked | Standard persistence. Aggressive compression between sessions |

## Dangerous Memory

Some memory items should NOT be persisted regardless of policy:

1. API keys, tokens, passwords, or credentials encountered during execution
2. Personal identifying information unless the mission explicitly requires it
3. Raw data from external systems that may have usage restrictions
4. Internal tool error dumps that contain system paths or configuration details

These items are flagged by the Memory Manager (see 14 Specialist Agent Patterns) and purged from all layers above Working Memory.
