# 92 Example Team Topology: REST API Microservice Build

This is the fully compiled team topology for `mission-build-user-auth-service`. It was produced by the Topology Compiler (doc 21) from the project instance (doc 90) using the Build mission type to topology mapping (doc 21, Mission Type to Topology Mapping). Cross-reference with the agent specification in doc 91 for the full `arch-user-auth` spec.

---

## Topology Pattern

**Architect plus Builders plus Critic**

Selected because:

- Mission type is Build with structural complexity (multiple distinct components: auth logic, database layer, route handlers, test suite)
- Quality bar is Medium-High, which triggers Critic inclusion per the scaling rules (doc 21)
- Risk is Medium (security-sensitive domain), which confirms Critic inclusion
- Balanced policy permits up to 5 agents, which fits the 5-agent instance below

Agent count: 5 (within Balanced policy limit of 5).

Runtime: Claude Code. All agents execute on the Claude Code runtime. Topology validated against the Claude Code capability manifest (doc 57): subagent nesting depth 1 (satisfied: no agent spawns subagents), max concurrent agents 8 (satisfied: 5 agents), all required tool categories available.

---

## Lead Agent

| Field | Value |
|---|---|
| Name | `arch-user-auth` |
| Role type | Architect |
| Authority | Decide |
| Responsibilities | Coordinates all phases. Distributes task briefs to Builder agents. Receives Critic feedback and resolves findings. Evaluates Quality Gate criteria. Receives Verifier report and produces the final mission handoff to the human operator |
| Full spec | See doc 91 |

The Architect is the lead agent for this topology. It owns the mission execution sequence and is the single convergence point for all phase outputs.

---

## Specialist Agents

| Agent Name | Role Type | Authority | Primary Responsibility |
|---|---|---|---|
| `critic-security` | Critic | Execute | Adversarial review of the architecture specification for security gaps, anti-patterns, and constraint violations. Produces severity-ranked findings before Build phase begins |
| `builder-core` | Builder | Execute | Implements authentication business logic: password hashing (bcrypt), JWT creation and validation (RS256), and token blacklist management |
| `builder-api` | Builder | Execute | Implements FastAPI route handlers, Pydantic request/response models, SQLAlchemy ORM models, database session management, and OpenAPI annotations |
| `verifier-coverage` | Verifier | Execute | Executes the full test suite, validates coverage threshold (80%), confirms OpenAPI spec generation, and checks PEP 8 compliance |

### Capability Profiles (Abbreviated)

| Dimension | critic-security | builder-core | builder-api | verifier-coverage |
|---|---|---|---|---|
| Abstraction depth | Medium | Medium | Medium | Medium |
| Precision | High | High | High | High |
| Creativity | Low | Low | Low | Low |
| Risk tolerance | Low | Low | Low | Low |
| Adversarial rigor | High | Low | Low | Medium-High |
| Tool access | Low | High | High | High |
| Domain expertise | Medium | Medium-High | Medium-High | Medium |

Profiles follow the defaults for each specialist pattern (doc 11, Capability Profiles by Specialist Pattern).

---

## Execution Phases and Handoff Sequence

The topology executes in five ordered phases. Quality Gates are evaluated by the lead agent unless a dedicated evaluator is specified.

```
Phase 1: Context Validation
  Lead: arch-user-auth
  Gate: Sufficient Context Gate
  Condition: All inputs confirmed. Proceed to Phase 2.

Phase 2: Architecture
  Worker: arch-user-auth (acting as architect, not coordinator)
  Output: architecture_specification, interface_contracts, dependency_map,
          security_decision_record, builder_task_briefs

Phase 3: Architecture Review
  Worker: critic-security
  Gate: Architecture Coherence Gate
  Input: From arch-user-auth (Handoff)
  Output: security_critique_report

Phase 4: Build (Parallel)
  Workers: builder-core, builder-api (simultaneous)
  Gate: Implementation Readiness Gate (evaluated per builder before start)
  Inputs: Builder task briefs from arch-user-auth (Handoff per builder)
  Outputs: Core implementation files, API implementation files

Phase 5: Verification
  Worker: verifier-coverage
  Input: All source files from builder-core and builder-api (Handoff)
  Output: verification_report

Final Gate: Shipping Readiness Gate
  Evaluator: arch-user-auth (lead)
  Input: verification_report + all prior gate results
  Output: Ship / No-ship decision
```

