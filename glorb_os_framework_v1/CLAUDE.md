# GLORB Repository Operating Rules

## Purpose

This repository is a control plane framework for agent systems.
Claude should use it as a source of truth for architecture, orchestration, governance, memory, runtime adaptation, and project instantiation.

## Required Read Order

Before doing meaningful work in this repository:

1. Read `README.md`
2. Read `QUICKSTART.md`
3. Read all files in `docs/core/`
4. Read all files in `docs/agents/`
5. Read all files in `docs/orchestration/`
6. Read all files in `docs/memory/`
7. Read all files in `docs/deliverables/`
8. Read all files in `docs/runtimes/`
9. Read all files in `docs/templates/`
10. Read all files in `docs/prompts/`

## Source Of Truth Hierarchy

1. Generated mission instance files
2. `docs/core/*`
3. `docs/agents/*`
4. `docs/orchestration/*`
5. `docs/memory/*`
6. `docs/deliverables/*`
7. `docs/runtimes/*`
8. `docs/templates/*`
9. `docs/prompts/*`

## Core Rules

1. Treat GLORB as runtime agnostic unless a runtime is explicitly selected.
2. Do not invent agent structures outside the framework unless justified.
3. Do not invent orchestration logic outside the framework unless justified.
4. Preserve governance, permission, and authority boundaries.
5. Prefer reusable agent and topology patterns over ad hoc design.
6. Generate mission instance files before pretending the project is fully instantiated.
