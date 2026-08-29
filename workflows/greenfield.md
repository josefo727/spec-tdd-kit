# Workflow — Greenfield

A new project, from zero to a first working feature under SDD + TDD. Allow ~1–3 sessions for a small feature; scale with complexity.

## Preconditions

- Empty or near-empty repository.
- Claude Code session open in the project root.
- `spec-tdd-kit` present at `~/Projects/spec-tdd-kit/` with slash commands installed (see [`../INSTALL.md`](../INSTALL.md)).
- `context7` MCP available.

## Phase map

```
0. Bootstrap                  5. Break down
1. Constitution               6. Implement (TDD loop)
2. Specify                    7. Verify
3. Clarify                    8. Close feature
4. Plan
```

---

## 0. Bootstrap

Initialize the repo and the `.specs/` tree.

```bash
git init
mkdir -p .specs/adr
```

Add a minimal `.gitignore` appropriate to the planned stack (decide stack only at the plan phase; for now, a generic ignore of OS files and editor metadata is enough).

Create an empty `.specs/constitution.md` placeholder; do not fill it yet.

Create `JOURNAL.md` at the repo root with the first session entry (see [`../sessions.md`](../sessions.md)). This is how session continuity is preserved.

Create `.claude/settings.json` from [`../templates/claude-settings.md`](../templates/claude-settings.md), adjusted to the planned stack (or the generic tiers if the stack is not yet decided), and commit it. This encodes the project's permission boundary from day one.

Agent: confirm the workflow choice with the user in Spanish. If the user wants Spec Kit interop too, read [`../tools/spec-kit-compat.md`](../tools/spec-kit-compat.md) before proceeding.

---

## 1. Constitution

Run `/sdd-constitution`. The command prompts Claude Code to draft principles based on:

- user-declared project goals (1–3 sentences),
- mandatory default articles ("test-first", "boundary-only mocks", "spec-anchored"),
- optional stack-agnostic articles the user picks from a menu.

Output: `.specs/constitution.md` populated from the [constitution template](../templates/constitution.md).

Gate: user approves each article. Commit as `spec: initial constitution v1`.

Detail: [`../phases/00-constitution.md`](../phases/00-constitution.md).

---

## 2. Specify

Pick the first feature. Name it with a three-digit prefix: `001-<kebab-name>`. For the first feature, prefer a **thin vertical slice** that exercises one user story end-to-end.

Create the feature folder:

```bash
mkdir -p .specs/001-<kebab-name>/contracts
```

Run `/sdd-specify`. The command prompts:

- user story (role, want, so-that),
- 3–7 acceptance criteria in observable terms,
- non-goals,
- applicable constitution articles,
- explicit `[NEEDS CLARIFICATION: …]` markers.

Output: `.specs/001-<kebab-name>/spec.md`. The spec **does not mention stack or libraries**.

Commit: `spec: 001 initial spec with N clarifications pending`.

Detail: [`../phases/01-specify.md`](../phases/01-specify.md).

---

## 3. Clarify

Run `/sdd-clarify`. For each `[NEEDS CLARIFICATION: …]`, Claude Code opens an `AskUserQuestion` with 2–4 option choices. User picks; the spec is updated in place.

Gate: zero unresolved markers. If a decision seems unimportant, use the "default" option choice rather than removing the marker silently.

Commit: `spec: 001 clarifications resolved`.

Detail: [`../phases/02-clarify.md`](../phases/02-clarify.md).

---

## 4. Plan

Run `/sdd-plan`. Claude Code:

1. Spawns the `Plan` subagent or drafts inline, depending on size.
2. Calls `context7` for library research; writes findings to `research.md` with `captured: YYYY-MM-DD`.
3. Proposes **stack choice**, architecture, module boundaries; each decision traces to a research note or a constitution article. Decisions with multi-task impact get an ADR in `.specs/adr/`.
4. Produces contracts in `contracts/` (YAML, OpenAPI, JSON schema, or typed interfaces — stack-appropriate).

Outputs:

- `.specs/001-<kebab-name>/plan.md`
- `.specs/001-<kebab-name>/research.md`
- `.specs/001-<kebab-name>/contracts/*`
- `.specs/adr/NNNN-*.md` (optional)

