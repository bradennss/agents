# Instruction patterns and calibrating control

Techniques for writing skill bodies that work. Based on https://agentskills.io/skill-creation/best-practices. Not every skill needs every pattern; use the ones that fit.

## Start from real expertise

A skill an LLM invents from general knowledge is full of vague filler ("follow best practices for authentication"). The value is in the specifics: your API patterns, your edge cases, your conventions. Get those from real sources.

Extract from a hands-on task. Do the real task with an agent, correcting and guiding it, then pull out the reusable pattern. Pay attention to:

- Steps that worked, in the order that worked.
- Corrections you made ("use library X, not Y", "check for edge case Z").
- Input and output formats.
- Project facts the agent didn't already know.

Or synthesize from existing artifacts. Feed real material into an LLM and ask it to draft a skill. Build a pipeline skill from your team's actual incident reports and runbooks instead of a generic article, so it captures your schemas and failure modes. Good sources: internal docs and runbooks, API specs and schemas, code review comments, version history (patches and fixes show what really changes), and past failures with their fixes.

## Spend context wisely

Once a skill activates, its whole body sits in the context window next to the conversation and other active skills. Every token competes for attention.

### Add what the agent lacks, cut what it knows

Focus on what the agent wouldn't know on its own: your conventions, your procedures, non-obvious edge cases, the exact tools to use. Skip general knowledge. For each piece of content ask "would the agent get this wrong without it?" If no, cut it. If unsure, test it. And if the agent already handles the whole task without the skill, the skill may not be worth keeping.

### Design coherent units

Scoping a skill is like scoping a function: one coherent unit of work that composes with others. Too narrow and several skills load for one task, risking overhead and conflicting instructions. Too broad and it can't trigger cleanly. "Query the database and format results" is one unit; adding database admin is probably two skills.

### Aim for moderate detail

Over-stuffed skills hurt, because the agent struggles to find what matters and chases steps that don't apply. Write concise, stepwise guidance with a working example instead of exhaustive docs. When you're covering every edge case, ask whether the agent's own judgment handles most of them.

### Structure large skills with progressive disclosure

Keep `SKILL.md` under 500 lines and 5,000 tokens: just the core needed every run. When a skill really needs more, move detail into `references/` and tell the agent when to load each file. "Read `references/api-errors.md` if the API returns a non-200 status" is useful; "see references for details" is not.

## Calibrate control

Match how prescriptive you are to how fragile the task is. Most skills mix both; calibrate each part on its own.

Give freedom where many approaches work and variation is fine. For flexible parts, explain why rather than dictating steps. An agent that gets the purpose makes better context-dependent calls:

```markdown
## Code review process

1. Check all database queries for SQL injection (use parameterized queries)
2. Verify authentication checks on every endpoint
3. Look for race conditions in concurrent code paths
4. Confirm error messages don't leak internal details
```

Be exact where operations are fragile, consistency matters, or the sequence is strict:

```markdown
## Database migration

Run exactly this sequence:

    python scripts/migrate.py --verify --backup

Do not modify the command or add flags.
```

### Provide defaults, not menus

When several tools could work, pick a default and note alternatives briefly:

```markdown
<!-- Too many options -->

You can use pypdf, pdfplumber, PyMuPDF, or pdf2image...

<!-- Clear default with an escape hatch -->

Use pdfplumber for text extraction. For scanned PDFs that need OCR, use pdf2image with pytesseract instead.
```

### Favor procedures over declarations

Teach how to approach a class of problems, not what to produce for one instance:

```markdown
<!-- Specific answer: only useful for this exact task -->

Join the orders table to customers on customer_id, filter region = 'EMEA', and sum the amount column.

<!-- Reusable method: works for any analytical query -->

1. Read the schema from references/schema.yaml to find relevant tables
2. Join tables using the _id foreign key convention
3. Apply filters from the user's request as WHERE clauses
4. Aggregate numeric columns and format as a markdown table
```

Specific details (output templates, constraints like "never output PII", tool-specific steps) are still fine. The approach should generalize even when the details are exact.

## Patterns for effective instructions

### Gotchas

Often the highest-value part of a skill. List environment-specific facts that defy reasonable assumptions. These are concrete corrections, not general advice:

```markdown
## Gotchas

- The users table uses soft deletes. Queries must include WHERE deleted_at IS NULL or results include deactivated accounts.
- The user ID is user_id in the database, uid in the auth service, and accountId in the billing API. All three are the same value.
- The /health endpoint returns 200 whenever the web server is up, even if the database is down. Use /ready for full service health.
```

Keep gotchas in `SKILL.md` so the agent reads them before hitting the situation. When an agent makes a mistake you correct, add the correction here. It's one of the most direct ways to improve a skill.

### Templates for output format

When you need a specific output format, give a template. Agents pattern-match against concrete structures better than they follow prose. Short templates go inline; longer or conditional ones go in `assets/` and load when needed.

```markdown
## Report structure

Use this template, adapting sections as needed:

# [Analysis Title]

## Executive summary

[One-paragraph overview]

## Key findings

- Finding with supporting data

## Recommendations

1. Specific actionable recommendation
```

### Checklists for multi-step workflows

An explicit checklist helps the agent track progress and skip nothing, useful when steps depend on each other or have validation gates:

```markdown
## Form processing workflow

Progress:

- [ ] Step 1: Analyze the form (run scripts/analyze_form.py)
- [ ] Step 2: Create field mapping (edit fields.json)
- [ ] Step 3: Validate mapping (run scripts/validate_fields.py)
- [ ] Step 4: Fill the form (run scripts/fill_form.py)
- [ ] Step 5: Verify output (run scripts/verify_output.py)
```

### Validation loops

Have the agent check its own work before moving on: do the work, run a validator, fix issues, repeat until it passes.

```markdown
## Editing workflow

1. Make your edits
2. Run validation: python scripts/validate.py output/
3. If validation fails, read the error, fix the issues, and run it again
4. Only proceed when validation passes
```

A reference document can act as the validator too: tell the agent to check its work against the reference before finalizing.

### Plan-validate-execute

For batch or destructive work, have the agent write an intermediate plan in a structured format, validate it against a source of truth, then execute:

```markdown
## PDF form filling

1. Extract fields: python scripts/analyze_form.py input.pdf > form_fields.json
2. Create field_values.json mapping each field name to its value
3. Validate: python scripts/validate_fields.py form_fields.json field_values.json
4. If validation fails, revise field_values.json and re-validate
5. Fill: python scripts/fill_form.py input.pdf field_values.json output.pdf
```

The key is step 3: a validator that checks the plan against the source of truth. Errors like "Field 'signature_date' not found, available: customer_name, order_total, signature_date_signed" give the agent enough to self-correct.

### Bundle reusable scripts

When you iterate on a skill, compare execution traces across runs. If the agent keeps reinventing the same logic (building a chart, parsing a format, validating output), write a tested script once and bundle it in `scripts/`. See [scripts.md](scripts.md).
