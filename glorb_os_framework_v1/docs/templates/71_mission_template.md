# 71 Mission Template

## Mission Summary
*A one-to-two sentence overview of what this mission is and why it is being run. Provides orientation without restating every field below. Example: "Audit the authentication module for security vulnerabilities and produce a ranked remediation plan."*

## Objective
*The single concrete, verifiable goal the mission must achieve. Must narrow scope to this specific mission, not restate the project objective. Example: "Identify all authentication vulnerabilities with severity ratings and recommended fixes."*

## Constraints
*Normalized constraint profile for this mission covering deadline, budget, quality bar, risk tolerance, reversibility, and legal or policy limits. See doc 03 for valid levels. Example: "Risk tolerance: High. Quality bar: High. Human approval: Checkpoint at major decisions."*

## Risk Level
*The overall risk level of this mission's failure or side effects. Valid values: Low, Medium, High. Drives critic intensity, quality gate selection, and human approval requirements. Example: "High"*

## Reversibility
*Whether the mission's actions can be undone if something goes wrong. Valid values: Fully reversible, Partially reversible, Irreversible. Affects topology and human approval thresholds. Example: "Partially reversible"*

## Required Rigor
*The quality and verification intensity required for this mission's outputs. Valid values: Low, Medium, High. Determines whether Critic, Verifier, and Reviewer agents are added and how many review passes occur. Example: "High"*

## Expected Deliverables
*List the specific output artifacts required when the mission completes, drawn from the mission type's deliverable structure (doc 41). Example: "Audit report with ranked findings, evidence for each finding, and required remediation actions."*

## Runtime Preference
*The preferred execution backend for this mission, or a statement that runtime selection should be determined automatically. Valid values: claude_code, openclaw, nanoclaw, auto. Example: "claude_code"*

## Human Approval Requirements
*When and what the human operator must approve before the mission proceeds. Valid values: Autonomous (no approval needed), Checkpoint at major decisions, Approve every significant action. Example: "Checkpoint at major decisions"*
