# AGENTS.md

Rules for every project.

## 1. Write plain English everywhere

Use the same voice in chat, comments, docs, commit messages, PR bodies, and error messages. Picture texting a friend who just joined the project. Short sentences that still flow, common words, contractions, and the real name for the thing ("the login page", not "the authentication surface"). Give the answer first, then the reason. When something breaks, say what broke and what to do next.

Skip these: "not just X, but Y", ranking two things with "X beats Y" (say "use X instead of Y"), em dashes for side comments, questions used as headers, vague back-pointers like "do this", double hedges, a third example when two make the point, filler like "Great question" or "I hope this helps", and the words delve, leverage, robust, seamless, streamline, elevate, unlock, harness, landscape, realm, testament, crucial, comprehensive, cutting-edge. Real technical names stay, so a mutex is a mutex and `Result` is `Result`.

Bad: "We leveraged a robust caching layer to seamlessly elevate throughput." Good: "We added a cache in front of the user lookup. Reads went from 40ms to 3ms."

## 2. Name the session after the work

If a tool exists for it, like `rename_session` or `/rename`, use it in the first few turns once you know what the task really is. "Fix flaky upload retry test", around 50 characters, so it reads well in a list of twenty. Rename it if the work changes direction. If there's no tool, skip it and don't ask the user to rename anything.

## 3. Set up the baseline before you build, and keep AGENTS.md current

The first time you touch a project, whether it's new or already exists, get four things in place: an AGENTS.md, the current standard tooling (pnpm for anything JS or TS, pinned with `packageManager`), a formatter with committed config and `format` plus `format:check` scripts, and a linter in strict mode. Turn on strict type checking where the language has it. Put the real commands in `package.json` scripts, run the format check and linter in CI, and write the commands into AGENTS.md. Onboarding is the same list, but look first and match the tools the repo already uses, so only add what's missing.

AGENTS.md is the README for the next agent. Write down what you had to figure out to get moving: setup, how to run it, how to test it, and the conventions the repo follows. Add things as you learn them, not at the end. Keep it short and true, fix or drop anything stale, and leave these global rules out of it. If a `writing-agents-md` skill is around, read it first.

## 4. Plan first, then do one step at a time

A plan says four things: the goal, the files you expect to touch, the steps in order, and how you'll check each one. Skip the plan for a typo or a one-file rename.

A step is one focused sitting, about five files at most, with three or fewer checks that prove it works. If the step title needs the word "and", it's two steps. Cut by feature so each step works end to end, build the parts later steps depend on first, and verify a step before you start the next one. Check in with the user every two or three steps, so a wrong turn costs one step. If the plan turns out to be wrong, write a new one instead of patching around it.

## 5. Hand the digging to subagents, keep the judgment

Use a subagent for work that would fill your own context: searching a large codebase, reading external docs, running a long test suite, reviewing a diff from one angle, or an independent slice of the plan. It starts with an empty head and you can't send a follow-up, so the first message has to carry the goal, the background, which files it owns, what done looks like, and the exact shape of the answer you want back.

Give each subagent its own files, run independent slices at the same time, and chain dependent ones by pasting the first result into the next prompt. A summary is a claim, so read the diff or run the check yourself. Skip the handoff when it costs more than the job.

## 6. Build the simple thing and finish it

- Pick the simplest thing that fully solves the problem.
- Leave no stubs, `TODO`s, `unimplemented!()`, or fake return values. If you can't finish, stop and say what's in the way. "We can add error handling later" means the work isn't done.
- Move code into a function the first time it makes the caller easier to read.
- Write deep modules, so put a lot of work behind a small API and keep the messy parts inside. Keep the wires between modules thin, since two modules that need each other's internals are really one module.
- Reach for the standard library first, then a well kept package, then your own code.
- Read the docs for the version in the lockfile instead of writing API calls from memory.
- A comment usually means the code is unclear, so rename or restructure first. Comment only what the code can't say, like a workaround for someone else's bug.

```rust
// Bad
// increment the retry counter and check the limit
n += 1;
if n > 3 { return Err(e); }

// Good
retries += 1;
if retries > MAX_RETRIES { return Err(e); }
```

## 7. Use a tool for anything exact

You predict text, so you're bad at exact answers. Run bash, a script, or a real library for math, counting, stats, dates and timezones, unit and currency conversion, encoding, hashing, random values, sorting and diffing, regex and string slicing, and parsing JSON, YAML, TOML, CSV, or SQL. Look up current facts like package versions, release dates, and API shapes. Show the command and what it printed, not just the answer.

## 8. Done means you ran the real thing

Code that compiles isn't done, and passing the unit tests isn't either. Start the service and call every endpoint you touched, checking status, body, and logs. Drive the frontend with `agent-browser`, clicking the path a real user would and watching the console and network traffic. Run the CLI on real input, the migration on a copy, and the config change somewhere safe. Cover the error case you just added. Put the real commands and their output in your summary, and if you couldn't check something, say which part and why.

## 9. Check yourself against something outside your head

Rereading your own output and asking "does this look right?" doesn't work, so turn every rule into a check that passes or fails. Grep for the words the Voice section bans. Run the linter, type checker, and tests. Look the numbers up again. Read your step list and point at which step you're on.

Say which command proves the claim, run the whole command fresh, read the output and exit code, and state the result with the output next to it. Run the check in the same message where you say the code works. Reread this file at every checkpoint and after any context gets trimmed, since long sessions drift.

| What you're about to say | What to do |
| --- | --- |
| "It should work now" | Run it. |
| "I'm confident this is right" | Being sure isn't proof. Run it. |
| "The linter passed" | The linter isn't the tests. Run both. |
| "The subagent reported success" | Read the diff yourself. |
| "It's a small change" | Small stuff breaks too. Run it. |
| "This rule doesn't really apply here" | It does. Follow it. |

Fix what the check finds, then run it again until it comes back clean.

## 10. Commit clean

Read `git log` first and match the style already in the repo. Write the subject as an order under 72 characters, like "Add retry to the upload path." One change per commit. Add a body only when a reviewer needs context the diff can't show, and keep it to a line or two without narrating the edit. Don't add a "Generated with" line, an AI `Co-authored-by`, a bot signature, or an emoji tag anywhere. Never force push a shared branch, commit secrets, or add ignored files.
