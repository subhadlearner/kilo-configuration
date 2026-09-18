---
description: Fresh-context adversarial challenge for high-risk architecture, specification, migration, security, or contract decisions
agent: planner
model: anthropic/claude-sonnet-5
---

# Adversarial Check Workflow

Challenge a high-risk artifact before relying on it.

This command is not a replacement for `/verify` or `/review`.
It does not become the decision authority.

Load the global `adversarial-check` skill.

## Stage 1 — Select the Reviewable Claim

Identify the smallest artifact that carries the high-risk decision:

- architecture decision or ADR
- specification behavior/invariant
- migration/cutover plan
- IAM/security policy design
- concurrency/idempotency design
- public API/event contract
- recovery strategy
- other high-blast-radius decision

State why the decision is material.

## Stage 2 — Extract Artifact + Contract

Prepare only:

### Artifact

The smallest relevant text/diff/design.

### Contract

The requirements, invariants, acceptance criteria, ADR constraints, or guarantees it must satisfy.

Do not include the author's preferred conclusion or reasoning narrative in the adversary input.

## Stage 3 — Fresh-Context Challenge

Delegate to the `adversary` subagent.

Its instruction is to find ways the artifact can fail the contract, not to validate the author's confidence.

## Stage 4 — Reconcile Findings

For every material finding classify it as exactly one:

- `CONTRACT_MISREAD`
- `ACTIONABLE`
- `ACCEPTED_TRADEOFF`
- `UNSUPPORTED_NOISE`

Re-read the artifact before accepting the adversary's claim.

For `ACTIONABLE` findings, propose or apply the correction only within the current stage's authority.

For `ACCEPTED_TRADEOFF`, document the consequence and why it is accepted.

## Stage 5 — Bounded Recheck

If the artifact materially changed, one additional fresh adversarial pass may be run.

Do not exceed two adversarial cycles automatically.

If substantive unresolved findings remain, escalate to the owning workflow/human rather than grinding.

## Output

Return a concise reconciliation report.

Finish with:

`ADVERSARIAL_CLEAR`

when no unresolved material finding remains.

Finish with:

`ADVERSARIAL_FINDINGS`

when a material finding still requires a decision or correction.

These statuses are advisory risk signals, not implementation completion or merge verdicts.