---

## Handoff Rules

Handoffs follow the protocol defined in doc 22. Each handoff is a structured message with: artifact, completion status, open issues, and provenance references.

### Handoff 1: arch-user-auth → critic-security

| Field | Value |
|---|---|
| Trigger | Architecture phase complete. All output contract items produced |
| From | `arch-user-auth` |
| To | `critic-security` |
| Artifact | Architecture specification document (all sections) |
| Included context | Project constraints, security requirements, out-of-scope list, mission objective |
| Validation by receiver | `critic-security` confirms it has received: component list, interface contracts, dependency map, security decision record, and the evaluation criteria from the project constraints. If any section is missing, sends a Request back to `arch-user-auth` before beginning review |
| Format | Markdown document passed as Handoff message payload |

### Handoff 2: critic-security → arch-user-auth

| Field | Value |
|---|---|
| Trigger | Security critique complete |
| From | `critic-security` |
| To | `arch-user-auth` |
| Artifact | Security critique report: severity-ranked findings (High / Medium / Low), recommended resolutions per finding |
| Required action by receiver | `arch-user-auth` must resolve all High findings before issuing builder task briefs. Medium and Low findings may be deferred with documented rationale |
| Format | Structured Markdown with one section per finding: severity, description, affected component, recommended resolution |

### Handoff 3: arch-user-auth → builder-core

| Field | Value |
|---|---|
| Trigger | Architecture Coherence Gate passes. All High critique findings resolved |
| From | `arch-user-auth` |
| To | `builder-core` |
| Artifact | Builder task brief for core authentication logic: scope, acceptance criteria, interface contracts for `auth/security.py` and `auth/tokens.py`, confirmed tool list |
| Format | Markdown task brief |

### Handoff 4: arch-user-auth → builder-api

| Field | Value |
|---|---|
| Trigger | Same trigger as Handoff 3. Issued simultaneously |
| From | `arch-user-auth` |
| To | `builder-api` |
| Artifact | Builder task brief for API layer: scope, acceptance criteria, interface contracts for `api/routes/auth.py`, `api/models.py`, `db/models.py`, `db/session.py`, confirmed tool list |
| Format | Markdown task brief |
| Parallelism note | Handoffs 3 and 4 are issued in the same response. `builder-core` and `builder-api` execute simultaneously per doc 22, Parallel Execution Rules. Neither has a blocking dependency on the other's output |

### Handoff 5: builder-core + builder-api → verifier-coverage

| Field | Value |
|---|---|
| Trigger | Both Builder agents complete and signal readiness. Convergence point: neither Verifier task begins until both builders have returned results |
| From | `arch-user-auth` (lead mediates the merge) |
| To | `verifier-coverage` |
| Artifact | All implementation source files, the full test suite, the acceptance criteria list, the quality thresholds (80% coverage, ruff pass, OpenAPI generation) |
| Merge note | The lead agent performs a lightweight Inventory and Scope Alignment check (doc 24) before dispatching to Verifier: confirms both builders produced all required files, no interface contract was left unimplemented |
| Format | File list with paths + verification acceptance criteria document |

### Handoff 6: verifier-coverage → arch-user-auth

| Field | Value |
|---|---|
| Trigger | Verification complete |
| From | `verifier-coverage` |
| To | `arch-user-auth` |
| Artifact | Verification report: coverage summary (percentage + per-file), OpenAPI validation result, ruff result, per-criterion pass/fail against success criteria from doc 90 |
| Format | Structured Markdown report |

---

## Merge Rules

Merge and Reduction (doc 24) occurs at two points in this topology.

### Merge Point 1: Builder Convergence (Pre-Verification)

