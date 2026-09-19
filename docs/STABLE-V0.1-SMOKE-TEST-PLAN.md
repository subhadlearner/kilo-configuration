# Stable v0.1 Smoke-Test Plan

## Purpose

This document defines the controlled validation plan for the Kilo workflow release tagged:

`stable_v_0.1.0`

The goal is to validate the **released workflow contract and state transitions** without repeating expensive model-routing and planning exercises that have already been exercised during framework development.

This plan is intentionally cost-controlled.

It validates the parts of the framework that materially affect correctness after the Stable v0.1 hardening work:

- first-spec verification before any implementation commit
- review-before-commit
- canonical implementation-state evidence
- freshness after committing identical verified contents
- stale-evidence rejection after implementation changes
- Git mode/type identity
- verification mutation detection
- normal repair loop
- human-authorized waiver flow
- review gating
- persisted evidence
- narrow adversarial reconciliation
- project-init propagation of the Stable-v0.1 evidence contract

It does **not** require Claude for the default smoke test.

---

## Release Under Test

Test the exact release/tag:

```text
stable_v_0.1.0
```

Before execution, confirm that the tag points to the final intended Stable v0.1 commit.

Record in the smoke-test report:

- `kilo-configuration` tag and commit SHA
- `production-ai-project` tag/commit SHA or exact baseline commit used as the disposable test project
- Kilo Code version
- Git version
- OS
- runtime/tool versions materially relevant to the chosen fixture

Do not run the smoke test against an unrecorded moving branch.

---

## Cost Policy

### Default model usage

Use the normal Stable-v0.1 routing:

| Activity | Model |
| --- | --- |
| Implementation | DeepSeek Flash |
| Verification | DeepSeek Flash |
| Fix | DeepSeek Flash |
| Diagnosis, if genuinely needed | DeepSeek Flash |
| Default adversarial review | DeepSeek Flash |
| Pre-review | DeepSeek Flash |
| Senior review | GPT-5.6 Sol |
| Lightweight project-init/orchestration | GPT-5.6 Luna |

### Claude policy

Do **not** invoke Claude Sonnet, Haiku, or Opus during this smoke test unless the human owner explicitly requests one isolated paid routing test.

Claude routing should otherwise be validated statically from configuration and agent definitions.

### Planning-model policy

Do not rerun broad PRD/architecture exercises merely to prove that GPT-5.6 Sol can be called.

Use static inspection for already-established routing and lifecycle rules.

If a minimal specification is needed for the disposable smoke fixture, keep it deliberately small and bounded.

### Stop-loss rule

If the same workflow defect causes two materially identical failed attempts:

1. stop repeating the expensive model call,
2. preserve the evidence,
3. diagnose the framework defect,
4. fix the framework in a later version rather than burning additional smoke-test budget.

---

## Test Environment

Use a disposable branch or disposable clone/worktree of the project-side template.

Recommended target:

`subhadlearner/production-ai-project`

Do not perform the smoke test directly on its protected `main` branch.

Recommended branch:

```text
smoke/stable-v0.1
```

The smoke-test implementation must be intentionally trivial so failures are attributable to workflow mechanics rather than application complexity.

A suitable fixture is a tiny deterministic utility with:

- one observable behavior
- one unit-test seam
- no external cloud dependency
- no database
- no network call
- no authentication
- no paid service

Examples include:

- a small string/number transformation
- a deterministic validation function
- a tiny command or API-free library function

The fixture is not the subject of the test. The workflow is.

---

# Phase 0 — Static Release Gate

**Model cost: zero or negligible**

Before running any mutating workflow, inspect the released configuration.

## 0.1 Lifecycle

Confirm the released global policy retains:

```text
/grill (optional)
→ /prd
→ /architect
→ /project-init
→ /spec
→ /implement
→ /verify
→ CLEAR or CLEAR_WITH_EXCEPTION
→ /review
```

Confirm repair paths remain present:

```text
/verify → NOT_DONE → /fix → /verify
/verify → NOT_DONE → /diagnose → /fix → /verify
/verify → NOT_DONE → /waive → CLEAR_WITH_EXCEPTION → /review
/review → CHANGES_REQUIRED or REQUEST CHANGES → /fix → /verify → /review
```

