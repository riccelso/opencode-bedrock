---
tags: [delegation, preserve, write-point, skill-interaction, workflow]
modules: [skills/]
applies_to: [skills]
confidence: inferred
---
# Pattern: Skill Delegation (Single Write Point)

<!-- vibeflow:auto:start -->
## What
All vault writes are centralized through `skill({ name: "preserve" })`. Other skills (teach, sync, compress) detect and analyze, then delegate entity creation/update to preserve. This ensures consistent write logic, bidirectional linking, git commits, and provenance tracking.

## Where
The delegation pattern is used by `skills/teach/SKILL.md`, `skills/sync/SKILL.md`, and `skills/compress/SKILL.md`. The target is always `skills/preserve/SKILL.md`.

## The Pattern
The delegation flow follows a consistent 3-step pattern:

1. **Detection skill** analyzes content and produces a structured entity list:
   ```yaml
   entities:
     - type: actor
       name: "billing-api"
       action: update
       content: "new context..."
       relations:
         actors: ["other-actor"]
       source: "confluence"
       source_url: "https://..."
       source_type: "confluence"
   ```

2. **User confirmation** — the detection skill presents a summary table and waits for explicit approval before delegating.

3. **Delegation** — the detection skill invokes `skill({ name: "preserve" })` via the Skill tool, passing the structured entity list. Preserve handles:
   - Matching with existing vault entities
   - Creating new entities from templates
   - Updating existing entities (merge/append-only rules)
   - Bidirectional wikilink management
   - Populating `sources` field with provenance
   - Git commit + push

The delegation contract:
- `source_url` and `source_type` flow from the detection skill to preserve, which populates the `sources` frontmatter field
- Preserve returns: list of created/updated entities, commit hash, errors/warnings
- The detection skill produces the final report using preserve's return data

## Rules
- `skill({ name: "preserve" })` is the ONLY skill that writes to entity files
- Detection skills (teach, sync, compress) NEVER write entities directly
- The structured entity list format is the contract between skills
- `source_url` and `source_type` MUST be passed through for provenance tracking
- User confirmation is required before delegation
- Preserve handles all git operations (commit, push)

## Examples from this codebase
File: skills/teach/SKILL.md (Fase 4 — Delegar)
```markdown
## Fase 4 — Delegar Entidades ao skill({ name: "preserve" })

Todas as entidades confirmadas pelo usuario (Fase 3) sao delegadas ao `skill({ name: "preserve" })`.
O `skill({ name: "teach" })` NAO cria nem atualiza entidades diretamente — essa responsabilidade e do `skill({ name: "preserve" })`.

### 4.2 Invocar skill({ name: "preserve" })
Use a tool Skill para invocar `skill({ name: "preserve" })` passando a lista estruturada como argumento.
```

File: skills/preserve/SKILL.md (Fase 1.1 — Input estruturado)
```yaml
- type: actor | person | team | topic | discussion | project | fleeting | knowledge-node
  name: "nome canonico da entidade"
  action: create | update
  content: "conteudo a incluir no corpo da entidade"
  relations:
    actors: ["actor-slug-1"]
    people: ["person-slug-1"]
  source: "github | confluence | jira | session | manual | gdoc | csv | graphify"
  metadata: {}
```

File: skills/sync/SKILL.md (delegation pattern)
```markdown
### 3.4 Compilar lista para skill({ name: "preserve" })
Monte uma lista estruturada com TODAS as mudancas detectadas...
### 3.5 Invocar skill({ name: "preserve" })
Use a tool Skill para invocar `skill({ name: "preserve" })` passando a lista.
```
<!-- vibeflow:auto:end -->

## Anti-patterns (if found)
- `skill({ name: "compress" })` modifies entities directly in its Phase 4 (consolidation) rather than fully delegating to preserve. This is an intentional exception because compress needs fine-grained control over claim-level edits, but it breaks the single-write-point pattern.