| Field | Value |
|---|---|
| Performed by | `arch-user-auth` (lead, acting as lightweight Synthesizer) |
| Inputs | Output from `builder-core`, output from `builder-api` |
| Conflict type expected | Soft conflicts only: minor terminology differences or duplicated utility functions. Hard conflicts (contradictory implementations of the same interface) are unexpected because the Architect issued disjoint task briefs |
| Conflict resolution | If a hard conflict is detected (e.g., both builders implemented the same function differently), `arch-user-auth` resolves by applying the interface contract as the authority. The implementation that matches the contract wins. The deviation is logged |
| Scope alignment check | Lead verifies all interface contracts from the architecture specification are implemented. Any gap is flagged as incomplete work and returned to the responsible builder |
| Redundancy removal | If both builders produced overlapping utility functions (e.g., error classes), the lead designates the canonical location per the architecture's module structure |
| Merge output | A coherent file set with no duplicate implementations and no unimplemented contracts, passed to `verifier-coverage` as Handoff 5 |

### Merge Point 2: Final Deliverable Compilation

| Field | Value |
|---|---|
| Performed by | `arch-user-auth` (lead) |
| Inputs | Architecture specification, security critique report, verification report, all implementation files |
| Provenance map | Each deliverable section is attributed to its producing agent |
| Output | Project deliverable package: implementation files + README authored by `arch-user-auth` post-verification |

---

## Quality Gates

Gates follow the definitions in doc 26. The Balanced policy activates the five gates listed below (doc 90, Selected Policy).

### Gate 1: Sufficient Context Gate

| Field | Value |
|---|---|
| When | Before `arch-user-auth` begins architectural work |
| Evaluator | `arch-user-auth` (lead) |
| Criteria | Mission objective is unambiguous. Constraint set is complete and non-contradictory. All required inputs are present (mission objective, constraint set, runtime manifest, specialist availability, out-of-scope list). At least one viable architectural approach has been identified |
| Pass | All criteria met. Architecture phase begins |
| Fail | Missing inputs or unresolved ambiguities. Escalate to human operator. Do not begin architecture |
| Retry limit | 1 retry (human provides missing information). If unresolved after 1 retry, mission is blocked |

### Gate 2: Architecture Coherence Gate

| Field | Value |
|---|---|
| When | After `arch-user-auth` produces the architecture specification, before Builder agents begin |
| Evaluator | `critic-security` |
| Criteria | All components in scope are present and necessary. No speculative additions. Component interfaces are fully typed (input/output/error contracts). Dependency graph is acyclic. Design satisfies stated constraints (security: bcrypt + RS256 + no secrets in source, scope: only in-scope components, quality: contracts precise enough for 80% test coverage target). Risk areas are identified with mitigations |
| Pass | All criteria met, or remaining issues are Low severity and documented. Build phase begins |
| Fail | Any High severity finding. Return to `arch-user-auth` for revision. Revision includes only the components cited in the High findings |
| Retry limit | 2 retries. After 2 failed revisions, escalate to human operator before a third attempt |

### Gate 3: Implementation Readiness Gate (Per Builder)

| Field | Value |
|---|---|
| When | Before each Builder begins. Evaluated separately for `builder-core` and `builder-api` |
| Evaluator | `arch-user-auth` (lead) |
| Criteria | Builder task brief includes acceptance criteria. Required tools are confirmed available (Read, Write, Edit, Bash for test execution). Scope is bounded. No unresolved blocking dependencies on the other builder's output |
| Pass | Builder begins immediately |
| Fail | Return builder task brief to the Architect for completion. Builder waits |
| Retry limit | 1 (the Architect corrects the brief) |

### Gate 4: Factual Confidence Gate

| Field | Value |
|---|---|
| When | After implementation is complete and before verification of shipping readiness. Evaluated after Builder agents finish |
| Evaluator | `critic-security` |
| Criteria | All docstrings and inline comments accurately describe behavior. README claims match actual implementation. No hallucinated capabilities (e.g., claiming Redis support when only in-memory blacklist exists). Security claims in documentation are verifiable against the code |
| Pass | Documentation is factually accurate. Proceed to Shipping Readiness Gate |
| Fail | Inaccurate claims identified. Return to responsible Builder for correction with specific findings |
| Retry limit | 2 retries. After 2 failures, flag inaccurate claims as known issues in the deliverable |

### Gate 5: Shipping Readiness Gate