## 0.2 Planning modes

Confirm `planning-worker` supports exactly the intended modes:

- `AUTHOR`
- `CONTINUE`
- `RECONCILE_ONLY`

Confirm adversarial reconciliation uses `RECONCILE_ONLY` instead of rerunning the complete authoring workflow.

## 0.3 Model routing

Confirm:

- GPT-5.6 Sol: reasoning/planning and senior review
- GPT-5.6 Luna: project initialization/light orchestration
- DeepSeek Flash: implementation, verification, fix, diagnosis, pre-review, default adversary
- Claude Sonnet/Opus: optional paid escalation only

Confirm no mandatory Claude call exists in the normal lifecycle.

## 0.4 Security and cost policy

Confirm:

- OWASP Top 10:2025 risk taxonomy remains referenced
- security-verification skill remains present
- cloud/operating cost remains a first-class architecture requirement
- fixed, variable, storage, network, observability, scaling, and operational-burden costs remain considered

## 0.5 Evidence contract

Confirm the following files are byte-for-byte identical:

```text
kilo-configuration:
kilo/contracts/implementation-state-evidence-v1.md

production-ai-project:
docs/workflow/IMPLEMENTATION-STATE-EVIDENCE-V1.md
```

Confirm Contract v1 contains:

- canonical manifest authority
- path identity
- effective Git mode/type identity
- content/blob identity
- `MATCH`
- `MISMATCH`
- `UNRECONSTRUCTABLE`
- fixed evidence exclusion set
- ignored-file boundary
- verification-environment boundary
- waiver binding
- fail-closed malformed evidence behavior

### Phase 0 pass condition

All static invariants are present with no contradiction.

If Phase 0 fails, do not spend model budget on runtime smoke tests.

---

# Phase 1 — Project Initialization Contract

**Expected paid/model use: GPT-5.6 Luna only if project-init must actually run**

Use a disposable project repository or resettable smoke branch.

Run `/project-init` only if necessary for the chosen fixture.

## Validate

Project initialization must ensure:

```text
docs/discovery/
docs/prd/
docs/architecture/
docs/adr/
docs/specs/
docs/diagnostics/
docs/verification/
docs/verification/waivers/
docs/reviews/
docs/workflow/
```

It must also ensure:

```text
docs/workflow/IMPLEMENTATION-STATE-EVIDENCE-V1.md
```

The project copy must be byte-for-byte identical to the global canonical contract.

### Negative test

Temporarily make exact contract synchronization impossible in the disposable environment.

Expected result:

```text
PROJECT_INIT_BLOCKED
```

The workflow must not silently create an independently summarized contract.

Restore the disposable environment before continuing.

---

# Phase 2 — First-Spec, Fully Uncommitted Implementation

This is a critical Stable-v0.1 scenario.

**Expected model use: DeepSeek Flash for implementation and verification**

## Preconditions

The repository must already have an existing baseline HEAD from project/template initialization.

The smoke implementation itself must have **no implementation commit**.

Create or use one small approved smoke specification.

Run:

```text
/implement
```

Do not commit the resulting source or test changes.

Confirm Git shows implementation changes as:

- modified tracked files, and/or
- untracked non-ignored files

Then run:

```text
/verify
```

## Expected verification behavior

The verification report must record:

- branch
- verification base HEAD
- `implementation-state-evidence-v1`
- canonical implementation-state manifest
- implementation-state fingerprint
- pre/post freshness outcome
- verification commands and results
- acceptance-criterion evidence
- materially relevant environment facts

The canonical manifest must include every identity-bearing implementation path even though those files are uncommitted.

Expected state when checks pass and no verification command mutates implementation state:

```text
Verification Result: DONE
Delivery Gate: CLEAR
Freshness: MATCH
```

### Pass condition

A first-spec implementation can reach `CLEAR` without an implementation commit.

A dirty working tree by itself must not be treated as a blocker.

---

# Phase 3 — Review Before Commit

Immediately after Phase 2, with the verified implementation still uncommitted, run:

```text
/review
```

## Expected behavior

Before invoking any reviewer:

- applicable verification evidence is selected by specification/change + branch
- current canonical manifest is reconstructed relative to the recorded base HEAD
- current manifest is byte-for-byte identical to the persisted manifest
- freshness is `MATCH`

Then:

1. DeepSeek pre-review runs
2. if it returns `READY_FOR_SENIOR_REVIEW`, GPT-5.6 Sol senior review runs
3. review evidence is persisted under `docs/reviews/`

### Pass condition

Review is allowed before the implementation is committed.

The workflow must not demand a commit solely to establish freshness.

---

# Phase 4 — Commit Identical Verified State

After successful verification/review of the uncommitted implementation:

1. commit the exact verified implementation contents,
2. do not modify those contents,
3. keep workflow evidence changes separate from implementation identity as defined by the contract.

Then run freshness validation again through `/review` or the minimum supported review-entry path.

## Expected behavior

HEAD is now different from verification-time HEAD.

However, reconstruction relative to the original verification base HEAD must produce the **same canonical implementation-state manifest**.

Expected:

```text
MATCH
```

### Pass condition

A later commit of identical verified state does **not** invalidate verification.

Commit identity must not be mistaken for implementation-state identity.

---

# Phase 5 — Content Mutation Invalidates Evidence

Starting from a fresh valid verified state:

1. change one implementation byte in an identity-bearing source/test/configuration path,
2. do not rerun verification,
3. invoke `/review`.

## Expected behavior

Review must stop **before pre-review**.

Expected freshness:

```text
MISMATCH
```

Expected action:

```text
fresh /verify required
```

### Pass condition

The same HEAD cannot make evidence appear fresh when working-tree contents changed.

No reviewer model should be called after freshness failure.

---

# Phase 6 — Git Mode/Type Mutation Invalidates Evidence

This validates the final Stable-v0.1 hardening.

Use a disposable file where Git mode can be represented reliably on the test environment.

Examples:

- regular file `100644` → executable `100755`
- regular file → symbolic link where reliably supported
- another deterministic Git mode/type transition supported by the environment

Do not change the file contents if testing executable-bit identity.

## Procedure

1. establish fresh verification with `MATCH`,
2. change only effective Git mode/type,
3. do not rerun verification,
4. invoke review freshness validation.

## Expected behavior

Canonical manifest differs even when blob contents are identical.

Expected:

```text
MISMATCH
```

If the current platform cannot determine the effective Git mode/type reliably, expected:

```text
UNRECONSTRUCTABLE
```

In both cases review must fail closed.

### Windows note

Windows may not expose executable-bit semantics in the same way as Unix-like filesystems.

Do not fake a result.

If the selected mode transition cannot be represented/detected reliably under the current Git/filesystem configuration, record `UNRECONSTRUCTABLE` for that case or run this specific test in an appropriate Git-compatible environment such as WSL.

---

# Phase 7 — Verification Command Mutates Implementation State

This tests pre/post verification stability.

Use a disposable verification command or fixture behavior that intentionally changes one non-evidence identity-bearing file while verification executes.

Do not weaken or alter real quality checks merely to make this scenario.

## Expected behavior

Required checks may all pass factually.

The verification result may therefore be:

```text
Verification Result: DONE
```

But pre/post canonical manifests differ.

Expected delivery state:

```text
Freshness: MISMATCH
Delivery Gate: BLOCKED
```

Expected next action:

```text
/verify
```

after stabilizing the repository.

### Pass condition

A green test command is not sufficient when the code changed during verification.

---

# Phase 8 — Normal Failure and Fix Loop

Introduce one simple deterministic implementation defect.

Run:

```text
/verify
```

Expected:

```text
Verification Result: NOT_DONE
Delivery Gate: BLOCKED
```

Confirm a history-preserving verification report is written.

Then run:

```text
/fix
```

The fix workflow must consume the latest **applicable** persisted evidence for this specification/change and branch.

Then rerun:

```text
/verify
```

Expected after a correct fix:

```text
Verification Result: DONE
Delivery Gate: CLEAR
Freshness: MATCH
```

### Pass condition

No historical `NOT_DONE` report is overwritten.

The new verification produces a new report.

