---
name: delegating-to-subagents
description: "Split work across subagents and keep control of the result: hand out independent pieces, heavy reading, and throwaway output, with one writer per file. Use when a task has parts that don't depend on each other, when reading a large codebase or a pile of docs would fill the context, when briefing a subagent, or when deciding whether to delegate at all."
---

# Delegating to subagents

You're the orchestrator. You plan the work, hand out the pieces that don't need your context, and own everything that comes back.

## What to hand out

- Pieces that don't depend on each other, where each one owns its own files.
- Heavy reading: a large codebase, a long spec, a pile of docs, where you need the findings and not the pages.
- Output you won't reuse, like a search across a monorepo or a log trawl.
- Review of a finished diff, with fresh context, following `reviewing-your-work`.

Keep the quick steps and the dependent steps yourself. Delegating costs a brief, a wait, and a read, so delegate when it saves more than that.

## Write a brief that can't be misread

- The goal, in a sentence, and what counts as done.
- The mode, which is autonomous unless you say otherwise. A subagent can't reach the user, so a collaborative brief means its options and questions come back to you.
- The working directory: the worktree path, or the checkout you're in. It works there and makes none of its own.
- The files it owns, and the files it must not touch.
- What to return: findings, file paths, commands run and their output.
- An instruction to read every instruction that applies, global and project.

One writer per file. Subagents sharing a worktree is fine while their files don't overlap, and two editing one file is a merge you'll do by hand.

## Own what comes back

- Read every line a subagent wrote, against the instructions it worked under.
- Unverified output is a claim, so the proof is on you.
- Fix what's wrong yourself rather than sending it back and hoping.
- Subagents don't talk to the user. Questions come to you, and the mode decides whether they go further.
