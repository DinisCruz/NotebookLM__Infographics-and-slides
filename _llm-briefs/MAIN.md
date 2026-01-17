# LLM Brief: NotebookLM Infographics & Slides Repository

> **Start here** — This is the main context brief for working with this repository in Cowork sessions.

---

## What This Repository Is

This is **Dinis Cruz's content library** — a collection of AI-generated infographics, slide decks, and supporting documentation covering software development, cybersecurity, GenAI, and technical leadership.

### Content Pipeline

```
Source Document (PDF/MD) → NotebookLM → Infographic (PNG/JPG) + Slide Deck (PDF)
```

All visual content is generated using **Google NotebookLM** from source documents (white papers, research briefs, technical documentation).

### Repository Statistics

| Metric | Count |
|--------|-------|
| Total Visual Assets | ~271 |
| PDF Documents | ~171 |
| PNG Infographics | ~100 |
| Organized Folders | ~126 |
| Repository Size | ~2.1 GB |

---

## Folder Structure

```
NotebookLM__Infographics-and-slides/
├── _llm-briefs/           # ← YOU ARE HERE - Instructions for LLMs
│   ├── MAIN.md            # This file - start here
│   ├── create-readme.md   # How to create README.md files
│   └── create-content.md  # How to create CONTENT.md files (SKG serialization)
│
├── _for_linkedin/         # LinkedIn publishing pipeline
│   ├── published/         # Already shared on LinkedIn
│   └── to-publish/        # Queue for future posts
│
├── by-topic/              # Primary content organized by subject
│   ├── GenAI development/
│   ├── osbot-utils/
│   ├── Graphs/
│   ├── Cyber security.../
│   └── ...
│
└── by-collaborator/       # Content organized by contributor
```

---

## The Two Documentation Files

Each content folder should have two markdown files:

### 1. README.md — Navigation & Discovery

**Purpose**: Help humans browse and find content

**Contains**:
- Breadcrumb navigation (🏠 Home → parent → **current**)
- Quick links table: 📄 Source | 🖼️ Infographic | 📊 Slides
- Embedded infographic image
- NotebookLM attribution
- Optional key themes

**Example breadcrumb**:
```markdown
[🏠 Home](../../../../README.md) / [For LinkedIn](../../../) / [Published](../../) / [Development](../) / **PDD**
```

📖 **Full instructions**: [create-readme.md](./create-readme.md)

---

### 2. CONTENT.md — Semantic Knowledge Graph (SKG) Serialization

**Purpose**: Structured metadata for search, discovery, and graph database integration

**This IS a Semantic Knowledge Graph** serialized in markdown format — the same data you'd store in Neo4j, MGraph-DB, or OSBot-Utils Semantic Graphs.

**Structure** (human-readable first, graph at bottom):

```markdown
# Title

> *Semantic Knowledge Graph (SKG) - markdown serialization...*

## Summary          ← 2-4 sentences
## Key Concepts     ← 4-6 bolded terms with explanations
## Core Arguments   ← 4-6 numbered logical points
## Key Quotes       ← 2-4 memorable quotes from source
## Tags             ← 10-15 lowercase tags in backticks
## Search Phrases   ← 6-10 natural language queries
## Metadata         ← Table: type, domain, author, date, license
## Related Content  ← Links using relationship predicates

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology (valid node types & predicates)
### Taxonomy (category hierarchy)
### Graph
#### Nodes (entities)
#### Edges (relationships)

</details>
```

**Key principles**:
- Human-readable content FIRST
- SKG structure in collapsible `<details>` section at bottom
- Use **readable node IDs**: `pdd`, `green_bar` (not `n1`, `n2`)
- 8-15 nodes maximum per content piece
- Predicates need inverses for bidirectional traversal

📖 **Full instructions**: [create-content.md](./create-content.md)

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **SKG** | Semantic Knowledge Graph — nodes + edges + ontology + taxonomy |
| **Ontology** | Schema defining valid node types and predicates |
| **Taxonomy** | Hierarchical category structure |
| **Nodes** | Entities/concepts (map to `(:Type {id, name})` in Neo4j) |
| **Edges** | Relationships (map to `-[:PREDICATE]->` in Neo4j) |
| **Predicates** | Relationship types (e.g., `addresses`, `includes`, `produces`) |
| **Projection** | Human-readable view of graph data (Summary, Key Concepts, etc.) |

---

## Common Tasks

### Creating documentation for a new content folder

1. **List folder contents** to identify: source PDF, infographic(s), slide deck
2. **Read the source PDF** to understand the content
3. **Create README.md** following [create-readme.md](./create-readme.md)
4. **Create CONTENT.md** following [create-content.md](./create-content.md)

### Updating navigation when folders move

- Recalculate relative paths in breadcrumbs
- Update parent README folder listings
- URL-encode special characters (spaces → `%20`, parentheses → `%28`/`%29`)

### Adding content to LinkedIn pipeline

- New content goes in `_for_linkedin/to-publish/[category]/`
- After publishing, move to `_for_linkedin/published/[category]/`

---

## URL Encoding Reference

| Character | Encoded |
|-----------|---------|
| Space | `%20` |
| `(` | `%28` |
| `)` | `%29` |
| `'` | `%27` |
| `£` | `%C2%A3` |
| `–` (en-dash) | `%E2%80%91` |

---

## Author & License

- **Author**: Dinis Cruz ([@DinisCruz](https://github.com/DinisCruz), dinis.cruz@owasp.org)
- **License**: CC BY 4.0 — Share and adapt with attribution

---

## Quick Start Checklist

When starting a new Cowork session on this repo:

- [ ] Read this brief (`_llm-briefs/MAIN.md`)
- [ ] Understand current task (new content? navigation fix? bulk updates?)
- [ ] Check staging area (`_for_linkedin/to-publish/`) for pending work
- [ ] Reference specific briefs as needed (`create-readme.md`, `create-content.md`)
- [ ] Maintain human-first design in all CONTENT.md files
- [ ] Use readable IDs in SKG structures
- [ ] URL-encode all special characters in links