Gate: user approves the plan and stack choice. Commit: `plan: 001 initial plan + research`.

Detail: [`../phases/03-plan.md`](../phases/03-plan.md), [`../tools/mcp-context7.md`](../tools/mcp-context7.md), [`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md).

At this point, create the project skeleton matching the plan:

- initialize the package manager,
- install the test runner named in the stack file (see [`../stacks/`](../stacks/)),
- add a single hello-world test that fails,
- wire CI if practical.

Commit: `chore: project skeleton (no feature code yet)`.

---

## 5. Tasks — break down

Run `/sdd-tasks`. Claude Code reads `spec.md`, `plan.md`, `contracts/`, and `constitution.md`, then produces `tasks.md` populated from the [tasks template](../templates/tasks.md):

- `T001` … `T0NN`, zero-padded.
- Each task has: spec-ref, contract-ref, constitution-ref, DoD, R/G/F sub-steps, optional `[P]`.
- Contract tests come before consumer tests.
- Scaffolding tasks (tooling, linting, type-checking) come before feature tasks.

Gate: user approves the ordering. Commit: `plan: 001 tasks breakdown`.

Detail: [`../phases/04-tasks.md`](../phases/04-tasks.md).

---

## 6. Implement — the TDD loop

For each task `T{NNN}` in order (or in parallel for `[P]` tasks with disjoint files):

1. **`/tdd-red`**
   - Write the failing test.
   - Run the test. Capture the **failure output**; it goes into the commit body.
   - Commit: `test: T{NNN} red — <criterion>`.
2. **`/tdd-green`**
   - Write the minimal code that passes.
   - Run the full test suite; all other tests must remain green.
   - Commit: `feat: T{NNN} green — <criterion>`.
3. **`/tdd-refactor`** (optional; skip only with reason)
   - Refactor while green.
   - Commit: `refactor: T{NNN} <what improved>` OR record "F: skipped — no smell detected" in the task entry.

Close the task in `tasks.md`. Move on.

Detail: [`../phases/05-implement.md`](../phases/05-implement.md), [`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md).

---

## 7. Verify

Run `/sdd-verify`. Claude Code:

- cross-checks that every acceptance criterion in `spec.md` maps to at least one green test (R1 of the fusion rules);
- cross-checks that every task in `tasks.md` is closed;
- runs the full test suite, linter, type checker;
- looks for orphan tasks (closed but with no commit trace), orphan tests (no spec criterion), and stale `[NEEDS CLARIFICATION]` markers.

If anything fails, loop back to the minimum phase needed (usually `implement` or `clarify`).

Detail: [`../phases/06-verify.md`](../phases/06-verify.md).

---

## 8. Close feature

Tag the commit: `git tag 001-<kebab-name>-done`. Update `.specs/001-<kebab-name>/spec.md` with a closing footer:

```
## Closed
- Date: YYYY-MM-DD
- Commit: <sha>
- Notes: <anything non-obvious for future readers>
```

Commit: `spec: 001 closed`.

If lessons emerged, feed them to the kit via [`../update.md`](../update.md) triggers (e.g., new anti-pattern, new stack gotcha).

---

## Mini-map of commands used

| Phase | Slash command |
|---|---|
| Constitution | `/sdd-constitution` |
| Specify | `/sdd-specify` |
| Clarify | `/sdd-clarify` |
| Plan | `/sdd-plan` |
| Tasks | `/sdd-tasks` |
| Implement | `/sdd-implement`, then `/tdd-red`, `/tdd-green`, `/tdd-refactor` per task |
| Verify | `/sdd-verify` |

## Typical durations (order of magnitude)

- Tiny feature (one endpoint, one table, no external deps): 1–2 hours.
- Small feature (3–5 endpoints, contracts, one third party): half-day to full day.
- Medium feature: 2–3 days.

If a feature projects larger than 3 days, slice it.

## Common pitfalls

- Skipping constitution → confusion later. Do not skip.
- Skipping clarify → the agent guesses; you re-do work.
- Stack chosen during `specify` → contaminates the spec. Keep the spec stack-free.
- Parallel `[P]` on overlapping files → merge pain. Audit.
