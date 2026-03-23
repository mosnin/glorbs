# GLORB

GLORB is a runtime agnostic agent operating system and orchestration framework.

It is designed to sit above execution backends such as Claude Code, OpenClaw, NanoClaw, and later a native GLORB runtime.

## What GLORB Does

GLORB defines:

1. How missions are modeled
2. How agents are specified
3. How specialist agents are created
4. How teams of agents are orchestrated
5. How memory is governed
6. How deliverables are compiled
7. How runtime backends are adapted without changing the control plane

## What GLORB Is Not

GLORB is not:

1. A single prompt
2. A single runtime
3. A chat wrapper
4. A fixed bundle of hard coded agents

GLORB is a control plane for intelligence systems.

## Core Layers

### Core
Reasoning, constraints, policy, governance, memory constitution

### Agents
Primitives, capability model, lifecycle, creation framework, foundry

### Orchestration
Routing, topology compilation, teams, subagents, merge logic, quality gates

### Deliverables
Output schemas, mission types, workflow archetypes, deliverable compiler

### Runtime Adapters
Backend compatibility for Claude Code, OpenClaw, NanoClaw, and future runtimes

### Templates
Project instantiation templates for new GLORB missions

## Repository Structure

```text
glorb_os_framework_v1/
  README.md
  QUICKSTART.md
  CLAUDE.md
  docs/
    core/
    agents/
    orchestration/
    memory/
    deliverables/
    runtimes/
    templates/
    prompts/
    examples/
  schemas/
  instances/
    templates/
```

## Operating Model

1. Read the framework docs
2. Generate a mission specific instance from templates
3. Select a runtime backend
4. Compile the mission into agents, topology, memory policy, governance policy, and deliverables
5. Execute through the chosen runtime while GLORB remains the control plane

## Design Goal

The goal of GLORB is to make agent systems:

1. Modular
2. Portable
3. Governable
4. Composable
5. Measurable
6. Evolvable
