# 73 Specialist Agent Template

## Specialist Name
*Unique name for this specialist agent instance within the mission topology. Convention: role-type prefix plus a distinguishing suffix. Example: "critic-security-1"*

## Specialist Type
*The specialist pattern this agent instantiates from doc 14. Valid values: Strategist, Architect, Builder, Researcher, Critic, Router, Synthesizer, Verifier, Reviewer, Memory Manager. Determines default capability profile, tool permissions, and behavioral contract. Example: "Critic"*

## Capability Gap It Solves
*The specific mission requirement or capability dimension that this specialist addresses that general agents cannot cover. Reference doc 11 capability dimensions. Example: "No available agent has Adversarial rigor: High — required to stress-test the proposed authentication design"*

## Mission Scope
*The bounded slice of the mission this specialist is responsible for. Must be narrower than the overall mission objective and explicitly exclude adjacent areas. Example: "In scope: evaluate the authentication module design for security flaws and logical gaps. Out of scope: implementation review, performance testing, UI design"*

## Allowed Tools
*The specific tools this specialist may use, drawn from the runtime's available tool categories. Must not exceed the parent agent's tool scope. Valid values: None, Read, Write, Execute, Full, or a named subset. Example: "Read (all work products and project memory). No write access."*

## Memory Access
*Which GLORB memory layers this specialist may read from or write to. Valid values: Working only, Session, Mission, Project, Full. Must not exceed the parent agent's memory scope. Example: "Mission (read-only)"*

## Output Contract
*The specific artifacts this specialist commits to producing when it completes its task. Each output must be named, typed, and described. Example: "critique_report (document): severity-ranked findings with evidence and suggested improvements for each finding"*

## Temporary Or Persistent
*Whether this agent is dissolved after completing its task or retained for reuse later in the mission. Valid values: Temporary (retired on task completion), Persistent (retained for potential reuse). Example: "Temporary — retired after the critique pass is complete"*

## Evaluation Criteria
*Verifiable conditions used to evaluate this specialist's performance at mission completion. Should reference the evaluation dimensions from doc 16 relevant to this specialist type. Example: "Output quality: findings are complete and address all evaluation criteria. Scope discipline: no outputs outside the authentication module scope. Adversarial rigor: each finding includes evidence and a severity rating."*
