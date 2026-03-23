# 54 NanoClaw Adapter

## Purpose

The NanoClaw Adapter maps GLORB framework concepts to a lightweight, constrained agent runtime designed for fast, isolated, single-purpose tasks. NanoClaw represents a class of runtimes optimized for speed and cost over capability, with strict execution boundaries.

## Adapter Contract (per 51 Runtime Adapter Contract)

### Available Tools

1. Limited tool set: typically text generation and basic analysis only
2. No file system access
3. No code execution
4. No web access (unless explicitly configured)
5. Structured output generation (JSON, YAML)

### File System Access Model

1. No file system access. All inputs must be provided in the prompt
2. All outputs are returned as response content
3. If file operations are needed, the adapter must proxy through an external service

### Session Model

1. Stateless. Each invocation is completely independent
2. No session continuity. No message history
3. All context must be provided in a single prompt per invocation
4. Extremely fast response times due to minimal overhead

### Memory Interface

| GLORB Memory Layer | NanoClaw Implementation |
|---|---|
| Working memory | The current prompt content |
| Session memory | Not supported. Each call is independent |
| Mission memory | Must be injected into the prompt by the adapter |
| Project memory | Must be injected into the prompt by the adapter |
| Reusable memory | Must be injected into the prompt by the adapter |
| Archived memory | Not practical due to prompt size constraints |

### Subagent Support

1. No native subagent support
2. The adapter can simulate subagents by making multiple independent NanoClaw calls
3. No parent-child context inheritance. Each call is isolated
4. Parallel calls are supported (independent API calls)

### Approval Model

1. No built-in approval. All calls execute immediately
2. Given the limited tool set, approval is rarely needed
3. The adapter implements approval if the NanoClaw output feeds into a sensitive action on another runtime

### Sandbox Model

1. Fully sandboxed by design. No access to external systems
2. Input is the prompt. Output is the response. Nothing else
3. No side effects possible within the NanoClaw runtime itself

### Runtime Limits

1. Strict token limits per call (typically smaller models with shorter context windows)
2. Fast execution but limited reasoning depth
3. No budget tracking built in. The adapter tracks costs
4. High throughput. Suitable for many small parallel calls

### Logging Model

1. Input prompt and output response are the complete log
2. No internal reasoning trace available
3. The adapter logs call metadata (timestamp, token count, latency)

## GLORB Concept Mapping

| GLORB Concept | NanoClaw Implementation |
|---|---|
| Agent | A single NanoClaw invocation with a task-specific prompt |
| Specialist | Prompt engineering encodes the specialist's role and constraints |
| Agent communication | Adapter chains outputs from one call as inputs to the next |
| Topology | Adapter manages the call graph. NanoClaw has no topology awareness |
| Quality gates | Adapter evaluates NanoClaw outputs against gate criteria (may use a separate NanoClaw call for evaluation) |
| Memory | All memory management is external to NanoClaw |

## Best Use Cases

NanoClaw is the right runtime when:

1. The task is well-defined and narrow (single-purpose evaluation, formatting, classification)
2. Speed and cost are the primary constraints
3. No tool access is needed
4. The task can be completed with the information provided in the prompt
5. Multiple independent subtasks can run in parallel

## When NOT to Use NanoClaw

1. Tasks requiring multi-step reasoning across turns
2. Tasks requiring file system access or code execution
3. Tasks requiring memory across calls
4. Tasks where the full context exceeds NanoClaw's prompt limits
5. Tasks requiring high abstraction depth or creative synthesis

## Typical GLORB Roles for NanoClaw

| Role | Suitability | Notes |
|---|---|---|
| Builder | Poor | No file access, no code execution |
| Researcher | Poor | No web access, no memory |
| Critic | Good | Evaluate a work product provided in the prompt |
| Verifier | Good | Check specific claims against provided evidence |
| Reviewer | Moderate | Can review if the work product fits in the prompt |
| Synthesizer | Poor | Limited context window constrains multi-source synthesis |
| Router | Good | Classify and route based on provided criteria |
| Memory Manager | Poor | No persistent storage access |

## Multi-Runtime Missions

NanoClaw is most effective as part of a multi-runtime topology:

1. Use Claude Code or OpenClaw as the primary runtime for the lead agent and complex specialists
2. Delegate narrow evaluation tasks (critique, verification, classification) to NanoClaw
3. The adapter manages the routing between runtimes
4. The lead agent on the primary runtime coordinates and the NanoClaw calls are subagent tasks
