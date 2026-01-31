# Architecture Brief: Three-Layer Content Structure

> **sources/** · **topics/** · **platforms/** — A separation-of-concerns architecture for the NotebookLM Infographics & Slides repository

**Date**: 31 January 2026
**Author**: Dinis Cruz
**Status**: Approved for implementation

---

## Executive Summary

This document defines a new three-layer architecture for organizing content in the NotebookLM Infographics & Slides repository. The architecture separates **source documents** (organized by date), **curated content** (organized by topic), and **publication artifacts** (organized by platform), enabling cleaner maintenance, better discoverability, and future extensibility.

---

## Context & Problem Statement

### The Original Structure

The repository began with a flat structure where all content lived in `linkedin-published/` folders, mixing:
- Raw source documents (PDFs, research briefs)
- NotebookLM-generated outputs (infographics, slide decks)
- Claude-generated documentation (README.md, SEMANTIC-GRAPH.md)
- Publication artifacts (LinkedIn webloc files)
- Derived files (slide mosaics)

### Problems with the Original Approach

| Problem | Impact |
|---------|--------|
| **Mixed concerns** | Source files, derived docs, and publication metadata all in one folder |
| **Unclear organization** | Hard to find "what was created on date X" vs "what topics exist" |
| **Duplication risk** | Same content might appear in multiple category folders |
| **Rigid structure** | Adding new publication platforms (Medium, blog) requires restructuring |
| **Navigation complexity** | Breadcrumbs and links had inconsistent depth calculations |
| **Git history pollution** | Binary assets and markdown changes mixed in commits |

### The Insight

Content in this repository has three distinct lifecycles:

1. **Creation** — Source documents are created on a specific date and rarely change
2. **Curation** — Documentation is generated/refined over time, organized by topic
3. **Publication** — Content is published to platforms, with ongoing engagement tracking

These three lifecycles should map to three separate organizational structures.

---

## The Three-Layer Architecture

```
NotebookLM__Infographics-and-slides/
│
├── sources/                              # LAYER 1: Source Documents
│   └── [YYYY]/[MM]/[DD]/[Topic]/
│
├── topics/                               # LAYER 2: Curated Documentation
│   └── [Category]/[Subcategory]/[Topic]/
│
└── platforms/                            # LAYER 3: Publication Artifacts
    └── [platform]/[Category]/[Subcategory]/[Topic]/
```

### Design Principles

| Principle | Implementation |
|-----------|----------------|
| **Separation of concerns** | Each layer has one job |
| **Single source of truth** | Raw assets live in `sources/` only |
| **Date immutability** | Source creation dates don't change |
| **Topic flexibility** | Category hierarchy can evolve independently |
| **Platform extensibility** | New platforms added without restructuring |
| **Relative linking** | All links are relative (GitHub compatible) |

---

## Layer 1: sources/

### Purpose

Store **original/raw content** organized by the date it was created. This is the immutable source of truth for all assets.

### Structure

```
sources/
└── [YYYY]/
    └── [MM]/
        └── [DD]/
            └── [Topic Title]/
                ├── [Source Document].pdf
                ├── [DD] [Mon] - [Infographic Title].jpg
                ├── [DD] [Mon] - [Slides_Title].pdf
                └── deep-research-report.md          (if present)
```

### Example

```
sources/
└── 2026/
    └── 01/
        └── 29/
            ├── Beyond Taste - The New Code Quality Moat in the AI Era/
            │   ├── Beyond_Taste__The_New_Code_Quality_Moat_in_the_AI_Era.pdf
            │   ├── 29 Jan - Code Quality - Design vs Implementation.jpg
            │   └── 29 Jan - Code_is_Cheap_Design_is_Priceless.pdf
            │
            └── GDPR Gemini Workspace Rights/
                ├── GDPR implications of deletion....pdf
                ├── 29 Jan - GDPR Compliance and Google Gemini.jpg
                ├── 29 Jan - Gemini_Workspace_Data_Rights_and_Risk.pdf
                └── deep-research-report.md
```

### What Goes Here

| File Type | Description | Example |
|-----------|-------------|---------|
| **Source PDF** | Original research brief, white paper, or document | `GDPR implications of....pdf` |
| **Infographic** | NotebookLM-generated image | `29 Jan - GDPR Compliance.jpg` |
| **Slide deck** | NotebookLM-generated slides | `29 Jan - Gemini_Workspace.pdf` |
| **Deep research** | Extended research notes | `deep-research-report.md` |

### What Does NOT Go Here

- README.md (goes in `topics/`)
- SEMANTIC-GRAPH.md (goes in `topics/`)
- slides_mosaic.png (goes in `topics/` — it's a transformation)
- webloc files (go in `platforms/`)
- Post text (goes in `platforms/`)

### File Naming Convention

| File Type | Pattern | Example |
|-----------|---------|---------|
| Source document | `[Title with spaces].pdf` | `GDPR implications of deletion.pdf` |
| Infographic | `[DD] [Mon] - [Descriptive Title].jpg` | `29 Jan - GDPR Compliance and Google Gemini.jpg` |
| Slide deck | `[DD] [Mon] - [Slides_Title_With_Underscores].pdf` | `29 Jan - Gemini_Workspace_Data_Rights_and_Risk.pdf` |
| Deep research | `deep-research-report.md` | `deep-research-report.md` |

### Folder Naming Convention

Use the full topic title as the folder name:
- `Beyond Taste - The New Code Quality Moat in the AI Era`
- `GDPR Gemini Workspace Rights`
- `Raising the Quality Bar for Generative AI Code in Production`

---

## Layer 2: topics/

### Purpose

Store **curated/derived documentation** organized by a hierarchical topic taxonomy. This is where Claude-generated content lives.

### Structure

```
topics/
└── [Category]/
    └── [Subcategory]/              (as deep as makes sense)
        └── [Topic]/
            ├── README.md
            ├── SEMANTIC-GRAPH.md
            └── slides_mosaic.png
```

### Example

```
topics/
├── GenAI/
│   ├── Claude Skills/
│   │   └── Production-Grade AI Skill Development/
│   │       ├── README.md
│   │       ├── SEMANTIC-GRAPH.md
│   │       └── slides_mosaic.png
│   └── Code Quality/
│       └── Beyond Taste - AI Era Code Quality/
│           ├── README.md
│           ├── SEMANTIC-GRAPH.md
│           └── slides_mosaic.png
│
├── Cybersecurity/
│   └── GDPR/
│       └── Gemini Workspace Rights/
│           ├── README.md
│           ├── SEMANTIC-GRAPH.md
│           └── slides_mosaic.png
│
└── Graphs/
    ├── G3 Architecture/
    │   └── ...
    └── Static Graph Visualization/
        └── ...
```

### What Goes Here

| File Type | Description | Links To |
|-----------|-------------|----------|
| **README.md** | Human navigation, embedded images, quick links | Assets in `sources/`, posts in `platforms/` |
| **SEMANTIC-GRAPH.md** | Machine-readable SKG with Mermaid + Cypher | Assets in `sources/` |
| **slides_mosaic.png** | 4x4 grid preview of slides | Generated from slides in `sources/` |
| **Future files** | Additional derived documentation | TBD |

### Category Hierarchy Guidelines

Design the hierarchy to:
- **Avoid massive folders** — Subcategorize when a category exceeds ~15-20 items
- **Follow natural ontology** — Group by domain concepts, not arbitrary buckets
- **Enable discovery** — A user browsing should understand the landscape
- **Stay consistent** — Use the same taxonomy in `platforms/`

Example category structure:
```
topics/
├── GenAI/
│   ├── Claude Skills/
│   ├── Code Quality/
│   ├── LLM Workflows/
│   └── Prompt Engineering/
├── Cybersecurity/
│   ├── GDPR/
│   ├── Privacy Architecture/
│   └── Data Protection/
├── Graphs/
│   ├── Knowledge Graphs/
│   ├── Neo4j/
│   └── Visualization/
├── AWS/
│   ├── Marketplace/
│   ├── Well-Architected/
│   └── Serverless/
└── Development/
    ├── Testing/
    ├── Architecture/
    └── Best Practices/
```

### README.md Structure

```markdown
[🏠 Home](../../../README.md) / [Category](../../README.md) / [Subcategory](../README.md) / **Topic**

---

# Topic Title

One-line description of what this content covers.

| 📄 Source | 🖼️ Infographic | 📑 Slides |
|-----------|----------------|-----------|
| [Document](../../../sources/YYYY/MM/DD/Topic/source.pdf) | [View](../../../sources/YYYY/MM/DD/Topic/image.jpg) | [Deck](../../../sources/YYYY/MM/DD/Topic/slides.pdf) |

> *Generated with [Google NotebookLM](https://notebooklm.google.com/)*

---

## 🖼️ Infographic

![Alt text](../../../sources/YYYY/MM/DD/Topic/image.jpg)

---

## 📑 Slide Deck (X slides)

[![All Slides](./slides_mosaic.png)](../../../sources/YYYY/MM/DD/Topic/slides.pdf)

*Click image to open* · [⬇️ Download PDF](raw-github-url)

---

## 🧠 Semantic Knowledge Graph

[📖 View SEMANTIC-GRAPH.md](./SEMANTIC-GRAPH.md)

---

## 📢 Published

- [LinkedIn Posts](../../../platforms/linkedin/Category/Subcategory/Topic/)

---

## Key Topics

- Topic 1
- Topic 2
```

### SEMANTIC-GRAPH.md Structure

See existing `.claude/create-semantic-graph.md` for full specification. Key addition:

```markdown
[📖 README](./README.md) · [🖼️ Infographic](../../../sources/YYYY/MM/DD/Topic/image.jpg) · [📑 Slides](../../../sources/YYYY/MM/DD/Topic/slides.pdf) · [🏠 Home](../../../README.md)
```

---

## Layer 3: platforms/

### Purpose

Store **publication artifacts** organized by platform and mirroring the topic taxonomy. This tracks what has been published and captures ongoing engagement.

### Structure

```
platforms/
└── [platform]/
    └── [Category]/
        └── [Subcategory]/
            └── [Topic]/                    ← FOLDER (not file)
                ├── post.md                 ← post text
                ├── [LinkedIn post].webloc  ← link to live post
                ├── analytics.md            ← future: engagement metrics
                ├── comments.md             ← future: notable comments
                └── follow-ups.md           ← future: ideas sparked
```

### Example

```
platforms/
└── linkedin/
    ├── GenAI/
    │   └── Code Quality/
    │       └── Beyond Taste - AI Era Code Quality/
    │           ├── post.md
    │           └── LinkedIn post with slides.webloc
    │
    └── Cybersecurity/
        └── GDPR/
            └── Gemini Workspace Rights/
                ├── post.md
                ├── LinkedIn post with Slides.webloc
                ├── LinkedIn post with research.webloc
                └── LinkedIn post with question.webloc
```

### What Goes Here

| File Type | Description | When Created |
|-----------|-------------|--------------|
| **post.md** | The actual text posted to the platform | After publishing |
| **\*.webloc** | macOS link files pointing to live posts | After publishing |
| **analytics.md** | Engagement metrics (likes, comments, shares) | Ongoing |
| **comments.md** | Notable comments worth preserving | Ongoing |
| **follow-ups.md** | Ideas sparked by engagement | Ongoing |
| **SEMANTIC-GRAPH.md** | Graph of the post's impact/reach | Future |

### Why Folders (Not Files)

Using a folder per topic (instead of a single file) enables:
- Multiple webloc files (same content, multiple posts)
- Separate analytics tracking
- Comment preservation
- Follow-up idea capture
- Future file types we haven't anticipated

### post.md Structure

```markdown
# [Topic Title]

**Platform**: LinkedIn
**Published**: YYYY-MM-DD
**URL**: https://linkedin.com/posts/diniscruz_...

---

## Post Text

[The actual text of the LinkedIn post, including hashtags]

---

## Related

- [📖 README](../../../topics/Category/Subcategory/Topic/README.md)
- [🖼️ Infographic](../../../sources/YYYY/MM/DD/Topic/image.jpg)
- [📑 Slides](../../../sources/YYYY/MM/DD/Topic/slides.pdf)
- [🧠 SEMANTIC-GRAPH](../../../topics/Category/Subcategory/Topic/SEMANTIC-GRAPH.md)
```

### Taxonomy Consistency

The category/subcategory hierarchy in `platforms/` **must mirror** `topics/`:

```
topics/Cybersecurity/GDPR/Gemini Workspace Rights/
                    ↓ same path
platforms/linkedin/Cybersecurity/GDPR/Gemini Workspace Rights/
```

This ensures:
- Easy cross-referencing
- Consistent mental model
- Predictable link paths

### Supported Platforms (Current & Future)

| Platform | Folder | Status |
|----------|--------|--------|
| LinkedIn | `platforms/linkedin/` | Active |
| Medium | `platforms/medium/` | Future |
| Blog | `platforms/blog/` | Future |
| Twitter/X | `platforms/twitter/` | Future |

---

## Cross-Layer Navigation

### Link Patterns

All links use **relative paths** (the only format that works reliably on GitHub).

#### From topics/ to sources/

```markdown
![Infographic](../../../sources/2026/01/29/Topic%20Name/29%20Jan%20-%20Image.jpg)
```

Calculate depth:
- `topics/Category/Subcategory/Topic/README.md` → 4 levels up to root
- Then down into `sources/YYYY/MM/DD/Topic/`

#### From topics/ to platforms/

```markdown
[LinkedIn Posts](../../../platforms/linkedin/Category/Subcategory/Topic/)
```

#### From platforms/ to topics/

```markdown
[README](../../../topics/Category/Subcategory/Topic/README.md)
```

#### From platforms/ to sources/

```markdown
[Infographic](../../../sources/2026/01/29/Topic/image.jpg)
```

### URL Encoding

Always encode special characters in paths:

| Character | Encoded |
|-----------|---------|
| Space | `%20` |
| `(` | `%28` |
| `)` | `%29` |
| `'` | `%27` |
| `–` (en-dash) | `%E2%80%93` |

---

## Workflow: Processing New Content

### Step 1: Content Arrives in sources/

New content is added to `sources/YYYY/MM/DD/[Topic]/` with:
- Source document PDF
- NotebookLM infographic
- NotebookLM slides
- deep-research-report.md (if present)

### Step 2: Create topics/ Documentation

1. **Determine category placement** — Where in the topic hierarchy does this belong?
2. **Create folder** — `topics/[Category]/[Subcategory]/[Topic]/`
3. **Generate slides_mosaic.png** — Using pdftoppm + montage
4. **Create README.md** — Following template, linking to `sources/`
5. **Create SEMANTIC-GRAPH.md** — Following existing guide

### Step 3: After Publication (platforms/)

When content is published to LinkedIn:
1. **Create folder** — `platforms/linkedin/[Category]/[Subcategory]/[Topic]/`
2. **Move/copy webloc files** — From wherever they were captured
3. **Create post.md** — With post text and metadata
4. **Update topics/README.md** — Add link to platforms/ folder

### Step 4: Ongoing (platforms/)

Over time, add:
- analytics.md with engagement data
- comments.md with notable discussions
- follow-ups.md with ideas

---

## File Generation Commands

### Slide Mosaic

```bash
cd "sources/2026/01/29/[Topic]/"
pdftoppm -png -r 100 "29 Jan - Slides.pdf" slide_thumb
montage slide_thumb-*.png -tile 4x4 -geometry 300x+5+5 -background white slides_mosaic.png
rm slide_thumb-*.png
mv slides_mosaic.png "../../../../../topics/[Category]/[Subcategory]/[Topic]/"
```

### Extract webloc URL

```bash
grep -o 'https://[^<]*' "LinkedIn post.webloc"
```

---

## Summary

| Layer | Location | Organized By | Contains | Lifecycle |
|-------|----------|--------------|----------|-----------|
| **sources/** | `sources/YYYY/MM/DD/Topic/` | Creation date | Raw assets | Immutable |
| **topics/** | `topics/Category/.../Topic/` | Subject hierarchy | Curated docs | Evolving |
| **platforms/** | `platforms/platform/Category/.../Topic/` | Platform + subject | Publication artifacts | Ongoing |

### Key Benefits

1. **Clear separation** — Know exactly where to find/put any file type
2. **Immutable sources** — Date-organized assets never need to move
3. **Flexible topics** — Category hierarchy can evolve without touching sources
4. **Extensible platforms** — Add new platforms without restructuring
5. **Consistent linking** — Predictable relative paths across layers
6. **Future-proof** — Room for analytics, comments, follow-ups, and more

---

## Appendix: Migration Notes

*Migration of existing content to this new structure will be covered in a separate document.*

---

## Appendix: Related Documentation

- `.claude/create-readme.md` — README.md generation guide
- `.claude/create-semantic-graph.md` — SEMANTIC-GRAPH.md generation guide
- `.claude/create-slide-mosaic.md` — Slide mosaic creation guide
- `.claude/mermaid-templates.md` — Mermaid diagram templates
- `.claude/review-checklists.md` — Validation checklists

*These guides will need updating to reflect the new three-layer architecture.*
