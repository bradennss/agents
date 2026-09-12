---
name: working-autonomously
description: "Take a task all the way to done: ask every important question up front, then build, prove, review, and land it without further approval. Use when the mode for a task is autonomous, when the user says to run with it, build it end to end, or not to check back, when the work is trivial, or when a task needs finishing while the user is away."
---

# Working autonomously

You own every decision the user didn't make, and the task ends when the work is done rather than when the next question comes up.

## Ask everything up front, once

Before the first edit, work out every open decision that would change what you build, then ask them together in one pass. Each question carries the default you'd take, so the user can approve the lot in a sentence.

What to settle in that pass:

- What done means, and what's out of scope.
- Any shape other code will depend on. Follow the `agreeing-an-interface` skill and put the options in the pass.
- The direction for a screen, when the task has one. Follow the `designing-frontends` skill and put the rough takes in the pass.
- Access you'll need: credentials, services, test accounts, seed data.
- Anything in the request that reads two ways.

Read the code before you write that pass, following the `planning-a-change` skill. Questions the repo already answers don't belong in it.

When the user doesn't answer, take the defaults you named and go.

## Then decide for yourself

After the pass, stop asking. For a question you didn't foresee, pick the option that matches the patterns already in the codebase and the intent the user stated, then keep a list of what you chose and why for the final report.

## Three things stop you

Stop when one of these shows up, and only these.

A scope change. The task turns out bigger or different, part of it has to come out, the cost moves a long way from what the user agreed, or finishing it properly means work they never asked for.

A blocker you can't clear. Uncommitted changes in the checkout, a diverged history, a base that doesn't build, missing credentials or access, a service you can't reach, a test suite broken before you started.

Anything irreversible. Pushing, merging, rewriting history, deleting data or branches, dropping a database, deleting anything outside the worktree, writing outside the repo, spending money, sending mail or messages, touching production.

When one hits, say what it is, what you'd do about it, what you need from the user, then wait. Don't route around it.

## Run it to done

1. Set up the environment, following the `setting-up-dev-environment` skill.
2. Work out what the change touches and which approach to take, following the `planning-a-change` skill.
3. Ask the question pass above, then build it.
4. Prove it works in a real environment, following the `proving-it-works` skill.
5. Review the session diff, following the `reviewing-your-work` skill, and keep going until a review comes back with nothing.
6. Commit, following the `finishing-up-changes` skill. Where the branch goes is irreversible, so that's the one thing you ask at the end.
7. Take the environment down, following the `cleaning-up-dev-environment` skill.

Hand independent pieces and heavy reading to subagents along the way, following the `delegating-to-subagents` skill.

## Report at the end

- What you built, and where it is.
- The commands you ran and what they printed.
- The decisions you made after the question pass, and why.
- What you left out, and anything that needs the user.
- The branch name and the worktree path, when either is still there.
