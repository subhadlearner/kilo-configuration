# Global AI Engineering Policy

## Objective

Produce production-grade software with minimum unnecessary complexity, strong engineering discipline, and minimum practical infrastructure cost.

## Universal Workflow

For normal feature work, use this lifecycle:

`/prd → /architect → /project-init → /spec → /implement → /verify → /review`

Repair loops:

- `/verify → NOT_DONE → /fix → /verify`
- `/review → CHANGES_REQUIRED or REQUEST CHANGES → /fix → /verify → /review`

Production deployment always requires human approval.

## Core Principles

1. Prefer the simplest architecture that satisfies approved requirements.
2. Do not invent product requirements.
3. Do not silently change approved architecture, ADRs, or acceptance criteria during implementation.
4. Prefer incremental, reversible changes.
5. Reuse existing project conventions before introducing new abstractions.
6. Never claim completion without deterministic evidence.
7. Never expose, print, modify, or commit secrets.
8. Treat project-level `AGENTS.md`, approved architecture, ADRs, and specifications as authoritative project context.

## Technology Decision Authority

- `/prd` defines product requirements and constraints.
- `/architect` chooses the major technology baseline.
- `/project-init` records and operationalizes that baseline in the repository.
- `/spec` decomposes approved work.
- `/implement` executes the approved specification.
- `/verify` owns deterministic `DONE` / `NOT_DONE`.
- `/review` owns AI review after verification.
- `/fix` repairs verification or review blockers without redesigning the system.

Implementation agents must not choose a missing major technology decision on their own.

## Specification Discipline

Specifications should be independently implementable and verifiable.

Target context size:

- Preferred: 30K–60K tokens
- Warning: 60K–80K tokens
- Hard ceiling: 100K tokens

Split specifications that exceed the hard ceiling or create unsafe coupling.

## Branch and Worktree Discipline

Do not implement directly on protected integration branches such as `main`, `master`, `develop`, or `release`.

Use a dedicated specification branch, preferably:

`spec/<spec-id>-<short-description>`

For concurrent mutating work, use separate branches and Git worktrees.

Do not automatically:

- stash unrelated work
- reset or discard unrelated changes
- rebase shared branches
- force-push
- auto-merge protected branches

## Implementation

The implementation agent must:

- follow the approved specification
- follow architecture and ADRs
- use the approved technology stack
- write production-grade code
- implement applicable tests
- preserve security, reliability, and public contracts
- report failures honestly

If requirements conflict or a required architecture decision is missing, stop and report the blocker.

## Testing and Verification

Use applicable:

- unit tests
- integration tests
- contract tests
- E2E tests
- negative and boundary tests
- security tests
- formatting/lint/static-analysis checks
- dependency/security scans
- IaC validation

Never:

- delete a failing test merely to obtain green status
- weaken assertions to make implementation pass
- mock away the behavior being tested
- disable a required quality/security check simply to obtain a pass

`/verify` is the authoritative completion gate.

A specification is `DONE` only when all required applicable deterministic checks and acceptance criteria have verifiable evidence.

## Review

Review occurs only after deterministic verification succeeds.

Review sequence:

1. DeepSeek pre-review
2. Claude Sonnet senior review only if pre-review returns `READY_FOR_SENIOR_REVIEW`
3. CI
4. PR/merge
5. human-approved production deployment

Do not invoke the senior reviewer when pre-review has blocking findings.

## Security

Never:

- hard-code credentials
- expose secrets in logs
- bypass authentication or authorization
- disable security checks merely to make CI pass

Treat these as sensitive by default:

- `.env`
- `.env.*`
- `*.pem`
- `*.key`
- credential files
- cloud credentials
- API tokens

## Cloud and Cost

Cloud cost is a first-class requirement.

For AWS or Azure decisions consider:

- fixed monthly cost
- variable cost
- storage cost
- network/data-transfer cost
- observability cost
- scaling behavior
- operational burden
- failure modes
- recovery strategy

Challenge unnecessary always-on or premium infrastructure.

Prefer managed/serverless services when they provide the best balance of reliability, simplicity, and cost.

## Model Escalation

Use cheaper models for routine implementation, repair, and pre-review.

Use Claude Sonnet for planning, architecture, specification design, and senior review.

Use Claude Opus only for explicitly approved, high-risk unresolved architecture decisions.
