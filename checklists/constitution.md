# Checklist — Constitution

Use before committing a new or amended `.specs/constitution.md`.

## Content

- [ ] Preamble present: one paragraph stating purpose and audience.
- [ ] Mandatory articles I–VI included with statements, rationale, enforcement.
- [ ] Article VII — Boundaries — lists every boundary relevant to the project.
- [ ] Every article is enforceable (test, lint, review gate, or ADR trigger).
- [ ] No article encodes a preference without teeth. Preferences moved to stack appendices.

## Form

- [ ] Articles numbered continuously; no gaps.
- [ ] Each article has a short title (5–10 words).
- [ ] Language: declarative, present tense.
- [ ] Under 15 articles total; if more, consolidate.

## Brownfield-specific (skip for greenfield)

- [ ] `Current` section present; each rule has evidence (file path).
- [ ] `Aspirational` section present; each article has `applies_from` and `migration`.
- [ ] Every mandatory article (I–VI) appears in either Current or Aspirational.

## Amendments

- [ ] `Amendments` table present at the bottom.
- [ ] If this change is an amendment, a linked ADR exists in `.specs/adr/`.

## Approval

- [ ] User has reviewed article by article.
- [ ] User approves in Spanish.
- [ ] Commit message matches convention:
  - New: `spec: initial constitution v1`
  - Amendment: `spec: amend article N (see ADR NNNN)`
