# 70 Project Instantiation Template

## Project Name
*A unique human-readable identifier for this project within the GLORB system. Example: "auth-system-redesign"*

## Mission Name
*A specific name for the mission being instantiated under this project. Should reflect the mission's goal. Example: "design-authentication-module"*

## Mission Type
*The category of mission being run, which drives topology selection and deliverable structure. Valid values: Exploration, Decision, Design, Build, Audit, Negotiation, Synthesis, Execution. Example: "Design"*

## Runtime Target
*The execution backend for this mission. Valid values: claude_code, openclaw, nanoclaw, or a multi-runtime specification. Example: "claude_code"*

## Core Objective
*A single concrete statement of what the mission must accomplish. Must be specific and verifiable, not a restatement of the project name. Example: "Produce a module boundary design and interface contracts for the authentication system"*

## Constraints
*The operational limits that shape how the mission is executed. Specify deadline, budget, quality bar, risk tolerance, and any legal or policy boundaries. Reference doc 03 for normalization. Example: "Quality bar: High. Deadline: Low. Budget: Medium."*

## Selected Policy
*The named operational preset that governs topology, critique intensity, memory depth, and autonomy. Valid values: Fastest acceptable, Highest rigor, Lowest cost, Balanced, High exploration, Low hallucination bias, Human in loop required, Autonomous unless blocked. Example: "Balanced"*

## Proposed Topology
*The team structure selected to execute this mission. Valid values: Solo agent, Planner and executor, Architect plus builders plus critic, Researcher swarm plus reducer, Strategist plus operator plus verifier. Example: "Architect plus builders plus critic"*

## Required Agents
*List each agent role needed in this topology, with name and specialist pattern. One agent per line. Example: "alpha-architect (Architect), builder-1 (Builder), critic-1 (Critic)"*

## Required Deliverables
*List the output artifacts the mission must produce, matching the mission type's deliverable structure from doc 41. Example: "Architecture blueprint with component specs, interface contracts, and dependency map"*

## Success Criteria
*List verifiable pass/fail conditions that determine mission completion. Each criterion must be testable at a Quality Gate. Example: "All component interfaces are defined. No circular dependencies. Constraints are satisfied by the design."*
