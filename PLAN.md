---
status: in-progress
phase: 3
updated: 2026-05-01
---

# Implementation Plan: opencode-bedrock Migration Cleanup

## Goal
Resolve all remaining validation issues from Round 3: replace claude-in-chrome MCP with Playwright, fix concepts/ scan gaps, and finalize the opencode-bedrock migration.

## Context & Decisions

| Decision | Rationale | Source |
|----------|-----------|--------|
| Replace `@anthropic-ai/claude-in-chrome-mcp` with `@playwright/mcp` | Claude-in-Chrome is a Claude Code extension; Playwright is the opencode standard | Round 3 Stale Refs validator |
| Playwright in project config (not global) | Other users of this repo need browser MCP declared in `opencode.jsonc` | Round 3 Runtime Compat validator |
| Add `concepts/` to scan globs in 3 skills | Upstream bug — concept entity defined in entities/ but never added to scan lists in preserve/ask/sync | Round 3 Cross-Ref validator |
| `_template_node.md` not fixed | By design — template lives in user vaults (graphify-created), not plugin-distributed | `.vibeflow/specs/rename-knowledge-node-to-code-part-1.md` |
| Playwright tool API: navigate → evaluate → snapshot | Single-page model, no tab management needed | Round 3 Runtime Compat validator |

## Completed Work

### Phase 1: Repository Restructuring [COMPLETE]
- [x] 1.1 Remove `.claude-plugin/` directory (plugin.json, marketplace.json)
- [x] 1.2 Remove root `CLAUDE.md`
- [x] 1.3 Create `.opencode/` directory with `.gitignore`
- [x] 1.4 Move `skills/` → `.opencode/skills/` (10 skill dirs)
- [x] 1.5 Create `AGENTS.md` (migrated from CLAUDE.md)
- [x] 1.6 Create `ORIGIN.md` (attribution + migration map)
- [x] 1.7 Create `VERSION` file (`2.0.0`)

### Phase 2: Configuration [COMPLETE]
- [x] 2.1 Create `.opencode/opencode.jsonc` with MCP servers, permissions, instructions, compaction
- [x] 2.2 Remove `skills/` from root

### Phase 3: Skills Migration [COMPLETE]
- [x] 3.1 Update all 10 SKILL.md frontmatters (remove `user_invocable`/`allowed-tools`, add `compatibility: opencode`)
- [x] 3.2 Replace MCP tool refs (`mcp__plugin_github_github__*` → `github_*`, etc.)
- [x] 3.3 Replace skill invocations (`/bedrock:X` → `skill({ name: "X" })`)
- [x] 3.4 Fix graphify-out/ paths, CLAUDE.md refs, H1 titles

### Phase 4: Documentation + CI/CD [COMPLETE]
- [x] 4.1 Rewrite README.md for opencode
- [x] 4.2 Batch update ~80 `.vibeflow/` files
- [x] 4.3 Rewrite `.github/workflows/release.yml` (VERSION file)
- [x] 4.4 Create `VERSION` (2.0.0)

### Phase 5: Validation Round 1 + Fixes [COMPLETE]
- [x] 5.1 5-agent validation (config, skills, docs, structure, schema)
- [x] 5.2 Fix `.opencode/.gitignore` (add `!opencode.jsonc`, `!skills/`)
- [x] 5.3 Fix `healthcheck/SKILL.md` (.claude-plugin → .opencode refs)
- [x] 5.4 Fix `confluence-to-markdown/SKILL.md` (Claude Code → opencode)
- [x] 5.5 Fix `.vibeflow/patterns/skill-architecture.md` (allowed-tools → compatibility)
- [x] 5.6 Commit + push (`606006f`)

### Phase 6: Validation Round 2 + Fixes [COMPLETE]
- [x] 6.1 6-agent validation (config, skills-deep, stale-refs, git, crossrefs, runtime)
- [x] 6.2 Fix `setup/SKILL.md` graphify paths (`~/.claude/skills/` → `~/.config/opencode/skill/`)
- [x] 6.3 Fix `setup/SKILL.md` concepts/ missing from mkdir + template copy
- [x] 6.4 Fix `confluence-to-markdown/SKILL.md` ToolSearch + select: syntax
- [x] 6.5 Fix `gdoc-to-markdown/SKILL.md` ToolSearch + select: syntax
- [x] 6.6 Fix `.vibeflow/conventions.md` + `index.md` (plugin.json → opencode.jsonc)

### Phase 7: Validation Round 3 [COMPLETE]
- [x] 7.1 6-agent validation round
- [x] 7.2 Results: 0 config issues, 0 skill frontmatter issues, 1 stale ref (claude-in-chrome-mcp in config), 2 cross-ref gaps (concepts/ scans), 1 runtime issue (browser Layer 3 API)

## Remaining Work

### Phase 8: Browser MCP Migration [IN PROGRESS]
- [ ] **8.1 Replace `@anthropic-ai/claude-in-chrome-mcp` with `@playwright/mcp` in `opencode.jsonc`** ← CURRENT
- [ ] 8.2 Rewrite Layer 3 in `confluence-to-markdown/SKILL.md` for Playwright API
  - Replace `browser_tabs_context_mcp`, `browser_tabs_create_mcp` → `playwright_browser_navigate`
  - Replace `browser_navigate` → `playwright_browser_navigate`
  - Replace `browser_javascript_tool` → `playwright_browser_evaluate`
- [ ] 8.3 Rewrite Layer 3 in `gdoc-to-markdown/SKILL.md` for Playwright API (same pattern)
- [ ] 8.4 Update `setup/SKILL.md` dependency table: browser MCP → Playwright

### Phase 9: Entity Scan Fix [PENDING]
- [ ] 9.1 Add `concepts/` to `preserve/SKILL.md` entity collection scan globs (~line 480-489)
- [ ] 9.2 Add `concepts/` to `ask/SKILL.md` search + wikilink resolution (~line 177-179, 230-231)
- [ ] 9.3 Add `concepts/` to `sync/SKILL.md` entity catalog (~line 237-243)

### Phase 10: Commit + Final Validation [PENDING]
- [ ] 10.1 Commit all changes (Phase 6 uncommitted files + Phase 8 + Phase 9)
- [ ] 10.2 Run final 6-agent validation round
- [ ] 10.3 Confirm CLEAN/VALID verdict across all validators
- [ ] 10.4 Push to main

## Notes
- 2026-05-01: 5 files from Phase 6 fixes still uncommitted (3 SKILL.md + 2 .vibeflow/)
- 2026-05-01: Playwright MCP tools: `playwright_browser_navigate`, `playwright_browser_evaluate`, `playwright_browser_snapshot`, `playwright_browser_click`, `playwright_browser_take_screenshot`
- 2026-05-01: Key difference from claude-in-chrome: no tab management, single-page model
- 2026-05-01: Browser Layer 3 is last-resort fallback (Layers 1-2: MCP direct + API still work)
- 2026-05-01: `concepts/` confirmed as upstream bug — compress and healthcheck DO include concepts/, but preserve/ask/sync omit it
- 2026-05-01: `_template_node.md` excluded by design (graphify vault-side template)
