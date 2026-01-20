# Html_MGraph Multi-Graph Architecture - LLM Usage Brief (v1.4.54)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

Html_MGraph is a multi-graph architecture that transforms HTML documents into interconnected, specialized graphs—enabling powerful manipulation, analysis, and perfect round-trip reconstruction. Instead of representing HTML as a single cluttered graph, it separates concerns into 5 purpose-specific graphs (body, head, attrs, scripts, styles) that share node identities via `Node_Id`. This is like database normalization for HTML: the DOM structure, attributes, scripts, and styles each live in their own optimized graph, yet they're all linked by shared Node_Id values enabling seamless recombination.

---

## Key Concepts

- **Multi-Graph Architecture**: 5 specialized graphs instead of one cluttered graph — body_graph (DOM structure), head_graph (metadata), attrs_graph (tags + attributes), scripts_graph (JavaScript), styles_graph (CSS).

- **Shared Node_Id Pattern**: Critical design principle where element nodes use the **same `Node_Id`** across all relevant graphs — enables instant cross-graph lookups and seamless recombination.

- **Three-Node Attribute Model**: Attributes use element_node → instance_node → name_node structure for deduplication — name nodes shared across elements, value nodes shared for common values, position preserved for round-trip.

- **Perfect Round-Trip**: HTML → Graphs → HTML preserves everything including boolean attributes, attribute order, and whitespace — `assert reconstructed == original_html`.

- **Separation of Concerns**: Work with structure without attribute noise — visualize only DOM tree, query only attributes, analyze only scripts — each graph has focused responsibility.

- **Type-Safe Foundation**: Built on MGraph-DB with full runtime type checking via OSBot-Utils Type_Safe framework.

---

## Core Arguments

1. Traditional HTML-to-graph approaches create a single graph with everything mixed together, making visualization cluttered and transformations complex — multi-graph architecture separates concerns cleanly.

2. Shared Node_Id across graphs enables "database-like" lookups: get element from body_graph, lookup tag in attrs_graph, lookup attributes in attrs_graph — all using the same identifier.

3. The three-node attribute model provides both deduplication (shared name/value nodes) and preservation of attribute order for perfect round-trip reconstruction.

4. Separate scripts_graph and styles_graph allow targeted analysis of JavaScript and CSS without traversing the entire DOM structure.

5. Perfect round-trip enables safe transformations: modify the graph, reconstruct HTML, and be confident nothing was lost (boolean attributes, attribute order, whitespace).

6. Type-safe foundation with MGraph-DB ensures runtime validation, catching errors at assignment time rather than deep in business logic.

---

## Key Quotes

> "Think of it like a database normalization for HTML: the DOM structure, attributes, scripts, and styles each live in their own optimized graph, yet they're all linked by shared Node_Id values."

> "Same Node_Id used across graphs enables instant cross-graph lookups."

> "Attributes use a sophisticated model for deduplication and ordering: element_node → instance_node → name_node. Position preserved for round-trip. Name nodes shared across elements."

> "Boolean attributes, attribute order, whitespace - all preserved."

---

## Tags

`html-mgraph` `multi-graph` `html-parsing` `dom-graph` `node-id-sharing` `attribute-model` `round-trip` `html-reconstruction` `mgraph-db` `type-safe` `html-analysis` `script-extraction` `style-extraction` `graph-visualization`

---

## Search Phrases

- "Html_MGraph multi-graph architecture"
- "HTML to graph transformation"
- "shared Node_Id pattern"
- "three-node attribute model"
- "perfect HTML round-trip"
- "separation of concerns HTML graph"
- "MGraph-DB HTML service"
- "DOM structure graph"
- "script extraction from HTML graph"
- "cross-graph lookups"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Usage Guide / LLM Brief |
| **Domain** | Software Development |
| **Sub-domain** | HTML Processing / Graph Databases |
| **Author** | Dinis Cruz |
| **Date Created** | January 2026 |
| **Version** | v1.4.54 |
| **Package** | mgraph_ai_service_html_graph.service.html_mgraph |
| **Depends On** | MGraph-DB (owasp-sbot/MGraph-DB) |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `part_of` | HTML Graph Service |
| `depends_on` | MGraph-DB |
| `uses` | Type_Safe Framework (OSBot-Utils) |
| `related_to` | HTML Store & Retrieval Consumer Guide (v1.4.53) |
| `related_to` | IFD Snapshot Generator (v1.4.55) |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `graph` | A specialized graph in the multi-graph system |
| `pattern` | A design pattern used in the architecture |
| `principle` | A key design principle |
| `capability` | A feature or capability of the system |
| `operation` | A conversion or manipulation operation |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `contains` | `part_of` | Document contains graphs |
| `shares` | `shared_by` | Graphs share Node_Id |
| `enables` | `enabled_by` | Pattern enables capability |
| `uses` | `used_by` | Graph uses pattern |
| `converts` | `converted_by` | Operation converts format |

### Taxonomy

```
html_mgraph_architecture
├── graphs
│   ├── Html_MGraph__Document (orchestrator)
│   ├── Html_MGraph__Head (metadata)
│   ├── Html_MGraph__Body (content)
│   ├── Html_MGraph__Attributes (properties)
│   ├── Html_MGraph__Scripts (javascript)
│   └── Html_MGraph__Styles (css)
├── patterns
│   ├── shared_node_id
│   ├── three_node_attribute
│   └── separation_of_concerns
├── operations
│   ├── html_to_graphs
│   ├── graphs_to_html
│   └── to_dot_visualization
└── capabilities
    ├── perfect_round_trip
    ├── cross_graph_lookup
    ├── clean_visualization
    └── targeted_queries
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `html_mgraph` | `system` | Html_MGraph Multi-Graph |
| `document_graph` | `graph` | Html_MGraph__Document |
| `body_graph` | `graph` | Html_MGraph__Body |
| `head_graph` | `graph` | Html_MGraph__Head |
| `attrs_graph` | `graph` | Html_MGraph__Attributes |
| `scripts_graph` | `graph` | Html_MGraph__Scripts |
| `styles_graph` | `graph` | Html_MGraph__Styles |
| `shared_node_id` | `pattern` | Shared Node_Id Pattern |
| `three_node_attr` | `pattern` | Three-Node Attribute Model |
| `round_trip` | `capability` | Perfect Round-Trip |
| `cross_graph_lookup` | `capability` | Cross-Graph Lookups |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `html_mgraph` | `contains` | `document_graph` |
| `html_mgraph` | `contains` | `body_graph` |
| `html_mgraph` | `contains` | `head_graph` |
| `html_mgraph` | `contains` | `attrs_graph` |
| `html_mgraph` | `contains` | `scripts_graph` |
| `html_mgraph` | `contains` | `styles_graph` |
| `body_graph` | `shares` | `shared_node_id` |
| `attrs_graph` | `shares` | `shared_node_id` |
| `shared_node_id` | `enables` | `cross_graph_lookup` |
| `attrs_graph` | `uses` | `three_node_attr` |
| `three_node_attr` | `enables` | `round_trip` |

</details>