| Field | Value |
|---|---|
| When | After `verifier-coverage` produces the verification report |
| Evaluator | `arch-user-auth` (lead) |
| Criteria | All prior gates have passed. Verification report shows: all four endpoints callable (pytest passes), coverage at or above 80%, OpenAPI spec generated without error, ruff exits with code 0 or documented suppressions, no plaintext password storage, JWT uses RS256. All acceptance criteria from doc 90 Success Criteria section are met. No open High severity findings from `critic-security` |
| Pass | Deliverable is compiled. Mission is complete. Human operator is notified |
| Fail | Any unmet criterion. Return to the responsible agent (builder if implementation gap, verifier if measurement gap). Document unresolvable issues as known limitations |
| Retry limit | 2 retries per responsible agent. After 2 failures, document as known limitation and present to human for disposition |

---

## Stop Conditions

The topology halts and all active agents are paused under these conditions:

| Condition | Trigger | Action |
|---|---|---|
| `FREEZE` command | Human operator | All agents pause. State is preserved. No agent sends or receives messages until `RESUME` |
| Architecture Coherence Gate fails after 2 retries | Automated | Escalate to human before third revision attempt. Mission execution pauses |
| Hard conflict at Builder convergence cannot be resolved by interface contract authority | Lead agent | Escalate to human. Document both conflicting implementations |
| Shipping Readiness Gate fails after 2 retries and the criterion is a High security requirement | Lead agent | Escalate to human. Do not document as known limitation without human approval for security criteria |
| Mission abort command | Human operator | All agents retire. Mission memory is preserved for post-mortem. No deliverable is compiled |
| Agent count budget would be exceeded by any dynamic addition | Lead agent | Do not spawn additional agents. Route any capability gap through existing agents or escalate |

---

## Appendix: Disagreement Engine Example

This section shows a concrete challenge-response-adjudication cycle from `mission-build-user-auth-service`, occurring during Phase 3 (Architecture Review). The Disagreement Engine was enabled for this mission because the Selected Policy is Balanced and the risk classification is Medium (doc 90, Selected Policy: "Disagreement engine: Enabled for decisions with risk Medium or higher").

The challenged decision involves the token blacklist implementation specified in the architecture: an in-memory Python dictionary used by `builder-core` to track invalidated JWT tokens at logout.

---

### Trigger

During the Architecture Coherence Gate evaluation, `critic-security` identified the in-memory token blacklist as a candidate for an Implementation Risk challenge. The challenge was initiated under Step 1 of the Challenge Protocol (doc 25): a Quality Gate evaluation triggered adversarial review. `critic-security` activated the Disagreement Engine and issued the following challenge message.

---

### Challenge Message

```
DISAGREEMENT ENGINE — CHALLENGE INITIATED
Challenge ID: DE-001
Issuing agent: critic-security
Receiving agent: arch-user-auth
Phase: Architecture Review (Phase 3)
Gate: Architecture Coherence Gate

TARGET
Component: auth/tokens.py — token blacklist
Specific decision: Architecture specification section "Token Invalidation" states that
invalidated JWT identifiers (jti claims) are stored in a module-level in-memory set
(blacklisted_tokens: set[str]) within auth/tokens.py. No persistence layer is
referenced for this structure.

CHALLENGER TYPE
Implementation Risk (type 6 per doc 25)

CLAIM
The in-memory token blacklist is not production-safe. A module-level set is destroyed
on every service restart, process crash, or horizontal scale event. Any token
invalidated before the restart is treated as valid after it. An attacker who holds an
invalidated token (e.g., one that was explicitly logged out) can regain authenticated
access simply by waiting for a restart or targeting a second instance in a load-balanced
deployment.

EVIDENCE
1. Python module-level state is not shared across processes. A deployment with two
   worker instances of the service will each maintain independent blacklists, meaning
   a logout on instance A leaves the token valid on instance B.
2. The architecture specification's dependency map contains no persistence component
   for the blacklist (only PostgreSQL for user records). Redis or equivalent is absent.
3. The logout endpoint acceptance criterion (doc 90, Success Criteria) requires that
   the token be "invalidated" — the word carries an implicit durability expectation
   that the in-memory design does not satisfy in any multi-process scenario.
4. The architecture's security decision record does not acknowledge this limitation
   or provide a mitigation.

SEVERITY
Significant

A Blocking classification was considered but rejected: the scope constraint (doc 90)
explicitly excludes "deployment infrastructure," which is the domain where multi-process
and multi-instance concerns are resolved. However, the risk is real enough that it
must be addressed in the architecture's security decision record before the Build
phase proceeds.

SUGGESTED RESOLUTION
One of the following:
  a) Replace the in-memory set with a Redis-backed blacklist (adds a dependency;
     may conflict with the scope constraint's exclusion of deployment infrastructure).
  b) Document the in-memory limitation explicitly in the architecture specification's
     security decision record as a known constraint, with a stated upgrade path. Mark
     the current implementation as scope-appropriate with a note that production
     deployment requires a persistent blacklist.
  c) Redesign logout to use short-lived tokens only (reduce token TTL so the
     invalidation window is acceptable) — eliminates the need for a blacklist
     but changes the token refresh contract.
```

