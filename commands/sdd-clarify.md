---
description: Resolve [NEEDS CLARIFICATION] markers in the active spec (SDD phase 2).
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/02-clarify.md

Goal: remove every `[NEEDS CLARIFICATION]` marker from the active `spec.md`. Each resolution comes from explicit user dialog.

Steps:

1. Find the active feature folder. If unclear, ask the user to pick from `.specs/NNN-*`.

2. Parse the spec for `[NEEDS CLARIFICATION: …]` markers. Number them internally (Q1, Q2, …).

3. For each question, in order:
   - Frame the question in one sentence (Spanish).
   - Propose 2–4 options, always including:
     - the minimal/default path,
     - a more rigorous path,
     - `Otro: texto libre`.
   - Use the AskUserQuestion tool when available. Otherwise enumerate in chat and wait.

4. On the user's answer:
   - Edit the spec: remove the marker, reflect the choice in the criterion.
   - If the decision is substantive, append an entry to `clarify.md` in the same feature folder (create the file on first use), with: question, options, decision, reason, date.

5. If a clarification reveals the spec is wrong (not merely ambiguous):
   - Stop the loop.
   - Tell the user in Spanish.
   - Return to `/sdd-specify` for the affected section.

6. When zero markers remain:
   - Show the user the final spec or the diff.
   - Commit: `spec: NNN clarifications resolved`

Reminders:
- Never remove a marker without a user answer.
- If the user says "tú decides", pick the minimal/default path AND record the delegation explicitly in `clarify.md`.
- Avoid compound questions; split into multiple Qs.
