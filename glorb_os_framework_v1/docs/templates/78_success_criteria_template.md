# 78 Success Criteria Template

## Outcome Quality
*Verifiable criteria confirming the mission's primary output meets its stated objective and quality bar. Each criterion must be testable at the Shipping Readiness Gate (doc 26). Example: "All component interfaces are defined with typed input/output contracts. Architecture contains no circular dependencies. Design satisfies all stated constraints from the constraint profile."*

## Constraint Compliance
*Pass/fail checks confirming the mission operated within all constraints specified in the constraint profile (doc 03). Each check maps to a constraint dimension. Example: "Budget: token usage did not exceed approved cap. Deadline: deliverable produced before the stated deadline. Legal: no PII written to unapproved storage."*

## Deliverable Completeness
*Verification that every required section of the mission's deliverable is populated and meets the section quality criteria from doc 41 (completeness, traceability, consistency, clarity, accuracy). Example: "All 8 required Design deliverable sections are populated. Each section links to at least one Provenance Graph record. No section contradicts another."*

## Risk Handling
*Criteria confirming that identified risks were addressed, mitigated, or explicitly documented as accepted. Required when mission Risk tolerance is Low or Risk level is High. Example: "Each identified risk area in the architecture has a documented mitigation strategy. Risks accepted without mitigation are listed with rationale in the deliverable's risk assessment section."*

## Runtime Fit
*Confirmation that the topology and agent configuration were compatible with the selected runtime, validated against the runtime's Capability Manifest (doc 57). Example: "All agents used only tools available on the selected runtime. Subagent nesting depth did not exceed the runtime's limit. No runtime capability gaps were left unresolved."*

## Human Approval Compliance
*Verification that all human approval requirements specified in the constraint profile were honored — approvals were obtained at the correct points and no agent proceeded past a required gate without sign-off. Example: "Human approved the architecture design before builders began. Human reviewed the final Shipping Readiness Gate result before deliverable was compiled."*
