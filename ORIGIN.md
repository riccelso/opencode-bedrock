# Origin & Attribution

## Original Project

This repository is a **fork and migration** of [claude-bedrock](https://github.com/iurykrieger/claude-bedrock) by **Iury Krieger**.

- **Original repository:** https://github.com/iurykrieger/claude-bedrock
- **Original author:** [Iury Krieger](https://github.com/iurykrieger)
- **License:** MIT — Copyright (c) 2026 Iury Krieger
- **Original version:** 1.2.1

## What Changed

This fork migrates the project from **Claude Code plugin** format to **[OpenCode](https://opencode.ai)** native skill format. Key changes:

| Aspect | Original (Claude Code) | This Fork (OpenCode) |
|---|---|---|
| Config | `.claude-plugin/plugin.json` | `.opencode/opencode.jsonc` |
| Instructions | `CLAUDE.md` | `AGENTS.md` |
| Skills location | `skills/*/SKILL.md` | `.opencode/skills/*/SKILL.md` |
| Skill invocation | `/bedrock:skillname` | `skill({ name: "skillname" })` |
| MCP references | `mcp__plugin_github_github__*` | `github_*` |
| Skill frontmatter | `user_invocable`, `allowed-tools` | `compatibility: opencode` |

## What Stayed the Same

- All entity definitions (`entities/`)
- All templates (`templates/`)
- All skill logic and content (only paths/references updated)
- Landing page (`site/`, `index.html`, `docs/banner.png`) — kept for upstream sync
- MIT License with original attribution

## Migration Date

- **Migrated:** 2026-05-01
- **OpenCode version target:** v1.14.31+
- **OpenCode docs:** https://opencode.ai/docs/

## Upstream Sync

The `site/` directory, `index.html`, and `docs/banner.png` are kept unchanged to facilitate pulling upstream changes from the original repository. All OpenCode-specific changes are in `.opencode/`, `AGENTS.md`, and `ORIGIN.md`.
