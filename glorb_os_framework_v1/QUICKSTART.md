# QUICKSTART

## Goal

Use GLORB to instantiate a new mission specific agent system on top of an existing runtime.

## Step 1

Read these files first:

1. `docs/core/00_glorb_system_overview.md`
2. `docs/core/01_core_principles.md`
3. `docs/agents/10_agent_primitives.md`
4. `docs/orchestration/20_router.md`
5. `docs/orchestration/21_topology_compiler.md`
6. `docs/runtimes/50_runtime_abstraction_layer.md`
7. `docs/templates/70_project_instantiation_template.md`
8. `docs/prompts/80_kickoff_system.md`

## Step 2

Create a mission instance from the templates.

At minimum generate:

1. Project instance
2. Mission definition
3. Initial agent set
4. Initial team topology
5. Deliverable definition
6. Runtime profile
7. Success criteria

## Step 3

Select a runtime backend.

Supported design targets:

1. Claude Code
2. OpenClaw
3. NanoClaw

## Step 4

Compile the mission into:

1. Agent specs
2. Topology
3. Memory policy
4. Governance policy
5. Deliverable schema
6. Runtime adapter mapping

## Step 5

Run the startup logic in `docs/prompts/81_master_execution_prompt.md`
