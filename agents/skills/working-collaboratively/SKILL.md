---
name: working-collaboratively
description: "Run a task with the user in the loop: propose options, build in short rounds, and stop at a check-in after each one. Use when the mode for a task is collaborative, when the user says they want to make the decisions, when they ask to design or plan something together, when they ask for options before code, or when you asked which mode and no answer came."
---

# Working collaboratively

The user makes the calls. You find the options, say which one you'd pick, and wait. Work arrives in short rounds.

## Work in rounds

- A round is one small piece: a plan, an interface, a screen, one slice of behavior. Pick the next piece, do it, stop at a check-in.
- Set the environment up before the first edit with `setting-up-dev-environment`. A round that only produces a plan or options needs none of that yet.
- Keep a round short enough that a wrong turn costs one round. Split one that grows past that.
- Trivial work is a round like any other, so it ends at a check-in rather than a commit.
- A subagent on a collaborative brief works in the directory the brief names, makes no worktree, commits and lands nothing, and sends its options and questions back to whoever briefed it.

## Put these to the user before building

- The approach, when more than one is reasonable. Follow `planning-a-change`.
- Any shape other code will depend on. Follow `agreeing-an-interface`.
- The look and feel of a screen. Follow `designing-frontends`.
- Anything irreversible: pushing, merging, deleting data, rewriting history, spending money, touching production.
- A scope change, including work you'd add and work you'd cut.
- Anything blocking: a base that doesn't build, tests that already failed before your first edit, a diverged history, access you don't have.

Bring two or three options each time, with what each costs, the one you'd pick, and why. Then wait. These come before the round that builds on them, and several landing together go in one ask.

## End every round at a check-in

1. What you did, and where it is. Name the files.
2. Proof, not a verdict: the commands you ran and what they printed, or screenshots from the `agent-browser` CLI for a screen. A plan-only round says there was nothing to run.
3. What you'd do next.
4. What you're unsure about, and the calls you want the user to make.

Then stop. Silence isn't approval, and the next round starts when the user says it does.

## Keep rounds cheap

- No reviewers, no commits, no landing between check-ins. Those happen once, at the end.
- Work that pauses for the day is the exception: commit what's there so the branch can be picked up, and say that's what the commit is.
- Ask questions as they come up, one or two at a time. A question that blocks the round ends it, so ask and wait rather than guessing.

## Close the task when the user says it's done

Ask which closing steps they want, say which you'd pick, and run the ones they choose in this order:

1. Prove the whole change works, with `proving-it-works`.
2. Review the session diff, with `reviewing-your-work`.
3. Commit and decide where the branch goes, with `finishing-up-changes`.
4. Take the environment down, with `cleaning-up-dev-environment`, after the branch decision unless the user wants the stack left up.

Uncommitted work and a running stack are the two that bite later, so say so when the user skips them. A change the user chose not to prove gets handed over as unproven, in those words.

A task that ends in a decision rather than a diff has none of this to run. Write the decision down in your reply, with what it rules out.
