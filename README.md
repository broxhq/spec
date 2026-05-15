<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/broxhq/.github/main/profile/assets/logo-dark.svg">
  <img alt="brox" src="https://raw.githubusercontent.com/broxhq/.github/main/profile/assets/logo.svg" width="240">
</picture>

### Skill Specification

</div>

---

The format that defines what an AI agent skill looks like and how `brox` packages, distributes, and installs them.

## What is a skill?

A **skill** is a self-contained bundle of instructions, scripts, and resources that extends an AI agent's capabilities. It is the unit of distribution in the Brox registry.

A skill is a directory containing at minimum:

```
my-skill/
├── skill.json      # manifest (required)
├── SKILL.md        # instructions for the agent (required)
├── scripts/        # executable scripts (optional)
├── references/     # static context files (optional)
└── README.md       # human-facing docs (optional, recommended)
```

## `skill.json` — the manifest

```json
{
  "name": "@username/skill-name",
  "version": "1.0.0",
  "description": "One-sentence description shown in search results.",
  "license": "MIT",
  "author": "username <email@example.com>",
  "homepage": "https://github.com/username/skill-name",
  "agents": ["claude", "cursor", "cline"],
  "entry": "SKILL.md",
  "permissions": ["fs:read", "fs:write", "network"],
  "keywords": ["pdf", "extraction", "documents"],
  "dependencies": {
    "@otheruser/some-skill": "^1.0.0"
  }
}
```

### Field reference

| Field | Required | Description |
|-------|----------|-------------|
| `name` | yes | Scoped name `@user/skill`. Lowercase, kebab-case after scope. |
| `version` | yes | [SemVer](https://semver.org). |
| `description` | yes | Max 200 chars. Plain text. |
| `license` | yes | [SPDX identifier](https://spdx.org/licenses/). |
| `author` | yes | `name <email>` or `name (url)`. |
| `entry` | no | Path to the main instruction file. Default: `SKILL.md`. |
| `agents` | no | Which agents this skill targets. Default: `["claude"]`. |
| `permissions` | no | Capabilities the skill needs. See [Permissions](#permissions). |
| `keywords` | no | For search. Max 10. |
| `dependencies` | no | Other skills this depends on. |
| `homepage` | no | URL to repo or docs. |

### Permissions

| Permission | Meaning |
|------------|---------|
| `fs:read` | Read files outside the skill directory |
| `fs:write` | Write files anywhere |
| `network` | Make outbound network requests |
| `exec` | Run shell commands |
| `secrets` | Access environment variables / credentials |

The CLI shows requested permissions on install and asks for confirmation.

## `SKILL.md` — the instructions

A markdown file the agent reads when the skill is invoked. Start with frontmatter:

```markdown
---
name: pdf-extractor
trigger: When the user provides a PDF and asks to extract data from it.
---

# PDF Extractor

You are now equipped to extract structured data from PDF files.

## How to use

1. Identify the PDF path from the user's message.
2. Run `scripts/extract.py <path>` to get raw text.
3. Parse the output according to the user's request...
```

The frontmatter `trigger` field tells the agent **when** this skill is relevant. The body tells it **how** to act.

## Naming rules

- Scoped: `@scope/name`
- Scope: 2-39 chars, lowercase, alphanumeric + hyphens
- Name: 2-50 chars, lowercase, alphanumeric + hyphens
- Reserved scopes: `@brox`, `@official`, `@anthropic`

## Versioning

Follows [SemVer](https://semver.org):
- **MAJOR** — breaking change to behavior or interface
- **MINOR** — new capability, backward-compatible
- **PATCH** — bug fix or doc update

## Registry distribution

A published skill consists of:
1. A git tag matching `v<version>` in the source repo
2. An entry in [broxhq/registry](https://github.com/broxhq/registry) `index.json` pointing to that repo + tag
3. SHA-256 checksum of the tagged tree

Skills are fetched directly from their source repos at install time. The registry is just a directory.
