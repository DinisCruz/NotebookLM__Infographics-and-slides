# Claude Context: NotebookLM Infographics & Slides

> **Auto-loaded context for Claude Code / Cowork sessions** — Read this first, then see `.claude/` for detailed guides

---

## 🎯 Quick Start

This repository contains AI-generated infographics and slide decks from Google NotebookLM, enhanced with **Semantic Knowledge Graphs** for machine-readable metadata.

### Key Conventions

| Item | Convention |
|------|------------|
| **Metadata file** | `SEMANTIC-GRAPH.md` (not ~~CONTENT.md~~) |
| **Slide previews** | `slides_mosaic.png` — 4x4 grid thumbnail |
| **Graph database** | Neo4j Cypher export at end of SEMANTIC-GRAPH.md |
| **Visualizations** | Mermaid diagrams (flowchart, mindmap, classDiagram, graph) |

---

## 📁 Repository Structure

```
├── CLAUDE.md                 ← You are here (auto-loaded)
├── .claude/                  ← Detailed workflow guides
│   ├── MAIN.md              ← Full context (read this for deep work)
│   ├── create-readme.md     ← README structure & templates
│   ├── create-semantic-graph.md  ← SEMANTIC-GRAPH.md format
│   ├── create-slide-mosaic.md    ← pdftoppm + montage workflow
│   ├── mermaid-templates.md      ← Copy-paste Mermaid diagrams
│   ├── neo4j-import.md           ← Neo4j sandbox import guide
│   └── create-curated-guide.md   ← Collection page creation
├── linkedin-published/
│   ├── published/            ← Live content with full structure
│   └── to-publish/           ← Queue for upcoming posts
├── by-topic/                 ← Content organized by subject
├── by-collaborator/          ← Content organized by contributor
└── curated-guides/           ← Themed collections
```

---

## 🔧 Common Tasks

### 1. Create/Update Content Folder

For each NotebookLM output folder, create:

1. **README.md** — Navigation, embedded infographic, slide mosaic, SKG section
2. **SEMANTIC-GRAPH.md** — Structured metadata with Mermaid + Cypher
3. **slides_mosaic.png** — Visual preview of all slides

See `.claude/create-readme.md` and `.claude/create-semantic-graph.md` for templates.

### 2. Generate Slide Mosaic

```bash
# Extract slides as images
pdftoppm -png -r 100 "slides.pdf" slide_thumb

# Create 4x4 mosaic
montage slide_thumb-*.png -tile 4x4 -geometry 300x+5+5 -background white slides_mosaic.png

# Cleanup
rm slide_thumb-*.png
```

### 3. SEMANTIC-GRAPH.md Structure

```markdown
# Title

[📖 README](./README.md) · [🖼️ Infographic](./image.jpg) · [📑 Slides](./slides.pdf) · [🏠 Home](../../../../README.md)

> *Semantic Knowledge Graph (SKG) — machine-readable metadata...*

## Summary | Key Concepts | Core Arguments | Key Quotes
## Tags & Search Phrases
## Mermaid Visualizations (flowchart, mindmap, classDiagram, graph)
## Cypher Import (Neo4j)
```

### 4. README.md Structure

```markdown
# Title

[← Back](../README.md) · [LinkedIn Published](../../README.md) · [🏠 Home](../../../../README.md)

> Overview blockquote

| 📄 Source | 🖼️ Infographic | 📑 Slides |
|-----------|----------------|-----------|

## 🖼️ Infographic (embedded image)
## 📑 Slide Deck (mosaic thumbnail, click to open)
## 🧠 Semantic Knowledge Graph (prominent section linking to SEMANTIC-GRAPH.md)
## Key Topics | Contents | Source Information
```

---

## 🧠 Mermaid Diagram Types

Use these in SEMANTIC-GRAPH.md files:

| Type | Use For |
|------|---------|
| `flowchart LR/TB` | Workflows, processes, architectures |
| `mindmap` | Taxonomies, hierarchies |
| `classDiagram` | Ontologies, entity types |
| `graph TB` | Knowledge graphs, relationships |

See `.claude/mermaid-templates.md` for copy-paste templates.

---

## 📊 Neo4j Integration

Every SEMANTIC-GRAPH.md includes Cypher import code. To visualize:

1. Create free sandbox at [sandbox.neo4j.com](https://sandbox.neo4j.com/)
2. Paste Cypher code from SEMANTIC-GRAPH.md
3. Run: `MATCH p=()-[]-() RETURN p`
4. Screenshot and add to SEMANTIC-GRAPH.md

---

## ⚠️ Important Notes

- **URL encoding**: Spaces → `%20`, parentheses → `%28` `%29`
- **webloc files**: macOS bookmark files containing LinkedIn URLs (XML plist format)
- **File detection**: `DD Mon - Title.pdf` = slides, `Title_Underscores.pdf` = source doc
- **Always add navigation** to SEMANTIC-GRAPH.md files (README, Infographic, Slides, Home links)

---

*For detailed guides, see the `.claude/` folder*
