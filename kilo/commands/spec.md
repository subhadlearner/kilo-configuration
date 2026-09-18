---
description: Decompose an approved architecture into independently implementable specifications
agent: planner
model: anthropic/claude-sonnet-5
---

# Specification Workflow

Create implementation specifications from the approved PRD and architecture.

Do not implement application code.

## Stage 1 — Preconditions

Read:

- approved PRD
- approved architecture
- relevant ADRs
- project-level `AGENTS.md`
- project initialization result when available

Proceed only when:

- PRD is approved
- architecture returned `ARCHITECTURE_READY`
- required technology baseline is established
- project initialization returned `PROJECT_INIT_READY`
- project-level `AGENTS.md` reflects the approved technology baseline

If any prerequisite is missing or blocked, do not continue.

Return:

`SPEC_BLOCKED`

and state the missing prerequisite.

## Stage 2 — Decompose the Work

Split the approved scope into independently implementable specifications.

Each specification should:

- deliver a coherent unit of value
- have clear acceptance criteria
- minimize unnecessary coupling
- avoid overlapping file ownership where practical
- be suitable for an isolated branch/worktree
- fit within the context budget

Target size:

- preferred: 30K–60K tokens
- warning: 60K–80K tokens
- hard ceiling: 100K tokens

If a specification would exceed the hard ceiling, split it.

### Greenfield Bootstrap and CI Ownership

For a greenfield project, determine whether the approved architecture requires repository/bootstrap work that does not yet exist, including where applicable:

- initial application/project scaffolding
- CI workflow implementation
- infrastructure-as-code bootstrap
- dependency/package configuration
- baseline quality gates
- baseline security/dependency scanning
- deployment pipeline scaffolding

If such work is required, create an explicit early implementation specification for it rather than assuming the repository or CI pipeline already exists.

Prefer a bootstrap specification such as:

`SPEC-000 — Project Bootstrap and CI`

when that naming fits the project.

The bootstrap specification must:

- implement only the architecture-approved tooling and CI/CD approach
- establish the build/test/quality/security gates required by later specifications
- avoid inventing a new technology stack
- avoid implementing unrelated product functionality
- define deterministic acceptance criteria for the bootstrap work

Later specifications that depend on CI, generated project structure, IaC foundations, or repository tooling must declare that bootstrap specification as a prerequisite.

Do not treat selecting a CI provider during `/architect` or documenting it during `/project-init` as equivalent to implementing the CI pipeline.

## Stage 3 — Define Dependencies

For every specification identify:

- prerequisite specs
- external dependencies
- shared contracts
- migration ordering
- deployment ordering where relevant

Avoid circular dependencies.

Mark specs that can safely run in parallel.

## Stage 4 — Specification Content

Each specification must include:

### Identity

- spec ID
- title
- objective

### Scope

- included behavior
- excluded behavior
- non-goals

### Dependencies

- prerequisite specs
- architecture/ADR references
- external dependencies

### Technology Constraints

Record only the approved stack relevant to this specification.

Do not independently choose a different technology.

### Interfaces

Where applicable define:

- APIs
- events
- queues/topics
- file formats
- commands
- integration contracts

### Data

Where applicable define:

- entities
- fields
- indexes
- migrations
- retention/TTL
- consistency expectations

### Behavior

Define normal behavior and important edge cases.

### Validation

Define input and business validation.

### Error Handling

Define expected failures and observable responses.

### Security

Define relevant:

- authentication
- authorization
- secrets
- sensitive data handling
- abuse controls

### Observability

Define relevant:

- logs
- metrics
- traces
- alarms
- correlation IDs

### Performance and Reliability

Define relevant:

- latency
- throughput
- timeout
- retry
- idempotency
- concurrency
- recovery expectations

### Cost Constraints

Record implementation-relevant cost requirements.

### Acceptance Criteria

Every acceptance criterion must be objectively verifiable.

### Testing Requirements

Define applicable:

- unit tests
- integration tests
- contract tests
- E2E tests
- negative tests
- boundary tests
- security tests

### Verification Requirements

Identify the deterministic checks expected from `/verify`.

### Definition of Done

A specification is not complete until:

- implementation is complete
- required tests exist
- `/verify` returns `DONE`
- `/review` passes
- CI passes

## Stage 5 — Parallelism and Branch Safety

Identify whether specs can be implemented concurrently.

When parallel implementation is safe:

- avoid overlapping mutation scope where practical
- document shared dependencies
- recommend separate branches/worktrees

Do not force parallelism where it increases coordination risk.

## Stage 6 — Traceability

Each spec should trace back to:

- PRD requirements
- acceptance criteria
- architecture decisions
- ADRs

Do not introduce new product requirements.

## Blocked Output Contract

Whenever this workflow cannot safely continue, the blocked response must contain these fields:

### Blocking issue

State exactly what is missing, conflicting, unsafe, or unresolved.

### Owner

Use exactly one of:

- `PRODUCT`
- `ARCHITECTURE`
- `PROJECT_INIT`
- `SPECIFICATION`
- `REPOSITORY`
- `USER_APPROVAL`

Choose the owner that must resolve the blocker.

### Why it blocks

Explain why this stage cannot safely continue without the decision or correction.

### Required action

State the minimum action, clarification, or correction required to unblock the workflow.

### Next command

State the exact next workflow command to run after the blocker is resolved.

Use only one of:

- `/prd`
- `/architect`
- `/project-init`
- `/spec`
- `/implement`

If a user clarification or repository cleanup must happen first, say so in `Required action`, then name the command to rerun in `Next command`.

The final line must be the workflow's blocked status token and nothing may appear after it.

## Storage

Store specifications under:

`docs/specs/`

Use stable IDs such as:

`SPEC-001`, `SPEC-002`, ...

## Output Status

If the specification set is complete and implementation-ready, finish with exactly:

`SPEC_READY`

If a prerequisite or material ambiguity prevents safe decomposition, use the Blocked Output Contract.

Routing rules:

- use owner `PRODUCT` and next command `/prd` when the blocker is a requirement or acceptance-criteria ambiguity
- use owner `ARCHITECTURE` and next command `/architect` when the blocker is an architecture/ADR decision
- use owner `PROJECT_INIT` and next command `/project-init` when project initialization is missing or incomplete
- use owner `SPECIFICATION` and next command `/spec` when the issue is local to decomposition and can be corrected without changing upstream decisions

Finish with exactly:

`SPEC_BLOCKED`

Do not output anything after the final status token.