---

# Phase 9 — Optional Diagnosis Loop

Run this phase only if a bounded deterministic diagnostic fixture can be created cheaply.

Otherwise mark it:

```text
NOT_EXECUTED — already covered by prior smoke testing / no cost-effective diagnostic fixture
```

For an unclear or intermittent failure:

```text
/verify
→ NOT_DONE
→ /diagnose
→ /fix
→ /verify
```

Validate:

- diagnosis persists under `docs/diagnostics/`
- diagnosis does not redefine intended behavior
- fix consumes relevant diagnosis/evidence
- fresh verification is required afterward

Do not manufacture an elaborate flaky system merely to exercise this path.

---

# Phase 10 — Human Waiver Flow

Create a safe, deliberately chosen smoke failure that project policy allows the human owner to accept temporarily.

Do **not** use a security-critical or destructive failure merely for testing.

Start from:

```text
Verification Result: NOT_DONE
Delivery Gate: BLOCKED
```

Run:

```text
/waive
```

Provide explicit human authorization, bounded expiry, justification, residual risk, and remediation.

## Expected waiver behavior

The waiver must remain separate from the verification report.

It must bind to:

- exact verification report
- canonical implementation-state manifest
- fingerprint
- exact accepted failure set
- expiry
- human authorization

Expected effective gate:

```text
Verification Result: NOT_DONE
Delivery Gate: CLEAR_WITH_EXCEPTION
```

The original verification truth remains `NOT_DONE`.

Then run:

```text
/review
```

Reviewers receive both:

- original failed verification evidence
- active waiver

### Negative test

Change one implementation-state identity field after the waiver:

- content, or
- mode/type

Then attempt review again without fresh verification/waiver.

Expected:

```text
MISMATCH
```

and the waiver must no longer authorize review of that state.

---

# Phase 11 — Malformed/Unreconstructable Evidence Fails Closed

Use only disposable copied evidence.

Do not corrupt canonical repository history.

Test one inexpensive case such as:

- remove a required field from a copied verification report,
- make the recorded base HEAD unavailable in a disposable shallow/altered environment,
- otherwise make exact reconstruction impossible without changing production artifacts.

## Expected behavior

Freshness:

```text
UNRECONSTRUCTABLE
```

Review/waiver use stops.

Expected next action:

```text
/verify
```

### Pass condition

The workflow does not infer freshness from:

- current HEAD
- matching fingerprint alone
- a newer unrelated verification report
- chat history

---

# Phase 12 — Evidence-Path Exclusion Test

Starting from fresh verified implementation state:

1. add only a new verification/review/diagnostic evidence artifact under an excluded path,
2. do not alter implementation identity.

Normative excluded paths are exactly:

```text
docs/verification/**
docs/reviews/**
docs/diagnostics/**
```

## Expected behavior

The implementation-state canonical manifest remains unchanged.

Freshness remains:

```text
MATCH
```

### Counter-test

Modify one of:

```text
docs/specs/**
docs/architecture/**
docs/adr/**
AGENTS.md
.kilo/**
source/test files
build/package configuration
lockfiles
CI/IaC configuration
```

Expected:

```text
MISMATCH
```

---

# Phase 13 — Narrow Adversarial Reconciliation

This phase validates orchestration, not Claude routing.

Use DeepSeek as the default adversary.

Choose one small architecture/spec artifact with a deliberately challengeable but bounded decision.

Run the appropriate adversarial path.

When findings require reconciliation, confirm the planning worker receives:

```text
RECONCILE_ONLY
```

## Expected behavior

Reconciliation reads only the challenged artifact plus the minimum affected contract/ADR/spec context.

It must not restart the entire authoring workflow.

At most one targeted follow-up adversarial pass should occur when the challenged decision materially changes, according to current workflow policy.

### Pass condition

No full `AUTHOR` rerun occurs merely because adversarial findings were returned.

No Claude call is required.

---

# Phase 14 — Review Persistence and Blocking Behavior

Create or retain one case where the DeepSeek pre-reviewer finds a genuine blocking defect.

Run:

```text
/review
```

Expected:

```text
CHANGES_REQUIRED
Senior Review: NOT_RUN
```

