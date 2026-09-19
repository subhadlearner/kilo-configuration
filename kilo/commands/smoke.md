---
description: Orchestrate restartable FAST or FULL workflow smoke testing using approved disposable fixture projects
agent: smoke-orchestrator
model: openai/gpt-5.6-luna
---

# Smoke-Test Orchestrator

Run or resume a controlled workflow smoke test.

Supported invocation forms:

```text
/smoke FAST
/smoke FULL
/smoke FAST <fixture-id>
/smoke FULL <fixture-id>
/smoke RESUME <run-id>
/smoke STATUS <run-id>
```

Canonical profile names are:

- `FAST`
- `FULL`

Treat user phrases `FAST_SMOKE` and `FULL_SMOKE` as aliases when they occur inside the `/smoke` invocation.

Do not treat free-form text outside this command as an executable smoke run.

## Stage 1 — Load smoke contracts

Read:

- `kilo/smoke/STABLE-V0.1-SMOKE-TEST-PLAN.md`
- `kilo/smoke/fixtures.json`
- global `AGENTS.md`
- the minimum command/agent files needed for the next smoke stage

The runbook is the smoke-test policy.

The normal workflow command files remain authoritative for each underlying lifecycle stage.

## Stage 2 — Select profile

If profile is neither `FAST` nor `FULL`, stop with:

`SMOKE_PROFILE_REQUIRED`

Show:

```text
FAST
FULL
```

and one-line guidance from the runbook.

## Stage 3 — Select fixture project

Filter the fixture registry to entries supporting the selected profile.

If the user supplied `DEFAULT`:

- resolve the single registry entry whose `default_for` contains the selected profile
- require exactly one default
- if no unique default exists, return `SMOKE_FIXTURE_INVALID`

If the user supplied a fixture ID other than `DEFAULT`:

- require an exact registry match
- require that fixture supports the selected profile
- otherwise return `SMOKE_FIXTURE_INVALID`

If the user did not supply a fixture ID:

1. display the compatible fixture list
2. mark the registry default
3. ask the user to choose one, or explicitly choose `DEFAULT`
4. return `SMOKE_FIXTURE_REQUIRED`

Do not invent an unregistered fixture during an automated smoke run.

Examples:

```text
/smoke FAST DEFAULT
/smoke FULL full-minimal-api
```

## Stage 4 — Establish or resume smoke state

Smoke progress belongs in the target disposable project under:

```text
docs/verification/smoke/<run-id>.md
```

This location is inside the Contract-v1 evidence exclusion set.

A smoke-run record must contain:

- run ID
- profile
- fixture ID
- source/template repository
- release/tag and exact configuration SHA
- target project branch/worktree
- started-at timestamp
- current stage
- completed scenarios
- skipped scenarios with reason
- current authoritative artifact paths
- current applicable verification/review/diagnosis/waiver evidence
- checkpoint notes
- model invocation ledger
- observed cost/usage when available
- Claude invocation count
- defects
- final result

Do not rely on prior chat history to resume.

For `RESUME`, reconstruct state from this file and the repository.

For `STATUS`, report state only. Do not mutate or invoke models.

If a supplied run ID cannot be reconstructed safely, return:

`SMOKE_RUN_UNRECONSTRUCTABLE`

## Stage 5 — Prepare disposable project safely

Use the selected registry fixture and its product brief.

The configured source repository is a template/baseline, not architecture authority.

For a new run:

- require a disposable branch/worktree/clone
- never mutate protected `main`, `master`, `develop`, or `release`
- never discard unrelated changes
- never force-reset or clean a non-disposable repository
- record the exact baseline HEAD

If safe disposable state cannot be established, return:

`SMOKE_BLOCKED`

with the exact repository action required.

## Stage 6 — Determine the correct entry point

Inspect persisted repository artifacts first.

Use the runbook's Start/Resume Decision Table and Artifact Invalidation Rules.

Do not blindly start from `/grill`.

For a brand-new FULL run, normally begin at `/grill`.

For a brand-new FAST run, prefer the earliest stage required by the FAST profile and selected fixture. Reuse valid planning artifacts from the template/project when available.

When prior valid artifacts exist:

- reuse them
- record them in the smoke-run file
- do not regenerate them merely to spend a model call

When upstream authority changed:

- mark invalidated downstream artifacts
- resume from the earliest invalidated authority stage

## Stage 7 — Execute underlying workflows with preserved model routing

The smoke orchestrator coordinates; it does not replace stage ownership.

### Planning stages

For:

- `/grill`
- `/prd`
- `/architect`
- `/spec`

delegate to `planning-worker` with the workflow's normal model and an explicit execution mode:

- `AUTHOR`
- `CONTINUE`
- `RECONCILE_ONLY`

For the default Stable-v0.1 smoke path, use GPT-5.6 Sol for these planning stages.

If the child returns `USER_INPUT_REQUIRED`:

