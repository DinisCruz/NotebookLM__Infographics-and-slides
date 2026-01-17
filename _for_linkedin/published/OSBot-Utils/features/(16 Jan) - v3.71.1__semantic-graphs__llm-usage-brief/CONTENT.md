# Semantic Graphs - LLM Usage Brief (v3.71.1)

> *Semantic content index for search, discovery, and knowledge graph integration*

---

## Summary

Semantic Graphs is a comprehensive, type-safe framework for building, validating, and projecting knowledge graphs in Python. It provides a rigorous schema layer for defining graph structures (nodes, edges, properties), ontologies that constrain what's valid, taxonomies for categorizing concepts, and business logic utilities that operate on this data.

---

## Key Concepts

- **The Four Pillars**: Taxonomy (categories), Ontology (vocabulary), Graph (instance data), Projection (human view)
- **ID/Ref Duality**: Internal IDs for referential integrity, human-readable Refs for display and config
- **Schema vs Logic Separation**: Schemas are pure data (no methods), all operations live in Utils/Builders/Validators
- **Deterministic IDs**: Same seed produces same ID - enables reproducible builds and testing
- **Edge Rules**: Ontology defines which node type → predicate → node type combinations are valid

---

## Core Capabilities

1. **Schema-based graph modeling** - Type-safe nodes, edges, and properties
2. **Ontology definitions** - Define valid node types, predicates, and edge rules
3. **Taxonomy hierarchies** - Categorize node types into hierarchical structures
4. **Fluent builder API** - Construct graphs with method chaining
5. **Validation engine** - Validate graphs against ontology constraints
6. **Human-readable projections** - Transform IDs to refs for export and debugging

---

## Key Quotes

> "Schemas are pure data — contain only type annotations, never methods"

> "IDs provide referential integrity, Refs enable human-readable projections"

> "Type-safe throughout — runtime type checking on every operation"

---

## Design Principles

- **Type_Safe enforces** runtime type checking on every operation
- **Deterministic reproducibility** - Generate consistent IDs from seeds
- **Ontology-driven validation** - Edge rules define what's valid; validators enforce it
- **Projections for humans** - Transform internal schemas to human-readable formats

---

## Tags

`semantic-graphs` `knowledge-graphs` `osbot-utils` `type-safe` `ontology` `taxonomy` `graph-database` `python` `schema-validation` `projections` `id-ref-duality`

---

## Search Phrases

- "OSBot-Utils semantic graphs"
- "type-safe knowledge graph Python"
- "ontology validation framework"
- "graph schema definition"
- "ID vs Ref pattern"
- "deterministic ID generation"
- "semantic graph builder"
- "taxonomy hierarchy Python"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Documentation / LLM Brief |
| **Domain** | Software Development |
| **Sub-domain** | Knowledge Graphs, Type Systems |
| **Author** | Dinis Cruz |
| **Version** | v3.71.1 |
| **Date Created** | 16 Jan 2026 |
| **Source Format** | Markdown |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |
| **Repository** | https://github.com/owasp-sbot/OSBot-Utils |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `part_of` | OSBot-Utils framework |
| `uses` | Type_Safe runtime primitives |
| `supports` | MGraph-DB project |
| `related_to` | [PDD + Runtime Type Safety](../../Development%20and%20Enginering/(16%20Jan)%20-%20Combining%20Pass%E2%80%91Driven%20Development%20and%20Runtime%20Type%20Safety%20for%20Resilient%20Software/) |

---

## Semantic Graph

<details>
<summary>Click to expand graph structure (for programmatic consumption)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `pillar` | Core framework component |
| `class` | Python class in framework |
| `pattern` | Design pattern used |
| `capability` | What the framework can do |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `implements` | `implemented_by` | Class realizes pattern |
| `validates` | `validated_by` | Component checks another |
| `transforms` | `transformed_by` | Component converts another |
| `contains` | `contained_by` | Component includes another |

### Taxonomy

```
framework_component (root)
├── pillar
│   ├── taxonomy
│   ├── ontology
│   ├── graph
│   └── projection
├── class
│   ├── schema (pure data)
│   └── utils (operations)
├── pattern
│   ├── id_ref_duality
│   ├── schema_logic_separation
│   └── deterministic_ids
└── capability
    ├── validation
    ├── projection
    └── fluent_building
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `taxonomy` | `pillar` | Taxonomy (Categories) |
| `ontology` | `pillar` | Ontology (Vocabulary) |
| `graph` | `pillar` | Graph (Instance Data) |
| `projection` | `pillar` | Projection (Human View) |
| `builder` | `class` | Semantic_Graph__Builder |
| `validator` | `class` | Semantic_Graph__Validator |
| `projector` | `class` | Semantic_Graph__Projector |
| `id_ref` | `pattern` | ID/Ref Duality |
| `schema_logic` | `pattern` | Schema vs Logic Separation |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `ontology` | `contains` | `taxonomy` |
| `graph` | `validated_by` | `ontology` |
| `validator` | `validates` | `graph` |
| `projector` | `transforms` | `graph` |
| `projection` | `transformed_by` | `projector` |
| `builder` | `implements` | `id_ref` |
| `validator` | `implements` | `schema_logic` |

</details>
