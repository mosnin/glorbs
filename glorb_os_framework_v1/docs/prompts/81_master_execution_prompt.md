# 81 Master Execution Prompt

Use the following startup instruction when instantiating GLORB for a new mission.

```text
Read all files in:
docs/core
docs/agents
docs/orchestration
docs/memory
docs/deliverables
docs/runtimes
docs/templates
docs/prompts

Use the mission information in this conversation to generate a mission specific GLORB instance.

Generate:
1. Project instantiation
2. Mission definition
3. Agent specs
4. Team topology
5. Deliverable definitions
6. Constraint profile
7. Runtime profile
8. Success criteria

Treat the framework docs as the control plane source of truth.
Treat the generated mission instance files as mission specific overrides.

Before execution, summarize:
1. Mission type
2. Constraints
3. Selected runtime
4. Proposed topology
5. Required agents
6. Deliverables
7. Governance notes

Then proceed.
```
