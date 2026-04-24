# Claude Code — Skills

Skills are reusable instruction sets stored at `~/.claude/skills/<name>/SKILL.md` (user-level) or inside a plugin. They auto-invoke when their description matches the user's intent.

## Skills shipped by this kit

Installed from [`../skills/`](../skills/) per [`../INSTALL.md`](../INSTALL.md).

| Skill | Purpose |
|---|---|
| `spec-tdd-greenfield` | Drive the greenfield workflow end-to-end |
| `spec-tdd-brownfield` | Drive the brownfield onboarding |
| `spec-tdd-cycle` | Execute a TDD cycle (R-G-F) for a single task |

Invocation in a session:

- *"Onboard this repo."* → `spec-tdd-brownfield` likely auto-matches.
- *"Arranca un proyecto nuevo con SDD+TDD."* → `spec-tdd-greenfield`.
- *"Hagamos el ciclo TDD de T042."* → `spec-tdd-cycle`.

## Skills the kit leverages (not shipped)

The user already has these installed globally. The kit references them.

| Skill | When the kit invokes it |
|---|---|
| `php-guidelines-from-spatie` | Any Laravel/PHP `implement` or `refactor` work |
| `context7-auto-research` | Exploratory library questions outside `plan` |
| `fewer-permission-prompts` | One-off cleanup of permission prompts after long sessions |
| `frontend-design` | UI polish tasks (after behavior is green) |
| `react-components` | Converting designs to React components (paired with stack/react.md) |
| `security-review` | Verify R8 when a feature touches auth, money, PII |
| `review-pr` | Optional, for Spatie-style PR review on packages |
| `flare` | Optional observability triage during verify |
| `pdf`, `copywriting`, `copy-editing`, `content-strategy`, `email-sequence`, `social-content` | Not typically touched by the kit; available for content tasks outside the code path |

## Writing a new skill

A skill is a folder with `SKILL.md`:

```
~/.claude/skills/<name>/SKILL.md
```

Frontmatter:

```
---
name: <skill-name>
description: <short phrase that will match user intent; verbs + domain>
---
```

Body: concrete steps, preconditions, tools used, examples.

## Precedence

When multiple skills seem to match:

1. Explicit user request by name wins.
2. More specific description wins.
3. The agent chooses and surfaces why.

## Anti-patterns

- A skill that duplicates a slash command with the same name. Decide one home; the other becomes an alias or is removed.
- A skill whose description is vague ("helps with code"). It will auto-trigger on unrelated prompts.
- A skill whose body repeats the entire kit. Link instead.

## Cross-references

- Subagents: [`./claude-code-subagents.md`](./claude-code-subagents.md)
- Install: [`../INSTALL.md`](../INSTALL.md)
- Skill sources: [`../skills/`](../skills/)
