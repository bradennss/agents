---
name: spec-driven-features
description: >
  Turn a sizable or fuzzy feature request into a written spec, a plan, and a dependency-ordered task list before writing code, so the agent builds what was actually asked for and the work stays traceable. Use when starting a nontrivial new feature, when the request is big or ambiguous, when the user wants a spec, design doc, or planning artifacts, or when past work drifted from what was agreed, even if the user just says "let's plan this out first."
---

# Spec-driven features

For anything bigger than a small fix, write the spec before the code. The spec, not the chat, is the source of truth for what "done" means. This keeps the agent from guessing, keeps the work traceable back to a requirement, and stops the quiet drift where session 3 forgets what session 1 agreed to.

The flow is: **spec -> plan -> tasks -> implement -> verify**. Each step writes a file that feeds the next, so a fresh session can pick up the thread.

## When to use this

Use it for a nontrivial feature: one that touches several files, has real acceptance criteria, or is fuzzy enough that you'd otherwise guess. Skip it for a typo, a one-file rename, or an obvious small fix, where the spec costs more than the work.

If the project uses the `tracking-project-work` skill, the tasks this produces go straight into that project's ledger. The two fit together: this skill scopes a feature, that one tracks it across sessions.

## Where the artifacts live

Put them under the repo so they're version-controlled and reviewable:

```
specs/<feature-name>/
  spec.md    # what and why
  plan.md    # how
  tasks.md   # the ordered backlog
```

## Step 1: write the spec

Capture what the feature does and how you'll know it works. Don't describe the implementation yet.

```markdown
# Spec: <feature name>

## Problem

Who needs this and why, in a sentence or two.

## User stories

- As a <user>, I can <action> so that <outcome>.

## Requirements

- FR-1: <a functional requirement, testable>
- FR-2: ...

## Acceptance criteria

- <observable check that proves a requirement is met>

## Out of scope

- <what this feature deliberately does not do>

## Open questions

- <anything you need the user to decide before building>
```

Stop here and get the open questions answered before planning. A wrong assumption caught now costs one edit; caught after coding it costs a rewrite.

## Step 2: write the plan

Now decide how to build it. This is where the design lives.

```markdown
# Plan: <feature name>

## Approach

The shape of the solution in a few sentences.

## Components touched

- <file or module>: <what changes>

## Data and API changes

- <new tables, migrations, endpoints, or contracts>

## Risks and rollback

- <what could break, and how to undo it>

## Test strategy

- <how each acceptance criterion gets checked>
```

## Step 3: break it into tasks

Turn the plan into small, ordered, independently verifiable tasks. Each is a thin vertical slice that works end to end, built in dependency order.

```markdown
# Tasks: <feature name>

- [ ] T2010 Add the signup form and route
  - spec: FR-1, FR-2
  - depends on: none
  - scope: src/auth/signup.ts, tests/auth/signup.test.ts
  - done when: a valid submission creates an account; a duplicate email is rejected; tests pass

- [ ] T2020 [P] Add the confirmation email
  - spec: FR-3
  - depends on: T2010
  - scope: src/email/confirm.ts
  - done when: a new account triggers one email with a working link
```

Task rules:

- **Stable IDs** (`T2010`), never reused, so commits and PRs can reference them. Leave gaps so you can slot work in later.
- **Explicit dependencies**, so a blocked task can't be started early.
- **`[P]` only when truly parallel-safe**: the task's files don't overlap another ready task's files. This is the marker for handing slices to subagents.
- **A done-when line with observable checks**, not "implementation complete".
- **Small enough to build and verify in one sitting.** If a task title needs "and", it's two tasks.

## Step 4: implement one task at a time

- Plan before you edit. For multi-file or uncertain work, confirm the plan with the user before changing code.
- Take one task, or one wave of parallel-safe tasks, then stop and verify.
- Verify for real against the done-when line (see "Done means verified" in the global `AGENTS.md`), then check the task off and commit with the task ID in the message.
- If a task ledger exists, mark it done there too.

## The rule that prevents drift

Don't quietly widen scope. If you hit a new requirement or a better idea mid-build, update `spec.md` and the task list first, then build it. An untracked change the user never agreed to is how the code and the spec drift apart. The spec is the contract, so change the contract in the open.
