---
name: planning-a-change
description: "Understand a system before changing it: find every place the change lands, check the current facts about the tools involved, and pick the simplest approach that solves the whole problem. Use when starting work on an existing codebase, when a change might touch callers, data, or other services, when more than one approach looks reasonable, when a library or API is involved and its current behavior matters, or when a task needs a plan before code."
---

# Planning a change

The plan comes from the code and from current documentation, not from memory.

## Find where the change lands

Read the code around the change and account for all of it:

- [ ] Callers of what you're changing, and their callers when the signature moves.
- [ ] Data already on disk or in a database in the old shape.
- [ ] Other services, jobs, and clients that depend on the behavior.
- [ ] Tests, fixtures, and seed data.
- [ ] Docs, config, and examples stating the old behavior.

Hand large reading to subagents with `delegating-to-subagents`. You still read what you're about to change. When the change touches a shape other code depends on, follow `agreeing-an-interface`.

## Check the current facts

Look up anything that changes rather than recalling it: library APIs, framework behavior, service contracts, package versions, and the current recommended way to build the thing. Do it before you plan, since it's what makes one approach better than another. Say which versions you checked against, and link the docs when the answer was surprising.

## Pick an approach

- Weigh performance, safety, complexity, and future maintenance. Take the simplest approach that fully solves the problem, and say what you traded away.
- A pattern already in the codebase settles the question. Say so and follow it.
- When more than one approach is genuinely reasonable, write two or three with the cost and risk of each. Collaborative mode puts them to the user, says which you'd pick, and waits. Autonomous mode picks one, says why in a line, and carries on.

## Write the plan down

Before the first edit, state the approach in your reply as a short list: the files you'll touch, the callers and data you'll migrate, and how you'll prove it works. It's what you check yourself against at the end.
