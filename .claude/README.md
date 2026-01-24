[🏠 Home](../README.md) / **Claude Workflow Guides**

---

# Claude Workflow Guides

> Instructions for Claude Code / Cowork sessions on how to work with this repository

This folder contains detailed guides for maintaining and enhancing the NotebookLM content library. Claude automatically loads `../CLAUDE.md` at session start for quick context.

---

## 🚀 Start Here

| Guide | Purpose |
|-------|---------|
| **[MAIN.md](./MAIN.md)** | **Start here** — Full context for new sessions |

---

## 📂 Content Creation Guides

| Guide | Purpose |
|-------|---------|
| [create-readme.md](./create-readme.md) | Create README.md files with navigation, mosaics, SKG sections |
| [create-semantic-graph.md](./create-semantic-graph.md) | Create SEMANTIC-GRAPH.md files with Mermaid + Cypher |
| [create-slide-mosaic.md](./create-slide-mosaic.md) | Generate 4x4 slide preview grids using pdftoppm + montage |
| [create-curated-guide.md](./create-curated-guide.md) | Create themed collection pages linking related content |

---

## 🔧 Reference Guides

| Guide | Purpose |
|-------|---------|
| [mermaid-templates.md](./mermaid-templates.md) | Copy-paste Mermaid diagram templates (flowchart, mindmap, classDiagram, graph) |
| [neo4j-import.md](./neo4j-import.md) | Import Cypher into Neo4j sandbox and create visualizations |

---

## Usage

**New session?**
- Claude auto-loads `CLAUDE.md` at repo root
- For deep work, read [MAIN.md](./MAIN.md)

**Specific task?**
- Creating/updating navigation → `create-readme.md`
- Creating semantic graphs → `create-semantic-graph.md`
- Creating slide previews → `create-slide-mosaic.md`
- Creating collections → `create-curated-guide.md`
- Mermaid diagram help → `mermaid-templates.md`
- Neo4j visualization → `neo4j-import.md`

---

## File Naming Convention

| Old Name | New Name | Reason |
|----------|----------|--------|
| `CONTENT.md` | `SEMANTIC-GRAPH.md` | Clearer purpose |
| `create-content.md` | `create-semantic-graph.md` | Matches file it creates |

---

## Key Conventions (January 2026)

| Item | Convention |
|------|------------|
| Metadata file | `SEMANTIC-GRAPH.md` |
| Slide preview | `slides_mosaic.png` |
| Graph export | Neo4j Cypher at end of SEMANTIC-GRAPH.md |
| Visualizations | Mermaid diagrams (GitHub-rendered) |
| Navigation | Links at top of SEMANTIC-GRAPH.md |
