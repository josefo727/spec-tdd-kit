# Sessions and the Journal

Practice for preserving continuity between work sessions when a project is carried forward across days, weeks, or pauses — especially when the work is solo and AI-assisted.

Added in kit `0.2.0`.

## Why it exists

The kit already has:

- `tasks.md` with `status: open | in_progress | closed | skipped`.
- `commits` field per task.
- Git history with conventional messages.

None of those capture:

- Mental state when pausing (hypotheses, dead ends, intuitions).
- Partial artifacts (a test half-written, a fixture not yet committed).
- Discovered issues that are not yet opened tasks.
- Blockers under investigation.
- Cross-feature observations ("working on 001 but noticed X that affects 004").

The journal fills that gap. It is the session-level complement to the task-level structure.

## The file

Path: **`JOURNAL.md`** at the project root (not under `.specs/` — it is repo-level, cross-feature).

Format: **append-only Markdown**, newest entry at the bottom. Never rewrite older entries; dead ends are value.

Granularity: **one entry per work session**, not per commit. A session with five commits becomes one entry.

Audience: future-you + Claude Code on the next session.

## Entry format

```md
## YYYY-MM-DD HH:MM — session N (short tag)

### Context
<one or two lines: what feature, what phase, where in the flow>

### Done this session
- <bullet>
- <bullet, include commit SHAs when useful>

### Open
- <next concrete actions, in order>

### Blockers / open questions
- <if any; "none" is valid>

### Decisions recorded elsewhere
- <ADRs written, spec amendments, constitution amendments>

### Dead ends / discarded
- <things tried and abandoned, with reason>

### Resume from
<very specific: "run /sdd-clarify 001", or "T042 green beat — test at tests/X.test.ts:17 already written, needs implementation in src/domain/Y.ts">
```

Only `Context`, `Done`, `Open`, and `Resume from` are required. The rest are optional when they have no content.

## Protocol

### On pause (end of session)

1. Claude Code announces the pause: *"voy a añadir entrada al journal antes de cerrar"*.
2. Appends a new entry with the current date/time.
3. Proposes the entry to the user; user may edit / extend before committing.
4. Commits the entry: `docs: journal entry YYYY-MM-DD`.

If the user closes the session silently (no pause signal):

- The next session **opens** with a journal post-mortem step:
  - Read the last commit, last journal entry timestamps.
  - If last commit is newer than last journal entry, propose a retrospective entry.
  - Ask for user input; do not fabricate.

### On resume (start of session)

1. Claude Code reads **only** the last journal entry first.
2. Summarises in Spanish: *"retomamos desde X; queda pendiente Y"*.
3. Proposes the **specific next step**: which slash command, which file to open, which test to continue.
4. Waits for user confirmation before proceeding.
5. Never assumes the user wants to continue exactly where they stopped; always offer the option to switch context.

### Mid-session check-ins

If a session runs long and takes a sharp turn (new blocker, pivot to a different feature), append a mid-session interim entry with the current date/time. The next entry supersedes it semantically but both stay in the file.

## Quality rules

- **Be honest about blockers.** "Blocked on licensing clarification" beats "in progress".
- **Include dead ends.** Future-you needs to know what you already tried.
- **Concrete resume pointers.** "Test at `tests/X.test.ts:17` half-written" is useful; "continue working" is not.
- **Reference specs, tasks, commits by ID or SHA.** The journal is navigational.
- **One paragraph maximum per section.** Journals rot when they become essays.

## Journal vs. tasks.md

| | Journal | tasks.md |
|---|---|---|
| Scope | Repo / session | Feature |
| Shape | Prose, chronological | Structured, indexed |
| Lifetime | Forever (append-only) | Closes with the feature |
| Written | Once per session | Once per task beat |
| Purpose | Continuity, narrative | Progress, gates |

A single feature's `tasks.md` may span multiple journal entries. A single journal entry may touch multiple features.

## Git

- Commit the journal entry separately when it doesn't coincide with a feature commit:
  `docs: journal entry 2026-04-24`
- When it coincides with a feature commit (closing a task, amending a spec), append the journal update to that commit — one less context switch.
- No squash, no rewrite of journal entries. History is the point.

## Agent behavior (summary)

- **On resume**: read last entry → summarise in Spanish → propose next step → wait.
- **On pause signal**: propose entry → user edits → commit.
- **On silent close**: next session detects gap → offers retrospective entry.
- **Never**: rewrite old entries, fabricate facts, skip the journal because "nothing happened".

## Cross-references

- Agent directives: [`AGENTS.md`](./AGENTS.md) § Session protocol
- Workflows reference journal creation at bootstrap: [`workflows/greenfield.md`](./workflows/greenfield.md), [`workflows/brownfield.md`](./workflows/brownfield.md)
- Changelog: [`CHANGELOG.md`](./CHANGELOG.md)
