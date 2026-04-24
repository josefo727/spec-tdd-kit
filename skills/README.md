# Skills — sources

Three skills that auto-invoke based on intent. Install to `~/.claude/skills/<name>/SKILL.md` per [`../INSTALL.md`](../INSTALL.md).

| Skill | Triggers on |
|---|---|
| `spec-tdd-greenfield` | Starting a new project with SDD+TDD |
| `spec-tdd-brownfield` | Adopting SDD+TDD in an existing repo |
| `spec-tdd-cycle` | Running a single task's Red-Green-Refactor cycle |

Each skill's body is a concise runbook the agent follows. Skills reference the kit files, so they stay thin.

## Conventions

- Frontmatter: `name` + `description`. The description is what auto-match uses; write it with verbs and domain words.
- Body: numbered steps, preconditions, tools used, examples.
- Always include a pointer back to the relevant `workflows/*.md` and `phases/*.md`.

## Maintenance

When a skill's flow changes, update `SKILL.md` here and re-install (or rely on the symlink).
