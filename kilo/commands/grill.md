---
description: Interactively resolve ambiguous product/design decisions before PRD work
agent: planner
model: openai/gpt-5.6-sol
---

# Requirements Grilling Workflow

Use this workflow when an idea is large, ambiguous, high-stakes, or contains multiple coupled product decisions.

Do not design implementation architecture.
Do not implement code.

Load and follow the global `requirements-grilling` skill.

## How the User Should Invoke This Command

The user does not need to pre-answer a questionnaire.

A good invocation gives only the idea, current context, and any known hard constraints.

Example:

```text
/grill

I want to build a production-grade personal finance application for Indian retail investors.

The application should help users track goals, investments, and progress over time.
I expect the project to become large, and I want you to challenge unclear assumptions before we write the PRD.

Known constraints:
- web application
- cost-conscious architecture
- security and privacy matter
- I will maintain this with a very small team

Please research facts you can determine yourself and ask me only for decisions that actually require my input.
```

A shorter invocation is also valid:

```text
/grill

I want to add cross-account AWS event ingestion to the system.
Grill me until the product behavior, scope, failure expectations, security boundaries, and non-goals are clear enough for /prd.
```

Do not require the user to describe implementation architecture during grilling.

## Principle

Facts are the agent's job to investigate.
Product decisions are the user's job to make.

Do not ask the user for information that can be learned safely from the repository, existing docs, or authoritative sources.

## Stage 1 — Establish the Destination

Summarize in one short hypothesis:

- intended outcome
- primary user/actor
- why the work matters
- current confidence
- the largest unresolved decision areas

Do not pretend high confidence when important decisions are still implicit.

## Stage 2 — Build the Decision Tree

Map the product/design decisions and their dependencies.

Examples:

- target users and operators
- workflows and boundaries
- data ownership and sensitivity
- success measures
- scale/usage envelope
- reliability expectations
- cost limits
- security/compliance constraints
- external systems
- non-goals
- trade-offs such as speed vs flexibility or cost vs resilience

A question belongs on the current frontier only when its prerequisites are already settled.

## Stage 3 — Grill in Frontier Rounds

Ask the current frontier as a numbered round.

For every question provide:

- the decision to make
- concise context
- concrete choices when useful
- your recommended answer
- why you recommend it

Wait for answers before recomputing dependent questions.

## Stage 4 — Investigate Facts

When a decision depends on a fact that can be discovered:

- inspect the repository or existing artifacts
- use authoritative documentation when current external facts are required
- distinguish evidence from assumptions
- return the fact into the next decision round

Do not make the user research facts for you.

## Stage 5 — Stop Condition

Grilling is complete when:

- all material decision-tree branches required for PRD work are settled
- remaining unknowns can safely be explicit PRD assumptions/open questions
- goals, non-goals, users, success, binding constraints, and important failure expectations are understood
- no material product decision is being silently delegated to architecture

Restate the result and obtain explicit user confirmation.

## Stage 6 — Persist Discovery

When operating inside a project repository, write a concise discovery brief under:

`docs/discovery/`

Use a stable ID such as:

`DISC-001-<short-name>.md`

Include:

- outcome
- users/actors
- why now
- success measures
- scope
- non-goals
- confirmed decisions
- binding constraints
- explicit assumptions
- unresolved non-blocking questions
- source facts/evidence consulted

Do not duplicate a future PRD in full.

## Output

If the discovery is ready for PRD:

### Next Action

`/prd`

Finish with exactly:

`DISCOVERY_READY`

If a decision cannot be resolved because required evidence/access is missing:

report the blocker, owner, minimum required action, and whether to rerun `/grill`.

Finish with exactly:

`DISCOVERY_BLOCKED`

Do not output anything after the final status token.