---

### Response Message

```
DISAGREEMENT ENGINE — RESPONSE
Challenge ID: DE-001
Responding agent: arch-user-auth
Response type: Rebut

POSITION
The challenge is technically accurate but the severity assessment does not account
for the authoritative scope constraint. The project instance (doc 90, Scope Constraints)
explicitly lists "deployment infrastructure" as out of scope. A persistent, multi-process
token blacklist is a deployment infrastructure concern: it requires a running Redis
cluster, connection management, TTL configuration, and failure-mode handling for the
cache layer — none of which are within the defined scope of this mission.

The Architecture Coherence Gate criterion (doc 92, Gate 2) requires that the design
"satisfies stated constraints (scope: only in-scope components)." Adding Redis to
satisfy a deployment concern that the project instance has already excluded would
violate that criterion. The gate would fail on scope grounds if Redis were introduced.

COUNTER-EVIDENCE
1. Doc 90, Scope Constraints, Out of Scope: "deployment infrastructure, production
   secrets management, rate limiting." A Redis instance is deployment infrastructure.
   The scope constraint is authoritative and was set by the human operator at mission
   instantiation.
2. The in-memory blacklist satisfies the logout invalidation criterion as stated:
   within a single process, the token is invalidated correctly. The acceptance criterion
   in doc 90 does not specify durability across restarts. The criterion is: "logout
   endpoint (invalidates token)." The implementation satisfies this literally.
3. Doc 90, builder-core responsibility: "token blacklist (in-memory or Redis stub)."
   The project instance itself anticipated an in-memory implementation as a valid
   choice, and the parenthetical "(in-memory or Redis stub)" was written knowing that
   a full Redis deployment is out of scope.

REGARDING THE SUGGESTED RESOLUTIONS
  a) Rejected: adding Redis violates the scope constraint.
  c) Rejected: changing the token refresh contract is a scope expansion, not a fix,
     and would require human approval.
  b) Accepted: the architecture specification's security decision record will be
     updated to explicitly acknowledge the in-memory limitation, state that it is
     scope-appropriate, and document a concrete upgrade path (swap the module-level
     set for a Redis client call in auth/tokens.py — the interface contract does not
     change, only the backing store).

PROPOSED ACTION
The architecture specification will be revised to add the following entry to the
security decision record before builder task briefs are issued:

  "Token blacklist: In-memory implementation (set[str]). Scope-appropriate per
  doc 90: deployment infrastructure is out of scope. Known limitation: blacklist
  state is not durable across restarts or shared across process instances. Upgrade
  path: replace the module-level set with a Redis client in auth/tokens.py; the
  is_blacklisted() and blacklist_token() interface contracts remain unchanged."

No change to the implementation design is required. The builder task brief for
builder-core will reference this security decision record entry.
```

---

### Adjudication Record

The response constitutes a Rebut. Per doc 25, Step 4 (Adjudication), a Significant-severity challenge that has received a reasoned rebuttal does not block progress and does not require further cycles. However, `critic-security` did not withdraw the challenge, leaving the resolution to be formally adjudicated by the lead agent.

