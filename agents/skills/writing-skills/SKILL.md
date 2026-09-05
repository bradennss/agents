---
name: writing-skills
description: >
  Write, structure, and refine Agent Skills (SKILL.md files and their bundled
  scripts, references, and assets). Use when asked to create a skill, author a
  SKILL.md, package a workflow or expertise into a reusable skill, fix a skill
  that won't trigger, or evaluate and improve an existing skill, even if the
  request doesn't say "skill" by name (for example "turn this runbook into
  something the agent can reuse").
---

# Writing skills

A skill is a folder with a `SKILL.md` file that teaches an agent how to do a
task. The file has YAML frontmatter (`name` and `description`) followed by
Markdown instructions. Agents load skills in stages: at startup they read only
the name and description, then load the full body once a task matches, then pull
in extra files as needed. This is progressive disclosure, and it's the single
idea that shapes every choice below. Keep the always-loaded parts small, and
push detail into files that load on demand.

## When to build a skill at all

Build a skill when the agent needs something it wouldn't know on its own:
project conventions, a domain workflow, an unfamiliar API, or non-obvious edge
cases. If the agent already does the task well without help, skip the skill. The
best way to check is to run the task once with no skill and see where it goes
wrong. Those failures are the content of your skill.

Don't ask an LLM to invent a skill from general knowledge. You'll get vague
filler like "handle errors appropriately." Start from real expertise instead:

- Do the task with an agent, correct it as you go, then write down what you
  corrected and the steps that worked.
- Or feed in real project material (runbooks, schemas, review comments, past
  fixes) and pull the reusable pattern out of that.

## Build steps

1. **Pick the scope.** One coherent unit of work, like a function. "Query the
   database and format the results" is one skill. Add "and administer the
   database" and it's doing too much. Too narrow and the agent has to load
   several skills for one task; too broad and it can't trigger cleanly.
2. **Create the folder.** Name it with lowercase letters, numbers, and hyphens,
   1-64 characters, no leading or trailing hyphen and no `--`. The folder name
   must match the `name` field. Put it in whichever skills directory your agent
   loads from.
3. **Write the frontmatter.** `name` and `description` are required. See
   [references/specification.md](references/specification.md) for every field
   and its limits.
4. **Write the description last, or rewrite it once the body exists.** This
   field does all the triggering work, so it's worth real care. See
   "Write the description" below.
5. **Write the body.** Core instructions only, ideally under 500 lines and
   5,000 tokens. See "Write the body" below.
6. **Add scripts, references, and assets only when they earn their place.** See
   "Split out extra files" below.
7. **Run it on a real task, then fix what breaks.** See "Refine with real runs"
   below. This step is not optional. A skill you haven't run isn't done.

## Write the description

The description is the only thing the agent sees when deciding whether to load
the skill. Get it wrong and a perfect body never runs.

- Say what the skill does and when to use it, in that order.
- Use imperative phrasing aimed at the agent: "Use when the user wants to..."
  rather than "This skill helps with..."
- Describe user intent, not internal mechanics. The agent matches against what
  the user asked for.
- Be a bit pushy. List the cases where it applies, including ones where the user
  won't name the domain, like "even if they don't say 'CSV' or 'analysis'."
- Keep it to a few sentences. The hard limit is 1024 characters.

```yaml
# Weak
description: Process CSV files.

# Strong
description: >
  Analyze CSV and tabular data files: compute summary statistics, add derived
  columns, generate charts, and clean messy data. Use when the user has a CSV,
  TSV, or Excel file and wants to explore, transform, or visualize it, even if
  they don't explicitly say "CSV" or "analysis."
```

One nuance: agents skip skills for tasks they can already do in one step. "Read
this PDF" may not trigger a PDF skill because basic tools cover it. Skills help
most on tasks that need special knowledge. If a skill won't trigger reliably,
that's a
description problem, and [references/descriptions.md](references/descriptions.md)
has a full test-and-tune loop for it.

## Write the body

The body loads into the same context window as the conversation and every other
active skill, so every token competes for attention. Two rules cover most of it:

**Add what the agent lacks, cut what it knows.** Don't explain what a PDF is or
how HTTP works. Jump straight to the part it would get wrong. For each line ask
"would the agent get this wrong without it?" If no, cut it.

```markdown
<!-- Too verbose: the agent already knows what PDFs are -->
PDF (Portable Document Format) files contain text, images, and other content.
To extract text you need a library. pdfplumber is recommended because it
handles most cases well.

<!-- Better: straight to what it wouldn't know -->
Use pdfplumber for text extraction. For scanned documents, fall back to
pdf2image with pytesseract.
```

**Teach a method, not one answer.** The skill runs on many prompts, not the one
in front of you. Write the reusable approach ("read the schema, join on the
`_id` convention, apply filters as WHERE clauses") instead of the answer to a
single query. Specific details like output templates and constraints are fine;
the overall approach should still generalize.

Match how strict you are to how fragile the task is. Give the agent room where
many approaches work, and for those cases explain the why so it can judge for
itself. Be exact where a wrong move is costly:

```markdown
## Database migration

Run exactly this sequence. Do not add flags:

    python scripts/migrate.py --verify --backup
```

When several tools would work, pick a default and mention alternatives briefly.
Don't hand the agent a menu of equal options. The most valuable thing you can
add is often a "Gotchas" section: concrete corrections to mistakes the agent
will make otherwise, like "the `users` table uses soft deletes, so filter
`WHERE deleted_at IS NULL`." For the full set of instruction patterns (gotchas,
output templates, checklists, validation loops, plan-validate-execute), read
[references/best-practices.md](references/best-practices.md).

## Split out extra files

Keep `SKILL.md` to the core the agent needs on every run. Move the rest into
sibling directories and tell the agent when to load each one:

- `references/` for docs the agent reads on demand. Say when to load them, like
  "read `references/api-errors.md` if the API returns a non-200" instead of a
  vague "see references for details."
- `scripts/` for tested, reusable code. If you notice the agent rebuilding the
  same logic every run, bundle it as a script. See
  [references/scripts.md](references/scripts.md) for one-off commands,
  self-contained scripts, and how to design a script an agent can drive.
- `assets/` for templates, images, schemas, and other static files.

Reference files with relative paths from the skill root, and keep references one
level deep. Avoid long chains of files that point at more files.

## Refine with real runs

The first draft usually needs work. Run the skill on real tasks and read the
full execution traces, since the final output alone hides where the agent
struggled. Common tells:

- The agent tries several approaches before one works: instructions too vague.
- It follows a step that doesn't apply: instructions too broad, or too many
  options with no clear default.
- It makes a mistake you have to correct: add the correction to "Gotchas."

Feed the results (all of them, not only failures) back into the next draft. One
pass of run-then-revise already helps a lot. For a structured version with test
cases, assertions, grading, and with-skill-versus-without comparison, read
[references/evaluating.md](references/evaluating.md).

## Validate

Check the frontmatter against the spec before you call it done:

```bash
skills-ref validate ./writing-skills
```

`skills-ref` lives at https://github.com/agentskills/agentskills. It confirms the
frontmatter is valid and the naming rules hold.

## Reference files

- [references/specification.md](references/specification.md): the full SKILL.md
  format, every frontmatter field, directory conventions, and validation.
- [references/best-practices.md](references/best-practices.md): patterns for
  effective instructions and how to calibrate control.
- [references/descriptions.md](references/descriptions.md): test and tune the
  description so it triggers on the right prompts.
- [references/evaluating.md](references/evaluating.md): eval-driven iteration on
  output quality.
- [references/scripts.md](references/scripts.md): running commands and bundling
  scripts for agentic use.
