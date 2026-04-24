# Compatibility — GitHub Spec Kit

This kit is **independent** of GitHub's [Spec Kit](https://github.com/github/spec-kit). It reuses the vocabulary (constitution, specify, plan, tasks, implement) because those concepts are sound. You do not need Spec Kit to use this kit.

Some teams or projects may still want Spec Kit (standardization, plugins, ecosystem). This doc explains how to combine them without collisions.

## Default: do not install Spec Kit

Unless a project *requires* Spec Kit, skip it. Reasons:

- Extra runtime dependency (`uv`, Python).
- Command namespace collision (`/speckit.*` vs. our `/sdd-*`).
- Generated artifact paths (`.specify/`) differ from ours (`.specs/`).
- Spec Kit's TDD is light; you would still need this kit's `implement` phase.

## If Spec Kit is required

Install Spec Kit per its README:

```
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
specify init <project> --ai claude
# or inside an existing project:
specify init --here --ai claude --force
```

Then apply the adaptations below.

### Path convention

- Our docs assume artifacts in `.specs/`. Spec Kit writes to `.specify/`.
- Choice 1 (preferred): keep both. Spec Kit writes its own tree; our kit's artifacts (test plans, ADRs, brownfield index) live under `.specs/`.
- Choice 2: bind-mount or symlink `.specs/` → `.specify/specs/` (not recommended; fragile).

### Commands

Spec Kit ships `/speckit.*`. Our kit ships `/sdd-*` and `/tdd-*`. They do not collide.

- Drive the SDD phases with either `/sdd-*` or `/speckit.*`.
- Drive the TDD beats with `/tdd-red`, `/tdd-green`, `/tdd-refactor` — Spec Kit has no equivalent.

### Constitution

Spec Kit's constitution lives at `.specify/memory/constitution.md`. Mirror our required articles there; keep one copy as source of truth. If the project uses Spec Kit, its location is the canonical one, and our `workflows/*.md` references follow it.

### Phase gates

Spec Kit does not enforce contract-first scheduling or Red-before-Green. You still apply our gates on top. The gates in [`../AGENTS.md`](../AGENTS.md) override silence.

### Slash commands coexisting

You can use both in the same session:

- `/speckit.specify` to draft a Spec Kit–shaped spec.
- `/sdd-clarify` to run the kit's clarify with AskUserQuestion.
- `/sdd-tasks` to produce a tasks file with R-G-F.
- Spec Kit's `/speckit.implement` is a simple runner; prefer our `/sdd-implement` + `/tdd-*` for TDD discipline.

### Updates

When Spec Kit publishes a release, trigger our [`../update.md`](../update.md) with focus on "Spec Kit interop" and patch this file.

## Checklist before mixing

- [ ] Team actually needs Spec Kit.
- [ ] Paths decided: `.specs/` vs. `.specify/` or both.
- [ ] Constitution location canonical and noted in `README.md`.
- [ ] Commands documented for new contributors.
- [ ] CI does not run Spec Kit commands that overwrite our artifacts.

## Last note

If Spec Kit later adds strict TDD, reconsider. Until then, the fusion in this kit stays first-party.