```
DISAGREEMENT ENGINE — ADJUDICATION RECORD
Challenge ID: DE-001
Adjudicator: arch-user-auth (lead agent, acting in adjudicator role per doc 25,
             Step 4: Significant severity, one cycle complete)
Date: Mission execution session 1, Phase 3

POSITIONS EVALUATED
  Challenger (critic-security): In-memory blacklist is not production-safe.
    Severity: Significant. Evidence: multi-process state isolation, absence of
    Redis in dependency map, implicit durability expectation in the logout criterion.
  Producer (arch-user-auth): Scope constraint (doc 90) excludes deployment
    infrastructure. In-memory implementation is explicitly anticipated by the project
    instance. The logout acceptance criterion does not specify durability. Proposed
    fix: update the security decision record with an explicit documented limitation
    and upgrade path.

EVALUATION AGAINST PROJECT CONSTRAINTS
The scope constraint in doc 90 is authoritative. It was established by the human
operator at mission instantiation and cannot be overridden by agent consensus. The
constraint explicitly lists "deployment infrastructure" as out of scope. A persistent
cache layer (Redis) is deployment infrastructure. Requiring it would violate the
scope boundary.

The challenger's technical assessment is correct: the in-memory blacklist does not
survive restarts and does not scale horizontally. This is a real limitation. However,
the resolution rules in doc 25 require evaluating which position is supported by
stronger evidence relative to the project's own constraints. The scope constraint
is the stronger authority here.

The producer's proposed action — adding an explicit documented limitation to the
security decision record — directly addresses the challenger's concern without
violating the scope constraint. It also preserves the upgrade path for when the
service moves to a production deployment context.

OUTCOME
Rules in favor of: arch-user-auth (producer)

The in-memory token blacklist design stands. The architecture specification's
security decision record will be updated as proposed in the Response message before
builder task briefs are issued to builder-core and builder-api.

SEVERITY RECLASSIFICATION
The Disagreement Engine records this finding as:

  Medium severity — documented limitation, not a design flaw.

"Medium severity" means the limitation is real and must be visible to any future
consumer of this codebase, but it does not constitute a design error given the
stated scope. The finding is not a High severity defect and does not block the
Build phase.

IMPACT ON WORK PRODUCT
  - Architecture specification, security decision record: updated with explicit
    in-memory blacklist limitation and Redis upgrade path. (Revision made before
    Handoff 3 and Handoff 4 are issued.)
  - builder-core task brief: will reference the security decision record entry so
    that builder-core implements auth/tokens.py with awareness of the documented
    limitation.
  - No implementation change required. No interface contract change required.
  - The finding is NOT an unresolved High severity finding. The Architecture
    Coherence Gate criterion "zero unresolved High findings at build start"
    (doc 90, Success Criteria) is satisfied.

DOES THIS BLOCK PROGRESS?
No. Per doc 25, a Significant-severity challenge that has been adjudicated by the
lead agent does not block progress.
```

---

### Provenance Graph Entry

Per doc 25 (Disagreement Record), the complete cycle is recorded in the Provenance Graph.

```
PROVENANCE GRAPH — DISAGREEMENT ENGINE ENTRY
Node ID: PROV-DE-001
Mission: mission-build-user-auth-service
Phase: Phase 3 — Architecture Review

Challenge details
  ID: DE-001
  Issued by: critic-security
  Target: auth/tokens.py, token blacklist (in-memory set)
  Challenger type: Implementation Risk (type 6)
  Claim: In-memory blacklist is not durable across restarts or process instances
  Severity (as issued): Significant

Response details
  Responding agent: arch-user-auth
  Response type: Rebut
  Counter-authority: doc 90 scope constraint ("deployment infrastructure" out of scope)
  Proposed action: Update security decision record with documented limitation and
                   upgrade path; no implementation change

Adjudication details
  Adjudicator: arch-user-auth (lead)
  Outcome: Challenge rejected. Producer's position upheld
  Severity reclassification: Medium — documented limitation, not a design flaw
  Authority applied: doc 90 scope constraint (authoritative, set by human operator)

Resolution
  Work product change: Architecture specification, security decision record — one
    new entry added acknowledging the in-memory limitation and stating the Redis
    upgrade path
  Implementation change: None
  Interface contract change: None
  Gate impact: Architecture Coherence Gate — passes. Zero unresolved High findings

Links
  Challenge message: DE-001
  Response message: DE-001-R
  Adjudication record: DE-001-ADJ
  Affected specification section: Architecture Specification v1, Security Decision
    Record, entry "Token blacklist"
  Downstream task brief: builder-core task brief, section "Known Limitations"
```
