# 27 Mission Graph

## Purpose

The Mission Graph is a directed acyclic graph that represents the structure of a mission's execution plan. It maps objectives to tasks, tracks dependencies, identifies parallel opportunities, and provides a real-time view of progress. It is the execution counterpart to the topology (which defines who does the work) — the Mission Graph defines what work is done and in what order.

## Mission Graph Nodes

1. Objectives
2. Dependencies
3. Assumptions
4. Parallel branches
5. Convergence points
6. Quality gates
7. Decision points
8. Blockers

## Node Definitions

### 1 Objective Node

- Description: A discrete goal that contributes to the mission objective
- Properties: Name, description, assigned agent, status (pending, in_progress, complete, blocked, abandoned)
- Connections: Depends on other objective nodes or assumption nodes. Feeds into convergence points or the final deliverable

### 2 Dependency Node

- Description: A link between two objective nodes indicating that one must complete before the other can start
- Properties: Source objective, target objective, dependency type (hard: must complete, soft: should complete but target can start with partial info)
- Connections: Connects exactly two objective nodes

### 3 Assumption Node

- Description: An assumption that one or more objectives rely on
- Properties: Assumption statement, confidence level (High, Medium, Low), source (which agent or human introduced it), locked status (see 09 LOCK ASSUMPTIONS)
- Connections: Feeds into the objective nodes that depend on this assumption

### 4 Parallel Branch Node

- Description: A fork point where the graph splits into independently executable paths
- Properties: Branch count, branch descriptions
- Connections: One input, multiple outputs. Each output is an independent path that requires no information from the others

### 5 Convergence Point Node

- Description: A join point where parallel branches merge
- Properties: Expected inputs (list of branches that must complete), merge strategy (see 24 Merge and Reduction)
- Connections: Multiple inputs from parallel branches. One output that carries the merged result

### 6 Quality Gate Node

- Description: An evaluation checkpoint (see 26 Quality Gates)
- Properties: Gate type, evaluator agent, pass/fail status, retry count
- Connections: One input (the work product to evaluate). Two outputs: pass (proceed) or fail (return to a prior node)

### 7 Decision Point Node

- Description: A point where the execution path depends on a choice
- Properties: Decision description, options, decision criteria, decided by (agent or human)
- Connections: One input. Multiple outputs, one per option. Only one output is followed

### 8 Blocker Node

- Description: An obstacle that prevents progress on one or more objectives
- Properties: Blocker description, affected objectives, severity (blocking or degrading), resolution status
- Connections: Blocks the downstream objective nodes. Removed when resolved

## Graph Construction

### Automatic Construction

The Mission Graph is built automatically by the Topology Compiler (see 21) during Step 5 (Produce Execution Plan).

1. Each agent assignment becomes an Objective Node
2. Handoff sequences become Dependency Nodes (hard dependencies)
3. Parallel agent assignments become Parallel Branch Nodes
4. Merge points become Convergence Point Nodes
5. Quality Gate positions become Quality Gate Nodes
6. Known assumptions from the Constraint Compiler (see 03) become Assumption Nodes

### Manual Augmentation

The lead agent or human may add nodes during execution:

1. New Blocker Nodes when obstacles are discovered
2. New Decision Point Nodes when unexpected choices arise
3. New Assumption Nodes when agents introduce assumptions
4. New Objective Nodes when scope is expanded (via REDIRECT command, see 09)

## Graph Traversal and Execution

### Execution Semantics

1. Execution starts at the root Objective Node (the mission objective)
2. The graph is traversed in topological order respecting dependencies
3. When a Parallel Branch Node is reached, all branches are launched simultaneously
4. When a Convergence Point Node is reached, execution waits until all required inputs are complete
5. When a Quality Gate Node is reached, the gate is evaluated. On pass, execution continues. On fail, execution returns to the gate's failure target node
6. When a Decision Point Node is reached, execution pauses until the decision is made. Only the chosen path is followed
7. When a Blocker Node is encountered, affected downstream objectives are paused. Recovery is triggered (see 07)

### Progress Tracking

Each Objective Node maintains its status. Progress is calculated as:

1. Total objective count (excluding abandoned objectives)
2. Completed objective count
3. Progress percentage = completed / total
4. Critical path: the longest dependency chain through the graph. This determines the minimum remaining execution time

## Cycle Detection

The Mission Graph must be a directed acyclic graph (DAG). Cycles are invalid.

1. Cycle detection runs during graph construction and after any modification
2. If a cycle is detected, the Topology Compiler identifies the edge that creates the cycle
3. The cycle is broken by inserting a Convergence Point with a Synthesizer agent, converting the cycle into a sequential flow
4. If the cycle cannot be broken without losing essential dependencies, escalate to human review

## Graph Modification During Execution

| Modification | Trigger | Process |
|---|---|---|
| Add Objective | REDIRECT command or scope expansion | Insert node, add dependencies, validate DAG |
| Remove Objective | Scope reduction (see 07 Reduce Scope) | Mark as abandoned, remove from critical path calculation |
| Add Blocker | Agent reports obstacle | Insert blocker, pause affected downstream nodes |
| Resolve Blocker | Recovery succeeds | Remove blocker, resume downstream nodes |
| Add Decision Point | Unexpected choice discovered | Insert node, pause until decided |
| Recompile | RECOMPILE command | Rebuild graph from new topology. Preserve completed nodes |

## Graph Visualization

The Mission Graph can be exported in these formats for human inspection:

1. **Summary**: Text list of objectives with status, indented by dependency depth
2. **Critical path**: The longest dependency chain, highlighted with completion percentage
3. **Blocker report**: All active blockers with affected objectives and resolution status
4. **Assumption register**: All assumption nodes with confidence levels and lock status
