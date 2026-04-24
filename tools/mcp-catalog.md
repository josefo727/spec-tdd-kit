# MCP catalog

Additional MCP servers that can augment the SDD+TDD flow. None is mandatory except `context7`. Use each only when its value is clear.

## Mandatory

### `context7`

Library documentation. See [`./mcp-context7.md`](./mcp-context7.md).

## Optional — issue/PR/code collaboration

### GitHub (`plugin:gitlab` if applicable)

- **When:** projects hosted on GitHub/GitLab.
- **Value:** open/link issues and PRs, comment with verify reports, read PR context.
- **Caveat:** `gh` CLI also covers most flows; prefer it for scripted actions.

## Optional — observability / errors

### Sentry (`mcp__sentry__*`)

- **When:** the project uses Sentry for error tracking.
- **Value during verify:** read recent issues to validate that the feature's deploy did not introduce regressions visible in Sentry.
- **Flow:**
  - After merge, query `find_projects`, then `list_issues` filtered by release.
  - Report in the verify closure of the next feature.

### Flare (skill `flare`)

- **When:** the project uses Flare.
- **Value:** similar to Sentry; triage after deploy.

## Optional — product/design context

### Jam (`mcp__Jam__*`)

- **When:** frontend feature driven from a Jam recording.
- **Value:** import user reports as context during `specify` and `clarify`.
- **Caveat:** keep Jam context in the spec's "inputs" section, not as acceptance criteria verbatim.

## Optional — data / calendar

### Google Drive / Gmail / Calendar

- **When:** specs or research require stakeholder docs stored in Google Workspace.
- **Value:** ingest directly.
- **Caveat:** do not paste confidential content into the spec. Summarize and link.

## Rules

1. **Cite every MCP-derived finding** in the relevant artifact (`research.md`, `spec.md`'s inputs section, or an ADR).
2. **Do not let an MCP drive decisions unchallenged.** MCPs surface information; humans and tests decide.
3. **Catalog updates** belong in an `update.md` pass; add or remove entries as the ecosystem shifts.

## Absent from the catalog (with reason)

- **Database MCPs:** prefer ORM/driver abstractions + testcontainers over live DB MCPs inside agent loops.
- **Browser-automation MCPs:** prefer Playwright/Cypress inside the test suite. Browser MCPs are for exploration, not for production flows.
- **Shell MCPs:** the Bash tool is sufficient; extra shell MCPs add surface area without value.

## Cross-references

- MCP in Claude Code: [`./claude-code-skills.md`](./claude-code-skills.md), [`./claude-code-subagents.md`](./claude-code-subagents.md)
- Self-update: [`../update.md`](../update.md) (trigger 3)
