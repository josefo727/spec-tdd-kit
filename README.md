# spec-tdd-kit

A dense, opinionated playbook for practicing **Spec-Driven Development** and **Test-Driven Development** *together* inside Claude Code. Stack-agnostic core with per-stack appendices. Brownfield and greenfield are both first-class.

> **Language rule:** All artifacts in this kit are written in English. The agent (Claude Code) talks to the user in Spanish unless explicitly asked otherwise. See [`AGENTS.md`](./AGENTS.md).

---

## Why this exists

SDD tools like GitHub Spec Kit prove the value of specs-as-source. They under-serve TDD (they stop at "test-first" as a slogan) and they are agent-agnostic, so they do not exploit Claude Code's native features (skills, subagents, MCPs, slash commands, `@` file refs). This kit fills both gaps: **SDD for alignment, TDD for safety, Claude Code as the harness**.

## What you get

- A **constitution-first** flow adapted from Spec Kit (`specify → clarify → plan → tasks → implement → verify`).
- A **TDD cycle (Red → Green → Refactor)** embedded inside every task. No task is considered done without the three beats.
- **Greenfield** and **brownfield** workflows of equal weight.
- **Stack appendices** for Laravel/PHP, Vue, React, Node, Python, Rust, Go, Flutter, Quasar, VTEX.
- **Slash commands** (`/sdd-*`, `/tdd-*`) and **skills** you can install into `~/.claude/`.
- **MCP integration**: `context7` is mandatory for library research; optional MCPs documented in [`tools/mcp-catalog.md`](./tools/mcp-catalog.md).
- A **self-update protocol** (`update.md`) with a versioned `CHANGELOG.md`.

## How to use this kit

### From inside another project

Reference the kit using Claude Code's `@` file syntax. Example prompts:

```
Follow @~/Projects/spec-tdd-kit/workflows/greenfield.md for this project.
```

```
Onboard this repo using @~/Projects/spec-tdd-kit/workflows/brownfield.md.
```

```
Read @~/Projects/spec-tdd-kit/AGENTS.md before proceeding.
```

### Install slash commands and skills

See [`commands/README.md`](./commands/README.md) and [`skills/README.md`](./skills/README.md), or jump to [`INSTALL.md`](./INSTALL.md).

## Table of contents

**Philosophy** — why
- [`philosophy/01-sdd.md`](./philosophy/01-sdd.md) — Spec-Driven Development foundations
- [`philosophy/02-tdd.md`](./philosophy/02-tdd.md) — Test-Driven Development foundations
- [`philosophy/03-sdd-tdd-fusion.md`](./philosophy/03-sdd-tdd-fusion.md) — How SDD and TDD compose

**Workflows** — where to start
- [`workflows/greenfield.md`](./workflows/greenfield.md) — New project, end-to-end
- [`workflows/brownfield.md`](./workflows/brownfield.md) — Existing project adoption
- [`workflows/feature-flow.md`](./workflows/feature-flow.md) — Adding a feature to a kit-managed project

**Phases** — the process
- [`phases/00-constitution.md`](./phases/00-constitution.md)
- [`phases/01-specify.md`](./phases/01-specify.md)
- [`phases/02-clarify.md`](./phases/02-clarify.md)
- [`phases/03-plan.md`](./phases/03-plan.md)
- [`phases/04-tasks.md`](./phases/04-tasks.md)
- [`phases/05-implement.md`](./phases/05-implement.md)
- [`phases/06-verify.md`](./phases/06-verify.md)

**Templates** — fill in the blanks
- [`templates/`](./templates/) — constitution, spec, plan, research, contracts, tasks, ADR, test-plan

**Stacks** — the concrete test stack
- [`stacks/_generic.md`](./stacks/_generic.md) plus per-stack files

**Tools** — Claude Code as the harness
- [`tools/mcp-context7.md`](./tools/mcp-context7.md)
- [`tools/mcp-catalog.md`](./tools/mcp-catalog.md)
- [`tools/claude-code-skills.md`](./tools/claude-code-skills.md)
- [`tools/claude-code-subagents.md`](./tools/claude-code-subagents.md)
- [`tools/spec-kit-compat.md`](./tools/spec-kit-compat.md)

**Commands and skills** — installable
- [`commands/`](./commands/) — slash commands
- [`skills/`](./skills/) — auto-invokable skills

**Examples, checklists, glossary, anti-patterns**
- [`examples/`](./examples/), [`checklists/`](./checklists/), [`glossary.md`](./glossary.md), [`anti-patterns.md`](./anti-patterns.md)

**Maintenance**
- [`update.md`](./update.md) — self-update protocol
- [`CHANGELOG.md`](./CHANGELOG.md) — version history

## Conventions

- Each project managed with this kit stores its SDD artifacts under `.specs/` at the project root (not `.specify/`, to avoid collision with the Spec Kit CLI). Example layout:
  ```
  .specs/
    constitution.md
    001-feature-name/
      spec.md
      plan.md
      research.md
      contracts/
      tasks.md
  ```
- Feature folders use the pattern `NNN-kebab-case-name`, zero-padded sequentially.
- ADRs (Architecture Decision Records) live in `.specs/adr/NNNN-title.md`.
- Every task in `tasks.md` carries an identifier `T{NNN}` and three sub-steps: `R` (red), `G` (green), `F` (refactor).

## Versioning

The kit itself follows SemVer. See [`CHANGELOG.md`](./CHANGELOG.md). To upgrade the kit's own docs, run the process in [`update.md`](./update.md).

## Status

- Version: `0.1.0`
- Date: `2026-04-24`
- Maintainer: you (the repo owner)
