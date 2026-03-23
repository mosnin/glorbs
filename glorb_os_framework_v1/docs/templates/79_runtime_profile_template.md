# 79 Runtime Profile Template

## Runtime Name
*The unique identifier for this runtime as declared in its Capability Manifest (doc 57). Valid values: claude_code, openclaw, nanoclaw, or the name of a custom runtime adapter. Example: "claude_code"*

## Tool Model
*The categories of tools available on this runtime, drawn from the Capability Manifest tool_categories field (doc 57). Valid values: file_read, file_write, code_execution, web_search, web_fetch, structured_output (list all that apply). Determines which agent roles can run on this runtime. Example: "file_read, file_write, code_execution, web_search, web_fetch, structured_output"*

## Session Model
*Whether this runtime maintains state across agent turns within a mission, from the Capability Manifest session_model field (doc 57). Valid values: stateful (context persists across turns), stateless (each call is independent, context must be passed explicitly). Drives whether async orchestration mode is needed. Example: "stateful"*

## Memory Interface
*The GLORB memory layers this runtime supports, from the Capability Manifest memory_layers_supported and memory_persistence fields (doc 57). List supported layers and persistence duration. Valid persistence values: none, session, mission, permanent. Example: "Supported layers: working, session, mission, project, reusable, archived. Persistence: permanent (via filesystem and git)"*

## File Access
*How this runtime handles file system operations — whether access is direct, API-mediated, or unavailable. Reference docs 52, 53, 54 for per-runtime details. Example: "Full read-write access to working directory and subdirectories. Atomic per tool call. No restrictions within project scope."*

## Subagent Support
*Whether this runtime can spawn and coordinate subagents, from the Capability Manifest subagent_nesting_depth and max_concurrent_agents fields (doc 57). Specify nesting depth and concurrency limit. Example: "Nesting depth: 1 (subagents cannot spawn their own subagents). Max concurrent agents: 10."*

## Approval Model
*How human approval is implemented on this runtime, from the Capability Manifest approval_model field (doc 57). Valid values: built_in (native user prompts), adapter_provided (adapter implements approval gates), none (all calls execute immediately). Example: "built_in — tool calls may require user approval based on configured permission mode"*

## Limits
*The key operational constraints of this runtime: maximum context size (tokens), command timeout, rate limits, and any other hard boundaries declared in the Capability Manifest (doc 57). Example: "Max context size: 200,000 tokens. Bash timeout: 120 seconds (configurable to 600s). No built-in token budget tracking."*

## Notes
*Any additional characteristics, known limitations, workarounds, or best practices specific to this runtime that mission designers should be aware of. Reference the adapter doc for this runtime (docs 52, 53, or 54). Example: "No persistent state between separate invocations unless written to files. Use CLAUDE.md as the project memory anchor. Subagents do not share context — include relevant context in each subagent prompt."*
