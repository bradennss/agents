# Using scripts

Skills can tell agents to run shell commands and can bundle reusable scripts in `scripts/`.

## One-off commands

When an existing package already does the job, reference it directly in `SKILL.md` without a `scripts/` directory. Many ecosystems auto-resolve dependencies at runtime:

- `uvx` runs Python packages in isolated environments with aggressive caching. Ships with `uv`, needs a separate install.
- `pipx` runs Python packages in isolated environments. Available through OS package managers, a mature alternative to `uvx`.
- `npx` runs npm packages on demand. Ships with npm. Pin versions with `npx package@version`.
- `bunx` is Bun's equivalent of `npx`. Only use it when the environment has Bun.
- `deno run` runs scripts from URLs or specifiers. Needs permission flags like `--allow-read`.
- `go run` compiles and runs Go packages. Built into the `go` command.

Tips:

- Pin versions, like `npx eslint@9.0.0`, so behavior stays stable over time.
- State prerequisites in `SKILL.md`, like "Requires Node.js 18+", rather than assuming they exist. For runtime needs, use the `compatibility` frontmatter field.
- Move complex commands into scripts. When a command grows hard to get right on the first try, a tested script in `scripts/` is more reliable.

## Referencing scripts

Use relative paths from the skill root. The agent resolves them automatically, so no absolute paths. List the available scripts in `SKILL.md` so the agent knows they exist, then instruct it to run them.

## Self-contained scripts

Bundle scripts that declare their own dependencies inline, so the agent runs them with one command and no separate install step:

- Python: PEP 723 inline metadata inside `# ///` markers. Run with `uv run` (or `pipx run`). Pin versions with PEP 508 specifiers, constrain with `requires-python`, and use `uv lock --script` for a lockfile.
- Deno: `npm:` and `jsr:` import specifiers make scripts self-contained. Version with semver like `@1.0.0` or `@^1.0.0`.
- Bun: auto-installs missing packages at runtime when there's no `node_modules`. Pin versions in the import path. TypeScript works natively.
- Ruby: use `bundler/inline` to declare gems in the script. Pin versions explicitly, since there's no lockfile.

## Designing scripts for agents

The agent reads stdout and stderr to decide what to do next.

- Avoid interactive prompts. Agents run in non-interactive shells and can't answer TTY prompts, so a blocking script hangs forever. Take input via flags, environment variables, or stdin.
- Document usage with `--help`. That output is how the agent learns the interface. Include a short description, flags, and examples. Keep it concise.
- Write helpful error messages. Say what went wrong, what was expected, and what to try. An opaque "invalid input" wastes a turn.
- Use structured output. Prefer JSON, CSV, or TSV so both the agent and tools like `jq` can consume it. Send data to stdout and diagnostics to stderr.

Further considerations:

- Idempotency. Agents may retry, so "create if not exists" is safer than "create and fail on duplicate."
- Input constraints. Reject ambiguous input with a clear error instead of guessing. Use enums and closed sets.
- Dry-run support. A `--dry-run` flag lets the agent preview destructive or stateful operations.
- Meaningful exit codes. Use distinct codes for different failures and document them in `--help`.
- Safe defaults. Consider requiring `--confirm` or `--force` for destructive operations.
- Predictable output size. Many harnesses truncate output past a threshold. Default to a summary or a limit, and support flags like `--offset`, or require an `--output` flag for large output.
