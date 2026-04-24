# INSTALL.md

The kit ships source files for **slash commands** and **skills**. To make them usable by Claude Code globally, drop them into `~/.claude/commands/` and `~/.claude/skills/`.

> Transparent by design: no install script. Copy or symlink manually so you see exactly what lands in your Claude config.

## Prerequisites

- Claude Code installed (`claude --version`).
- This repo cloned/present at `~/Projects/spec-tdd-kit/`.

## Slash commands

Source: `~/Projects/spec-tdd-kit/commands/*.md` (ignore `README.md`).

### Option A — copy

```bash
cp ~/Projects/spec-tdd-kit/commands/sdd-*.md ~/.claude/commands/
cp ~/Projects/spec-tdd-kit/commands/tdd-*.md ~/.claude/commands/
```

Each file `foo.md` becomes the slash command `/foo`.

### Option B — symlink (recommended)

Symlinks keep installed commands in sync with the kit automatically:

```bash
for f in ~/Projects/spec-tdd-kit/commands/sdd-*.md ~/Projects/spec-tdd-kit/commands/tdd-*.md; do
  ln -sf "$f" ~/.claude/commands/"$(basename "$f")"
done
```

### Uninstall

```bash
rm ~/.claude/commands/sdd-*.md ~/.claude/commands/tdd-*.md
```

## Skills

Source: `~/Projects/spec-tdd-kit/skills/<name>/SKILL.md`.

### Option A — copy

```bash
for dir in ~/Projects/spec-tdd-kit/skills/spec-tdd-*; do
  name="$(basename "$dir")"
  mkdir -p ~/.claude/skills/"$name"
  cp "$dir"/SKILL.md ~/.claude/skills/"$name"/SKILL.md
done
```

### Option B — symlink (recommended)

```bash
for dir in ~/Projects/spec-tdd-kit/skills/spec-tdd-*; do
  name="$(basename "$dir")"
  ln -sfn "$dir" ~/.claude/skills/"$name"
done
```

### Uninstall

```bash
rm -rf ~/.claude/skills/spec-tdd-greenfield ~/.claude/skills/spec-tdd-brownfield ~/.claude/skills/spec-tdd-cycle
```

## Verify

```bash
ls ~/.claude/commands | grep -E '^(sdd|tdd)-'
ls ~/.claude/skills   | grep '^spec-tdd-'
```

Inside a Claude Code session:

- Type `/` and look for `sdd-` and `tdd-` entries.
- Ask: *"Listar skills instaladas relevantes para SDD."* — Claude should mention the three `spec-tdd-*` skills.

## Updates

When you run the kit's update protocol ([`update.md`](./update.md)), symlinks auto-update. If you copied instead, re-run the copy block after the update commit.
