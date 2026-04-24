# Slash commands — sources

These files are the **source** of the kit's slash commands. Install them to `~/.claude/commands/` per [`../INSTALL.md`](../INSTALL.md) so Claude Code exposes them as `/<name>`.

| File | Slash command | Phase |
|------|---------------|-------|
| `sdd-constitution.md` | `/sdd-constitution` | Constitution |
| `sdd-specify.md` | `/sdd-specify` | Specify |
| `sdd-clarify.md` | `/sdd-clarify` | Clarify |
| `sdd-plan.md` | `/sdd-plan` | Plan |
| `sdd-tasks.md` | `/sdd-tasks` | Tasks |
| `sdd-implement.md` | `/sdd-implement` | Implement (wrapper) |
| `sdd-verify.md` | `/sdd-verify` | Verify |
| `sdd-onboard.md` | `/sdd-onboard` | Brownfield intake |
| `tdd-red.md` | `/tdd-red` | Implement — Red |
| `tdd-green.md` | `/tdd-green` | Implement — Green |
| `tdd-refactor.md` | `/tdd-refactor` | Implement — Refactor |

## Conventions inside each file

- Frontmatter declares `description` and optional `argument-hint`.
- Body is the prompt injected when the command runs. It assumes the kit is at `~/Projects/spec-tdd-kit`.
- Every command starts with a short "preflight" block that reads the kit's AGENTS.md and the relevant phase doc.

## Updating

When a command changes, re-run the INSTALL copy block or update the symlink (symlinks track source automatically).
