# The Ratchet — converting failures into permanent infrastructure

Practice for making every agent correction permanent. A correction made only in conversation is
lost when the session ends; the ratchet turns it into a dated rule, and a repeated rule into an
automated check. The direction only tightens — hence the name.

Added in kit `0.3.0`. Decision record: [`adr/0001-ratchet-and-permission-boundaries.md`](./adr/0001-ratchet-and-permission-boundaries.md).

## Why it exists

Guide files (`AGENTS.md`, `CLAUDE.md`, project constitutions) accumulate rules one at a time.
Without discipline they become undated piles: nobody knows why a rule exists, rules contradict
each other, and the agent starts ignoring the file. Meanwhile, rules that matter keep being
enforced by *memory* — the agent's or the user's — instead of by structure.

Two principles, borrowed and adapted:

- **The ratchet** (Hashimoto): every time the agent makes a mistake, engineer a solution so that
  mistake never happens again. Fix the harness, not the conversation.
- **Three strikes** (Tan, Cursor): when the same correction is made a third time, it stops being
  text and becomes a gate.

## The rule format

Every rule added to a guide file carries a **date** and a **one-line trace** to the failure that
motivated it:

```md
- [2026-08-29] Never run `migrate:fresh` outside local env — agent wiped staging seed data (proj-x, T017).
```

- The date answers "when was this added and is it still current?".
- The trace answers "what breaks if we delete this?". A rule nobody can trace gets deleted or
  re-justified at the next prune.
- Rules without dates are allowed only in the initial bootstrap block (build/test/lint commands,
  project facts). Everything born from a failure is dated.

## The promotion ladder

| Occurrence | Action | Enforced by |
|---|---|---|
| 1st | Correct in conversation | Nobody (it will be lost) |
| 2nd | Add a **dated rule** to the project's guide file | The agent reading the file |
| 3rd | Promote to a **hook or automated check** | The harness — structurally |
| 4th | Should be impossible | — |

Promotion targets, strongest first:

1. **Permission rule** — the action becomes impossible or requires confirmation
   (`deny` / `ask` in `.claude/settings.json`; see [`templates/claude-settings.md`](./templates/claude-settings.md)).
2. **Hook** — the check runs automatically, not when the agent remembers
   (e.g., a `PostToolUse` hook running lint/static analysis after every edit, a pre-commit hook
   running the test suite).
3. **Test or linter rule** — the violation fails the suite (this is the kit's home turf: if the
   rule is about behavior, it belongs in a test written from acceptance criteria).

Pick the strongest target that fits. A rule that can be checked without subjective judgment
should not remain prose.

## Pruning

Monthly, or at every kit-managed release — whichever comes first:

- **Delete** rules that a hook, permission, linter, or test now enforces. Leave a pointer in the
  commit message, not in the file.
- **Consolidate** rules addressing the same failure class into one.
- **Re-justify or delete** rules whose trace no longer applies (the tool changed, the file is
  gone, the pattern is now idiomatic).
- A guide file that only grows is technical debt. Healthy growth rate declines over time as
  common failure classes get covered by structure.

## Agent behavior (summary)

- **On the second correction** of the same failure class: propose a dated rule for the project's
  guide file, in the format above. Never add it silently.
- **On the third occurrence** (rule violated, or user corrects again despite the rule): propose
  promotion — draft the `settings.json` diff, the hook, or the test. Never install it silently.
- **When writing any rule**: date it, trace it.
- **When asked to prune**: apply the three pruning moves above; list deletions for confirmation
  (user's global rule: never delete without confirmation).

## Cross-references

- Agent directives: [`AGENTS.md`](./AGENTS.md) § Ratchet protocol
- Permission template: [`templates/claude-settings.md`](./templates/claude-settings.md)
- Anti-patterns this defends against: [`anti-patterns.md`](./anti-patterns.md) §16 (Undated rule pile), §17 (Trust-based verification)
- Decision record: [`adr/0001-ratchet-and-permission-boundaries.md`](./adr/0001-ratchet-and-permission-boundaries.md)
