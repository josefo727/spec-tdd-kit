# Changelog

All notable changes to **spec-tdd-kit** are documented here. The kit follows [Semantic Versioning](https://semver.org/).

Format: `Added` / `Changed` / `Deprecated` / `Removed` / `Fixed` / `Security`.

## [Unreleased]

## [0.3.0] — 2026-08-29

### Added

- `adr/` — kit-level decision records. First entry: `adr/0001-ratchet-and-permission-boundaries.md` (accepted), from a gap analysis of the harness engineering playbook against the kit.
- `ratchet.md` — the ratchet discipline: dated rules traced to failures, promotion ladder (in-chat correction → dated guide rule → hook/permission/test), monthly pruning.
- `templates/claude-settings.md` — capability-budget template for per-project `.claude/settings.json` (allow / ask / deny tiers, Laravel example, hook promotion example).
- `AGENTS.md` — new §"Ratchet protocol": second correction proposes a dated rule, third occurrence proposes promotion to structure; never silently.
- `anti-patterns.md` — entries 16 ("Undated rule pile") and 17 ("Trust-based verification").
- `sessions.md` — new §"The recovery test": once per project, verify resume-from-journal works without repeated work or re-explanation.
- `workflows/greenfield.md` / `workflows/brownfield.md` — bootstrap/onboarding step creating a committed `.claude/settings.json` from the template.
- `update.md` — new research trigger 9: rule hygiene review across kit-managed projects.

### Changed

- `README.md` — TOC includes "Ratchet" section and the new template; version bumped.

## [0.2.0] — 2026-04-24

### Added

- `sessions.md` — journal protocol for session-to-session continuity. Every kit-managed project keeps a `JOURNAL.md` at repo root, append-only, written on pause and read on resume.
- `AGENTS.md` — new §"Session protocol (journal)" documenting on-resume / on-pause / silent-close agent behavior.
- `workflows/greenfield.md` — bootstrap step creates `JOURNAL.md` with first session entry.
- `workflows/brownfield.md` — onboarding step creates `JOURNAL.md` with first session entry.
- `update.md` — new research trigger 8: review journal quality across projects and amend `sessions.md` if patterns emerge.

### Changed

- `README.md` — TOC includes new "Sessions" section; version bumped.

## [0.1.0] — 2026-04-24

### Added

- Initial release of the kit.
- `README.md`, `AGENTS.md`, `INSTALL.md`, `update.md`, `glossary.md`, `anti-patterns.md`.
- Philosophy docs:
  - `philosophy/01-sdd.md`
  - `philosophy/02-tdd.md`
  - `philosophy/03-sdd-tdd-fusion.md`
- Workflows:
  - `workflows/greenfield.md`
  - `workflows/brownfield.md`
  - `workflows/feature-flow.md`
- Phase docs (`00-constitution` through `06-verify`).
- Templates: constitution, spec, plan, research, contracts, tasks, ADR, test-plan.
- Stack appendices: `_generic`, `laravel-php`, `vue`, `react`, `node`, `python`, `rust`, `go`, `flutter`, `quasar`, `vtex`.
- Tools docs: `mcp-context7`, `mcp-catalog`, `claude-code-skills`, `claude-code-subagents`, `spec-kit-compat`.
- Slash commands (source): `sdd-constitution`, `sdd-specify`, `sdd-clarify`, `sdd-plan`, `sdd-tasks`, `sdd-implement`, `sdd-verify`, `sdd-onboard`, `tdd-red`, `tdd-green`, `tdd-refactor`.
- Skills: `spec-tdd-greenfield`, `spec-tdd-brownfield`, `spec-tdd-cycle`.
- Checklists: constitution, spec, plan, done.
- Examples: `greenfield-minimal`, `brownfield-adoption`.
