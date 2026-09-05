# Specification

The exact format rules for Agent Skills.

## Directory structure

A skill is a directory containing at minimum a `SKILL.md` file.

```
skill-name/
├── SKILL.md          # required: metadata + instructions
├── scripts/          # optional: executable code
├── references/       # optional: documentation
├── assets/           # optional: templates, resources
└── ...               # any additional files
```

## SKILL.md format

`SKILL.md` must have YAML frontmatter followed by Markdown content.

### Frontmatter fields

| Field | Required | Constraints |
| --- | --- | --- |
| `name` | Yes | 1-64 chars. Lowercase letters, numbers, hyphens only. No leading, trailing, or consecutive hyphens. Must match the parent directory name. |
| `description` | Yes | 1-1024 chars, non-empty. Says what the skill does and when to use it. Include keywords that help agents spot relevant tasks. |
| `license` | No | License name or reference to a bundled license file. Keep it short. |
| `compatibility` | No | 1-500 chars. Environment needs such as intended product, system packages, or network access. Only include it when the skill has real requirements. |
| `metadata` | No | Map from string keys to string values for extra properties. Use distinctive key names to avoid conflicts. |
| `allowed-tools` | No | Space-separated string of pre-approved tools. Experimental, so support varies between agents. |

Emit valid YAML. Quote values that contain colons or other tricky syntax.

### Body content

The Markdown after the frontmatter holds the instructions. No format restrictions apply. Recommended sections: step-by-step instructions, input and output examples, and common edge cases. The agent loads the whole body once it activates the skill, so split longer content into referenced files.

## Optional directories

- `scripts/`: runnable code. Make scripts self-contained or document their dependencies, include helpful error messages, and handle edge cases. Language support depends on the agent, commonly Python, Bash, and JavaScript.
- `references/`: extra docs the agent reads when needed, such as `REFERENCE.md`, `FORMS.md`, or domain files like `finance.md`. Keep each file focused so on-demand loading stays cheap.
- `assets/`: static resources like templates, images, and data files.

## Progressive disclosure

Agents pull in detail only as a task needs it:

1. Metadata (about 100 tokens): `name` and `description` load at startup for every skill.
2. Instructions (under 5,000 tokens recommended): the full `SKILL.md` body loads when the skill activates.
3. Resources (as needed): files in `scripts/`, `references/`, or `assets/` load only when required.

Keep `SKILL.md` under 500 lines and move detailed material into separate files.

## File references

Reference other files with relative paths from the skill root, such as `references/REFERENCE.md` or `scripts/extract.py`. Keep references one level deep. Avoid chains where one reference points to another.

## Validation

Validate with the `skills-ref` reference library:

```bash
skills-ref validate ./skill-name
```

This checks that the frontmatter is valid and follows the naming rules.
