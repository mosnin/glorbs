# 51 Runtime Adapter Contract

## Purpose

This document defines the categories every runtime adapter must address to plug into GLORB. It provides the high-level contract; see 56 Adapter Interface Spec for formal method signatures, data structures, and compliance levels.

## Every Adapter Should Declare

1. Available tools — implemented by `list_available_tools()`, `execute_tool()` (see 56)
2. File system access model — declared in the CapabilityManifest `tool_categories` field (see 57)
3. Session model — declared in the CapabilityManifest `session_model` field (see 57)
4. Memory interface — implemented by `retrieve_memory()`, `persist_memory()`, `compress_memory()` (see 56)
5. Subagent support — declared in the CapabilityManifest `subagent_nesting_depth` field (see 57)
6. Approval model — implemented by `request_approval()`, declared in the CapabilityManifest `approval_model` field (see 56, 57)
7. Sandbox model — declared in the CapabilityManifest `sandbox_model` field (see 57)
8. Runtime limits — declared in the CapabilityManifest `max_context_size`, `max_concurrent_agents` fields (see 57)
9. Logging model — implemented by `log_event()`, `get_metrics()` (see 56)
