# Three-Layer Content Architecture

[📖 README](./README.md) · [🖼️ Infographic](../../../../sources/2026/01/31/Three-Layer-Content-Architecture/31%20Jan-%20Three-Layer%20Content%20Architecture%20Model.jpg) · [📑 Slides](../../../../sources/2026/01/31/Three-Layer-Content-Architecture/31%20Jan%20-%20Vault_Library_Newsstand_Model.pdf) · [🏠 Home](../../../../../README.md)

> *Semantic Knowledge Graph (SKG) — machine-readable metadata for search, discovery, and graph database integration*

---

## Summary

This document defines a three-layer architecture for organizing content in the NotebookLM repository: **sources/** for raw assets organized by date, **topics/** for curated documentation organized by category, and **platforms/** for publication artifacts. The architecture implements separation of concerns, enabling immutable source storage, flexible topic reorganization, and extensible platform support.

---

## Key Concepts

- **Separation of Concerns**: Each layer (sources/topics/platforms) has exactly one job, preventing mixed responsibilities
- **Single Source of Truth**: Raw assets live only in sources/, referenced by relative links elsewhere
- **Date Immutability**: Content creation dates don't change, providing stable source paths
- **Topic Flexibility**: Category hierarchy in topics/ can evolve without touching source files
- **Platform Extensibility**: New publication platforms added without restructuring existing content
- **Relative Linking**: All cross-layer links use relative paths for GitHub compatibility

---

## Core Arguments

1. Mixing source files, documentation, and publication artifacts in one folder creates maintenance chaos
2. Date-based organization for sources provides immutable, stable paths
3. Category-based organization for topics enables discovery and navigation
4. Platform-specific folders support different publication lifecycles
5. The three lifecycles (creation → curation → publication) map naturally to three layers
6. Relative links are the only format that works reliably on GitHub

---

## Key Quotes

> "Content in this repository has three distinct lifecycles: Creation, Curation, and Publication. These should map to three separate organizational structures."

> "Don't try to create the perfect architecture before you understand the problem. Ship something, learn from it, then improve."

---

## Tags

`architecture` `content-management` `separation-of-concerns` `repository-structure` `knowledge-management` `sources` `topics` `platforms` `relative-links` `github` `wardley-mapping` `evolution`

---

## Search Phrases

- "how to organize content repository"
- "separation of concerns for documentation"
- "three layer content architecture"
- "sources topics platforms structure"
- "managing notebooklm outputs"
- "date-based vs category-based organization"
- "github repository structure best practices"

---

## Semantic Knowledge Graph

### Architecture Diagram (Visual)

```mermaid
flowchart TB
    subgraph sources ["📁 SOURCES (by date)"]
        S1["2026/01/31/Topic/"]
        S2["Source PDF"]
        S3["Infographic"]
        S4["Slides"]
    end

    subgraph topics ["📚 TOPICS (by category)"]
        T1["Category/Subcategory/Topic/"]
        T2["README.md"]
        T3["SEMANTIC-GRAPH.md"]
        T4["slides_mosaic.png"]
    end

    subgraph platforms ["📢 PLATFORMS (by platform)"]
        P1["linkedin/Category/Topic/"]
        P2["post.md"]
        P3["*.webloc"]
    end

    S1 --> S2 & S3 & S4
    T1 --> T2 & T3 & T4
    P1 --> P2 & P3

    T2 -.->|links to| S3
    T2 -.->|links to| S4
    T2 -.->|links to| P1
    P2 -.->|links to| T2

    style sources fill:#e8f5e9,stroke:#4caf50
    style topics fill:#e3f2fd,stroke:#2196f3
    style platforms fill:#fff3e0,stroke:#ff9800
```

### Ontology

```mermaid
classDiagram
    class Layer {
        <<type>>
        Top-level organizational unit
    }
    class Sources {
        <<layer>>
        Raw assets by date
    }
    class Topics {
        <<layer>>
        Curated docs by category
    }
    class Platforms {
        <<layer>>
        Publication artifacts
    }
    class Artifact {
        <<type>>
        A file in the repository
    }

    Layer <|-- Sources
    Layer <|-- Topics
    Layer <|-- Platforms
    Sources --> Artifact : contains
    Topics --> Artifact : contains
    Platforms --> Artifact : contains
    Topics --> Sources : references
    Platforms --> Topics : references
```

### Taxonomy

```mermaid
mindmap
  root((Three-Layer Architecture))
    Sources Layer
      Date Organization
        YYYY/MM/DD/Topic
      Artifact Types
        Source Document
        Infographic
        Slides
    Topics Layer
      Category Hierarchy
        Category
        Subcategory
        Topic
      Documentation
        README
        SEMANTIC-GRAPH
        Mosaic
    Platforms Layer
      Platform Types
        LinkedIn
        Medium
        Blog
      Artifacts
        Post Text
        Links
        Analytics
```

### Knowledge Graph

```mermaid
graph TB
    subgraph problems ["❌ PROBLEMS"]
        P1["Mixed Concerns\n(challenge)"]
        P2["Inconsistent Paths\n(challenge)"]
        P3["No Clear Placement\n(challenge)"]
    end

    subgraph solution ["✅ SOLUTION"]
        S1["Three-Layer Architecture\n(methodology)"]
    end

    subgraph layers ["📁 LAYERS"]
        L1["sources/\n(layer)"]
        L2["topics/\n(layer)"]
        L3["platforms/\n(layer)"]
    end

    subgraph benefits ["🎯 BENEFITS"]
        B1["Separation of Concerns\n(benefit)"]
        B2["Stable Source Paths\n(benefit)"]
        B3["Flexible Categories\n(benefit)"]
    end

    P1 & P2 & P3 -.->|addressed_by| S1
    S1 -->|includes| L1 & L2 & L3
    L1 -->|provides| B2
    L2 -->|provides| B3
    S1 -->|produces| B1

    style P1 fill:#ffcdd2,stroke:#c62828
    style P2 fill:#ffcdd2,stroke:#c62828
    style P3 fill:#ffcdd2,stroke:#c62828
    style S1 fill:#c8e6c9,stroke:#2e7d32
    style L1 fill:#e3f2fd,stroke:#1976d2
    style L2 fill:#e3f2fd,stroke:#1976d2
    style L3 fill:#e3f2fd,stroke:#1976d2
    style B1 fill:#fff3e0,stroke:#f57c00
    style B2 fill:#fff3e0,stroke:#f57c00
    style B3 fill:#fff3e0,stroke:#f57c00
```

### Cypher Import (Neo4j)

```cypher
// ============================================
// NODES
// ============================================

// The methodology
CREATE (three_layer:Methodology {
    id: 'three_layer_architecture',
    name: 'Three-Layer Content Architecture',
    description: 'Separation of concerns: sources, topics, platforms'
})

// Layers
CREATE (sources:Layer {id: 'sources', name: 'sources/', organization: 'by date'})
CREATE (topics:Layer {id: 'topics', name: 'topics/', organization: 'by category'})
CREATE (platforms:Layer {id: 'platforms', name: 'platforms/', organization: 'by platform'})

// Problems addressed
CREATE (mixed_concerns:Challenge {id: 'mixed_concerns', name: 'Mixed Concerns'})
CREATE (inconsistent_paths:Challenge {id: 'inconsistent_paths', name: 'Inconsistent Paths'})
CREATE (no_placement:Challenge {id: 'no_placement', name: 'No Clear Placement Rules'})

// Benefits
CREATE (separation:Benefit {id: 'separation', name: 'Separation of Concerns'})
CREATE (stable_paths:Benefit {id: 'stable_paths', name: 'Stable Source Paths'})
CREATE (flexible_cats:Benefit {id: 'flexible_cats', name: 'Flexible Categories'})

// ============================================
// RELATIONSHIPS
// ============================================

CREATE (three_layer)-[:INCLUDES]->(sources)
CREATE (three_layer)-[:INCLUDES]->(topics)
CREATE (three_layer)-[:INCLUDES]->(platforms)

CREATE (three_layer)-[:ADDRESSES]->(mixed_concerns)
CREATE (three_layer)-[:ADDRESSES]->(inconsistent_paths)
CREATE (three_layer)-[:ADDRESSES]->(no_placement)

CREATE (three_layer)-[:PRODUCES]->(separation)
CREATE (sources)-[:PROVIDES]->(stable_paths)
CREATE (topics)-[:PROVIDES]->(flexible_cats)

CREATE (topics)-[:REFERENCES]->(sources)
CREATE (platforms)-[:REFERENCES]->(topics)
CREATE (platforms)-[:REFERENCES]->(sources)
```
