# 02 Reasoning Engine

## Purpose

The Reasoning Engine defines the structured cognitive operations that agents use to process information, make decisions, and produce outputs. Each function has a specific trigger, input/output contract, and role within the orchestration flow.

## Core Reasoning Functions

1. Decomposition
2. Abstraction management
3. Constraint evaluation
4. Tradeoff evaluation
5. Uncertainty handling
6. Synthesis
7. Critique
8. Decision support

## Function Protocols

### 1 Decomposition

- Trigger: An agent receives a task that cannot be completed in a single reasoning step
- Input: A complex objective or problem statement
- Process: Break the objective into sub-problems. Each sub-problem must be independently addressable and collectively exhaustive of the original objective. Identify dependencies between sub-problems
- Output: Ordered list of sub-problems with dependency annotations
- Feeds into: Router (see 20) for sub-task assignment, or sequential processing within a single agent
- Quality check: Every element of the original objective must map to at least one sub-problem. No sub-problem should restate the original objective

### 2 Abstraction Management

- Trigger: An agent needs to operate at a different level of detail than its current framing
- Input: Current work product and the target abstraction level (higher for strategy, lower for implementation)
- Process: Shift the framing up or down. When abstracting up, summarize details into principles and patterns. When abstracting down, expand principles into concrete specifics
- Output: Reframed work product at the target abstraction level
- Feeds into: Handoff messages (see 22) when passing between agents at different abstraction levels (e.g., Strategist to Architect)
- Quality check: Abstracting up and then back down should recover the essential content. Information loss should be intentional and documented

### 3 Constraint Evaluation

- Trigger: Before committing to an approach, or when a new constraint is introduced mid-execution
- Input: Proposed approach and active constraints (from the Constraint Compiler, see 03)
- Process: Test the proposed approach against each constraint. Flag violations. Identify constraints that are close to their limits
- Output: Constraint compliance report listing satisfied, at-risk, and violated constraints
- Feeds into: Decision support (function 8). If violations are found, feeds into tradeoff evaluation (function 4)
- Quality check: Every active constraint must be evaluated. No constraint may be silently ignored

### 4 Tradeoff Evaluation

- Trigger: Multiple viable approaches exist, or a constraint violation forces a choice between competing priorities
- Input: List of options with their benefits, costs, and constraint interactions
- Process: For each option, enumerate what is gained and what is sacrificed. Map tradeoffs to mission objectives and constraints. Rank options by alignment with the active Policy (see 04)
- Output: Tradeoff matrix with ranked options and a recommended choice with rationale
- Feeds into: Decision support (function 8). The Provenance Graph (see 06) records the tradeoff analysis
- Quality check: Every option must have at least one identified downside. If an option has no downsides, the analysis is incomplete

### 5 Uncertainty Handling

- Trigger: An agent encounters information gaps, ambiguous requirements, or low-confidence data
- Input: The uncertain element with context about what is known and what is not
- Process: Classify the uncertainty as resolvable (more information exists and can be obtained) or irreducible (no additional information will resolve it). For resolvable uncertainty, identify the action to resolve it (research, clarification, testing). For irreducible uncertainty, state the assumption being made and its confidence level
- Output: Uncertainty record with classification, action plan or assumption, and confidence level
- Feeds into: Sufficient Context Gate (see 26) for resolvable uncertainties. Provenance Graph (see 06) for assumption tracking. Factual Confidence Gate (see 26) for confidence assessment
- Quality check: No uncertainty may be silently resolved by guessing. Every assumption must be explicit

### 6 Synthesis

- Trigger: Multiple information sources or work products need to be combined into a unified output
- Input: Multiple inputs with their provenance and any conflict annotations
- Process: Identify common themes, complementary elements, and contradictions. Combine complementary elements. Resolve contradictions using evidence weighting (see 24 Merge and Reduction). Produce a unified output that preserves the strongest elements of each input
- Output: Synthesized work product with provenance annotations
- Feeds into: Deliverable Compiler (see 41). Merge and Reduction (see 24)
- Quality check: The synthesis must not introduce claims not present in any input. Every element in the synthesis must trace to at least one input

### 7 Critique

- Trigger: A work product needs adversarial evaluation before progressing
- Input: Work product, evaluation criteria, and the standard it should meet
- Process: Evaluate the work product against each criterion. For each finding, classify severity (High: blocks progress, Medium: should be addressed, Low: optional improvement). Provide specific, actionable feedback for High and Medium findings
- Output: Critique report with severity-ranked findings and suggested improvements
- Feeds into: Quality Gates (see 26). Disagreement Engine (see 25) if the critique challenges a fundamental assumption
- Quality check: A critique that finds nothing wrong is suspect. At minimum, identify areas of uncertainty or assumptions that could be challenged. Distinguish between genuine quality and insufficient scrutiny

### 8 Decision Support

- Trigger: A decision point is reached and the agent or human needs structured support to choose
- Input: Decision context, available options (from tradeoff evaluation), constraints (from constraint evaluation), and uncertainty profile
- Process: Present the decision with all relevant context. Recommend an option with explicit rationale. Identify what would change the recommendation. State the confidence level of the recommendation
- Output: Decision brief with recommendation, rationale, confidence, and sensitivity analysis
- Feeds into: Human Command Layer (see 09) when human approval is required. Provenance Graph (see 06) records the decision and its rationale
- Quality check: The recommendation must follow logically from the evidence presented. The sensitivity analysis must identify at least one factor that would change the recommendation

## Function Interactions

These functions are not isolated. Common chains include:

1. Decomposition -> Constraint Evaluation -> Tradeoff Evaluation -> Decision Support (strategic planning flow)
2. Uncertainty Handling -> Decomposition -> Synthesis (research flow)
3. Critique -> Tradeoff Evaluation -> Decision Support (review and revision flow)
4. Abstraction Management -> Decomposition -> Synthesis (cross-level handoff flow)

## Final Principle

Reasoning inside GLORB should be disciplined and structured enough to support orchestration, not just answer generation.
