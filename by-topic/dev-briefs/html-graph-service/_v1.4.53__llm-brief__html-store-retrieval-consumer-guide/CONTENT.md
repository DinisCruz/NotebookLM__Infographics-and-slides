# HTML Store & Retrieval - Consumer Integration Guide (v1.4.53)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

Consumer integration guide for the HTML Store & Retrieval system, providing unified, observable, versioned HTML caching that works seamlessly across REST APIs and internal services. The system uses an entity-centric model where each cached HTML page becomes an entity in the Cache Service, with HTML content stored as data files within that entity. This separation enables multiple data files per entity (raw HTML, transformed HTML, metadata), version tracking via cache_hash, and built-in observability via FLeT flows.

---

## Key Concepts

- **Entity-Centric Model**: Each cached HTML page is a Cache Entity containing cache_id (unique identifier), cache_key (human-readable key), cache_hash (content-based hash), and a data folder with HTML files and FLeT execution data.

- **Multi-Mode Storage**: Store HTML from raw content (auto-generated key), URLs (URL-derived key), or explicit cache keys — all operations go through observable FLeT pipelines.

- **Flexible Retrieval**: Load HTML by cache_id (direct), cache_hash (latest version with that content), cache_key (by logical path), or URL (converted to cache_key).

- **Automatic Versioning**: Same cache_key can have multiple versions — re-storing creates new entity with new cache_id, while cache_hash tracks content deduplication.

- **Data Key Organization**: Within each entity, data files are organized by `data_key` (folder path) and `data_file_id` (file name), enabling patterns like `html/raw`, `html/transformed`, `meta/info`.

- **Namespace Isolation**: Different applications or environments use different namespaces, isolating their caches while sharing the same cache service infrastructure.

---

## Core Arguments

1. The entity-centric model (page = entity, HTML = data file) enables storing multiple versions and transformations of the same page within a single logical unit.

2. Built-in observability via FLeT flows means every cache operation automatically records logs, tasks, and durations for debugging and monitoring.

3. Storage abstraction means the same code and APIs work unchanged whether running locally (in-memory), in integration tests, or in production.

4. URL-to-cache-key conversion with configurable query parameter handling enables consistent caching behavior across different URL patterns.

5. The pre-create entity pattern enables decoupled workflows where entity creation happens separately from HTML storage (useful for job queues).

6. Namespace isolation allows multiple applications to share the same cache infrastructure without conflicts.

---

## Key Quotes

> "Every HTML page becomes an entity in the Cache Service. The HTML content is stored as a data file within that entity."

> "This separation allows multiple data files per entity (raw HTML, transformed HTML, metadata, etc.), entity-level metadata and references, and version tracking via cache_hash."

> "Zero-config caching: Every page visited is automatically cached. Transparent to user: Same URL returns cached content seamlessly."

> "Use namespaces to isolate different applications or environments."

---

## Tags

`html-caching` `cache-service` `entity-model` `versioned-storage` `flet-pipeline` `rest-api` `consumer-guide` `mitmproxy` `url-caching` `observability` `namespace-isolation` `html-graph-service`

---

## Search Phrases

- "HTML store retrieval system"
- "entity-centric caching model"
- "versioned HTML storage"
- "FLeT observable pipeline"
- "URL to cache key conversion"
- "MitmProxy passive mode caching"
- "namespace isolated cache"
- "cache entity data files"
- "HTML graph service integration"
- "multi-mode HTML storage"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Consumer Integration Guide |
| **Domain** | Software Development |
| **Sub-domain** | Caching / HTML Processing |
| **Author** | Dinis Cruz |
| **Date Created** | January 20, 2026 |
| **Version** | v1.4.53 |
| **Package** | mgraph_ai_service_html_graph |
| **Target Audience** | LLM sessions, developers |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `follows` | FLeT REST API Implementation Debrief (v1.4.52) |
| `part_of` | HTML Graph Service |
| `uses` | Cache Service (entity + data model) |
| `uses` | FLeT Pipeline (observability) |
| `related_to` | Html_MGraph Multi-Graph Architecture (v1.4.54) |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `capability` | A system capability |
| `operation` | A store or load operation |
| `integration` | An integration pattern |
| `component` | A system component |
| `use_case` | A usage scenario |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `provides` | `provided_by` | System provides capability |
| `supports` | `supported_by` | Capability supports operation |
| `uses` | `used_by` | Integration uses component |
| `demonstrates` | `demonstrated_by` | Use case demonstrates capability |
| `isolates` | `isolated_by` | Namespace isolates cache |

### Taxonomy

```
html_store_retrieval_system
├── storage_operations
│   ├── store_raw_html
│   ├── store_from_url
│   └── store_with_key
├── retrieval_operations
│   ├── load_by_id
│   ├── load_by_hash
│   ├── load_by_key
│   └── load_by_url
├── integration_patterns
│   ├── rest_api_integration
│   ├── internal_service_integration
│   └── pre_create_entity_pattern
├── capabilities
│   ├── automatic_versioning
│   ├── built_in_observability
│   ├── namespace_isolation
│   └── multiple_html_versions
└── use_cases
    └── mitmproxy_passive_mode
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `html_store_retrieval` | `system` | HTML Store & Retrieval System |
| `entity_model` | `capability` | Entity-Centric Model |
| `auto_versioning` | `capability` | Automatic Versioning |
| `observability` | `capability` | Built-in Observability |
| `namespace_isolation` | `capability` | Namespace Isolation |
| `store_from_url` | `operation` | Store from URL |
| `load_by_id` | `operation` | Load by Cache ID |
| `rest_api` | `integration` | REST API Integration |
| `internal_service` | `integration` | Internal Service Integration |
| `mitmproxy_passive` | `use_case` | MitmProxy Passive Mode |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `html_store_retrieval` | `provides` | `entity_model` |
| `html_store_retrieval` | `provides` | `auto_versioning` |
| `html_store_retrieval` | `provides` | `observability` |
| `html_store_retrieval` | `provides` | `namespace_isolation` |
| `entity_model` | `supports` | `store_from_url` |
| `entity_model` | `supports` | `load_by_id` |
| `mitmproxy_passive` | `demonstrates` | `auto_versioning` |
| `rest_api` | `uses` | `html_store_retrieval` |
| `internal_service` | `uses` | `html_store_retrieval` |

</details>
