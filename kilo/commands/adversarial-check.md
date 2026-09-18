---
description: Fresh-context adversarial challenge for high-risk architecture, specification, migration, security, or contract decisions
agent: planner
model: openai/gpt-5.6-sol
---

# Adversarial Check Workflow

Challenge a high-risk artifact before relying on it.

This command is not a replacement for `/verify` or `/review`.
It does not become the decision authority.

Load the global `adversarial-check` skill.

## Optional User Model Selection

The user may choose the model for this workflow in natural language.

Examples:

```text
Use GPT.
Use Terra.
Use Luna.
Use Claude.
Use Haiku.
Use Opus.
Use DeepSeek.
```

Aliases resolve as:

- GPT / OpenAI / Sol → GPT-5.6 Sol
- Terra → GPT-5.6 Terra
- Luna → GPT-5.6 Luna
- Claude / Sonnet → Claude Sonnet 5
- Haiku → Claude Haiku 4.5
- Opus → Claude Opus 5
- DeepSeek → DeepSeek V4.1 Flash

If the requested model differs from the current planner model, route the substantive work through the model-selectable planning worker using the explicit per-task model override.

The user's model choice changes only the model. It does not change this workflow's role, authority, permissions, acceptance criteria, or safety rules.

If the requested model is unavailable, stop clearly rather than silently substituting another model.


## How the User Should Invoke This Command

The user should identify the artifact or decision they want challenged and, when useful, the risk they are particularly concerned about.

The user does not need to construct the artifact/contract extraction manually; the planner should derive it from the repository.

Example — normal/default adversary:

```text
/adversarial-check

Review the authentication and authorization design in the current architecture.

Focus especially on:
- privilege escalation
- tenant isolation
- token/session failure modes
- operational recovery

Use the normal adversarial path.
```

Example — concurrency/data-integrity decision:

```text
/adversarial-check

Challenge the DynamoDB idempotency and concurrency design in ADR-007 and the related architecture section.

Try to find any sequence of concurrent requests, retries, or duplicate events that can violate the stated uniqueness/data-integrity guarantees.
```

Example — user-directed Opus:

```text
/adversarial-check

Use Opus directly for this review.

Review the production cross-account IAM and event-ingestion architecture in ADR-011 and the current architecture document.

I want a premium fresh-context challenge focused on trust boundaries, confused-deputy risks, privilege escalation, failure recovery, and assumptions that could create a large production blast radius.
```

The last example is an explicit user authorization for that specific Opus adversarial invocation. Do not require a prior DeepSeek pass or an additional justification.

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

There are three valid entry modes.

### Mode A — Default

When the user did not request a specific paid Claude model:

- delegate first to the `adversary` subagent (DeepSeek Flash)
- treat DeepSeek as the default cost-controlled fresh-context adversary
- the owning GPT-5.6 Sol planner reconciles the findings
- do not start with Claude automatically

### Mode B — User-directed Sonnet

When the user explicitly asks for Claude Sonnet for this adversarial review:

- the user's request authorizes this specific paid invocation
- delegate directly to `adversary-sonnet`
- do not require a prior DeepSeek pass
- do not require Sol to justify the user's model choice
- do not invoke DeepSeek or Opus unless the user explicitly asks

Use this when the user wants a strong independent model-family second opinion without paying Opus rates.

### Mode C — User-directed Opus

When the user explicitly asks for Claude Opus for this adversarial review:

- the user's request authorizes this specific premium invocation
- delegate directly to `adversary-opus`
- do not require a prior DeepSeek or Sonnet pass
- do not require Sol to justify why Opus is warranted
- do not invoke another adversarial model unless the user explicitly asks

In every mode, the reviewer must find ways the artifact can fail the contract rather than validate the author's confidence.

## Stage 4 — Reconcile Findings

For every material finding classify it as exactly one:

- `CONTRACT_MISREAD`
- `ACTIONABLE`
- `ACCEPTED_TRADEOFF`
- `UNSUPPORTED_NOISE`

Re-read the artifact before accepting the adversary's claim.

For `ACTIONABLE` findings, propose or apply the correction only within the current stage's authority.

For `ACCEPTED_TRADEOFF`, document the consequence and why it is accepted.

## Stage 5 — Decide Whether Agent-Proposed Claude Escalation Is Justified

This stage applies only when Stage 3 used the default DeepSeek path.

After the GPT-5.6 Sol planner reconciles the DeepSeek findings:

### Sonnet escalation

Consider `adversary-sonnet` when a material design uncertainty remains and an independent model-family opinion would materially improve confidence.

Examples:

- security/trust-boundary reasoning
- concurrency or consistency assumptions
- public contract compatibility
- migration/cutover design
- a material disagreement between the default adversary and the Sol planner

Before an agent-proposed Sonnet call:

1. explain why a paid cross-model review is useful
2. ask for explicit user approval
3. after approval, delegate to `adversary-sonnet`
4. provide artifact + contract + unresolved material findings only

### Opus escalation

Consider `adversary-opus` only when at least one of these is true:

- the decision is unusually high-risk and hard to reverse
- failure could cause serious security, authorization, data-loss, corruption, or recovery impact
- the decision establishes a public/external contract that is very expensive to change
- the design contains distributed-concurrency, ordering, idempotency, or consistency guarantees that remain non-obvious
- the default adversary surfaced conflicting/material findings that Sonnet cannot confidently reconcile
- the architecture has major irreversible lock-in or recurring-cost consequences

If none apply, do not use Opus.

If Opus escalation is justified:

1. explain why DeepSeek + Sol, and any already-used Sonnet review, are insufficient for this decision
2. ask for explicit user approval
3. only after approval, delegate to `adversary-opus`
4. provide artifact + contract + only unresolved material findings
5. reconcile the Opus result; do not treat it as automatic authority

The approval/justification sequence is not required when the user explicitly selected Sonnet or Opus in Stage 3.

## Stage 6 — Bounded Recheck

When the default DeepSeek path was used and no Opus escalation occurred, one additional DeepSeek adversarial pass may be run only when the artifact changed materially.

When a user-directed Sonnet or Opus path was used, do not add a DeepSeek pass automatically.

Do not exceed two default adversarial cycles automatically.

An approved Claude escalation replaces further automatic adversarial cycling for that decision unless the user explicitly requests another model.

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
