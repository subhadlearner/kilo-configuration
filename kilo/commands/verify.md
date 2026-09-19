---
description: Deterministically verify an implementation, persist evidence, and evaluate configured security gates
agent: code
model: deepseek/deepseek-flash
---

# Verification Workflow

Verify the requested specification deterministically and persist the evidence.

This workflow evaluates executable evidence.

Do not perform subjective code review.

Do not modify application code or tests merely to obtain a passing result.

## Verification invariants

- Verification evidence is factual.
- A failing required check means the verification result is `NOT_DONE`.
- A waiver never changes a historical `NOT_DONE` result to `DONE`.
- Do not delete, skip, quarantine, weaken, or suppress a required check merely to obtain green status.
- Every non-trivial verification run creates a new immutable/history-preserving report under `docs/verification/`.
- Never overwrite an earlier verification report.
- Human risk acceptance is handled separately by `/waive`.
- A reviewable verification result must be bound to a stable implementation revision. Verification success alone must not authorize review of code that changed after the checks ran.

## Stage 1 — Determine Verification Scope

Read only the minimum project context required to determine how the requested specification must be verified.

Use, in priority order:

1. project-level `AGENTS.md`
2. the requested specification
3. relevant project configuration and package/build manifests
4. relevant architecture or ADR references only when required to determine mandatory checks
5. the approved `security-verification` skill when security verification is applicable

Determine all applicable required checks.

Capture repository identity before executing checks:

- current branch
- current HEAD commit SHA
- whether the working tree contains uncommitted or untracked changes outside workflow evidence paths

Workflow evidence paths are:

- `docs/verification/**`
- `docs/reviews/**`
- `docs/diagnostics/**`

Changes to source, tests, project configuration, specifications, architecture/ADRs, project instructions, dependency manifests/lockfiles, or other non-evidence paths mean the implementation revision is not stable for a reusable review gate.

Project-level `AGENTS.md` is the primary source for repository verification commands.

Do not invent a tool or command merely because it is commonly used with the technology stack.

## Stage 2 — Validate Verification Configuration

Before running checks, determine whether the repository contains enough information to execute the required verification.

If a required command is missing from `AGENTS.md`, inspect the relevant project manifest/configuration to determine whether an unambiguous approved command already exists.

Do not silently introduce a new testing, linting, security, dependency, secret-scanning, or IaC-security tool during verification.

If a required check is known to be required but cannot be executed because the project is not configured for it:

- record it as `FAIL`
- record the missing verification capability as a blocker
- do not mark the specification `DONE`

For a security-relevant project or specification, absence of executable approved security/dependency checks is not evidence of security. If architecture/project initialization requires such checks but they are missing, verification is `NOT_DONE`.

## Stage 3 — Run Applicable Deterministic Checks

Run only checks that are applicable to the project and requested change.

Execute commands individually.

Do not combine unrelated verification commands into one shell command.

Capture:

- exact command
- exit status
- concise relevant output/evidence
- result: `PASS` or `FAIL`

Use the actual commands defined by the project.

## Stage 4 — Security and Vulnerability Verification

When security is applicable, load and apply the approved `security-verification` skill.

Run only architecture/project-approved executable checks, which may include:

- dependency vulnerability scanning
- software supply-chain/package-source checks
- secret scanning
- SAST/static security analysis
- IaC security validation
- container/image scanning
- API/web security tests
- authentication/authorization tests
- project-specific abuse/security tests

Evaluate the applicable OWASP Top 10:2025 categories as a risk taxonomy:

- A01 Broken Access Control
- A02 Security Misconfiguration
- A03 Software Supply Chain Failures
- A04 Cryptographic Failures
- A05 Injection
- A06 Insecure Design
- A07 Authentication Failures
- A08 Software or Data Integrity Failures
- A09 Security Logging and Alerting Failures
- A10 Mishandling of Exceptional Conditions

For each category record exactly one coverage state:

- `PASS` — applicable, configured verification ran, and no blocking finding remains
- `FAIL` — applicable and a blocking finding/check failure exists
- `NOT_APPLICABLE` — category genuinely does not apply to this project/change
- `NOT_COVERED` — applicable risk exists but approved executable evidence is insufficient

`NOT_COVERED` is not equivalent to `PASS`.

If a category is materially relevant and the approved project policy requires coverage, `NOT_COVERED` is a verification blocker.

Use OWASP ASVS-style controls as a deeper verification reference for web/API technical controls where appropriate.

Do not claim broad "OWASP compliant", "secure", "security certified", or regulatory compliance merely because these checks pass.

Preferred wording is evidence-scoped, for example:

> No blocking findings were detected for the applicable security categories covered by the configured verification tooling and specification-specific security checks.

## Stage 5 — Run Other Required Checks

Where configured and applicable, run:

- integration tests
- E2E tests
- contract tests
- static analysis
- schema or migration validation
- API compatibility checks
- performance/reliability checks required by the specification

Only run checks that are part of the approved project workflow or explicitly required by the specification.

## Stage 6 — Test and Gate Integrity

Never:

