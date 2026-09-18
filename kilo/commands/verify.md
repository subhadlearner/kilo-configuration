---
description: Deterministically verify an implementation against project-defined checks
agent: code
model: deepseek/deepseek-flash
---

# Verification Workflow

Verify the requested specification deterministically.

This workflow evaluates executable evidence.

Do not perform subjective code review.

Do not modify application code or tests merely to obtain a passing result.

## Stage 1 — Determine Verification Scope

Read only the minimum project context required to determine how the requested specification must be verified.

Use, in priority order:

1. project-level `AGENTS.md`
2. the requested specification
3. relevant project configuration and package/build manifests
4. relevant architecture or ADR references only when required to determine mandatory checks

Determine all applicable required checks.

Project-level `AGENTS.md` is the primary source for repository verification commands.

Do not invent a tool or command merely because it is commonly used with the technology stack.

## Stage 2 — Validate Verification Configuration

Before running checks, determine whether the repository contains enough information to execute the required verification.

If a required command is missing from `AGENTS.md`, inspect the relevant project manifest/configuration to determine whether an unambiguous command already exists.

Do not silently introduce a new testing, linting, or security tool during verification.

If a required check is known to be required but cannot be executed because the project is not configured for it:

record it as a blocker.

Do not mark the specification `DONE`.

## Stage 3 — Run Applicable Deterministic Checks

Run only checks that are applicable to the project and requested change.

Execute commands individually.

Do not combine unrelated verification commands into one shell command.

Capture the exit status and relevant failure evidence for every command.

Use the actual commands defined by the project.

## Stage 4 — Run Additional Required Checks

Where configured and applicable, run:

- integration tests
- E2E tests
- contract tests
- static analysis
- security scans
- dependency/vulnerability scans
- secret scans
- infrastructure-as-code validation
- schema or migration validation
- API compatibility checks

Only run checks that are part of the approved project workflow or explicitly required by the specification.

## Stage 5 — Test Integrity Rules

Do not:

- delete failing tests
- skip failing tests without approved justification
- weaken assertions
- replace meaningful tests with trivial tests
- disable lint rules solely to pass verification
- suppress compiler/static-analysis errors merely to obtain success
- remove security checks
- ignore required verification failures

If a test or check appears incorrect, report the conflict.

Do not silently change the test or requirement.

## Stage 6 — Interpret Results

A check is `PASS` only when its command completes successfully and the expected verification actually ran.

A check is `FAIL` when:

- the command exits unsuccessfully
- required tests fail
- required lint/static-analysis checks fail
- a required security check fails
- required IaC validation fails
- the command cannot run because required project configuration is missing
- the command succeeds without actually executing the required check

A check may be `NOT_APPLICABLE` only when it is genuinely outside the requested specification or project technology.

Do not classify a missing required check as `NOT_APPLICABLE`.

## Stage 7 — Verify Acceptance Criteria

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

## Stage 8 — Determine Specification Status

Return `DONE` only when:

- all required applicable checks pass
- every required acceptance criterion has deterministic passing evidence
- no unresolved verification blocker remains

Return `NOT_DONE` when any required condition is not satisfied.

Do not use intermediate statuses.

## Output Format

### Verification Scope

State briefly:

- specification/change verified
- detected technology stack
- verification source

### Verification Results

For each executed check provide:

- command
- exit status
- result: `PASS` or `FAIL`
- concise failure details when applicable

For required checks that could not be executed provide:

- required check
- result: `FAIL`
- reason it could not be executed

For relevant checks that are genuinely unnecessary provide:

- check
- result: `NOT_APPLICABLE`
- concise reason

### Acceptance Criteria

For every required acceptance criterion provide:

- criterion ID
- evidence
- result: `PASS` or `FAIL`

### Blockers

List only blockers preventing completion.

If none:

`None.`

### Status

Return exactly one of:

`DONE`

or

`NOT_DONE`

The final line of the response must be exactly the status token.

Do not output anything after it.
