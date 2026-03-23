# 41 Deliverable Compiler

## Purpose

The Deliverable Compiler assembles the final mission output from agent work products, Quality Gate results, and Provenance Graph data. It maps each mission type to a deliverable structure, populates each section, and ensures completeness and traceability.

## Compilation Process

### Step 1: Select Deliverable Template

Based on the mission type (see 42 Mission Types), select the appropriate section structure from the mappings below.

### Step 2: Gather Inputs

1. Collect all agent outputs from Mission Memory (see 30)
2. Collect all Quality Gate results (see 26)
3. Collect the Provenance Graph for attribution (see 06)
4. Collect the Assumption Register from the Mission Graph (see 27)

### Step 3: Populate Sections

1. Map each agent output to the deliverable section it contributes to
2. If multiple agents contributed to the same section, use the merged output from the Merge and Reduction process (see 24)
3. Add provenance annotations to each section (which agent produced it, what evidence supports it)

### Step 4: Quality Check

1. Verify every section has content (flag empty sections as gaps)
2. Verify all Quality Gate results are referenced
3. Verify the deliverable addresses the original mission objective
4. Check for internal consistency across sections

### Step 5: Compile

1. Assemble sections in the template order
2. Add front matter: mission name, date, topology used, policy applied
3. Add appendices if Rigor is High: full Provenance Graph, assumption register, evaluation records

## Example Mappings by Mission Type

### Exploration

1. Research question
2. Methodology
3. Findings (structured by theme)
4. Source attribution
5. Confidence assessments
6. Open questions
7. Recommended next steps

### Decision

1. Decision context
2. Options considered
3. Evaluation criteria
4. Tradeoff analysis
5. Recommendation with rationale
6. Risks of recommended option
7. Sensitivity analysis (what would change the recommendation)
8. Next actions

### Design

1. Design objectives
2. Architecture overview
3. Component specifications
4. Interface contracts
5. Dependency map
6. Risk assessment
7. Build order
8. Open design questions

### Build

1. Build objectives
2. Architecture reference (from Design phase if applicable)
3. Implementation summary
4. Components delivered
5. Test results
6. Known limitations
7. Deployment instructions
8. Verification report

### Audit

1. Audit scope
2. Methodology
3. Findings (ranked by severity)
4. Evidence for each finding
5. Recommendations
6. Compliance status
7. Required remediation actions

### Negotiation

1. Negotiation context
2. Stakeholder positions
3. Points of agreement
4. Points of disagreement
5. Proposed resolution
6. Tradeoffs accepted
7. Remaining open items
8. Next steps

### Synthesis

1. Synthesis objective
2. Source materials summary
3. Integrated analysis
4. Key themes
5. Contradictions and their resolution
6. Unified conclusions
7. Gaps in source material

### Execution

1. Execution objective
2. Plan summary
3. Actions taken
4. Results per action
5. Deviations from plan
6. Issues encountered and resolution
7. Final status
8. Follow-up actions

## Section Quality Criteria

Each section is evaluated against these criteria during the Quality Check (Step 4):

| Criterion | Requirement |
|---|---|
| Completeness | Section addresses its topic fully. No obvious gaps |
| Traceability | Section content links to at least one Provenance Graph record |
| Consistency | Section does not contradict other sections in the deliverable |
| Clarity | Section is understandable without requiring reference to agent-internal context |
| Accuracy | Factual claims are sourced or flagged as assumptions |

## Deliverable Versioning

1. Each compilation produces a version number (v1, v2, etc.)
2. Recompilation after a Quality Gate failure produces a new version
3. Prior versions are preserved in Mission Memory for comparison
4. The final delivered version is marked as the canonical version

## Incremental Compilation

For long-running missions, the Deliverable Compiler can produce partial deliverables:

1. Sections are compiled as their contributing agents complete work
2. Partial deliverables are marked as draft with a list of pending sections
3. Each incremental compilation passes through available Quality Gates
4. The final compilation assembles all sections and runs the full gate sequence
