# Template — `.claude/settings.json` (capability budget)

> Copy the JSON below to `<project>/.claude/settings.json`, adjust to the stack, and **commit it**.
> It encodes the project's permission boundary: what the agent may do freely, what requires
> confirmation, and what is forbidden — reproducible across sessions and team members, instead
> of depending on interactive permission prompts.

Added in kit `0.3.0`. Decision record: [`../adr/0001-ratchet-and-permission-boundaries.md`](../adr/0001-ratchet-and-permission-boundaries.md).

## The three tiers

| Tier | Criterion | Examples |
|---|---|---|
| `allow` | Reversible, no external visibility | run tests, lint, static analysis, build |
| `ask` | Externally visible or hard to undo casually | `git push`, dependency installs, migrations |
| `deny` | Destructive or touching secrets | `rm -rf`, `migrate:fresh`, force-push, `.env` |

Rules follow Claude Code's permission syntax: `Tool(specifier)`, where Bash specifiers use
prefix matching with `:*`. `deny` beats `ask` beats `allow`.

## Example — Laravel/PHP

```json
{
  "permissions": {
    "allow": [
      "Bash(php artisan test:*)",
      "Bash(vendor/bin/pest:*)",
      "Bash(vendor/bin/pint:*)",
      "Bash(vendor/bin/phpstan:*)",
      "Bash(composer test:*)",
      "Bash(composer lint:*)"
    ],
    "ask": [
      "Bash(git push:*)",
      "Bash(composer require:*)",
      "Bash(composer update:*)",
      "Bash(php artisan migrate:*)",
      "Bash(npm install:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(php artisan migrate:fresh:*)",
      "Bash(php artisan db:wipe:*)",
      "Bash(git push --force:*)",
      "Read(./.env)",
      "Read(./.env.*)",
      "Edit(./.env)",
      "Edit(./.env.*)"
    ]
  }
}
```

Note the ordering subtlety: `php artisan migrate:*` is `ask`, but the destructive variants
(`migrate:fresh`, `db:wipe`) are explicitly in `deny`, which wins.

## Adapting to other stacks

Keep the tiers, swap the commands. From the stack appendices in [`../stacks/`](../stacks/):

- **Node/Vue/React/Quasar**: allow `npm test`, `npm run lint`, `npx vitest:*`; ask `npm install:*`, `npm publish:*`; deny `rm -rf:*`.
- **Python**: allow `pytest:*`, `ruff:*`, `mypy:*`; ask `pip install:*`, `uv add:*`.
- **Rust / Go**: allow `cargo test:*` / `go test:*`, `cargo clippy:*` / `go vet:*`; ask `cargo publish:*`.
- **Any stack**: deny reads/edits of secrets files (`.env*`, `*.pem`, credential stores); deny
  force-push and recursive deletes; ask anything that leaves the machine (push, publish, deploy).

## Hooks — promoting rules to structure

When a guide rule earns its third strike (see [`../ratchet.md`](../ratchet.md)), it can move
into a hook so the check runs automatically instead of when the agent remembers. Example: run
formatter + static analysis after every file edit:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "vendor/bin/pint --dirty --test && vendor/bin/phpstan analyse --no-progress" }
        ]
      }
    ]
  }
}
```

Keep hooks fast (they run often) and deterministic. A hook that fails intermittently gets
disabled and the rule silently regresses to prose — the opposite of the ratchet.

## Rules of thumb

- Start from this template at bootstrap; tighten with `deny` entries as failures occur (each one
  dated and traced per [`../ratchet.md`](../ratchet.md)).
- The file is per-project and committed. Personal overrides go in `.claude/settings.local.json`
  (gitignored), never in the shared file.
- Review the boundary at the same monthly prune as the guide files: an `ask` that always gets
  approved for the same safe command can graduate to `allow`; an `allow` that caused an incident
  tightens to `ask`.
