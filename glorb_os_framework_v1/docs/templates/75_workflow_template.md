# 75 Workflow Template

## Workflow Name
*A descriptive name for this workflow instance that reflects the archetype and mission context. Reference the archetypes from doc 43. Example: "idea-to-architecture for auth-system-redesign"*

## Mission Type
*The mission type this workflow is designed for, which determines the workflow structure and expected deliverable. Valid values: Exploration, Decision, Design, Build, Audit, Negotiation, Synthesis, Execution. Example: "Design"*

## Steps
*Ordered list of the workflow phases, each describing what happens, which agent(s) are active, and what output is produced. Map to the archetype steps from doc 43 and deliverable sections from doc 41. Example: "1. Architect produces component boundary design. 2. Builders implement per-component specs. 3. Critic evaluates the complete design. 4. Reviewer produces final assessment."*

## Decision Points
*Branch conditions within the workflow where the path diverges based on an evaluation result, quality gate outcome, or human decision. Specify the condition and the resulting path for each branch. Example: "After Architecture coherence gate: Pass -> proceed to builders. Fail -> return to Architect for revision (max 2 retries, then escalate to human)."*

## Quality Gates
*The gates (from doc 26) positioned within this workflow, specifying when each gate fires and what must pass before the workflow advances. Example: "Sufficient context gate: before Architect begins. Architecture coherence gate: after Architect, before builders. Shipping readiness gate: after Reviewer, before Deliverable Compiler."*

## Failure Paths
*How the workflow handles each category of failure: gate failure, agent failure, deadlock, and resource exhaustion. Reference doc 07 for recovery strategies. Example: "Gate failure: return to responsible agent, max 2 retries, then escalate to human. Agent failure: lead reassigns task to equivalent agent or escalates. Resource exhaustion: suspend workflow, notify human."*

## Deliverable Output
*The final artifact produced when the workflow completes successfully, mapped to the mission type's deliverable structure from doc 41. Example: "Design deliverable: architecture overview, component specifications, interface contracts, dependency map, risk assessment, build order, open design questions"*
