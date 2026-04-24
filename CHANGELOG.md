# Changelog

All notable changes to **spec-tdd-kit** are documented here. The kit follows [Semantic Versioning](https://semver.org/).

Format: `Added` / `Changed` / `Deprecated` / `Removed` / `Fixed` / `Security`.

## [Unreleased]

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
