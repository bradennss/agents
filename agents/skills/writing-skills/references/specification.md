# SKILL.md format reference

The full format for a skill. This mirrors the Agent Skills specification at https://agentskills.io/specification.

## Directory structure

A skill is a directory with at least a `SKILL.md` file:

```
skill-name/
├── SKILL.md          # Required: metadata + instructions
├── scripts/          # Optional: executable code
├── references/       # Optional: documentation loaded on demand
├── assets/           # Optional: templates, images, data files
└── ...               # Any other files or directories
```

## Frontmatter

`SKILL.md` starts with YAML frontmatter, then Markdown.

| Field | Required | Constraints |
| --- | --- | --- |
| `name` | Yes | 1-64 chars. Lowercase letters, numbers, hyphens. No leading/trailing hyphen, no `--`. Must match the parent directory name. |
| `description` | Yes | 1-1024 chars, non-empty. What the skill does and when to use it. |
| `license` | No | A license name or a reference to a bundled license file. |
| `compatibility` | No | Max 500 chars. Environment needs (intended product, system packages, network access). Most skills don't need it. |
| `metadata` | No | A map of string keys to string values for anything the spec doesn't cover. Use fairly unique keys to avoid clashes. |
| `allowed-tools` | No | Space-separated list of pre-approved tools. Experimental; support varies by agent. |

Minimal:

```markdown
---
name: skill-name
description: A description of what this skill does and when to use it.
---
```

With optional fields:

```markdown
---
name: pdf-processing
description: Extract PDF text, fill forms, merge files. Use when handling PDFs.
license: Apache-2.0
compatibility: Requires Python 3.14+ and uv
metadata:
  author: example-org
  version: "1.0"
allowed-tools: Bash(git:*) Bash(jq:*) Read
---
```

### name

- 1-64 characters.
- Only lowercase `a-z`, digits `0-9`, and hyphens.
- No leading or trailing hyphen, no consecutive hyphens.
- Must match the parent directory name.

Valid: `pdf-processing`, `data-analysis`, `code-review`. Invalid: `PDF-Processing` (uppercase), `-pdf` (leading hyphen), `pdf--processing` (double hyphen).

Some agents are lenient and allow the name to differ from the directory, but the standard requires a match. Keep them matched so the skill works everywhere.

### description

- 1-1024 characters.
- Cover both what the skill does and when to use it.
- Include keywords that help the agent spot relevant tasks.

Good: "Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction."

Poor: "Helps with PDFs."

See [descriptions.md](descriptions.md) for how to test and tune this field.

### license

Names the license for the skill. Keep it short: a license name or the name of a bundled license file, like `Proprietary. LICENSE.txt has complete terms`.

### compatibility

1-500 characters. Include only if the skill has real environment needs. It can name the intended product, required packages, or network access, like `Requires git, docker, jq, and access to the internet` or `Designed for terminal-based coding agents`.

### metadata

A map of string keys to string values. Clients use it for properties the spec doesn't define, like `author` and `version`. Pick reasonably unique key names.

### allowed-tools

A space-separated string of pre-approved tools, like `Bash(git:*) Bash(jq:*) Read`. Experimental, so support varies between agents.

## Body content

The Markdown after the frontmatter is the instructions. No format is required; write whatever helps the agent do the task. Useful sections: step-by-step instructions, input/output examples, and common edge cases. The whole file loads once the skill activates, so keep it under about 500 lines and move detail into referenced files.

## Optional directories

These names are conventions, not requirements. A skill can hold any files.

- `scripts/`: executable code the agent runs. Make it self-contained or document its dependencies, and give clear error messages.
- `references/`: docs the agent loads only when needed, like `REFERENCE.md` or domain files (`finance.md`, `legal.md`). Keep each file focused so it costs little context.
- `assets/`: static resources like templates, images, and data files.

## Progressive disclosure

Agents load a skill in three stages:

1. Metadata (~100 tokens): `name` and `description`, loaded at startup for every skill.
2. Instructions (recommend under 5,000 tokens): the full `SKILL.md` body, loaded when the skill activates.
3. Resources (as needed): files in `scripts/`, `references/`, and `assets/`, loaded only when required.

Keep `SKILL.md` under 500 lines and move detailed reference material into separate files.

## File references

Reference other files with relative paths from the skill root:

```markdown
See [the reference guide](references/REFERENCE.md) for details.

Run the extraction script: scripts/extract.py
```

Keep references one level deep. Avoid deep chains of files pointing at more files.

## Validation

Validate the skill with the `skills-ref` reference library:

```bash
skills-ref validate ./my-skill
```

It checks that the frontmatter is valid and the naming rules hold. Source: https://github.com/agentskills/agentskills/tree/main/skills-ref
