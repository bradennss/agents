---
name: working-autonomously
description: "Take a task all the way to done: ask every important question up front, then build, prove, review, and commit it without further approval. Use when the mode for a task is autonomous, when the user says to run with it, build it end to end, or not to check back, when trivial work comes up outside a collaborative session, when the user hands a task over before leaving, or when a brief doesn't name a mode."
---

# Working autonomously

You own every decision the user didn't make, and the task ends when the work is done rather than when the next question comes up.

Trivial work in this mode, meaning one file and no behavior change, takes the short path: run the git checks under "Check the base" in the `setting-up-dev-environment` skill, skip the worktree and that section's run of the repo's own checks, make the edit, run the checks the repo already has, then commit with the `finishing-up-changes` skill. No question pass, no proving run, no review loop, and nothing to tear down.

A task that ends in findings rather than a diff, like an audit or a piece of research, has nothing to build or land. Do the work, then report what you found.

A subagent runs autonomously inside the directory its brief names. It never makes its own worktree, never commits or lands anything, never runs a reviewer of its own, and only hands work to subagents of its own when the brief says to. Setup, commits, landing, and teardown belong to whoever sent the brief, and a blocker goes in what it returns rather than into a wait.

## Ask everything up front, once

Before the first edit, work out every open decision that would change what you build, then ask them together in one pass. Each question carries the default you'd take, so the user can approve the lot in a sentence.

What to settle in that pass:

- What done means, and what's out of scope.
- Any shape other code will depend on. Follow the `agreeing-an-interface` skill and put the options in the pass.
- The direction for a screen, when the task has one. Follow the `designing-frontends` skill and put the rough takes in the pass.
- Access you'll need: credentials, services, test accounts, seed data.
- Where the branch should go when you're done, since merging and pushing can't be taken back.
- Anything in the request that reads two ways.

When there's nobody to ask, like a subagent working from a brief, take the defaults and name them in what you report.

Read the code before you write that pass, following the `planning-a-change` skill. Questions the repo already answers don't belong in it.

When the user doesn't answer, take the defaults you named and go. A default you can't take back, like merging or pushing, isn't settled by silence.

## Then decide for yourself

After the pass, stop asking. For a question you didn't foresee, pick the option that matches the patterns already in the codebase and the intent the user stated, then keep a list of what you chose and why for the final report.

## Three things stop you

Stop when one of these shows up. Nothing else earns an interruption, apart from the places a skill you're following tells you to stop and ask.

A scope change. The task turns out bigger or different, part of it has to come out, the cost moves a long way from what the user agreed, or finishing it properly means work they never asked for.

A blocker you can't clear. Uncommitted changes in the checkout that aren't yours, a diverged history, a base that doesn't build, tests that already failed before your first edit, missing credentials or access, a service you can't reach. Changing the data or the schema of a resource other people share is one too, even under the lock the `setting-up-dev-environment` skill takes, though running against it is fine.

Anything irreversible outside your own worktree and the local resources you created for it, unless the question pass already settled it. Pushing, merging, rewriting history, deleting data or branches, dropping a database someone else uses, writing outside the repo, spending money, sending mail or messages, touching production. Migrating your own branch database and deleting the old path in your own worktree is the work, so that goes ahead.

A test that goes red halfway through changes nothing. Run the suspect tests against the commit you started from: red there makes it a blocker to report, red only with your edits makes it yours to fix. Commit what you have so it's safe, and say in the report that the commit is unproven.

When one hits, say what it is, what you'd do about it, and every question it raises, then wait. Don't route around it. When the user is away or the answer never comes, commit what you have on the branch so it's there when they're back, take the environment down with the `cleaning-up-dev-environment` skill so nothing sits holding a port or a lock, and put the blocker at the top of the report.

## Run it to done

1. Set up the environment, following the `setting-up-dev-environment` skill.
2. Work out what the change touches and which approach to take, following the `planning-a-change` skill.
3. Ask the question pass above, then build it.
4. Prove it works in a real environment, following the `proving-it-works` skill.
5. Review the session diff, following the `reviewing-your-work` skill, and fix everything it says blocks.
6. Commit, following the `finishing-up-changes` skill, and send the branch where the question pass said it goes.
7. Take the environment down, following the `cleaning-up-dev-environment` skill.

Hand independent pieces and heavy reading to subagents along the way, following the `delegating-to-subagents` skill.

## Report at the end

- What you built, and where it is.
- The commands you ran and what they printed.
- The decisions you made after the question pass, and why.
- What you left out, and anything that needs the user.
- The branch name and the worktree path, when either is still there.
