---
name: working-collaboratively
description: "Run a task with the user in the loop: propose options, build in short rounds, and stop at a check-in after each one. Use when the mode for a task is collaborative, when the user says they want to make the decisions, when they ask to design or plan something together, when they ask for options before code, or when you asked which mode and no answer came."
---

# Working collaboratively

The user makes the calls. You find the options, say which one you'd pick, and wait. Work arrives in short rounds rather than one long stretch that ends with everything done.

## Work in rounds

Set the environment up before the first edit, following the `setting-up-dev-environment` skill. A round that only produces a plan or a set of options needs none of that yet.

A subagent given a collaborative brief works in the directory the brief names. It makes no worktree, commits and lands nothing, and sends its options and questions back to whoever briefed it rather than waiting on a user it can't reach.

Trivial work inside a collaborative session is a round like any other, so it ends at a check-in rather than a commit.

A round is one small piece of the task: a plan, an interface, a screen, one slice of behavior. Pick the next piece, do it, then stop at a check-in.

Keep rounds short enough that a wrong turn costs the user one round. When a round grows past that, split it and check in earlier.

## Put these to the user before building

- The approach. Follow the `planning-a-change` skill to work out what the change touches, and bring the options when more than one is reasonable.
- Any shape other code will depend on. Follow the `agreeing-an-interface` skill.
- The look and feel of a screen. Follow the `designing-frontends` skill.
- Anything irreversible, like pushing, merging, deleting data, rewriting history, spending money, or touching production.
- A scope change, including work you want to add and work you think should come out.
- Anything blocking, like a base that doesn't build, tests that already failed before your first edit, a history that diverged, or access you don't have.

Two or three options each time, with what each one costs, the one you'd pick, and why. Then wait for the answer. These come before the round that builds on them, whether that's at the start of the task or the moment one turns up mid-round. When several land together, put them in one ask.

## End every round at a check-in

A check-in is four things, in this order:

1. What you did, and where it is. Name the files.
2. Proof, not a verdict. The commands you ran and what they printed. For a screen, screenshots taken with the `agent-browser` CLI. A round that only produced a plan or options says there was nothing to run.
3. What you'd do next.
4. What you're unsure about, and the calls you want the user to make.

Then stop. Don't roll into the next round, and don't treat silence as approval. The next round starts when the user says it does.

## Keep rounds cheap

Between check-ins, skip the reviewer subagent, the commits, and landing the branch. Those come once, at the end. Work that pauses for the day is the exception: commit what's there so the branch can be picked up, and say that's what the commit is.

Ask questions as they come up, one or two at a time, rather than saving them for a batch. A question that blocks the round ends it, so ask and wait instead of guessing.

## Close the task when the user says it's done

Ask which closing steps they want, and run the ones they pick, in this order:

1. Prove the whole change works in a real environment, following the `proving-it-works` skill.
2. Review the session diff, following the `reviewing-your-work` skill.
3. Commit, and decide where the branch goes, following the `finishing-up-changes` skill.
4. Take the environment down, following the `cleaning-up-dev-environment` skill. This one follows the branch decision unless the user wants the stack left up.

Say which ones you'd pick. Uncommitted work and a running stack are the two that bite later, so say so when the user skips them. A change the user chose not to prove gets handed over as unproven, in those words.

A task that ends in a decision rather than a diff has none of this to run. Write the decision down in your reply, with what it rules out.