The review report must persist:

- complete pre-review findings
- senior review status `NOT_RUN`
- reason senior review was skipped
- next action `/fix → /verify → /review`

GPT-5.6 Sol senior review must not run.

After fixing and freshly verifying the issue, rerun review.

### Pass condition

Expensive senior review is skipped while cheap pre-review blockers remain.

---

# Phase 15 — Static Claude Routing Check

**Do not invoke Claude.**

Inspect released configuration and confirm:

- Sonnet adversary exists
- Opus adversary exists
- permission requires approval where designed
- explicit user selection can route to Claude
- no normal smoke-test stage requires Claude

Record:

```text
CLAUDE_RUNTIME_TEST: NOT_RUN_BY_POLICY
```

This is a pass when static routing is correct.

A real paid cross-model invocation can be validated later during genuine project work or an explicitly approved isolated test.

---

# Smoke-Test Evidence

Create a smoke-test report in a disposable project location such as:

```text
docs/verification/SMOKE-STABLE-V0.1-001.md
```

The report should contain:

## Release Identity

- Kilo configuration tag/SHA
- test-project baseline SHA
- Kilo version
- Git version
- OS/environment

## Cost Summary

Record, where observable:

- DeepSeek usage/cost
- OpenAI invocation count
- Claude invocation count
- Claude spend

Expected default:

```text
Claude invocation count: 0
Claude spend: 0
```

## Scenario Results

For every phase:

- executed / not executed
- PASS / FAIL / NOT_APPLICABLE
- relevant artifact paths
- model invoked
- concise evidence
- defect reference when failed

## Defects

Classify failures as:

- FRAMEWORK_DEFECT
- TEST_FIXTURE_DEFECT
- ENVIRONMENT_LIMITATION
- MODEL_VARIANCE
- DOCUMENTATION_DEFECT

Do not silently patch the Stable-v0.1 tag during the test.

Any framework defect produces a later version/fix branch.

---

# Stable-v0.1 Acceptance Criteria

The smoke test passes when all **required** scenarios below pass:

1. static release invariants are intact
2. project-init propagates the exact evidence contract
3. first-spec implementation verifies while fully uncommitted
4. review can run before implementation commit
5. committing identical verified state preserves `MATCH`
6. content changes produce `MISMATCH`
7. mode/type changes produce `MISMATCH` or fail closed as `UNRECONSTRUCTABLE` when the platform cannot represent them reliably
8. verification-time implementation mutation produces `BLOCKED`
9. deterministic failure → fix → fresh verify works
10. waiver preserves `NOT_DONE` while establishing only `CLEAR_WITH_EXCEPTION`
11. stale waiver/evidence cannot authorize changed implementation state
12. malformed/unreconstructable evidence fails closed
13. evidence paths do not invalidate implementation identity while specs/architecture/code/config do
14. adversarial reconciliation remains `RECONCILE_ONLY`
15. blocking pre-review skips senior review and persists `Senior Review: NOT_RUN`
16. no mandatory Claude runtime call occurs

Optional diagnosis testing may be omitted when creating a realistic intermittent fixture would add disproportionate cost.

---

# Release Decision

After execution, record one release-validation status:

### `SMOKE_PASS`

All required Stable-v0.1 acceptance criteria passed.

### `SMOKE_PASS_WITH_ENVIRONMENT_LIMITATION`

All required workflow semantics passed, but a platform-specific mode/type case could not be represented and correctly failed closed as `UNRECONSTRUCTABLE`.

### `SMOKE_FAIL`

One or more required workflow semantics failed.

Do not move the Stable-v0.1 tag to incorporate fixes discovered during smoke testing.

Instead:

1. preserve the failing release evidence,
2. fix on a new branch,
3. review/verify the framework fix,
4. publish a subsequent version such as `stable_v_0.1.1`.

---

## Expected Stable-v0.1 Outcome

The intended result is a workflow that proves:

> The exact repository state that was deterministically verified is the state being reviewed, regardless of whether that implementation had already been committed, while stale, malformed, changed, or unreconstructable evidence fails closed.

The smoke test should prove that contract with the minimum practical model spend.
