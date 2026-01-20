# FLeT REST API - Technical Implementation Brief (v1.4.50)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

Technical implementation blueprint for a comprehensive REST API layer exposing FLeT (Flow-Level Execution Task) operations for HTML caching, retrieval, and observability. The API provides multiple abstraction levels: low-level routes for fine-grained control (requires cache_id), domain-level routes for convenience (handles entity creation/lookup), and full observability routes (flow data, logs, tasks, durations). Follows "thin routes, rich services" pattern where routes handle only request parsing and response formatting while services contain all business logic.

---

## Key Concepts

- **Separation of Concerns**: `/cache/*` routes handle generic cache operations (entity management, data files), `/flet/*` routes handle FLeT-specific operations (flow execution, observability). Cache routes designed for future migration to Cache Service.

- **Multiple Abstraction Levels**: Low-level routes require cache_id for fine-grained control, domain-level routes handle entity creation/lookup for convenience, observability routes expose flow data, logs, tasks, and durations.

- **Thin Routes, Rich Services**: Route classes handle ONLY request parsing, service calls, response formatting, and errors. Service classes handle ALL business logic, entity management, and FLeT orchestration.

- **Consistent URL Patterns**: `{namespace}` always in path (matches Cache Service patterns), `{cache_id}` in path for entity-specific operations, `{data_key:path}` for hierarchical data paths.

- **Four Service Classes**: `Cache__Entity__Service` (entity CRUD), `Cache__Data__Service` (data file operations), `FLeT__Flows__Service` (flow observability), `FLeT__Html__Service` (HTML store/load orchestration).

- **Domain-Level Orchestration**: Store operations (raw, URL, key) and load operations (by id, hash, key, URL) that handle entity creation/lookup internally, making it easy for clients to work without managing cache_ids directly.

---

## Core Arguments

1. Exposing FLeT internals via REST empowers clients to build sophisticated HTML processing applications, just as exposing cache internals enabled powerful debugging during FLeT development.

2. Thin routes/rich services pattern ensures business logic is independently testable and reusable — routes become trivial wrappers with no logic to test.

3. Separating cache routes from FLeT routes enables future migration of generic cache operations to the Cache Service without affecting FLeT-specific functionality.

4. Multiple abstraction levels serve different client needs: low-level for control, domain-level for convenience, observability for debugging.

5. Consistent URL patterns with namespace and cache_id in paths align with existing Cache Service patterns and enable predictable routing.

6. Five implementation phases (cache, observability, low-level, domain-level, aggregator) enable incremental delivery with testable milestones.

---

## Key Quotes

> "Just as exposing cache internals via REST enabled powerful debugging during FLeT development, exposing FLeT internals will empower clients to build sophisticated HTML processing applications."

> "Route classes handle ONLY: request parsing, service calls, response formatting, errors. Service classes handle ALL: business logic, entity management, FLeT orchestration."

> "Cache routes are designed for future migration to Cache Service."

> "The API enables clients to store HTML from raw content, URLs, or explicit keys, load HTML by cache_id, cache_hash, cache_key, or URL, and inspect flow execution data."

---

## Tags

`flet-api` `rest-api` `implementation-brief` `fastapi` `html-caching` `flow-observability` `thin-routes` `rich-services` `domain-orchestration` `cache-service` `api-design` `mgraph-ai`

---

## Search Phrases

- "FLeT REST API implementation"
- "thin routes rich services pattern"
- "flow observability API"
- "HTML caching REST API"
- "domain-level orchestration"
- "cache entity service"
- "multiple abstraction levels API"
- "FastAPI service architecture"
- "FLeT flow data endpoints"
- "HTML store load operations"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Implementation Brief |
| **Domain** | Software Development |
| **Sub-domain** | REST API Design / Architecture |
| **Author** | Dinis Cruz |
| **Date Created** | January 19, 2026 |
| **Version** | v1.4.50 |
| **Package** | mgraph_ai_service_html_graph |
| **Status** | Blueprint (implemented in v1.4.52) |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `implemented_by` | FLeT REST API Implementation Debrief (v1.4.52) |
| `part_of` | HTML Graph Service |
| `enables` | HTML Store & Retrieval Consumer Guide (v1.4.53) |
| `uses` | Type_Safe Framework |
| `uses` | FLeT Pipeline Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `route_group` | A group of related REST routes |
| `service` | A service class handling business logic |
| `principle` | A design principle |
| `phase` | An implementation phase |
| `client` | A client use case |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `contains` | `part_of` | Route group contains routes |
| `implements` | `implemented_by` | Service implements functionality |
| `follows` | `followed_by` | Principle followed in design |
| `serves` | `served_by` | API serves client |
| `precedes` | `follows` | Phase precedes another |

### Taxonomy

```
flet_rest_api_blueprint
├── route_groups
│   ├── cache_routes
│   │   ├── entity_routes
│   │   └── data_routes
│   └── flet_routes
│       ├── flows_routes
│       ├── execute_routes
│       └── domain_routes
├── services
│   ├── Cache__Entity__Service
│   ├── Cache__Data__Service
│   ├── FLeT__Flows__Service
│   └── FLeT__Html__Service
├── principles
│   ├── separation_of_concerns
│   ├── multiple_abstraction_levels
│   ├── thin_routes_rich_services
│   └── consistent_url_patterns
└── phases
    ├── phase_1_cache_routes
    ├── phase_2_observability
    ├── phase_3_low_level
    ├── phase_4_domain_level
    └── phase_5_aggregator
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `flet_api` | `system` | FLeT REST API |
| `cache_routes` | `route_group` | Cache Routes (/cache/*) |
| `flet_routes` | `route_group` | FLeT Routes (/flet/*) |
| `entity_service` | `service` | Cache__Entity__Service |
| `data_service` | `service` | Cache__Data__Service |
| `flows_service` | `service` | FLeT__Flows__Service |
| `html_service` | `service` | FLeT__Html__Service |
| `thin_routes` | `principle` | Thin Routes Rich Services |
| `separation` | `principle` | Separation of Concerns |
| `html_client` | `client` | html-client service UI |
| `mitmproxy` | `client` | MitmProxy service |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `flet_api` | `contains` | `cache_routes` |
| `flet_api` | `contains` | `flet_routes` |
| `cache_routes` | `implemented_by` | `entity_service` |
| `cache_routes` | `implemented_by` | `data_service` |
| `flet_routes` | `implemented_by` | `flows_service` |
| `flet_routes` | `implemented_by` | `html_service` |
| `flet_api` | `follows` | `thin_routes` |
| `flet_api` | `follows` | `separation` |
| `flet_api` | `serves` | `html_client` |
| `flet_api` | `serves` | `mitmproxy` |

</details>
