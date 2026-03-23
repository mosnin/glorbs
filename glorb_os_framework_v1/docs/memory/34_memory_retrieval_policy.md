# 34 Memory Retrieval Policy

## Purpose

The Retrieval Policy defines how agents query memory, which layers are searched in what order, how relevance is scored, and how much memory is injected into an agent's working context.

## Retrieval Priorities

1. Current mission relevant
2. Current project relevant
3. Reusable pattern relevant
4. Archived reference only if needed

## Retrieval Query Format

An agent issues a retrieval query with these parameters:

1. **Query**: What information is needed (natural language description or keyword)
2. **Scope**: Which layers to search (defaults to all layers the agent has access to, per 08 Governance Model)
3. **Recency bias**: Whether to prefer newer information (default: yes)
4. **Max results**: Maximum number of items to return (default: 5)
5. **Context budget**: Maximum size of retrieved content that can be injected into working memory

## Search Order

Layers are searched in this order. Earlier layers are searched first.

1. **Working memory**: Already in the agent's context. Searched implicitly
2. **Session memory**: Current session history. High recency
3. **Mission memory**: Shared mission state. High relevance to current work
4. **Project memory**: Cross-mission knowledge. Useful for patterns and precedent
5. **Reusable memory**: Framework-level knowledge. Useful for proven patterns
6. **Archived memory**: Only searched if explicitly requested or if no results found in layers 1-5

Each layer is searched completely before moving to the next. If sufficient results are found in an earlier layer, later layers are skipped.

## Relevance Scoring

Each retrieved item receives a relevance score based on these factors:

| Factor | Weight | Description |
|---|---|---|
| Topic match | 40% | How closely the item's content matches the query |
| Recency | 25% | How recently the item was created or last accessed. Newer items score higher |
| Authority | 15% | The authority level of the agent or human that created the item (see 08) |
| Provenance depth | 10% | Items with richer provenance (more evidence, more review) score higher |
| Layer proximity | 10% | Items from layers closer to the agent's current scope score higher |

Items are returned sorted by relevance score, highest first.

## Context Budget Management

Agents have a limited working memory capacity. Retrieved information must fit within the context budget.

1. If total retrieved content exceeds the context budget, lower-scored items are dropped
2. If a single high-relevance item exceeds the budget, it is compressed using Key Extraction (see 32 Memory Compression) before injection
3. The context budget varies by runtime. The Claude Code adapter (see 52) uses the context window as the primary constraint
4. As a guideline, retrieved memory should not exceed 30 percent of the agent's available working memory. The remaining 70 percent is reserved for reasoning and output generation

## Retrieval Staleness Detection

When a retrieved item is older than the most recent mission completion:

1. Flag the item as potentially stale
2. Check if a newer item on the same topic exists in a higher layer
3. If a newer item exists, prefer it and mark the older item for review
4. If no newer item exists, return the older item with a staleness warning

## Retrieval for Specific Use Cases

### Decision Making

When an agent retrieves memory to support a decision:

1. Search Mission Memory for prior decisions on the same topic
2. Search Project Memory for patterns and precedent
3. Include constraint context from the Constraint Compiler (see 03)
4. Max results: 3. Focus on quality over quantity

### Research

When a Researcher agent retrieves memory for information gathering:

1. Search all accessible layers with broad matching
2. Recency bias: reduced (historical information may be valuable)
3. Max results: 10. Breadth is more valuable than precision
4. Flag items with low confidence or stale markers for verification

### Quality Gate Evaluation

When a gate evaluator retrieves memory to assess a work product:

1. Search Mission Memory for the relevant success criteria and acceptance standards
2. Search Provenance Graph for the chain of decisions that produced the work product
3. Max results: No limit. Completeness is critical for gate evaluation

### Deliverable Compilation

When the Deliverable Compiler (see 41) retrieves memory:

1. Search Mission Memory for all agent outputs, decisions, and Quality Gate results
2. Search Provenance Graph for attribution
3. Max results: No limit. The deliverable must reflect all mission work

## Retrieval Logging

Every retrieval query and its results are logged to the Provenance Graph (see 06) as metadata. This enables post-mission analysis of what information was available to each agent when it made its decisions.
