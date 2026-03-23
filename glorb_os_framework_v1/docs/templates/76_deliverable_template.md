# 76 Deliverable Template

## Deliverable Name
*A unique name for this deliverable that identifies the mission and output type. Convention: mission-name plus deliverable type. Example: "auth-system-redesign-architecture-blueprint"*

## Mission Type
*The mission type that produced this deliverable. Determines which section structure from doc 41 applies. Valid values: Exploration, Decision, Design, Build, Audit, Negotiation, Synthesis, Execution. Example: "Design"*

## Audience
*The intended consumer(s) of this deliverable — human stakeholders, downstream agents, or both. Determines level of detail, terminology, and format. Example: "Human engineering lead (for approval) and Builder agents (as implementation input)"*

## Required Sections
*The mandatory sections for this deliverable based on the mission type mapping in doc 41. Every required section must have content before the Shipping Readiness Gate can pass. Example: "Design objectives, Architecture overview, Component specifications, Interface contracts, Dependency map, Risk assessment, Build order, Open design questions"*

## Optional Sections
*Additional sections that may be included based on policy, rigor level, or specific mission needs. Include when Rigor is High or when provenance annotations are required by the active policy (doc 04). Example: "Provenance annotations (required when Rigor is High), Assumption register, Evaluation records from Quality Gates"*

## Risk Notes
*Known risks, uncertainties, or assumptions embedded in this deliverable that the audience should be aware of. Each note should state the risk and its potential impact. Example: "Interface contracts assume the existing database schema remains unchanged. If schema changes, contracts must be revised before build begins."*

## Open Questions
*Unresolved issues that could not be answered within the mission scope but that the deliverable's audience must address before proceeding. Each question should state what is unknown and what decision it blocks. Example: "Authentication token expiry policy not decided — blocks token validation component spec. Requires stakeholder decision before builders begin."*
