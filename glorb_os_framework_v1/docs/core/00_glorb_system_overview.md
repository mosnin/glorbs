# 00 GLORB System Overview

## Purpose

GLORB is a runtime agnostic control plane for intelligence systems.

It governs how missions are modeled, how agents are created, how teams are orchestrated, how memory is governed, how deliverables are compiled, and how execution backends are selected.

## Core Thesis

Most agent systems are weak because they begin with execution.
GLORB begins with architecture.

## Control Plane Versus Runtime

### GLORB Owns

1. Mission typing
2. Agent specification
3. Capability modeling
4. Team topology
5. Routing logic
6. Memory policy
7. Governance policy
8. Deliverable schemas
9. Runtime selection policy

### Runtime Owns

1. Session execution
2. Tool calls
3. Sandboxing
4. File system access
5. Native runtime logging
6. Backend specific limits

## Final Principle

GLORB should feel like an operating system for agent architectures, not a bundle of prompts.
