---
description: Run cost-controlled two-stage production review after deterministic verification
agent: code
model: deepseek/deepseek-flash
---

# Production Review Workflow

Perform the production review pipeline for the requested change or specification.

Do not perform the code review yourself.

## Stage 1 — Determine Review Context

Identify:

- the intended scope of the change
- the relevant specification
- relevant architecture and ADRs
- available verification/test results

Do not load unrelated project documentation.

### Verification Gate

For implementation changes, review the latest verification result before invoking any reviewer.

If required verification has failed:

- STOP the review pipeline
- do not invoke `pre-reviewer`
- report the failed verification
- require the implementation to return to `/fix`

If no verification result exists for an implementation change:

- STOP the review pipeline
- request `/verify` before continuing

Documentation-only, planning-only, or other non-executable changes may proceed without executable verification when such verification is not applicable.

## Stage 2 — Pre-Review

Delegate the review to `pre-reviewer`.

Provide the pre-reviewer with:

- the requested specification or change scope
- relevant architecture/ADR references where available
- available verification results

The pre-reviewer must independently inspect the Git changes.

Wait for its result.

### If the result is:

`CHANGES_REQUIRED`

STOP the review pipeline.

Return the complete pre-review report to the user.

Do not invoke the senior reviewer.

State clearly:

`Senior review skipped because blocking issues remain in pre-review.`

The implementation must be corrected with `/fix`, then verified before `/review` is run again.

### If the result is:

`READY_FOR_SENIOR_REVIEW`

Continue to Stage 3.

## Stage 3 — Senior Review

Delegate to `code-reviewer`.

Provide the senior reviewer with:

- the intended change scope
- the relevant specification
- relevant architecture/ADR references
- available verification/test results
- the complete pre-review report, verbatim and without summarization

Do not rewrite, summarize, reinterpret, or omit findings from the pre-review before passing them to `code-reviewer`.

The senior reviewer must independently validate the implementation.

The pre-review is evidence, not authority.

Do not perform the senior review yourself.

## Stage 4 — Final Decision

Return the senior review result.

The only authoritative AI review decisions are:

`APPROVE`

or

`REQUEST CHANGES`

If:

`APPROVE`

report that the AI review gate has passed.

This does not bypass deterministic CI or human production approval.

If:

`REQUEST CHANGES`

the implementation must return to `/fix`, then `/verify`, then `/review`.

## Cost Control

Never invoke the senior reviewer when the pre-review result is:

`CHANGES_REQUIRED`

Do not repeat either review yourself.

Do not invoke reviewers multiple times unless:

- the implementation changed
- verification results changed
- explicitly requested by the user

Do not invoke the senior reviewer solely to confirm obvious blocking issues already identified by the pre-reviewer.

## Review Integrity

Do not treat the pre-review as final authority.

Do not treat the senior review as a substitute for deterministic verification.

Do not suppress, weaken, or reinterpret blocking findings to obtain an approval.

Do not invent defects merely to force a `REQUEST CHANGES` result.

Use the review agents according to their defined responsibilities.

## Authority

The review hierarchy is:

1. deterministic verification provides factual build/test evidence
2. `pre-reviewer` provides inexpensive first-pass quality filtering
3. `code-reviewer` provides authoritative AI merge review
4. CI remains the deterministic merge gate
5. human approval remains required for production deployment
