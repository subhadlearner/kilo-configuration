# Global AI Engineering Policy

## Objective

Produce production-grade software with minimum unnecessary complexity, strong engineering discipline, and minimum practical infrastructure cost.

## Universal Workflow

For normal feature work, use this lifecycle:

`/grill (optional) → /prd → /architect → /project-init → /spec → /implement → /verify → /review`

Use `/grill` before `/prd` when the idea is ambiguous, unusually large, high-stakes, or contains many coupled product decisions. Skip it for clear, bounded work.

Repair and incident loops:

- `/verify → NOT_DONE → /fix → /verify`
- `/review → CHANGES_REQUIRED or REQUEST CHANGES → /fix → /verify → /review`
- `/diagnose (optional) → /fix → /verify` for difficult runtime, integration, concurrency, performance, or intermittent defects

`/adversarial-check` is an auxiliary risk-control command for high-risk artifacts; it does not replace `/verify` or `/review`.

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

- `/grill` clarifies product intent and trade-offs; it does not choose implementation architecture.
- `/prd` defines product requirements and constraints.
- `/architect` chooses the major technology baseline.
- `/project-init` records and operationalizes that baseline in the repository.
- `/spec` decomposes approved work.
- `/implement` executes the approved specification.
- `/verify` owns deterministic `DONE` / `NOT_DONE`.
- `/review` owns AI review after verification.
- `/diagnose` localizes difficult defects without redefining intended behavior.
- `/fix` repairs diagnosed, verification, or review blockers without redesigning the system.
- `/adversarial-check` challenges assumptions and failure modes but does not become the decision authority.

Implementation agents must not choose a missing major technology decision on their own.

## Discovery, TDD, Diagnosis, and Adversarial Discipline

- Use `requirements-grilling` when unresolved product decisions would otherwise be guessed. The agent researches facts; the user decides scope, priorities, and trade-offs.
- Use `tdd` for behavior-bearing application code when a stable observable seam exists. Work in thin red → green slices rather than writing tests after a large implementation.
- Use `diagnosing-bugs` for non-trivial failures. Build a tight red-capable feedback loop for the exact symptom before asserting root cause.
- Use `adversarial-check` selectively for high-risk or hard-to-reverse decisions such as auth/IAM, destructive migrations, concurrency/idempotency, public contracts, data integrity, recovery, and security-sensitive infrastructure.
- Fresh adversarial review receives the smallest artifact plus its contract, not the author's preferred conclusion. Findings are evidence to reconcile, not authority.
- Two materially unchanged adversarial cycles with substantive unresolved findings require human or architecture escalation.

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
- load and apply relevant approved skills
- use test-first vertical slices when the specification marks TDD applicable
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

Never make verification green by weakening the verification mechanism.

Never:

- delete, skip, or quarantine a failing required test merely to obtain green status
- weaken assertions to make implementation pass
- mock away the behavior being tested
- lower required quality, coverage, performance, or security thresholds without approval
- add warning/lint/static-analysis suppressions solely to silence a failure
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

Use cheaper models for routine implementation, diagnosis, repair, default adversarial checks, and pre-review.

Use Claude Sonnet for discovery/grilling orchestration, planning, architecture, specification design, adversarial reconciliation, and senior review.

Default adversary: DeepSeek Flash.

Agent-proposed escalation adversary: Claude Opus, only for rare critical decisions after a DeepSeek adversarial pass and only with explicit user approval.

User-directed premium override: the user may explicitly request Claude Opus for an architecture, specification, or other adversarial review. That request authorizes the specific Opus invocation directly; a prior DeepSeek adversarial pass and Sonnet justification are not required. Do not add a DeepSeek adversarial pass unless the user asks for both.

Use Claude Opus on the agent's own initiative only when the decision is unusually high-risk, hard to reverse, security/data-integrity sensitive, or materially unresolved.
