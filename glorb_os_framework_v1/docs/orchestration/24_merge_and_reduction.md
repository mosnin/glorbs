# 24 Merge And Reduction

## Purpose

Merge and Reduction is the process of combining outputs from multiple agents into a single coherent result. It handles conflict detection, evidence weighting, redundancy removal, and final synthesis. This is typically performed by a Synthesizer agent (see 14 Specialist Agent Patterns).

## Merge Concerns

1. Conflict detection
2. Priority rules
3. Evidence weighting
4. Scope alignment
5. Redundancy removal
6. Final synthesis

## When Merge Occurs

1. After parallel agents complete their work streams and converge (see 22 Team Orchestration convergence point)
2. After a Researcher swarm returns multiple findings on the same topic
3. When the Disagreement Engine (see 25) produces competing positions that must be resolved
4. Before deliverable compilation (see 41) when multiple agents contributed sections

## Merge Algorithm

### Step 1: Inventory

The Synthesizer collects all outputs to be merged and catalogs them.

1. List each contributing agent, its role, and its output
2. Identify the scope each output covers (which part of the mission objective it addresses)
3. Flag outputs that cover overlapping scope

### Step 2: Conflict Detection

For each pair of overlapping outputs, the Synthesizer evaluates whether they conflict.

1. **No conflict**: Outputs are complementary. They cover different aspects of the same scope or agree on shared aspects
2. **Soft conflict**: Outputs differ in emphasis, framing, or level of detail but do not contradict each other. Resolution: Merge by selecting the more detailed or better-evidenced version for each aspect
3. **Hard conflict**: Outputs make contradictory claims or recommend incompatible approaches. Resolution: Proceed to evidence weighting

### Step 3: Evidence Weighting

For hard conflicts, the Synthesizer evaluates the evidence behind each position.

| Factor | Weight |
|---|---|
| Sourced from tool output (verified data) | Highest |
| Sourced from project or reusable memory | High |
| Supported by reasoning chain with cited premises | Medium |
| Based on agent judgment without explicit evidence | Low |
| Unsourced assertion | Reject unless flagged as assumption |

Resolution rules:

1. The position with the highest total evidence weight wins
2. If weights are equal, the position from the agent with the higher domain expertise capability score wins (see 11 Agent Capability Model)
3. If still tied, the conflict is escalated to the Disagreement Engine (see 25). If the Disagreement Engine is disabled, escalate to the lead agent
4. If the lead cannot resolve, escalate to human review
5. The losing position and its evidence are preserved in the Provenance Graph (see 06) as a documented alternative

### Step 4: Scope Alignment

After conflicts are resolved, the Synthesizer verifies that the merged result covers the full scope of the mission objective.

1. Map each section of the merged output to the mission objective it addresses
2. Identify any gaps: parts of the objective with no coverage
3. For gaps: check if a contributing agent was assigned that scope. If yes, flag it as incomplete work and return to the agent (see 07 Failure Recovery). If no, flag it as a scope gap and notify the lead

### Step 5: Redundancy Removal

1. Identify duplicate information across merged sections
2. Consolidate duplicates into the section where the information is most contextually relevant
3. Replace other occurrences with cross-references
4. Preserve all unique perspectives even if they overlap in topic

### Step 6: Final Synthesis

The Synthesizer produces the merged output.

1. Organize sections according to the deliverable schema (see 40 Output Schemas)
2. Ensure consistent terminology across sections from different agents
3. Add transition text between sections where necessary for coherence
4. Annotate each section with its provenance (which agent contributed it)
5. Flag any remaining assumptions, uncertainties, or documented alternatives

## Merge Output Format

The merged output includes:

1. **Unified content**: The synthesized deliverable sections
2. **Provenance map**: Per-section attribution to contributing agents
3. **Conflict log**: All detected conflicts, how they were resolved, and what evidence determined the outcome
4. **Coverage report**: Mapping of mission objective to merged output sections, with any gaps noted
5. **Assumption register**: All assumptions carried into the merged output with their confidence levels

## Merge Failure

A merge fails when:

1. A hard conflict cannot be resolved through evidence weighting or escalation
2. Scope gaps cover more than 30 percent of the mission objective
3. More than half of contributing agents' outputs are internally inconsistent

On merge failure:

1. Log the failure to the Provenance Graph with the specific cause
2. Match the failure to Recovery Strategy (see 07 Failure Recovery, Failed merge class)
3. The lead agent may recompile the topology to address the root cause (e.g., replace conflicting agents, add a Researcher to fill gaps)
4. Maximum 2 merge attempts per convergence point. After 2 failures, escalate to human review
