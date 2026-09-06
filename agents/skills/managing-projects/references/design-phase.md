# Design phase: spec-driven

Turn intent into a written spec, then into an epic and task graph. The spec is the source of truth for what and why. The graph is how the build phase runs.

Do not skip to code. The point of this phase is to make the decisions before they get expensive, and to leave a graph that any session can pick up.

## Flow

1. **Write the spec.** Capture the outcome, not the implementation. See the template below.
2. **Clarify.** Read the spec back and find every ambiguity, contradiction, or missing decision. Each one becomes a `decision` bead, not a silent guess.
3. **Write the technical plan.** Architecture, interfaces, data contracts, dependencies, and how each piece gets verified. This can live in the spec doc or its own doc.
4. **Decompose.** Break the plan into epics (one per slice) and tasks, then wire dependencies. This is the graph.

Keep technology choices out of the spec until the requirements are stable. Deciding the database before you know the access patterns is a rabbit hole.

## Spec template

Keep it in the repo as a markdown doc, for example `docs/specs/<feature>.md`. Link the spec from the epic bead's context.

```markdown
# <Feature name>

## Goal

One or two sentences. What outcome does this create and for whom.

## Non-goals

What this explicitly does not do. This is where scope creep dies.

## Requirements

- R1: <requirement>. Acceptance: <observable, testable condition>.
- R2: <requirement>. Acceptance: <observable, testable condition>.

## Constraints

Platform, performance, security, compatibility, deadlines.

## Risks and open questions

- <risk or unknown>. How we will resolve or de-risk it.

## Technical plan

Architecture, interfaces, data contracts, and the verification approach per piece. Fill this in after the requirements are stable.
```

Every requirement needs acceptance criteria that someone can check by observing the system. "Login works" is not testable. "A valid email and password returns a session cookie and a 200; a wrong password returns 401" is.

## Clarify into decisions

For each ambiguity, open a decision bead and record the outcome as memory once resolved.

```bash
bd create "Decide session storage: JWT vs server sessions" -t decision -p 1
# ...resolve it, then:
bd remember "Sessions use short-lived JWTs with refresh tokens in httpOnly cookies. Decided in bd-12."
bd close bd-12 -r "Chose JWT + refresh cookie. See memory."
```

A blocked task can depend on a decision, so real work does not start on a shaky assumption:

```bash
bd create "Implement session middleware" -t task --deps depends-on:bd-12
```

## Decompose into the graph

Turn the plan into epics and tasks. One epic per vertical slice. See `decomposition.md` for how to slice.

```bash
# Epic per slice
bd create "User can sign in" -t epic -p 1                 # -> bd-a3f8

# Tasks under the epic, hierarchical IDs
bd create "Sign-in walking skeleton" -t task --parent bd-a3f8 \
  --acceptance "Real form posts credentials, server returns a session, redirects home, covered by one e2e test"

# A task that needs another task's output is blocked by it
bd create "Remember me across restarts" -t task --parent bd-a3f8 --deps depends-on:bd-a3f8.1
```

When the graph is wired, `bd ready` shows the tasks with no open blockers. That is where the build phase starts.

## Right-sizing the effort

Match the process to the change.

- A large or risky new capability earns the full spec, clarify, plan, decompose flow.
- A small, well-understood change can go straight to a task bead with clear acceptance criteria. Do not write a PRD for a one-line fix.

The test is whether the decisions are already obvious. If they are, skip ahead. If they are not, write them down first.
