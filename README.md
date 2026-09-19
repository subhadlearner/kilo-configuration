# Kilo Configuration

Opinionated global configuration for an AI-assisted software-engineering workflow built around [Kilo Code](https://kilo.ai/).

This repository contains the reusable **global workflow layer** used across projects: engineering policy, lifecycle commands, specialized agents, model-routing rules, permissions, reusable skills, and the Stable-v0.1 implementation-state evidence contract.

The companion project-side template is:

- [subhadlearner/production-ai-project](https://github.com/subhadlearner/production-ai-project)

## What this repository does

The configuration establishes a production-oriented workflow for moving from an idea to reviewed implementation while keeping stage ownership explicit:

```text
/grill (optional)
  ↓
/prd
  ↓
/architect
  ↓
/project-init
  ↓
/spec
  ↓
/implement
  ↓
/verify
  ↓
CLEAR or CLEAR_WITH_EXCEPTION
  ↓
/review
```

It also defines repair and investigation loops for failed verification, diagnosis, fixes, human-authorized waivers, and adversarial review.

The main design goals are:

- production-grade engineering discipline
- explicit PRD → architecture → specification → implementation ownership
- deterministic verification before AI code review
- review-before-commit support
- persistent verification and review evidence
- bounded human risk acceptance without rewriting failed verification
- security verification based on OWASP-oriented risk coverage
- cost-aware architecture and infrastructure choices
- low-cost model routing for high-volume implementation work
- optional higher-cost model escalation only when justified or explicitly requested

## Repository layout

The deployable Kilo configuration lives under `kilo/`.

```text
kilo/
├── AGENTS.md
├── kilo.jsonc
├── agents/
├── commands/
├── contracts/
└── skills/
```

### `AGENTS.md`

Defines the global AI engineering policy, including:

- lifecycle and stage authority
- branch/worktree discipline
- TDD and diagnosis expectations
- verification and review rules
- security requirements
- cloud and operating-cost considerations
- model routing and escalation
- smoke-test cost policy
- context-quality rules

### `commands/`

Global slash-command workflows such as:

- `/grill`
- `/prd`
- `/architect`
- `/project-init`
- `/spec`
- `/implement`
- `/verify`
- `/waive`
- `/review`
- `/fix`
- `/diagnose`
- `/adversarial-check`

### `agents/`

Specialized agents used by the workflows, including:

- planning worker
- implementation/builder
- debugger
- DeepSeek pre-reviewer
- GPT-5.6 Sol senior code reviewer
- default and optional adversarial reviewers

Planning workflows support the explicit execution modes:

- `AUTHOR`
- `CONTINUE`
- `RECONCILE_ONLY`

The reconciliation mode prevents an adversarial review from unnecessarily rerunning an entire planning workflow.

### `contracts/`

Contains the normative implementation-state evidence contract used by `/verify`, `/review`, and `/waive`.

Stable v0.1 uses:

`contracts/implementation-state-evidence-v1.md`

The contract supports verification and review **before the implementation is committed**. It binds reusable evidence to a canonical repository-state manifest containing:

- repository-relative path
- effective Git mode/type
- content/blob identity

Freshness outcomes are fail-closed:

- `MATCH`
- `MISMATCH`
- `UNRECONSTRUCTABLE`

A later commit of the exact same verified repository state does not invalidate otherwise-fresh evidence.

### `skills/`

Curated reusable engineering skills covering areas such as:

- .NET
- Python
- PostgreSQL
- SQLite
- React / Next.js / frontend engineering
- AWS serverless
- AWS IAM
- DynamoDB
- Azure architecture
- TDD
- diagnosis
- adversarial review
- security verification
- requirements grilling

Skills provide implementation guidance but do not override approved PRDs, architecture, ADRs, specifications, or project-level rules.

### `kilo.jsonc`

Defines the global Kilo model and permission configuration.

The Stable-v0.1 routing strategy is intentionally cost-aware:

| Work | Default |
| --- | --- |
| PRD / architecture / specification | GPT-5.6 Sol |
| Project initialization / lightweight orchestration | GPT-5.6 Luna |
| Implementation / verification / fixes / diagnosis | DeepSeek Flash |
| Pre-review | DeepSeek Flash |
| Senior code review | GPT-5.6 Sol |
| Default adversarial review | DeepSeek Flash |
| Optional cross-model review | Claude Sonnet |
| Rare premium escalation | Claude Opus |

Explicit user model selection remains authoritative where supported.

## Verification and review model

Verification is factual:

- `DONE`
- `NOT_DONE`

Delivery-gate state is separate:

- `CLEAR`
- `BLOCKED`
- `CLEAR_WITH_EXCEPTION`

A failed verification is never rewritten to `DONE`.

A human-authorized waiver can establish `CLEAR_WITH_EXCEPTION` for an exact verification report, implementation state, failure set, and expiry while preserving the original `NOT_DONE` result.

Production review is cost-controlled:

1. DeepSeek pre-review
2. GPT-5.6 Sol senior review only when pre-review returns `READY_FOR_SENIOR_REVIEW`
3. CI
4. PR / merge
5. human-approved production deployment

## Architecture and operating cost

Cloud and operating cost are first-class architecture concerns.

Architecture is expected to document and challenge:

- fixed recurring cost
- variable/usage-based cost
- storage cost
- network/data-transfer cost
- observability cost
- scaling characteristics
- operational burden
- major cost drivers and risks
- irreversible platform or recurring-cost commitments

The default preference is the simplest architecture that provides the required production qualities at the lowest practical operating cost.

## Installation / local use

The contents of the repository's `kilo/` directory are intended to represent the global Kilo configuration directory.

On Windows, the global configuration location is typically:

```text
C:\Users\<username>\.config\kilo
```

The effective layout should therefore look like:

```text
C:\Users\<username>\.config\kilo
├── AGENTS.md
├── kilo.jsonc
├── agents\
├── commands\
├── contracts\
└── skills\
```

How the repository is cloned, copied, linked, or synchronized into that location is a local setup choice.

Keep credentials and API keys out of version control. Use environment variables or provider-specific secure configuration for secrets.

## About README files in the global config directory

A plain `README.md` is documentation only.

Kilo's global instruction loading is based on recognized instruction/configuration locations such as `AGENTS.md`, `kilo.jsonc`, and the dedicated commands/agents directories. A `README.md` placed in the global configuration directory does not become a global instruction file merely because it is present there.

This repository-level README is kept at the repository root so GitHub can present normal project documentation. If a copy also ends up under the local Kilo configuration directory, it is still harmless ordinary documentation.

## Project initialization

`/project-init` operationalizes approved architecture into a project repository.

Among other things it prepares project instructions, verification commands, workflow artifact directories, skill coverage, and synchronizes the Stable-v0.1 evidence contract into:

```text
docs/workflow/IMPLEMENTATION-STATE-EVIDENCE-V1.md
```

The project copy must match the global canonical contract exactly.

## Versioning

Stable framework releases are tagged in Git.

The first frozen release is intended to be:

```text
stable-v-0.1.0
```

After a stable tag, framework changes should be treated as deliberate versioned changes rather than incidental edits.

## Status

Stable-v0.1 focuses on making the workflow deterministic, evidence-driven, production-oriented, and practical to operate without unnecessary frontier-model or infrastructure spend.

The next validation step after freezing the release is a controlled smoke test of the lifecycle and evidence state transitions.
