# 74 Team Template

## Team Name
*A unique identifier for this team within the mission. Typically combines the topology type with the mission name. Example: "auth-design-architect-builders-critic"*

## Mission Purpose
*The specific mission objective this team is assembled to accomplish. Should be a concise statement of what the team collectively must deliver. Example: "Design the authentication module architecture and produce verified interface contracts"*

## Topology Pattern
*The team structure selected by the Topology Compiler (doc 21). Valid values: Solo agent, Planner and executor, Architect plus builders plus critic, Researcher swarm plus reducer, Strategist plus operator plus verifier. Example: "Architect plus builders plus critic"*

## Team Lead
*The name and role of the coordinating agent that receives the mission, distributes tasks, manages handoffs, and monitors for blockers. Typically the highest-authority agent in the topology. Example: "alpha-architect (Architect) — receives mission, assigns builder tasks, coordinates critic review"*

## Specialists
*List of all non-lead agents in the team, each with name, role type, and primary responsibility. One agent per line. Example: "builder-1 (Builder): implement authentication service. builder-2 (Builder): implement token validation. critic-1 (Critic): evaluate the complete design for flaws"*

## Handoff Rules
*The defined sequence in which agents pass work to each other, including what triggers each handoff and what the receiving agent must validate before accepting. Reference doc 22 for the standard handoff protocol. Example: "Architect -> builders on architecture gate pass. Builders -> critic when all components are implemented. Critic -> architect if revisions needed (max 2 rewinds)."*

## Merge Rules
*How outputs from multiple agents are combined into a unified result. Specify whether a Synthesizer is used, what merge priority applies when agents conflict, and where convergence occurs. Reference doc 24. Example: "Builder outputs merged by lead architect into unified component spec. Critic findings applied as revision notes. No Synthesizer needed — sequential pipeline."*

## Quality Gates
*List of quality gates that must pass before the team's work advances. Reference doc 26 for gate definitions. Specify which gates are active and who evaluates each. Example: "Architecture coherence gate (Critic evaluates after Architect). Implementation readiness gate (Lead evaluates before each builder starts). Shipping readiness gate (Reviewer evaluates before final deliverable)."*

## Stop Conditions
*Team-level conditions that halt all agents and trigger escalation or recovery. Distinct from individual agent stop conditions. Example: "Two pipeline rewinds exhausted without gate passing. Any agent hits FREEZE command. Budget cap reached. Lead agent detects deadlock it cannot break."*
