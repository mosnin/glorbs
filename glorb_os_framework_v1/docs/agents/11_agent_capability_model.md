# 11 Agent Capability Model

## Purpose

The Capability Model defines the dimensions along which agent abilities are measured, the scoring rubric for each dimension, and how capability scores influence routing, agent selection, and evaluation.

## Example Capability Dimensions

1. Abstraction depth
2. Precision
3. Creativity
4. Risk tolerance
5. Adversarial rigor
6. Synthesis strength
7. Tool access
8. Autonomy level
9. Memory breadth
10. Domain expertise

## Scoring Rubric

Each dimension is scored on a 3-point scale: Low, Medium, High.

### 1 Abstraction Depth

- Low: Operates at a single level of detail. Cannot shift between strategic and tactical framing
- Medium: Can shift between two adjacent levels (e.g., component to system, or system to strategy)
- High: Fluently moves between all levels from implementation detail to organizational strategy

### 2 Precision

- Low: Produces approximate, directional outputs. Acceptable for exploration
- Medium: Produces outputs that are mostly correct and internally consistent
- High: Produces outputs that are exact, verifiable, and free of ambiguity

### 3 Creativity

- Low: Follows established patterns and known solutions only
- Medium: Adapts known patterns to new contexts. Can generate alternatives when prompted
- High: Generates novel approaches, identifies non-obvious connections, and challenges conventional framing

### 4 Risk Tolerance

- Low: Avoids uncertain paths. Prefers safe, proven approaches. Escalates frequently
- Medium: Accepts moderate uncertainty. Will try an approach if the downside is bounded
- High: Comfortable with significant uncertainty. Will explore risky paths if the potential upside justifies it

### 5 Adversarial Rigor

- Low: Accepts inputs at face value. Does not challenge assumptions
- Medium: Identifies obvious flaws and inconsistencies. Asks clarifying questions
- High: Actively seeks weaknesses, challenges assumptions, stress-tests logic, and identifies failure modes

### 6 Synthesis Strength

- Low: Can combine inputs only when they are complementary and non-conflicting
- Medium: Can reconcile moderate conflicts and produce a unified output from diverse inputs
- High: Can integrate contradictory inputs, resolve deep conflicts, and produce coherent output that preserves nuance

### 7 Tool Access

- Low: No tool use or read-only access
- Medium: Can use standard tools (file operations, search)
- High: Full tool access including shell execution, external APIs, and build tools

### 8 Autonomy Level

- Low: Requires approval for each action. Supervised mode
- Medium: Operates independently within defined scope with periodic checkpoints
- High: Fully autonomous within scope. Escalates only when genuinely blocked

### 9 Memory Breadth

- Low: Works only with information provided in the current task input
- Medium: Draws on session and mission memory for context
- High: Draws on project, reusable, and archived memory. Can identify relevant historical precedents

### 10 Domain Expertise

- Low: General-purpose knowledge. No domain specialization
- Medium: Working knowledge of the relevant domain. Can apply standard practices
- High: Deep domain knowledge. Can identify domain-specific risks, patterns, and best practices

## Capability Profiles by Specialist Pattern

Default capability profiles for each specialist (see 14 Specialist Agent Patterns).

| Dimension | Strategist | Architect | Builder | Researcher | Critic | Router | Synthesizer | Verifier | Reviewer | Memory Manager |
|---|---|---|---|---|---|---|---|---|---|---|
| Abstraction depth | High | High | Medium | Medium | Medium | Medium | Medium-High | Medium | Medium-High | Medium |
| Precision | Medium | High | High | Medium | High | Medium | Medium | High | Medium | High |
| Creativity | High | Medium | Low | Medium | Low | Low | Medium | Low | Medium | Low |
| Risk tolerance | Medium | Low | Low | Medium | Low | Low | Medium | Low | Low | Low |
| Adversarial rigor | Medium | Medium | Low | Low | High | Low | Low | Medium-High | Medium | Low |
| Synthesis strength | High | Medium | Low | Low | Low | Low | High | Low | Medium | Low |
| Tool access | Low | Low | High | Medium | Low | Low | Low | High | Low | Medium |
| Autonomy level | Medium | Medium | Medium | Medium | Medium | High | Medium | Medium | Medium | Medium |
| Memory breadth | Medium | Medium | Low | High | Medium | Low | Medium | Medium | Medium | High |
| Domain expertise | Medium | Medium-High | Medium-High | Medium | Medium | Low | Medium | Medium | Medium-High | Low |

## How Capabilities Influence Decisions

### Routing

The Router (see 20) uses capability profiles to select agents.

1. Match mission requirements to capability dimensions. A Build mission needs High Precision and High Tool Access
2. Select the specialist pattern whose default profile best matches the requirements
3. If no pattern matches well, the Agent Foundry (see 15) may create a custom agent with a tailored profile

### Agent Selection

When multiple agents of the same pattern exist, select by comparing capability scores to task requirements.

1. For each required dimension, score the match: exact match = 2, one level off = 1, two levels off = 0
2. Sum the match scores. Select the agent with the highest total
3. Ties are broken by the agent with the better evaluation history (see 16 Agent Evaluation)

### Evaluation

Capability profiles set expectations for evaluation (see 16). An agent with High Precision is held to a higher standard on accuracy than one with Medium Precision.

## Capability Gaps

A capability gap exists when:

1. A mission requires a dimension at a level that no available agent provides
2. The gap is detected by comparing mission requirements (from the Constraint Compiler, see 03) to available agent profiles
3. Gaps are reported to the Agent Foundry (see 15) for resolution via agent creation or specialization
