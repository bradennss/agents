# Using scripts

Skills can tell agents to run shell commands, and can bundle reusable scripts in `scripts/`.

## One-off commands

When an existing package does the job, reference it in `SKILL.md` with no `scripts/` directory. Runners that resolve dependencies at runtime:

- `uvx`: Python packages in isolated environments, aggressive caching. Ships with `uv`, which installs separately.
- `pipx`: Python packages in isolated environments, available through OS package managers.
- `npx`: npm packages on demand, ships with npm.
- `bunx`: Bun's `npx`, only where the environment has Bun.
- `deno run`: scripts from URLs or specifiers, needs permission flags like `--allow-read`.
- `go run`: compiles and runs Go packages, built into `go`.

Pin versions, like `npx eslint@9.0.0`. State prerequisites in `SKILL.md`, like "Requires Node.js 18+", or use the `compatibility` frontmatter field for runtime needs. Once a command gets hard to get right first try, move it into a tested script.

## Referencing scripts

Use relative paths from the skill root, which the agent resolves. List the scripts in `SKILL.md` so the agent knows they exist, then tell it to run them.

## Self-contained scripts

Scripts that declare their own dependencies inline run with one command and no install step:

- Python: PEP 723 metadata inside `# ///` markers, run with `uv run` or `pipx run`. Pin with PEP 508 specifiers, constrain with `requires-python`, lock with `uv lock --script`.
- Deno: `npm:` and `jsr:` specifiers, versioned with semver like `@1.0.0`.
- Bun: auto-installs missing packages when there's no `node_modules`. Pin versions in the import path. TypeScript works natively.
- Ruby: `bundler/inline` to declare gems. Pin explicitly, since there's no lockfile.

## Designing scripts for agents

The agent reads stdout and stderr to decide what to do next.

- No interactive prompts. Agents run in non-interactive shells, so a blocking script hangs forever. Take input via flags, environment variables, or stdin.
- Document usage with `--help`: a short description, the flags, and examples. That output is how the agent learns the interface.
- Write errors that say what went wrong, what was expected, and what to try.
- Use structured output like JSON, CSV, or TSV, data on stdout and diagnostics on stderr.
- Be idempotent, since agents retry. "Create if not exists" beats "create and fail on duplicate".
- Reject ambiguous input with a clear error instead of guessing. Use enums and closed sets.
- Support `--dry-run` for destructive or stateful operations, and consider requiring `--confirm` or `--force`.
- Use distinct exit codes per failure and document them in `--help`.
- Keep output predictable in size, since harnesses truncate. Default to a summary or a limit, support `--offset`, or require `--output` for large output.
