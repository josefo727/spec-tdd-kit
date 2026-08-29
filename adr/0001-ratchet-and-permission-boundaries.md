# ADR 0001 — Adopt the ratchet discipline and per-project permission boundaries

> First kit-level ADR. Project-level ADRs live in each project's `.specs/adr/`; decisions about
> the kit itself live here, in `spec-tdd-kit/adr/`. Creating this directory is part of this proposal.

- **Status:** accepted
- **Date:** 2026-08-29
- **Deciders:** josefo
- **Context links:** `~/Projects/harness_final.pdf` (harness engineering playbook, independently compiled, Aug 2026)

## Context

A review of the harness engineering playbook ("Agent = Model + Harness: The 6-Layer Production
Playbook") was mapped against the kit. The playbook's six layers are: guides, sensors, agentic
loop, memory, permissions, observability.

The gap analysis concluded:

- **Already covered by the kit or by Claude Code natively:** guides (`AGENTS.md`, constitution),
  sensors (the R-G-F cycle, `/sdd-verify` R1–R10), agentic loop (Claude Code itself), memory
  (`JOURNAL.md` + `tasks.md` + `.specs/`, richer than the playbook's JSON checkpoint).
- **Genuine gaps:**
  1. **Rule hygiene.** Guide files (`AGENTS.md`, `CLAUDE.md`, constitutions) accumulate rules
     with no date, no traceability to the failure that motivated them, and no pruning schedule.
     The playbook calls an undated 200-line guide file "technical debt", correctly.
  2. **Guide-to-sensor promotion.** The kit trusts the agent to remember rules and to run
     `/sdd-verify`. Repeated corrections should instead be promoted to Claude Code **hooks**
     (structural enforcement), following the "three strikes" pattern (Tan, Cursor): a rule the
     agent violates repeatedly stops being text and becomes an automated check.
  3. **Permission encoding.** Nothing in the kit configures per-project permission boundaries.
     Behavior depends on interactive permission prompts, which is neither reproducible across
     sessions nor shareable across team members. The playbook's "capability budget" maps
     directly onto a project's `.claude/settings.json` (allow / ask / deny).
  4. **Recovery test.** `sessions.md` defines the journal but never requires verifying that
     resume-from-journal actually works. The playbook's "recovery test" (close mid-task, reopen,
     verify resumption without repeated work) is a cheap one-time check per project.

Deliberately **discounted** from the playbook: its benchmark claims (unverifiable), the
observability layer (trip wires, cost-per-task scorecards — aimed at unattended agent fleets,
not interactive supervised work), and the multi-agent chapter. Adopting those would violate the
playbook's own warning against the over-engineered harness.

## Options considered

### Option A — Fold everything into `anti-patterns.md`

- Pros: minimal diff; no new files; no install steps.
- Cons: anti-patterns are diagnostic ("what not to do"); the ratchet is an operating procedure
  ("what to do when the agent fails"). Mixing them buries the procedure. Permission templates
  do not fit the anti-patterns format at all.
- Effort / risk: low / medium (low discoverability, likely ignored).

### Option B — New first-class doc `ratchet.md` + permissions template + targeted amendments (chosen)

- Pros: the ratchet gets the same first-class status `sessions.md` got in 0.2.0; the permissions
  template becomes part of project bootstrap; `AGENTS.md` gains enforceable agent behavior;
  every change is additive (minor bump).
- Cons: kit grows by two files; workflows gain one bootstrap step each.
- Effort / risk: medium / low.

### Option C — Adopt the full six-layer harness (observability, budgets, trip wires, multi-agent)

- Pros: complete parity with the playbook.
- Cons: built for unattended agent fleets; the kit targets interactive, supervised, solo/small-team
  development. Cost-per-task telemetry and trip wires add maintenance with no consumer.
  The playbook itself warns: "if the agent spends more time checking its own work than doing
  it, the harness is too heavy."
- Effort / risk: high / high (bottleneck disguised as safety).

## Decision

Adopt **Option B**. The kit gains the ratchet discipline as a first-class protocol and a
capability-budget template for per-project `.claude/settings.json`, plus the recovery test in
`sessions.md`. Observability and multi-agent layers are explicitly out of scope.

## Proposed changes (file by file)

1. **`ratchet.md`** (new, kit root) — the ratchet protocol:
   - **Rule format:** every rule added to `AGENTS.md` / `CLAUDE.md` / a constitution carries a
     date and a one-line trace to the failure that motivated it
     (`- [2026-08-29] Never run migrations on shared DBs — agent wiped seed data on project X`).
   - **Promotion ladder:** in-chat correction → dated guide rule (second occurrence) → hook or
     automated check (third occurrence). The fourth occurrence should be impossible.
   - **Hook examples:** post-edit hook running lint/static analysis; pre-commit hook running the
     test suite; configured via the project's `.claude/settings.json` hooks section.
   - **Pruning:** monthly review; delete rules a hook/sensor now enforces; consolidate rules
     addressing the same failure class; a rule nobody can trace gets deleted or re-justified.
2. **`templates/claude-settings.md`** (new) — capability-budget template for a project's
   `.claude/settings.json`:
   - **Allow:** read/edit project files, run test/lint/build commands for the stack.
   - **Ask:** `git push`, dependency installs, DB migrations, anything externally visible.
   - **Deny:** destructive operations (`rm -rf`, `DROP`, force-push to main), secrets files.
   - One concrete example (Laravel: `php artisan test`, `vendor/bin/pint`, `vendor/bin/phpstan`
     allowed; `php artisan migrate:fresh` ask; `.env` write deny), plus generic guidance for the
     other stack appendices.
3. **`AGENTS.md`** — new §"Ratchet protocol":
   - When the user corrects the agent twice for the same failure class, the agent proposes a
     dated rule for the project's `AGENTS.md` (never adds it silently).
   - When a dated rule is violated or re-corrected a third time, the agent proposes promoting it
     to a hook, drafting the `settings.json` diff.
   - All rules the agent writes follow the `ratchet.md` format.
4. **`anti-patterns.md`** — append two entries:
   - **16. Undated rule pile** — guide file grows without dates, traces, or pruning; rules
     contradict each other; the agent starts ignoring the file. Defense: `ratchet.md` format +
     monthly prune.
   - **17. Trust-based verification** — checks that only run if the agent remembers to run them.
     Defense: promote recurring checks to hooks; the third repeated correction is the signal.
5. **`workflows/greenfield.md`** and **`workflows/brownfield.md`** — bootstrap gains one step:
   create the project's `.claude/settings.json` from `templates/claude-settings.md`, adjusted to
   the stack, and commit it.
6. **`sessions.md`** — new §"The recovery test": once per project (after the first real pause),
   close mid-task, reopen, and verify the agent resumes from `JOURNAL.md` + `tasks.md` without
   repeating work or asking the user to re-explain. If it fails, the journal entry format — not
   the agent — is what gets fixed.
7. **`update.md`** — new research trigger 9: review rule hygiene across kit-managed projects
   (undated rules, stale rules, rules eligible for promotion to hooks); feed findings into
   `ratchet.md` and project guide files.
8. **`CHANGELOG.md`** — new `[0.3.0]` entry; `README.md` TOC and version updated.

**Version bump:** minor → `0.3.0` (additive: new docs, new template, new sections).

## Consequences

- **Positive:** corrections become permanent instead of per-conversation; repeated failures
  convert into structural enforcement (hooks); permission behavior becomes reproducible and
  committable per project; guide files stay prunable and trustworthy.
- **Negative:** two more files to maintain; the monthly prune is a recurring obligation that
  will be skipped unless trigger 9 in `update.md` backs it up; hooks add per-project setup cost.
- **Follow-ups:** on acceptance, execute via the `update.md` protocol (plan → edit → consistency
  sweep → changelog → commit → tag `v0.3.0`); retrofit `.claude/settings.json` into currently
  active kit-managed projects opportunistically, not as a big-bang migration.

## Constitution impact

None on the kit. Project constitutions MAY add an article referencing the ratchet rule format
for their own guide files; that is per-project and requires the project's own ADR.

## References

- Research entries: gap analysis performed in-session, 2026-08-29 (PDF vs. kit structure).
- External sources:
  - `harness_final.pdf` — "Agent = Model + Harness: The 6-Layer Production Playbook" (treat
    benchmark figures as unverified).
  - M. Hashimoto, "My AI Adoption Journey" (the ratchet principle).
  - L. Tan (Cursor), repeated-review-comment promotion pattern ("three strikes").
  - B. Böckeler / martinfowler.com, guides-and-sensors taxonomy.
