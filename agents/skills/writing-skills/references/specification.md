# Specification

The format rules for Agent Skills.

## Directory structure

A skill is a directory holding at minimum a `SKILL.md`.

```
skill-name/
├── SKILL.md          # required: metadata + instructions
├── scripts/          # optional: executable code
├── references/       # optional: documentation
├── assets/           # optional: templates, resources
└── ...               # any additional files
```

- `scripts/`: runnable code. Make it self-contained or document its dependencies, with helpful error messages. Language support depends on the agent, commonly Python, Bash, and JavaScript.
- `references/`: docs the agent reads when needed, like `REFERENCE.md` or domain files like `finance.md`. Keep each file focused so on-demand loading stays cheap.
- `assets/`: templates, images, and data files.

## Frontmatter

`SKILL.md` must open with YAML frontmatter. Emit valid YAML, and quote values containing colons.

| Field | Required | Constraints |
| --- | --- | --- |
| `name` | Yes | 1-64 chars. Lowercase letters, numbers, hyphens only. No leading, trailing, or consecutive hyphens. Must match the parent directory name. |
| `description` | Yes | 1-1024 chars, non-empty. What the skill does and when to use it, with keywords that help agents spot relevant tasks. |
| `license` | No | License name or a reference to a bundled license file. Keep it short. |
| `compatibility` | No | 1-500 chars. Environment needs like intended product, system packages, or network access. Only when the skill has real requirements. |
| `metadata` | No | Map of string keys to string values. Use distinctive keys to avoid conflicts. |
| `allowed-tools` | No | Space-separated pre-approved tools. Experimental, so support varies. |

## Body

The Markdown after the frontmatter holds the instructions, in any format. Recommended: step-by-step instructions, input and output examples, common edge cases. The whole body loads when the skill activates, so split longer content into referenced files.

## Progressive disclosure

1. Metadata (about 100 tokens): `name` and `description`, loaded at startup for every skill.
2. Instructions (under 5,000 tokens recommended): the `SKILL.md` body, loaded when the skill activates.
3. Resources: files in `scripts/`, `references/`, or `assets/`, loaded only when needed.

Keep `SKILL.md` under 500 lines.

## File references

Reference files with relative paths from the skill root, like `references/REFERENCE.md` or `scripts/extract.py`. Keep references one level deep, and avoid chains where one reference points to another.

## Validation

```bash
skills-ref validate ./skill-name
```

Checks that the frontmatter is valid and follows the naming rules.
