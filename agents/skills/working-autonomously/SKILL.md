---
name: working-autonomously
description: "Take a task all the way to done: ask every important question up front, then build, prove, review, and commit it without further approval. Use when the mode for a task is autonomous, when the user says to run with it, build it end to end, or not to check back, when trivial work comes up outside a collaborative session, when the user hands a task over before leaving, or when a brief doesn't name a mode."
---

# Working autonomously

You own every decision the user didn't make. The task ends when the work is done, not when the next question comes up.

- Trivial work, meaning one file and no behavior change, takes the short path: the git checks under "Check the base" in `setting-up-dev-environment`, skipping the worktree and that section's run of the repo's checks, then the edit, then the repo's own checks, then a commit with `finishing-up-changes`. No question pass, no proving run, no review loop, nothing to tear down.
- A task that ends in findings rather than a diff, like an audit or research, has nothing to build or land. Do the work, then report what you found.
- A subagent works in the directory its brief names. It makes no worktree, commits and lands nothing, runs no reviewer of its own, and delegates only when the brief says to. Setup, commits, landing, and teardown belong to whoever sent the brief, and a blocker goes in what it returns.

## Ask everything up front, once

Read the code first with `planning-a-change`, then ask every open decision in one pass, each with the default you'd take, so the user can approve the lot in a sentence.

- What done means, and what's out of scope.
- Any shape other code will depend on. Follow `agreeing-an-interface`.
- The direction for a screen, when the task has one. Follow `designing-frontends` and put the rough takes in the pass.
- Access you'll need: credentials, services, test accounts, seed data.
- Where the branch goes when you're done.
- Anything in the request that reads two ways.

Leave out questions the repo already answers. No answer means take the defaults you named, except an irreversible one like merging or pushing, which silence doesn't settle. With nobody to ask, like a subagent working from a brief, take the defaults and name them in the report.

## Then decide for yourself

After the pass, stop asking. For a question you didn't foresee, pick what matches the patterns in the codebase and the intent the user stated, and keep a list of those choices for the report.

## Three things stop you

Nothing else earns an interruption, apart from where a skill you're following tells you to stop.

1. A scope change. The task is bigger or different, part has to come out, the cost moves a long way from what the user agreed, or finishing it properly means work they never asked for.
2. A blocker you can't clear. Uncommitted changes that aren't yours, a diverged history, a base that doesn't build, tests that already failed before your first edit, missing credentials or access, a service you can't reach, or changing the data or schema of a shared resource. Running against a shared resource is fine.
3. Anything irreversible outside your own worktree and the local resources you made for it, unless the question pass settled it: pushing, merging, rewriting history, deleting data or branches, dropping a shared database, writing outside the repo, spending money, sending mail or messages, touching production. Migrating your own branch database and deleting the old path in your own worktree is the work, so that goes ahead.

A test that goes red halfway through changes nothing on its own. Run the suspect tests against the commit you started from: red there is a blocker to report, red only with your edits is yours to fix.

When one hits, say what it is, what you'd do about it, and every question it raises, then wait. Don't route around it. When the answer never comes, commit what you have so it's safe and say in the report that the commit is unproven, take the environment down with `cleaning-up-dev-environment`, and put the blocker at the top of the report.

## Run it to done

1. `setting-up-dev-environment`.
2. `planning-a-change`.
3. The question pass above, then build it.
4. `proving-it-works`.
5. `reviewing-your-work`, fixing everything it says blocks.
6. `finishing-up-changes`, sending the branch where the question pass said it goes.
7. `cleaning-up-dev-environment`.

Hand independent pieces and heavy reading to subagents along the way, with `delegating-to-subagents`.

## Report at the end

- What you built, and where it is.
- The commands you ran and what they printed.
- The decisions you made after the question pass, and why.
- What you left out, and anything that needs the user.
- The branch name and the worktree path, when either is still there.
