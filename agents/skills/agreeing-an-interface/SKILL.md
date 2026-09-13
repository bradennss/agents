---
name: agreeing-an-interface
description: "Settle the shape of anything other code depends on before building it: HTTP and RPC endpoints, event and message payloads, public functions, types and modules, CLI commands and flags, config formats, and database schemas. Use when adding or changing one of those, when renaming or adding or removing a field, when the meaning of an existing field changes, or when two parts of a system need to agree on a contract."
---

# Agreeing an interface

An interface is everything other code has to match. Getting it wrong is expensive to undo, so settle it before you build it.

## What counts

- HTTP and RPC endpoints.
- Event and message payloads.
- Public functions, types, and modules.
- CLI commands and flags.
- Config formats and environment variables.
- Database schemas and migrations.

Changing one counts too: renames, added or removed fields, a field that now means something else, a default that moves, an error that becomes a different error.

## Show the real shapes

Write out what the caller sees, as an example payload or signature block rather than prose:

- Paths, methods, and status codes.
- Field names, types, and which ones are optional.
- Function signatures, with names and types.
- What happens when the call fails, including the error shape and what the caller does with it.

Cover the parts that are genuinely open, not every field in the schema.

## Put up two or three options

Each option gets the shape, what it costs the caller, and what it rules out. Say which one you'd pick and why. When a pattern already in the codebase answers the question, propose that pattern rather than a second way to do the same thing.

## Get the answer before you build

- Collaborative mode: stop and wait. One that turns up mid-task ends the round and goes to the user the same way.
- Autonomous mode: it goes in the question pass before the first edit, with the option you'd take as the default. No answer means build the default and name it in the report. A new interface question mid-task gets decided by the existing pattern and reported at the end, unless it breaks something outside the repo or outside the task's scope, like config files or stored payloads people already have, which makes it a scope change.

Build what was agreed, or the default you named. A shape that has to move after the fact goes back to the user.
