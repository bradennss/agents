---
name: brainstorming
description: Use when a request is vague, broad, or underspecified and you need to pin down what the user actually wants before planning or building. Covers probing with questions, surfacing options and tradeoffs, and converging on a concrete scope the user confirms. Trigger on prompts like "build me something to...", "I want a tool that...", "help me figure out...", "not sure how to approach...", or any goal that could be read several ways, even when the user doesn't ask to brainstorm.
---

# Brainstorming

A vague request can mean several different tasks. Turn it into one concrete scope the user agrees with before any planning or building starts. Don't guess what they mean. Ask the questions you need first.

## When to use

- The request could be read more than one way.
- Key details are missing: who it's for, what it must do, what it runs on, what "done" looks like.
- The user asks for something open-ended like "make me a tool for X" or "help me figure out Y".

If the request is already precise and single-meaning, skip this and go straight to research or build.

## Running the loop

1. Restate what you think they want in one or two plain sentences and ask if that's right.
2. Ask the questions you need to remove the ambiguity. Use the question tool when one exists, otherwise ask in chat. Batch related questions so the user answers in one pass.
3. When choices matter, lay out the options with their tradeoffs and give a recommended default, so the user can pick from real options.
4. Ask as many rounds as it takes. Stop when nothing important is still open.

## What to pin down

- The goal and who uses it.
- What it must have and what would be nice to have.
- Constraints: language, platform, existing systems, deadlines.
- What proves it works, so verification is clear later.

## Converging

Write the agreed scope back as a short summary and get a yes. That summary is the brief for everything that follows.

## Next

Hand the confirmed scope to `research` when it depends on external libraries or APIs, to `setting-up-a-project` for a greenfield build, or straight into building.