- delete failing tests
- skip or quarantine failing required tests merely because they are flaky
- weaken assertions
- replace meaningful tests with trivial tests
- disable lint rules solely to pass verification
- suppress compiler/static-analysis findings merely to obtain success
- remove or disable security checks
- lower required quality/performance/security thresholds without approved policy
- edit a previous verification report from `NOT_DONE` to `DONE`
- treat a waiver as if the failed check passed

If a test appears flaky or incorrect, report the conflict and preserve the failure evidence.

Use `/diagnose` when root cause is uncertain.

Use `/waive` only after the human owner deliberately accepts the documented residual risk.

## Stage 7 — Interpret Results

A check is `PASS` only when its command completes successfully and the expected verification actually ran.

A check is `FAIL` when:

- the command exits unsuccessfully
- required tests fail, including intermittent failures observed in this run
- required lint/static-analysis checks fail
- a required security check fails
- required IaC validation fails
- the command cannot run because required project configuration is missing
- the command succeeds without actually executing the required check

A check may be `NOT_APPLICABLE` only when it is genuinely outside the requested specification or project technology.

Do not classify a missing required check as `NOT_APPLICABLE`.

## Stage 8 — Verify Acceptance Criteria

For every acceptance criterion in the requested specification, identify deterministic evidence.

Acceptable evidence may include:

- a passing automated test
- a successful build/static-analysis/security check
- a deterministic command result
- a directly inspectable generated artifact where the criterion is non-executable

For each acceptance criterion report:

- criterion ID
- evidence
- result: `PASS` or `FAIL`

If a required acceptance criterion has no deterministic evidence, mark it `FAIL`.

Do not infer that an acceptance criterion passes merely because unrelated tests are green.

## Stage 9 — Determine Verification Result and Delivery Gate

Verification result is factual and uses only:

- `DONE`
- `NOT_DONE`

Return `DONE` only when:

- all required applicable checks pass
- every required acceptance criterion has deterministic passing evidence
- required security verification has no blocking failure or required uncovered gap
- no unresolved verification blocker remains

Return `NOT_DONE` when any required condition is not satisfied.

For this verification run, derive:

- `Delivery Gate: CLEAR` only when verification is `DONE` **and** the verified implementation revision is stable: the current HEAD SHA is recorded and there were no uncommitted/untracked non-evidence changes when verification began
- `Delivery Gate: BLOCKED` when verification is `NOT_DONE`
- `Delivery Gate: BLOCKED` when checks are `DONE` but the implementation revision is not stable enough to prove freshness for later review

When checks pass on an unstable working tree, preserve the factual result:

`Verification Result: DONE`

but record:

`Delivery Gate: BLOCKED`

with the blocker:

`Commit the intended implementation/spec/configuration changes, then rerun /verify so the evidence is tied to a stable revision.`

Do not produce `CLEAR_WITH_EXCEPTION` inside `/verify`.

That state can be established only by a separate valid, human-authorized `/waive` artifact for this exact verification report and commit.

## Stage 10 — Persist Verification Evidence

Create a new report under:

`docs/verification/`

Use a stable, monotonically increasing name scoped to the specification when possible:

`VERIFY-<SPEC-ID>-001.md`
`VERIFY-<SPEC-ID>-002.md`

If no specification ID exists, use a clear change identifier.

Never overwrite or rewrite a prior verification report to change its verdict.

The report must contain:

### Identity

- verification ID
- specification/change
- branch
- verified implementation HEAD commit SHA
- repository state at verification start: `STABLE` or `UNSTABLE`
- non-evidence changed paths, if any
- date/time when available from the environment

### Verification Scope

- detected technology stack
- verification source
- applicable project commands

### Verification Results

For each check:

- command
- exit status
- result
- concise evidence/failure detail

### Security Verification

Include:

- configured security tools/checks actually executed
- dependency/supply-chain result
- secret-scanning result when applicable
- static/IaC/container/security-test results when applicable
- OWASP Top 10:2025 coverage table using `PASS`, `FAIL`, `NOT_APPLICABLE`, or `NOT_COVERED`
- relevant ASVS-style control evidence where applicable
- residual/uncovered security risks

### Acceptance Criteria

For every required criterion:

- criterion ID
- evidence
- result

### Blockers

List only blockers preventing completion.

If none:

`None.`

### Verification Result

Exactly:

`DONE`

or:

`NOT_DONE`

### Delivery Gate

Exactly:

`CLEAR`

or:

`BLOCKED`

### Next Action

When `DONE` with `Delivery Gate: CLEAR`:

`/review`

When `DONE` with `Delivery Gate: BLOCKED` because the implementation revision was unstable:

commit the intended non-evidence changes, then rerun:

`/verify`

When `NOT_DONE`, select the truthful next path:

- `/fix` for understood repairable defects
- `/diagnose` for intermittent, flaky, concurrency, integration, performance, or unclear failures
- `/waive` only when the human owner explicitly chooses to accept the documented residual risk

## Chat Output

Summarize:

- verification report path
- key failed/passed gates
- security coverage/blockers
- verification result
- delivery gate
- next action

The final line of the response must be exactly the verification result token:

`DONE`

or

`NOT_DONE`

Do not output anything after it.
