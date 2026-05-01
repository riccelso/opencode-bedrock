<p align="center">
  <img src="docs/banner.png" alt="Bedrock — Knowledge graph visualization" width="600">
</p>

<h1 align="center">Bedrock</h1>

<p align="center">
  <strong>Turn any Obsidian vault into a structured Second Brain with AI agents</strong>
</p>

<p align="center">
  <a href="https://github.com/iurykrieger/claude-bedrock/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License"></a>
  <a href="https://opencode.ai"><img src="https://img.shields.io/badge/opencode--skill-ff6b6b" alt="OpenCode Skill"></a>
  <a href="https://github.com/ricelso/opencode-bedrock"><img src="https://img.shields.io/github/v/tag/ricelso/opencode-bedrock?label=version" alt="Version"></a>
</p>

---

Bedrock is an [OpenCode](https://opencode.ai) skill pack that automates Obsidian vault management through AI-powered skills. It organizes knowledge into **7 entity types** following adapted [Zettelkasten](https://zettelkasten.de/overview/) principles — entity detection, bidirectional linking, ingestion from external sources, deduplication, and sync.

> **Note:** This is a community fork of [iurykrieger/claude-bedrock](https://github.com/iurykrieger/claude-bedrock), migrated from Claude Code plugin to native OpenCode skill format. See [ORIGIN.md](ORIGIN.md) for attribution and migration details.

No build system. No runtime. Just markdown files, AI agents, and your Obsidian vault.

## Features

- **8 AI-powered skills** — setup, ask, teach, preserve, compress, sync, healthcheck, and vaults
- **7 entity types** — actors, people, teams, topics, discussions, projects, and fleeting notes
- **External source ingestion** — Confluence, Google Docs, GitHub repositories, and any file format supported by [docling](https://github.com/docling-project/docling) (DOCX, PPTX, XLSX, PDF, HTML, EPUB, images, and more)
- **Bidirectional wikilinks** — automatic cross-referencing with Obsidian graph view
- **Hierarchical tags** — multi-dimensional filtering (`type/`, `status/`, `domain/`, `scope/`)
- **Zettelkasten structure** — permanent, bridge, index, and fleeting note roles
- **Trunk-based git workflow** — structured commit conventions built in

## Installation

Clone this repo into your OpenCode skills directory:

```bash
git clone https://github.com/ricelso/opencode-bedrock ~/.config/opencode/skills/bedrock
```

Or add to your project's `.opencode/skills/` directory:

```bash
git clone https://github.com/ricelso/opencode-bedrock .opencode/skills/bedrock
```

## Quick Start

After installing, run the setup wizard:

```
skill({ name: "setup" })
```

This will guide you through:

1. **Language selection** — choose the vault content language (default: English)
2. **Dependency check** — verify `graphify` is installed (required)
3. **Vault objective** — pick a preset (engineering team, product management, company wiki, personal second brain, open source project, or custom)
4. **Scaffold** — create directories, templates, config, and connected example entities

The setup creates all entity directories, copies templates, generates a vault-level `AGENTS.md`, and scaffolds example entities with bidirectional wikilinks so you can see the graph in Obsidian immediately.

## Skills

| Skill | Purpose |
|---|---|
| `skill({ name: "setup" })` | Interactive vault initialization and configuration |
| `skill({ name: "ask" })` | Orchestrated vault reader — decomposes questions, searches graph and vault, cross-references entities |
| `skill({ name: "teach" })` | Ingest external sources — extract and create entities |
| `skill({ name: "preserve" })` | Single write point — detect, match, create/update entities with bidirectional links |
| `skill({ name: "compress" })` | Deduplication and vault health — broken links, orphans, stale content |
| `skill({ name: "sync" })` | Re-sync entities with external sources |
| `skill({ name: "healthcheck" })` | Read-only vault health diagnostic — graphify-out integrity, orphans, dangling content, stale entries |
| `skill({ name: "vaults" })` | Manage registered vaults — list, set default, remove |

## Vault Structure

```
your-vault/
├── actors/          # Systems, services, APIs (permanent notes)
├── people/          # Contributors, team members (permanent notes)
├── teams/           # Squads, organizational units (permanent notes)
├── topics/          # Cross-cutting subjects with lifecycle (bridge notes)
├── discussions/     # Meeting notes, conversations (bridge notes)
├── projects/        # Initiatives with scope and deadline (index notes)
└── fleeting/        # Raw ideas, unstructured captures (fleeting notes)
```

Each directory contains a `_template.md` defining the frontmatter schema for that entity type.

## How It Works

Bedrock turns your vault into a living knowledge graph by combining **8 skills** you invoke from OpenCode. You never write entities by hand — skills detect, create, and link them for you, with Obsidian rendering the result as a graph.

### First-time use

1. Open a folder you want to turn into a vault (or an existing Obsidian vault).
2. Run `skill({ name: "setup" })` — answers a few questions and scaffolds directories, templates, and example entities.
3. Open the folder in Obsidian. You'll already see a connected graph.

### Day-to-day loops

- **Capture knowledge from a source** — paste a Confluence page, Google Doc, GitHub repo, remote URL, or any local file into `skill({ name: "teach" })`. Bedrock extracts entities and writes them to the vault with bidirectional links.
- **Ask the vault questions** — use `skill({ name: "ask" })` for anything like *"who owns the billing API?"* or *"what's the status of project X?"*. It searches the graph, follows wikilinks, and answers with citations.
- **Keep sources fresh** — run `skill({ name: "sync" })` to re-pull external sources.
- **Clean up drift** — run `skill({ name: "compress" })` to fix broken backlinks, merge duplicates, and consolidate fragmented concepts. Run `skill({ name: "healthcheck" })` for a read-only report.
- **Manage multiple vaults** — register several vaults with `skill({ name: "vaults" })`; target a specific one with `--vault <name>`.

### What you get in Obsidian

Every entity has YAML frontmatter (type, status, domain, sources), hierarchical tags (`type/actor`, `status/active`, `domain/payments`), and bidirectional wikilinks. The graph view becomes a navigable map of people, systems, teams, topics, and projects — updated automatically as you teach Bedrock new content.

## Dependencies

| Tool | Purpose | Required? |
|---|---|---|
| [graphify](https://github.com/iurykrieger/graphify) | Semantic code extraction and knowledge-graph pipeline | Yes |
| [docling](https://github.com/docling-project/docling) | Universal file → markdown converter for DOCX, PPTX, XLSX, PDF, HTML, EPUB, images | Yes |

Both are auto-installed by `skill({ name: "setup" })`. You can also install manually via `pipx install graphify` / `pipx install docling`.

## Configuration

Configuration is stored in `.bedrock/config.json` inside your vault. Run `skill({ name: "setup" })` again at any time to reconfigure.

## Contributing

Contributions are welcome! Here's how to get started:

1. **Fork** the repository
2. **Clone** your fork into `.opencode/skills/bedrock/`
3. **Create a branch** for your feature or fix
4. **Make your changes** — skills live in `.opencode/skills/`, entity definitions in `entities/`, templates in `templates/`
5. **Test** by running the skill against a test vault
6. **Open a PR** against `main`

### Project Structure

```
opencode-bedrock/
├── .opencode/         # OpenCode configuration
│   ├── opencode.jsonc # MCP servers, permissions, instructions
│   └── skills/        # Skill definitions (SKILL.md per skill)
│       ├── setup/
│       ├── ask/
│       ├── teach/
│       ├── preserve/
│       ├── compress/
│       ├── sync/
│       └── ...
├── entities/          # Entity type definitions
├── templates/         # Frontmatter schema templates
├── docs/              # Documentation assets
├── AGENTS.md          # AI agent instructions
├── ORIGIN.md          # Fork attribution and migration notes
└── README.md
```

## License

[MIT](LICENSE) — Iury Krieger (original), ricelso (fork)
