# 90 Example Project Instance: Build a REST API Microservice

## Project Name

`project-rest-api-microservice-v1`

## Mission Name

`mission-build-user-auth-service`

## Mission Type

Build

A Build mission produces a concrete implemented artifact from a specification. The path is defined: the system knows what to produce. Agent topology defaults to Architect plus Builders plus Critic, upgraded here due to medium risk and medium-high quality requirements.

## Runtime Target

Claude Code

Selected because the mission requires native file read/write access, Bash execution for running tests, and full repository context. Session persistence via the filesystem satisfies the mission memory requirement. See runtime profile in `93_example_runtime_profile.md` and the selection rationale in doc 55.

## Core Objective

Design and implement a production-ready REST API microservice for user authentication. The service must expose endpoints for user registration, login, token refresh, and logout. It must be implemented in Python using FastAPI, backed by PostgreSQL, and return JWT tokens. The implementation must include unit tests with at least 80% code coverage and an OpenAPI specification auto-generated from the route definitions.

## Constraints

### Time

| Field | Value |
|---|---|
| Deadline classification | Medium |
| Target completion | Within the current session or two sessions maximum |
| Rationale | Soft deadline. Implementation is bounded in scope. Architect phase must complete before Builder begins |

### Scope

| Field | Value |
|---|---|
| In scope | FastAPI application skeleton, user registration endpoint, login endpoint (returns JWT), token refresh endpoint, logout endpoint (invalidates token), PostgreSQL schema via SQLAlchemy ORM, unit test suite, OpenAPI spec generation |
| Out of scope | Frontend, email verification flow, OAuth2 provider integration, deployment infrastructure, production secrets management, rate limiting |
| Scope classification | Medium. Well-defined boundary with no ambiguity |

### Quality

| Field | Value |
|---|---|
| Quality bar | High |
| Unit test coverage | Minimum 80% |
| Code style | PEP 8 compliant, enforced via `ruff` |
| API contract | All endpoints must match the OpenAPI spec produced by the Architect. No undocumented fields in request or response bodies |
| Security | Passwords must be hashed with bcrypt. JWT must use RS256 signing. No secrets in source files |

### Budget

| Field | Value |
|---|---|
| Budget classification | Medium |
| Agent count cap | 5 (per Balanced policy) |
| Token tracking | GLORB layer monitors agent count and task complexity |

### Risk

| Field | Value |
|---|---|
| Risk tolerance | Medium |
| Primary risk | Security defects in the authentication flow (password storage, token signing) |
| Mitigation | Critic agent performs adversarial review of the security-sensitive components before Builder produces final output |

## Selected Policy

**Balanced**

| Parameter | Value |
|---|---|
| Max agent count | 5 |
| Critique intensity | Single Critic, one review pass |
| Quality gates | Sufficient Context, Architecture Coherence, Implementation Readiness, Factual Confidence, Shipping Readiness |
| Memory depth | Working, session, and mission memory |
| Deliverable depth | Standard with rationale section |
| Disagreement engine | Enabled for decisions with risk Medium or higher |
| Human checkpoints | At mission start and final deliverable |
| Autonomy level | Autonomous within defined scope |

Policy selection rationale: Deadline is Medium, quality bar is High, risk is Medium. No single constraint dominates. Balanced policy is the correct default per doc 04 constraint-to-policy mapping.

## Proposed Topology

**Architect plus Builders plus Critic**

This is the standard topology for Build missions with structural complexity (see doc 21, Mission Type to Topology Mapping). The service has multiple distinct components (auth logic, database layer, route handlers, test suite) that require architectural decomposition before implementation begins.

```
Architect (lead)
    |
    +--> Critic (architecture review before build begins)
    |
    +--> Builder-Core (auth logic, JWT handling, password hashing)
    |
    +--> Builder-API (FastAPI routes, request/response models, OpenAPI)
    |
    +--> Verifier (runs tests, checks coverage, validates OpenAPI spec)
```

Execution phases:

1. Sufficient Context Gate: Lead verifies objective, constraints, and tool availability
2. Architecture phase: Architect produces component spec and interface contracts
3. Architecture Coherence Gate: Critic evaluates the architecture spec
4. Build phase: Builder-Core and Builder-API execute in parallel
5. Implementation Readiness Gate: Lead checks each Builder has a complete spec before starting
6. Verification phase: Verifier runs tests and validates coverage
7. Shipping Readiness Gate: Lead reviews final output against all acceptance criteria

## Required Agents

| Agent Name | Role Type | Responsibility |
|---|---|---|
| `arch-user-auth` | Architect | Designs module structure, defines component boundaries, writes interface contracts for all three implementation components |
| `critic-security` | Critic | Reviews the architecture for security gaps, anti-patterns, and constraint violations before build begins |
| `builder-core` | Builder | Implements the authentication business logic: password hashing (bcrypt), JWT creation and validation (RS256), token blacklist (in-memory or Redis stub) |
| `builder-api` | Builder | Implements FastAPI route handlers, Pydantic request/response models, SQLAlchemy ORM models, database session management, and OpenAPI annotations |
| `verifier-coverage` | Verifier | Executes the test suite via `pytest --cov`, validates coverage threshold, confirms OpenAPI spec is generated without errors, checks for PEP 8 compliance via `ruff` |

Lead agent: `arch-user-auth`. The Architect coordinates handoffs to Builders and receives Verifier results.

## Required Deliverables

| Deliverable | Owner Agent | Format |
|---|---|---|
| Architecture specification | `arch-user-auth` | Markdown document: component list, interface contracts, dependency map, security decisions |
| Security critique report | `critic-security` | Structured findings with severity (High / Medium / Low) per finding, recommended resolutions |
| Core implementation | `builder-core` | Python source files: `auth/security.py`, `auth/tokens.py`, unit tests in `tests/test_security.py`, `tests/test_tokens.py` |
| API implementation | `builder-api` | Python source files: `api/routes/auth.py`, `api/models.py`, `db/models.py`, `db/session.py`, unit tests in `tests/test_routes.py` |
| Verification report | `verifier-coverage` | Coverage summary (overall percentage, per-file breakdown), OpenAPI validation result (pass/fail), ruff report (pass/fail with any findings) |
| Project README | `arch-user-auth` (post-verification) | Setup instructions, endpoint reference, test run instructions |

## Success Criteria

| Criterion | Evaluation Method | Pass Threshold |
|---|---|---|
| All four endpoints are implemented and callable | Verifier executes `pytest tests/test_routes.py` | All tests pass |
| Unit test coverage meets threshold | Verifier runs `pytest --cov=auth --cov=api --cov-fail-under=80` | Exit code 0 |
| Passwords are not stored in plaintext | Critic inspects architecture; Verifier searches source for raw password storage | No plaintext password storage in any path |
| JWT uses RS256 | Architect specifies it in the interface contract; Builder-Core implements it; Verifier confirms via token decode test | Verification test passes |
| OpenAPI spec is auto-generated | Verifier imports the FastAPI app and confirms `/openapi.json` returns a valid schema | HTTP 200 with valid JSON schema |
| No secrets in source files | Critic and Verifier grep source for hardcoded keys or passwords | Zero findings |
| Code passes ruff linter | Verifier runs `ruff check .` | Exit code 0 or documented suppressions only |
| Architecture critique has no unresolved High severity findings | Critic report reviewed by Architect; all High findings resolved before build begins | Zero unresolved High findings at build start |
