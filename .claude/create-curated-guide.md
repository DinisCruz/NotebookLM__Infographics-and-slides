# LLM Brief: Creating Curated Guides

> Instructions for creating curated collection pages that link related SEMANTIC-GRAPH.md files

---

## Purpose

Curated guides are themed collections that group related content across the repository. They:

1. **Enable discovery** — Help users find related content on a topic
2. **Provide narrative** — Tell a story across multiple pieces
3. **Showcase depth** — Demonstrate expertise in an area
4. **Facilitate sharing** — Single link to share a collection

---

## Location

All curated guides live in:

```
curated-guides/
├── README.md                    # Index of all guides
├── from-idea-to-startup/        # Example guide
│   ├── README.md
│   └── CONTENT.md (optional)
└── semantic-knowledge-graphs/   # Example guide
    └── README.md
```

---

## Guide Structure

### Folder Structure

```
curated-guides/[guide-name]/
├── README.md        # Required: Main guide page
└── CONTENT.md       # Optional: Deep dive content
```

### README.md Template

```markdown
[🏠 Home](../../README.md) / [Curated Guides](../README.md) / **Guide Name**

---

# Guide Title

> **One-line description** — What this collection covers and why it matters

Brief introduction paragraph (2-3 sentences) explaining the theme and what readers will learn.

---

## 📊 Collection Overview

| # | Topic Area | Document | Key Focus |
|---|------------|----------|-----------|
| 1 | [Category] | [Title](../../path/to/folder/) | Brief description |
| 2 | [Category] | [Title](../../path/to/folder/) | Brief description |
| 3 | [Category] | [Title](../../path/to/folder/) | Brief description |

---

## 🔗 Direct Links to SEMANTIC-GRAPH.md Files

| Document | SEMANTIC-GRAPH.md | Neo4j Ready |
|----------|-------------------|-------------|
| Title 1 | [View Graph](../../path/to/SEMANTIC-GRAPH.md) | ✅ |
| Title 2 | [View Graph](../../path/to/SEMANTIC-GRAPH.md) | ✅ |

---

## 🧠 What's in Each File?

Each SEMANTIC-GRAPH.md file contains:

```
├── Summary & Key Concepts
├── Core Arguments & Quotes
├── Tags & Search Phrases
├── Mermaid Visualizations
│   ├── Workflow/Architecture Diagrams
│   ├── Ontology (classDiagram)
│   ├── Taxonomy (mindmap)
│   └── Knowledge Graph (graph TB)
└── Neo4j Cypher Import
```

---

## 🎯 Use Cases

- **Use case 1**: Description
- **Use case 2**: Description
- **Use case 3**: Description

---

## 📈 Statistics

| Metric | Count |
|--------|-------|
| Documents included | X |
| Mermaid diagrams | ~Y |
| Topic areas covered | Z |

---

*Created: [Month Year]*
```

---

## Naming Conventions

### Folder Names

Use lowercase with hyphens:
- ✅ `semantic-knowledge-graphs`
- ✅ `from-idea-to-startup`
- ❌ `Semantic Knowledge Graphs`
- ❌ `semantic_knowledge_graphs`

### File Names

- `README.md` — Required, main guide content
- `CONTENT.md` — Optional, for extended content

---

## Linking to Content

### Relative Paths

From `curated-guides/[guide]/README.md` to content:

```markdown
<!-- To _for_linkedin/published/Category/Folder/ -->
[Title](../../_for_linkedin/published/Category/Folder/)

<!-- To _for_linkedin/published/Category/Folder/SEMANTIC-GRAPH.md -->
[View Graph](../../_for_linkedin/published/Category/Folder/SEMANTIC-GRAPH.md)
```

### URL Encoding

Remember to encode special characters:
- Spaces → `%20`
- Parentheses → `%28` `%29`

```markdown
<!-- Folder with spaces and parentheses -->
[Link](../../_for_linkedin/published/Dev%20Briefs/AWS%20Well-Architected%20%28Review%29/)
```

---

## When to Create a Curated Guide

Create a guide when you have:

1. **3+ related pieces** on the same theme
2. **Cross-cutting topic** that spans categories
3. **Learning path** that should be consumed in order
4. **Showcase collection** for sharing externally

---

## Guide Ideas

| Theme | Potential Content |
|-------|-------------------|
| Testing Methodologies | PDD, TDD alternatives, agentic testing |
| Graph Technologies | Knowledge graphs, Neo4j, MGraph-AI |
| GenAI Development | Vibe coding, LLM workflows, context engineering |
| AWS Architectures | Well-Architected, deployment modes, serverless |
| Startup Journey | From idea to product, validation, scaling |

---

## Updating Parent README

After creating a guide, add it to `curated-guides/README.md`:

```markdown
## Available Guides

| Guide | Description |
|-------|-------------|
| [From Idea to Startup](./from-idea-to-startup/) | Complete journey... |
| [Semantic Knowledge Graphs](./semantic-knowledge-graphs/) | Enhanced SEMANTIC-GRAPH.md files... |
| [Your New Guide](./your-new-guide/) | Description of new guide |
```

---

## Example: Semantic Knowledge Graphs Guide

```markdown
[🏠 Home](../../README.md) / [Curated Guides](../README.md) / **Semantic Knowledge Graphs**

---

# Semantic Knowledge Graphs Collection

> **First batch of enhanced SEMANTIC-GRAPH.md files** — Machine-readable metadata with Mermaid visualizations and Neo4j import capability

This collection showcases the new Semantic Knowledge Graph format...

---

## 📊 Collection Overview

| # | Topic Area | Document | Key Focus |
|---|------------|----------|-----------|
| 1 | 3rd Party Content | [Unbaking the Cake](../../_for_linkedin/published/3rd%20Party%20Content/Unbaking%20the%20Cake%20-%20Capturing%20Data%20Before%20Entropy/) | Data entropy, native semantic capture |
| 2 | Wardley Maps | [Software Development Evolution](../../_for_linkedin/published/Wardley%20Maps/...) | Strategic evolution |

...
```

---

## Checklist

Before finalizing a curated guide:

- [ ] Folder created in `curated-guides/`
- [ ] README.md follows template structure
- [ ] All links are relative and working
- [ ] URLs are properly encoded
- [ ] Collection overview table is complete
- [ ] Direct SEMANTIC-GRAPH.md links provided
- [ ] Parent `curated-guides/README.md` updated
- [ ] Statistics are accurate
- [ ] Description is clear and compelling
