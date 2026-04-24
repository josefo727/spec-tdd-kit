# update.md — Self-update protocol for spec-tdd-kit

This is the procedure Claude Code must follow when the user asks to **update the kit itself** (not a project using the kit). Typical triggers:

- *"Realiza un update del kit."*
- *"Actualiza `spec-tdd-kit` para ponernos al corriente."*
- *"Sync the kit with the latest state of X."*

## Scope

**In scope** — this document covers updates to:

- `spec-tdd-kit/` docs (philosophy, workflows, phases, stacks, tools).
- Slash command sources in `commands/`.
- Skills in `skills/`.
- `CHANGELOG.md` and this `update.md` itself.

**Out of scope** — do not touch, during a kit update:

- Files outside `~/Projects/spec-tdd-kit/`.
- A user project's `.specs/` (that is managed by the project's own workflow).

## Update triggers — what to research

Run research passes for each of the following dimensions. Use `context7` MCP first; fall back to `WebSearch` / `WebFetch` when context7 does not index the source.

1. **Claude Code release notes** — new features (skills, subagents, MCPs, hooks, slash command syntax, settings schema). If any change affects the kit, reflect it in `AGENTS.md` and `tools/claude-code-*`.
2. **GitHub Spec Kit** (`github/spec-kit`) — versions, new phases/commands, deprecations. Update `tools/spec-kit-compat.md` and, if relevant, `philosophy/01-sdd.md`.
3. **MCP ecosystem** — new MCP servers in the user's `~/.claude/` or the wider ecosystem relevant to SDD workflows. Update `tools/mcp-catalog.md`.
4. **Testing frameworks per stack** — for each file in `stacks/`, verify that the recommended test runner and assertions are still current (e.g., Pest 3.x, Vitest 3.x, pytest 9.x, Jest 30.x, `@testing-library/react` 17.x, etc.).
5. **Language/framework major versions** — Laravel, Vue, React, Node LTS, Python, Rust, Go, Flutter, Quasar, VTEX IO. If a major jumped and the kit's guidance is no longer idiomatic, patch.
6. **User feedback** — search memory (`~/.claude/projects/-home-josefo/memory/`) for feedback memories written since the last kit update. Bake applicable lessons into the kit.
7. **Anti-patterns backlog** — scan recent project retros stored under `~/.claude/projects/*/memory/` for new anti-patterns; append to `anti-patterns.md`.

## Protocol

### 0. Pre-flight

```
cd ~/Projects/spec-tdd-kit
git status
```

- If the working tree is dirty, **stop** and ask the user what to do with pending changes.
- Read current `CHANGELOG.md` to identify the last released version.

### 1. Research pass

For each trigger above, produce a short note (in memory, not a file) with:

- Source (URL or MCP query).
- Finding.
- Kit impact: `none` | `docs` | `commands` | `skills` | `breaking`.

Group findings by target file. Skip targets with no impact.

### 2. Plan the update

Draft a plan **in the conversation**, not in a file:

- Files to edit, with one-line justification each.
- Proposed version bump: `patch` (docs only), `minor` (new files, new commands, additive changes), `major` (breaking rename of commands/skills/paths).
- Files to delete, if any (always confirm with the user first).

Present the plan to the user in Spanish. Wait for approval.

### 3. Execute

- Edit files. Keep diffs minimal and focused.
- Run an internal consistency sweep:
  - No doc links a file that does not exist (`find . -name '*.md' -exec grep -H '(./[^)]*.md)' {} \;`).
  - No slash command is referenced without a source file in `commands/`.
  - No skill is referenced without a `skills/*/SKILL.md`.
  - Versioned examples in `examples/` still apply.
- Update `CHANGELOG.md`: add a new section `## [X.Y.Z] — YYYY-MM-DD` with `Added / Changed / Removed / Fixed`.
- If `update.md` itself needed changes (new triggers, new protocol steps), edit it here and note in changelog.

### 4. Install deltas

If `commands/` or `skills/` changed:

- Re-sync with `~/.claude/commands/` and `~/.claude/skills/` following [`INSTALL.md`](./INSTALL.md).
- Surface any orphaned installed commands/skills that no longer have a source.

### 5. Commit

```
git add -A
git diff --cached --stat
git commit -m "kit: bump to X.Y.Z

<one-liner per file group>
"
git tag vX.Y.Z
```

Show the user the commit + tag. Do **not** push unless asked.

### 6. Report

In Spanish, summarise to the user:

- Version bumped to `X.Y.Z`.
- Files touched.
- Commands/skills that need re-installation (if any).
- Open questions that were not resolved in this pass.

## Versioning rules (SemVer for docs)

- **Patch** (`0.1.x`): typo fixes, clarifications, link fixes, example refreshes.
- **Minor** (`0.x.0`): new doc file, new stack appendix, new slash command, additive change to an existing command/skill.
- **Major** (`x.0.0`): renamed file or command, removed command/skill, phase flow restructured, default stack convention changed incompatibly.

## What to do when unsure

- If a research finding is ambiguous ("unclear if Claude Code deprecated `/plan`"), do not change the docs. Open an entry in `CHANGELOG.md` under `Unreleased` → `Open questions`, and ask the user.
- If multiple sources disagree, cite both in the commit body and ask the user.

## Update of this protocol

- If the kit grows (new dirs like `runbooks/`, new first-class concepts), amend this file **in the same update**.
- Add or remove triggers as the ecosystem shifts.
- Amendments bump the kit's version per the rules above.
