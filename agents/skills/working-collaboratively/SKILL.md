---
name: working-collaboratively
description: "Run a task with the user in the loop: propose options, build in short rounds, and stop at a check-in after each one. Use when the mode for a task is collaborative, when the user wants to make the decisions, when they ask to design or plan something together, when they ask for options before code, or when a task involves taste, product, or interface calls they haven't made yet."
---

# Working collaboratively

The user makes the calls. You find the options, say which one you'd pick, and wait. Work arrives in short rounds rather than one long stretch that ends with everything done.

## Work in rounds

A round is one small piece of the task: a plan, an interface, a screen, one slice of behavior. Pick the next piece, do it, then stop at a check-in.

Keep rounds short enough that a wrong turn costs the user one round. When a round grows past that, split it and check in earlier.

## Put these to the user before building

- The approach, when more than one is reasonable. Follow the `planning-a-change` skill and bring the options you found.
- Any shape other code will depend on. Follow the `agreeing-an-interface` skill.
- The look and feel of a screen. Follow the `designing-frontends` skill.
- Anything irreversible, like pushing, merging, deleting data, rewriting history, spending money, or touching production.
- A scope change, including work you want to add and work you think should come out.

Two or three options each time, with the one you'd pick and why. Then wait for the answer.

## End every round at a check-in

A check-in is four things, in this order:

1. What you did, and where it is. Name the files.
2. Proof, not a verdict. The commands you ran and what they printed. For a screen, screenshots taken with the `agent-browser` CLI.
3. What you'd do next.
4. What you're unsure about, and the calls you want the user to make.

Then stop. Don't roll into the next round, and don't treat silence as approval. The next round starts when the user says it does.

## Keep rounds cheap

Between check-ins, skip the closing steps. No reviewer subagent, no commits, no landing the branch. Those come once, at the end.

Ask questions as they come up, one or two at a time, rather than saving them for a batch.

## Close the task when the user says it's done

Ask which closing steps they want, and run the ones they pick, in this order:

1. Prove it works in a real environment, following the `proving-it-works` skill.
2. Review the session diff, following the `reviewing-your-work` skill.
3. Commit, and decide where the branch goes, following the `finishing-up-changes` skill.
4. Take the environment down, following the `cleaning-up-dev-environment` skill.

Say which ones you'd pick. Uncommitted work and a running stack are the two that bite later, so say so when the user skips them.
