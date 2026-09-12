---
name: agreeing-an-interface
description: "Settle the shape of anything other code depends on before building it: HTTP and RPC endpoints, event and message payloads, public functions, types and modules, CLI commands and flags, config formats, and database schemas. Use when adding or changing one of those, when renaming or adding or removing a field, when the meaning of an existing field changes, or when two parts of a system need to agree on a contract."
---

# Agreeing an interface

An interface is everything other code has to match. Getting it wrong is expensive to undo, so it gets settled before it gets built.

## What counts

- HTTP and RPC endpoints.
- Event and message payloads.
- Public functions, types, and modules.
- CLI commands and flags.
- Config formats and environment variables.
- Database schemas and migrations.

Changing one counts too: renames, added or removed fields, a field that now means something else, a default that moves, an error that becomes a different error.

## Show the real shapes

Write out what the caller sees:

- Paths, methods, and status codes.
- Field names, types, and which ones are optional.
- Function signatures, with names and types.
- What happens when the call fails, including the error shape and what the caller does with it.

A short example payload or signature block beats a paragraph describing one. Cover the parts that are genuinely open, rather than every field in the schema.

## Put up two or three options

Each option gets the shape, what it costs the caller, and what it forecloses. Say which one you'd pick and why.

When a pattern already in the codebase answers the question, say so and propose that pattern rather than inventing a second way to do the same thing.

## Get the answer before you build

- Collaborative mode: this is a check-in. Stop and wait.
- Autonomous mode: this goes in the question pass before the first edit, with the option you'd take as the default. A new interface question mid-task gets decided by the existing pattern and reported at the end, unless it breaks code outside the repo or outside the task's scope, which makes it a scope change.

Once it's agreed, build that. A shape that has to move after the fact goes back to the user.