- persist continuation state
- relay only the required question batch to the user
- return `SMOKE_USER_INPUT_REQUIRED`
- on the next `/smoke RESUME <run-id>`, delegate with `MODE: CONTINUE`

### Project initialization

Execute the `/project-init` contract using the orchestrator's GPT-5.6 Luna model.

### DeepSeek execution stages

Delegate to `smoke-executor` for exactly one of:

- `/implement`
- `/verify`
- `/fix`
- `/diagnose`

The executor must read and obey the corresponding command contract.

### Waiver

Execute the `/waive` contract using GPT-5.6 Luna.

Human risk acceptance can never be fabricated by the smoke orchestrator.

If explicit authorization is required:

- persist state
- return `SMOKE_USER_INPUT_REQUIRED`
- resume only after the user supplies/approves the required waiver details

### Review

Apply the normal `/review` contract.

Freshness must be validated before any reviewer invocation.

Use:

1. DeepSeek `pre-reviewer`
2. GPT-5.6 Sol `code-reviewer` only when pre-review returns `READY_FOR_SENIOR_REVIEW`

Negative freshness scenarios must invoke **zero reviewer models**.

### Adversarial review

Use the default DeepSeek `adversary`.

When material findings require planning correction:

- reconcile through `planning-worker`
- use `MODE: RECONCILE_ONLY`
- do not restart AUTHOR

Do not use Claude during smoke testing unless the user explicitly authorizes that isolated paid invocation.

## Stage 8 — Profile scenario selection

### FAST

Run only the FAST_SMOKE scenarios defined by the runbook.

Default intent:

- static release/config checks
- project-init contract propagation when applicable
- first-spec uncommitted verification
- review-before-commit
- identical commit freshness
- content-mutation stale-evidence gate
- one direct `/fix → /verify` loop
- one fail-closed evidence scenario
- static model-routing checks

Skip diagnosis, waiver, adversarial runtime, repeated senior review, persistence-heavy integration, and paid Claude unless the changed framework area specifically requires them.

### FULL

Run the FULL_SMOKE scenarios defined by the runbook.

Start from the correct current stage and exercise all required acceptance criteria, including:

- full lifecycle authority
- fix
- diagnosis
- waiver
- review
- adversarial reconciliation
- arbitrary-stage resume
- upstream invalidation routing

## Stage 9 — Fixture budget enforcement

Treat the selected fixture's test budget as the default maximum application-test surface.

Do not add broad integration/E2E/load/soak suites merely for smoke realism.

If an underlying approved spec genuinely requires a check outside the fixture budget:

- the real workflow contract wins
- record why the budget was exceeded

Never mark a genuinely applicable required check `NOT_APPLICABLE` merely to save tokens.

## Stage 10 — Failure injection

Use only the failure recipes permitted by the selected fixture registry entry.

Before injecting a failure:

- record the clean checkpoint/state
- state the expected workflow route
- change only what is necessary for that scenario

Never use a security, auth, data-integrity, destructive, or vulnerability failure as the trivial waiver recipe.

After each scenario:

- restore/remediate through the workflow path being tested
- obtain fresh verification whenever implementation identity changed

## Stage 11 — Cost controls

Enforce the runbook's token/cost rules:

- one fixture per run
- minimal tests
- static checks instead of model calls where sufficient
- no regeneration of valid artifacts
- zero reviewer calls after freshness failure
- normally one successful senior review
- one bounded default adversarial challenge
- stop after two materially identical failed attempts
- Claude invocation count target: zero

Record every substantive model invocation in the run ledger.

## Stage 12 — Persist after every meaningful transition

Update the smoke-run record after:

- stage completion
- blocker
- user-input request
- failure injection
- verification result
- review result
- fix/diagnosis result
- waiver result
- checkpoint/restoration
- defect classification

This makes the smoke run restartable without chat history.

## Stage 13 — Completion

For FAST, finish with exactly one of:

- `FAST_SMOKE_PASS`
- `FAST_SMOKE_PASS_WITH_ENVIRONMENT_LIMITATION`
- `SMOKE_FAIL`

For FULL, finish with exactly one of:

- `FULL_SMOKE_PASS`
- `FULL_SMOKE_PASS_WITH_ENVIRONMENT_LIMITATION`
- `SMOKE_FAIL`

When paused rather than complete, use only:

- `SMOKE_FIXTURE_REQUIRED`
- `SMOKE_USER_INPUT_REQUIRED`
- `SMOKE_BLOCKED`
- `SMOKE_RUN_UNRECONSTRUCTABLE`

Never report PASS merely because only a subset of required scenarios executed.

## Output discipline

During execution, keep chat output concise.

Persist detailed evidence in the smoke-run record and normal workflow artifacts.

In chat report only:

- run ID
- profile + fixture
- stage/scenario just completed
- PASS/FAIL/BLOCKED
- next stage
- material model/cost note
- required user action, if any
- final status token
