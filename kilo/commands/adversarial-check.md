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

## Stage 3 — Select Adversarial Model

There are two valid entry modes.

### Mode A — Default

When the user did not request a specific premium model:

- delegate first to the `adversary` subagent (DeepSeek Flash)
- treat DeepSeek as the default cost-controlled fresh-context adversary
- do not start with Opus automatically

### Mode B — User-directed Opus

When the user explicitly asks for Opus for this adversarial review:

- the user's request is authorization for this specific premium invocation
- delegate directly to `adversary-opus`
- do not require a prior DeepSeek adversarial pass
- do not require Sonnet to justify why Opus is warranted
- do not invoke DeepSeek as a second adversary unless the user explicitly asks for both

In either mode, the reviewer must find ways the artifact can fail the contract rather than validate the author's confidence.

## Stage 4 — Reconcile Findings

For every material finding classify it as exactly one:

- `CONTRACT_MISREAD`
- `ACTIONABLE`
- `ACCEPTED_TRADEOFF`
- `UNSUPPORTED_NOISE`

Re-read the artifact before accepting the adversary's claim.

For `ACTIONABLE` findings, propose or apply the correction only within the current stage's authority.

For `ACCEPTED_TRADEOFF`, document the consequence and why it is accepted.

## Stage 5 — Decide Whether Agent-Proposed Premium Escalation Is Justified

This stage applies only when Stage 3 used the default DeepSeek path.

After reconciling the default DeepSeek findings, consider an Opus adversarial escalation only when at least one of these is true:

- the decision is unusually high-risk and hard to reverse
- failure could cause serious security, authorization, data-loss, corruption, or recovery impact
- the decision establishes a public/external contract that is very expensive to change
- the design contains distributed-concurrency, ordering, idempotency, or consistency guarantees that remain non-obvious
- the default adversary surfaced conflicting/material findings that Sonnet cannot confidently reconcile
- the architecture has major irreversible lock-in or recurring-cost consequences

If none apply, do not use Opus.

If escalation is justified:

1. explain why DeepSeek + Sonnet are insufficient for this decision
2. ask for explicit user approval
3. only after approval, delegate to `adversary-opus`
4. provide artifact + contract + only the unresolved material DeepSeek findings
5. reconcile the Opus result; do not treat it as automatic authority

This justification/approval sequence is not required when the user already explicitly requested Opus in Stage 3.

## Stage 6 — Bounded Recheck

When the default DeepSeek path was used and no Opus escalation occurred, one additional DeepSeek adversarial pass may be run only when the artifact changed materially.

When the user-directed Opus path was used, do not add a DeepSeek pass automatically.

Do not exceed two default adversarial cycles automatically.

An approved Opus escalation replaces further automatic adversarial cycling for that decision.

If substantive unresolved findings remain after the bounded process, escalate to the owning workflow/human rather than grinding.

## Output

Return a concise reconciliation report.

Finish with:

`ADVERSARIAL_CLEAR`

when no unresolved material finding remains.

Finish with:

`ADVERSARIAL_FINDINGS`

when a material finding still requires a decision or correction.

These statuses are advisory risk signals, not implementation completion or merge verdicts.
