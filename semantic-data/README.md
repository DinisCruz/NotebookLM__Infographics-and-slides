# semantic-data/

> Three-layer content architecture: **sources/** · **topics/** · **platforms/**

This folder contains the refactored content structure implementing separation of concerns for the NotebookLM Infographics & Slides repository.

---

## Structure

```
semantic-data/
├── ONTOLOGY.md              # Schema: What types of things exist
├── TAXONOMY.md              # Hierarchy: Category tree
├── README.md                # This file
│
├── sources/                 # LAYER 1: Raw assets by date
│   └── YYYY/MM/DD/Topic/
│       ├── Source document (PDF/MD)
│       ├── Infographic (JPG/PNG)
│       └── Slides (PDF)
│
├── topics/                  # LAYER 2: Curated docs by category
│   └── Category/Subcategory/Topic/
│       ├── README.md
│       ├── SEMANTIC-GRAPH.md
│       └── slides_mosaic.png
│
└── platforms/               # LAYER 3: Publication artifacts
    └── linkedin/Category/Subcategory/Topic/
        ├── post.md
        └── *.webloc
```

---

## Current Content

### Topics (3)

| Category | Subcategory | Topic | Published |
|----------|-------------|-------|-----------|
| Meta | Repository Structure | [Three-Layer Content Architecture](./topics/Meta/Repository%20Structure/Three-Layer-Content-Architecture/) | ✅ LinkedIn |
| Meta | Repository Structure | [Repository Ontology and Taxonomy](./topics/Meta/Repository%20Structure/Repository-Ontology-and-Taxonomy/) | ✅ LinkedIn |
| Development | Issue Tracking | [Graph-Based Issue Tracking](./topics/Development/Issue%20Tracking/Graph-Based-Issue-Tracking/) | ✅ LinkedIn |

---

## Quick Links

- [ONTOLOGY.md](./ONTOLOGY.md) — Schema definition
- [TAXONOMY.md](./TAXONOMY.md) — Category hierarchy
- [sources/2026/01/31/](./sources/2026/01/31/) — Jan 31 source files
- [topics/](./topics/) — All topics by category
- [platforms/linkedin/](./platforms/linkedin/) — LinkedIn publications

---

## Documentation

- [Three-Layer Architecture Brief](./sources/2026/01/31/Three-Layer-Content-Architecture/31-jan__brief__notebooklm-repo__exploring-new-content-stucture.md)
- [Ontology & Taxonomy Brief](./sources/2026/01/31/Repository-Ontology-and-Taxonomy/31-jan__brief__repo-level-ontology-and-taxonomy.md)
