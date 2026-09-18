---
description: Repair verification or review blockers with the smallest safe corrective change
agent: debugger
model: deepseek/deepseek-flash
---

# Corrective Repair Workflow

Repair implementation issues identified by the latest verification or review result for the requested specification.

This workflow is for corrective work only.

Do not redesign the system.
Do not broaden scope.
Do not re-implement unrelated parts of the specification.

## Purpose

Use this workflow after any of the following:

- `/verify` returns `NOT_DONE`
- pre-review returns `CHANGES_REQUIRED`
- senior review returns `REQUEST CHANGES`

The goal is to:

- inspect the latest blocking evidence
- determine the smallest correct fix
- apply only necessary changes
- preserve the approved specification and architecture
- return control to `/verify`

This workflow does NOT decide completion.

Only `/verify` may return `DONE`.

## Stage 1 — Load Repair Context

Read only the minimum context required to repair the blocker.

Use, in priority order:

1. latest verification or review result
2. requested specification
3. relevant project-level `AGENTS.md`
4. relevant source files
5. relevant tests
6. relevant architecture or ADRs only when needed

Do not scan unrelated parts of the repository.

## Stage 2 — Confirm Repair Scope

Extract the concrete blocking findings.

For each blocker determine:

- affected file(s)
- likely cause
- whether the issue is a local implementation defect
- whether root-cause debugging is required
- whether the issue indicates an architecture/specification conflict

Do not fix non-blocking suggestions unless explicitly requested or directly relevant to a blocker.

## Stage 3 — Select Repair Mode

### Straightforward Repair

Use a minimal localized fix when the failure is obvious.

Examples:

- syntax or compile error
- missing import
- incorrect method signature
- obvious implementation defect
- lint/format issue
- missing validation required by the spec

### Root-Cause Debugging

Use debugger-style investigation when:

- runtime behavior differs from expected behavior
- failure is intermittent
- concurrency/race issues are suspected
- serialization or dependency-injection failures occur
- integration behavior is unclear
- a prior repair attempt did not resolve the same blocker

### Architecture or Specification Conflict

STOP if the required fix would need:

- changing the approved technology stack
- introducing a new major dependency
- changing public API contracts outside the specification
- changing persistence strategy
- changing architecture boundaries
- weakening an approved security requirement
- changing consistency/reliability guarantees
- contradicting an ADR
- changing an acceptance criterion
- implementing behavior not supported by the approved specification

Return:

`FIX_BLOCKED`

Include:

- the blocker
- why it cannot be repaired locally
- the architecture/specification conflict
- the minimum decision required

## Stage 4 — Apply Minimal Corrective Changes

When locally repairable:

- change only files necessary to address the blocker
- follow project-level `AGENTS.md`
- follow the approved specification
- follow architecture and ADR constraints
- preserve existing working behavior
- preserve public contracts unless explicitly required otherwise
- reuse project conventions
- avoid speculative refactoring

## Stage 5 — Test Integrity

Never:

- delete failing tests
- skip failing tests without approved justification
- weaken assertions
- alter expected behavior merely to make a test pass
- disable lint rules solely to pass verification
- suppress compiler/static-analysis failures without fixing the underlying issue
- disable security checks
- remove validation
- reduce authorization requirements
- bypass error handling
- replace production behavior with test-only shortcuts

If a test or review finding appears incorrect or conflicts with the approved specification:

STOP.

Return:

`FIX_BLOCKED`

Describe the conflict.

## Stage 6 — Dependency and Tooling Changes

Do not install a new package, runtime, testing tool, linter, scanner, or infrastructure dependency unless:

- it is already approved by the specification/architecture, or
- the user explicitly approves it

If the repair requires a new dependency not previously approved:

STOP and request approval.

## Stage 7 — Validate the Repair Locally

After making the corrective change, run only the smallest relevant checks needed to confirm that the specific blocker appears resolved.

The authoritative full verification must still be performed by `/verify`.

Do not return `DONE`.

## Stage 8 — Repair Loop Protection

Avoid repeated blind repair attempts.

If the same blocker remains unresolved after two meaningful repair attempts:

perform explicit root-cause debugging.

If it still cannot be resolved without changing architecture/specification:

return:

`FIX_BLOCKED`

Do not continue consuming tokens with repeated speculative fixes.

## Output Format

If corrective changes were made successfully, return:

`FIX_APPLIED`

Then report:

### Blockers Addressed

List the blockers targeted.

### Files Changed

List only files changed by the repair.

### Repair Summary

Briefly explain:

- root cause
- corrective change
- why the change is consistent with the specification and architecture

### Focused Validation

List:

- commands run
- results
- whether the original blocker now appears resolved

### Next Action

Return:

`RUN_VERIFY`

The final line must be exactly:

`RUN_VERIFY`

Do not output anything after it.

## Blocked Output

If the issue cannot be repaired safely, return:

`FIX_BLOCKED`

Then report the blocking issue, reason, and minimum required decision.

The final line must be exactly:

`FIX_BLOCKED`

Do not output anything after it.
