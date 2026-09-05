---
name: planning-work
description: >
  Plan a multi-step task before building: set a goal, size the steps, order them, and hand independent slices to subagents. Use when starting anything bigger than a one-file change, breaking a feature into steps, or deciding what to run in parallel or hand off, even if the user doesn't say "plan." For tracking those steps as tasks, use the managing-projects skill instead.
---

# Planning work

Plan first, then do one step at a time. If you try to do it all at once, you fill up your context and stack mistakes on top of each other.

Skip the plan when the change is tiny, like a typo or a one-file rename.

A plan says four things: the goal, the files you expect to touch, the steps in order, and how you'll check each one.

To track the steps as tasks (dependency graph, progress across sessions), use the `managing-projects` skill. This skill is about sizing and ordering the work, not tracking it.

## Size a step

- one focused sitting of work
- about five files at most
- three or fewer checks that prove it works
- if the step title needs the word "and", it's really two steps

## Run the steps

- Cut by feature, not by layer. "Signup works end to end" is a good step, but "Build all the database tables" isn't, because nothing works until the last one lands.
- Build the parts that other steps need first.
- Finish a step and verify it before you start the next one.
- Check in with the user every two or three steps, so a wrong turn costs one step instead of the whole afternoon.
- If the plan turns out to be wrong, stop and write a new one instead of patching around it.

## Handing work to subagents

If you have a subagent or task tool, use it for work that would fill up your own context. The subagent does the digging in its own context and hands you back only the answer.

Good work to hand off:

- searching a large codebase to find where something lives
- reading external docs or comparing two libraries
- running a long test suite and reporting what failed
- reviewing a diff from one angle, like security or test coverage
- independent slices of the plan that touch different files

A subagent starts with an empty head, so it can't see this conversation, your earlier tool calls, or anything you've worked out so far. You can't send it a follow-up either, so the first message has to carry everything:

- the goal
- the background it needs
- which files it owns
- what done looks like
- the exact shape of the answer you want back

Rules for handing off:

- Give each subagent its own files, since two of them editing the same file will collide.
- Run the independent slices at the same time, and chain the dependent ones by pasting the first result into the next prompt.
- Keep the planning and the final calls yourself. Hand off the work, not the judgment.
- Check what comes back, since a subagent's summary is a claim, not proof. Read the diff or run the check yourself.
- Skip the subagent for small or local work, where the handoff costs more than the job.
