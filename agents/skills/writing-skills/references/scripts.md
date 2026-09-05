# Commands and scripts in skills

How to reference one-off commands and bundle reusable scripts, and how to design a script an agent can drive. Based on https://agentskills.io/skill-creation/using-scripts.

## One-off commands

When an existing package already does the job, reference it in `SKILL.md` without a `scripts/` directory. Many ecosystems have runners that resolve dependencies at runtime:

- `uvx ruff@0.8.0 check .` (Python, ships with uv, caches aggressively)
- `pipx run 'ruff==0.8.0' check .` (Python, broad OS package availability)
- `npx eslint@9 --fix .` (npm, ships with Node.js)
- `bunx eslint@9 --fix .` (Bun's npx equivalent)
- `deno run --allow-read npm:eslint@9 -- --fix .` (Deno; needs permission flags)
- `go run golang.org/x/tools/cmd/goimports@v0.28.0 .` (built into Go)

Tips:

- Pin versions (`npx eslint@9.0.0`) so the command behaves the same over time.
- State prerequisites in `SKILL.md` ("Requires Node.js 18+") rather than assuming the environment has them. For runtime needs, use the `compatibility` frontmatter field.
- Move complex commands into a script. A one-off works for a tool plus a few flags; once it's hard to get right on the first try, a tested script is more reliable.

## Reference scripts from SKILL.md

Use relative paths from the skill root. The agent resolves them and runs commands from the skill directory, so paths in code blocks are relative to that root.

List the scripts so the agent knows they exist:

```markdown
## Available scripts

- scripts/validate.sh: validates configuration files
- scripts/process.py: processes input data
```

Then tell the agent to run them:

```markdown
1. Run validation: bash scripts/validate.sh "$INPUT_FILE"
2. Process results: python3 scripts/process.py --input results.json
```

## Self-contained scripts

For reusable logic, bundle a script that declares its own dependencies inline, so the agent runs it with one command and no separate install step.

Python (PEP 723), run with `uv run scripts/extract.py`:

```python
# /// script
# dependencies = ["beautifulsoup4"]
# ///

from bs4 import BeautifulSoup
html = '<p class="info">This is a test.</p>'
print(BeautifulSoup(html, "html.parser").select_one("p.info").get_text())
```

Other options: Deno with `npm:` or `jsr:` specifiers, Bun's runtime auto-install, and Ruby's `bundler/inline`. In every case, pin versions for reproducibility.

## Design scripts for agentic use

The agent reads stdout and stderr to decide its next move, so a few choices make a big difference.

Avoid interactive prompts. This is a hard requirement: agents run in non-interactive shells and can't answer a TTY prompt, so a script that blocks on input hangs forever. Take input via flags, environment variables, or stdin, and fail with a clear message when a required flag is missing:

```
$ python scripts/deploy.py
Error: --env is required. Options: development, staging, production.
Usage: python scripts/deploy.py --env staging --tag v1.2.3
```

Document usage with `--help`. It's how the agent learns the interface. Include a short description, the flags, and examples. Keep it brief; it enters the context window.

Write helpful error messages. The message shapes the agent's next attempt. Say what went wrong, what was expected, and what to try:

```
Error: --format must be one of: json, csv, table. Received: "xml"
```

Use structured output. Prefer JSON, CSV, or TSV over free-form text so both the agent and tools like `jq` can parse it. Send data to stdout and diagnostics (progress, warnings) to stderr so the agent captures clean output.

Also consider:

- Idempotency. Agents retry, so use "create if not exists" instead of "fail on duplicate."
- Reject ambiguous input with a clear error instead of guessing. Use enums and closed sets.
- A `--dry-run` flag for destructive or stateful operations.
- Distinct, documented exit codes for different failure types.
- Safe defaults. Consider requiring `--confirm` or `--force` for destructive actions.
- Predictable output size. Many agent harnesses truncate output past a threshold (10-30K characters), so default to a summary or limit and support flags like `--offset`, or require an `--output` file for large results.
