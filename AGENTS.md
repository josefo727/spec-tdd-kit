# AGENTS.md — Instructions for Claude Code when working inside this kit

This file is the primary directive for any Claude Code session that references `spec-tdd-kit`. Read it first.

## Language protocol

- **Communicate with the user in Spanish.**
- **Write all artifacts (specs, plans, tasks, tests, ADRs, commit messages, code comments) in English.**
- When quoting user decisions inside an artifact, translate them to English; keep the Spanish original in a `> Original:` blockquote only if nuance could be lost.

## Global behavior

1. **Never write production code before its test exists.** The TDD cycle is non-negotiable inside the `implement` phase. See [`phases/05-implement.md`](./phases/05-implement.md).
2. **Never edit the constitution silently.** Amendments require an ADR in `.specs/adr/`. See [`phases/00-constitution.md`](./phases/00-constitution.md).
3. **Never invent library APIs from memory.** Use `context7` MCP before writing code that touches an external library, framework, or SDK. See [`tools/mcp-context7.md`](./tools/mcp-context7.md).
4. **Never delete files without explicit confirmation** (inherited from user's global rule).
5. **Prefer small, reviewable commits**, one per Red-Green-Refactor beat when practical.

## Decision tree on session start

When the user invokes the kit or references any of its files:

1. **Detect state** of the target project:
   - Does `.specs/constitution.md` exist?
     - **No** and repo is empty/near-empty → [`workflows/greenfield.md`](./workflows/greenfield.md).
     - **No** and repo has significant code → [`workflows/brownfield.md`](./workflows/brownfield.md).
     - **Yes** → use [`workflows/feature-flow.md`](./workflows/feature-flow.md).
2. **Confirm the detected workflow with the user in Spanish** before proceeding.
3. **Load the relevant phase file** for the current step. Do not skip phases unless the user asks and you flag the risk.

## Phase gates (do not cross without evidence)

| From | To | Required evidence |
|---|---|---|
| constitution | specify | Constitution approved by user, committed |
| specify | clarify | Spec has at least one `[NEEDS CLARIFICATION]` resolved list OR user confirms no ambiguity |
| clarify | plan | Zero unresolved `[NEEDS CLARIFICATION]` markers |
| plan | tasks | `plan.md`, `research.md`, and `contracts/` present and user-approved |
| tasks | implement | `tasks.md` present, each task has R-G-F steps and DoD |
| implement | verify | All tasks closed, all tests green on CI or local equivalent |

If evidence is missing, **stop and surface the gap** to the user. Do not fabricate it.

## Use Claude Code features deliberately

- **Subagents**: spawn `Plan` for planning phase; `Explore` for brownfield discovery; stack-specific ones (`laravel-*`, `vue-developer`) when the stack matches. See [`tools/claude-code-subagents.md`](./tools/claude-code-subagents.md).
- **Skills**: the kit ships three (`spec-tdd-greenfield`, `spec-tdd-brownfield`, `spec-tdd-cycle`). Users may invoke them by name or they may auto-trigger from description.
- **MCPs**: `context7` for library docs. Other MCPs only when the catalog justifies.
- **AskUserQuestion tool**: when ambiguities arise, prefer structured multi-choice questions. Use it during `clarify` phase.

## Commits

- No `Co-Authored-By` lines (user's global rule).
- Conventional-ish prefixes allowed but not mandated: `spec:`, `plan:`, `test:`, `feat:`, `refactor:`, `docs:`.
- Commit granularity inside `implement`:
  - `test: T042 red — failing test for X`
  - `feat: T042 green — minimal pass for X`
  - `refactor: T042 clean — extract Y` (optional if nothing to refactor)

## When you are unsure

- Stop and ask. Prefer the `AskUserQuestion` tool.
- Never silently downgrade a principle, skip a test, or mock something the constitution forbids mocking.
- Never invent a spec detail. Mark with `[NEEDS CLARIFICATION: <question>]`.

## When a principle and a request conflict

- Surface the conflict to the user in Spanish.
- Propose two paths: (a) refuse the request, (b) amend the constitution via ADR.
- Do not proceed until the user chooses.

## Session protocol (journal)

Every kit-managed project has a `JOURNAL.md` at its root. Claude Code uses it to preserve continuity across sessions. Full spec: [`sessions.md`](./sessions.md).

### On resume (session start)

1. Read the **last entry** of `JOURNAL.md` before anything else.
2. Summarise in Spanish: *"retomamos desde X; queda pendiente Y"*.
3. Propose the **specific next step** (slash command, file, test).
4. Wait for user confirmation; never assume.

### On pause (session end)

1. Announce in Spanish: *"voy a añadir entrada al journal antes de cerrar"*.
2. Append a new entry with current date/time, following the format in [`sessions.md`](./sessions.md).
3. Offer the draft to the user for edits.
4. Commit: `docs: journal entry YYYY-MM-DD`.

### Silent close detection

If on the next session the latest commit is newer than the latest journal entry, propose a retrospective entry. Do not fabricate — ask the user for the missing context.

### When `JOURNAL.md` does not exist

Create it at the current session's bootstrap, with an initial entry describing the session about to start. Both `workflows/greenfield.md` and `workflows/brownfield.md` include this as an explicit step.

## Self-update

- If the user types anything like *"actualiza el kit"*, *"realiza un update"*, *"run update"*, open [`update.md`](./update.md) and follow the protocol.
