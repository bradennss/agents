---
name: engineering-practices
description: >
  Rules for writing and structuring code: picking an approach, splitting functions and modules, choosing libraries, and deciding when a comment earns its place. Use when writing or changing code, refactoring, designing a module or API, reaching for a dependency, or about to add a comment, even if the request doesn't name any of these.
---

# Engineering practices

## Approach

- Pick the simplest thing that fully solves the problem. If a shorter way works, use it.
- Finish what you start. Don't leave stubs, `TODO`s, `unimplemented!()`, `throw new Error("not implemented")`, or functions that return a fake value. If you can't finish, stop and say what's in the way.
- Do it now. Saying "we can add error handling later" just means the work isn't done, and if the change needs it to be correct, it's part of the change.

## Functions and modules

- Move code into a function the first time it makes the caller easier to read, instead of waiting for a second copy to show up.
- Write deep modules, so put a lot of work behind a small API, with few public items, arguments, and ways for a caller to get it wrong. Keep the messy parts inside so callers never touch them.
- Keep the wires between modules thin. If two modules need each other's internals, they're really one module.

## Libraries

- Reach for the standard library first, then a well kept package, then your own code. Write it yourself only when the package costs more than the code would.
- Read the docs before you use anything external. Pull up the docs for the version in the lockfile, and don't write API calls from memory or guess at flags, option names, or return types.

## Comments

A comment usually means the code is unclear, so try to rename or restructure first. Write a comment only for things the code can't say, like a workaround for someone else's bug or a rule a reader wouldn't guess. Don't just repeat what the line already says.

```rust
// Bad
// increment the retry counter and check the limit
n += 1;
if n > 3 { return Err(e); }

// Good
retries += 1;
if retries > MAX_RETRIES { return Err(e); }
```
