# HTML Graph Cache Mode Integration Guide (v0.8.11)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This integration guide details adding `mitm-mode=cache` to the MitmProxy service for passive HTML caching via HTML Graph without transformation. The flow checks cache on request (serving cached HTML on hits, continuing to upstream on misses), stores HTML on response, and adds diagnostic headers. Key insight: CACHE mode runs independently of the transformation pipeline — it does NOT trigger any transformation, just caches raw HTML for later offline modification workflows.

---

## Key Concepts

- **Cache Mode Cookie**: Browser sets `mitm-mode=cache` cookie to enable passive caching mode distinct from transformation modes (xxx, xxx-negative, hashes).

- **Short-Circuit Response**: On cache HIT, request processing returns cached HTML immediately without contacting upstream server — reducing latency for repeated requests.

- **HTML Graph Integration**: Cache handler delegates to HTML Graph service for storage and retrieval, using URL-based cache keys for content-addressable storage.

- **Response Header Instrumentation**: Cache operations add diagnostic headers (`x-cache-source`, `x-cache-key`, `x-html-graph-stored`) for debugging and verification.

- **Inactive Mode Classification**: CACHE mode added to `is_active()` exclusion list alongside OFF — ensuring transformation pipeline is skipped while cache operations proceed.

- **In-Memory Testing**: Tests run against real in-memory HTML Graph service instances — no mocks needed, enabling realistic integration testing.

---

## Core Arguments

1. Cache mode provides passive caching without transformation — enabling page snapshots for offline LLM modification workflows while preserving original HTML fidelity.

2. Request phase cache check enables short-circuit responses — cache HITs bypass upstream entirely, reducing latency and enabling offline browsing of cached content.

3. Response phase storage captures fresh HTML on cache misses — building the cache incrementally as users browse, without requiring explicit pre-caching.

4. Diagnostic headers enable visibility into cache behavior — developers can verify cache hits/misses and inspect stored content IDs without accessing logs.

5. CACHE mode coexists with existing transformation modes — users switch modes via cookie value, each mode handled by appropriate code path.

6. Integration follows established service patterns — Type_Safe classes, setup() returning self, handler injection via attributes, consistent with existing codebase.

---

## Key Quotes

> "CACHE mode runs independently of the transformation pipeline. It does NOT trigger any transformation — just caches raw HTML."

> "The cache mode provides passive HTML caching via HTML Graph without transformation."

> "All tests run against real in-memory service instances — no mocks needed."

> "On cache HIT, return cached HTML immediately (short-circuit)."

---

## Tags

`html-graph` `cache-mode` `mitmproxy` `mitm-mode` `passive-caching` `short-circuit` `response-headers` `integration-guide` `type-safe` `in-memory-testing` `cookie-versioning` `cache-handler`

---

## Search Phrases

- "mitm-mode cache HTML Graph integration"
- "passive HTML caching proxy"
- "cache mode short-circuit response"
- "HTML Graph Cache Handler implementation"
- "MitmProxy cache mode cookie"
- "x-cache-source response headers"
- "transformation mode enum extension"
- "in-memory service testing pattern"
- "Proxy Request Service cache check"
- "offline page modification workflow"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Integration Guide |
| **Domain** | Dev Briefs / HTML Graph Service |
| **Sub-domain** | Cache Integration / MitmProxy |
| **Format** | Markdown + PDF |
| **Version** | v0.8.11 |
| **Date** | January 2026 |
| **Target Audience** | Backend Developers, Service Integrators |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `extends` | MitmProxy Service |
| `uses` | HTML Graph Service |
| `related_to` | v1.5.0 In-Memory Services Pattern |
| `enables` | Vibe Coding Workflow for Rapid Product-Market Fit |
| `part_of` | MyFeeds.ai Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `component` | A service or handler component |
| `phase` | A request/response processing phase |
| `mode` | A transformation or cache mode |
| `file` | A source code file |
| `header` | An HTTP response header |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `processes` | `processed_by` | Phase processing |
| `produces` | `produced_by` | Header production |
| `modifies` | `modified_by` | File modification |
| `delegates_to` | `receives_from` | Service delegation |

### Taxonomy

```
html_graph_cache_mode
├── request_phase
│   ├── cookie_parsing
│   ├── mode_detection
│   └── cache_check
├── response_phase
│   ├── html_storage
│   └── header_addition
├── components
│   ├── html_graph_cache_handler
│   ├── proxy_request_service
│   └── proxy_response_service
└── modes
    ├── off
    ├── cache (NEW)
    ├── transform
    └── hashes
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `cache_handler` | `component` | HTML_Graph__Cache__Handler |
| `request_service` | `component` | Proxy__Request__Service |
| `response_service` | `component` | Proxy__Response__Service |
| `cache_mode` | `mode` | CACHE Mode |
| `request_phase` | `phase` | Request Phase |
| `response_phase` | `phase` | Response Phase |
| `x_cache_source` | `header` | x-cache-source Header |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `request_service` | `delegates_to` | `cache_handler` |
| `response_service` | `delegates_to` | `cache_handler` |
| `request_phase` | `produces` | `cache_hit_or_miss` |
| `response_phase` | `produces` | `x_cache_source` |
| `cache_mode` | `triggers` | `request_phase` |

</details>
