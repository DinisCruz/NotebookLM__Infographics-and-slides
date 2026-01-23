# In-Memory Service Pattern for FastAPI Microservices (v1.5.0)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This implementation brief describes an In-Memory Service Pattern enabling FastAPI microservices to run in three modes: Full Remote (production HTTP), Full In-Memory (testing via TestClient), and Hybrid (mixed). The pattern uses dependency injection at the request layer so client packages never require service dependencies in remote mode, while enabling seamless in-memory testing. Key insight: the `*__Requests` class is the ONLY injection point — everything above it is mode-agnostic, keeping client code clean and dependency graphs minimal.

---

## Key Concepts

- **In-Memory Service Pattern**: Architecture enabling services to run either via HTTP (remote) or in-process via Starlette TestClient (in-memory) with identical client code.

- **Request Layer Abstraction**: The `*__Requests` class decides whether to use TestClient (IN_MEMORY mode) or requests.Session (REMOTE mode) — single point of injection.

- **Service Composition**: In-memory services can be composed with injected clients — e.g., HTML Graph In-Memory receives an injected Cache Client pointing to Cache In-Memory.

- **Zero Service Dependencies**: In REMOTE mode, client packages have zero imports from service packages — only client packages are installed in production.

- **Fluent Factory Pattern**: `Client__*__Service().set__fast_api_app(app).client()` enables clean configuration with method chaining.

- **Environment-Driven Configuration**: Remote mode configures from environment variables (URL, API key, namespace) without code changes.

---

## Core Arguments

1. Current testing approaches using real HTTP servers have problems: slow startup, port conflicts in parallel tests, server lifecycle management, network overhead, and flaky tests from timing issues.

2. Starlette's TestClient can call FastAPI apps directly in-process via ASGI — the client doesn't care if it's talking to TestClient or requests.Session since both have the same interface (.get(), .post()).

3. The `*__Requests` class is THE MAGIC — it switches between TestClient for IN_MEMORY mode and requests.Session for REMOTE mode, making all code above mode-agnostic.

4. Full In-Memory testing enables millisecond tests, no port conflicts, no server lifecycle, deterministic results (no network flakiness) — transforming integration test reliability.

5. Hybrid mode enables developing one service locally against remote dependencies — e.g., HTML Graph In-Memory talking to production Cache service for realistic development.

6. Implementation follows Type_Safe patterns: all classes inherit from Type_Safe, attributes have type annotations, Safe_* primitives used, schemas are pure data, setup() returns self.

---

## Key Quotes

> "The client code above this layer is IDENTICAL for both modes."

> "Tests run in milliseconds — no port conflicts, no server lifecycle, deterministic (no network flakiness)."

> "The key insight is that the `*__Requests` class is the only injection point. Everything above it is mode-agnostic."

> "Zero-compromise flexibility: Testing with full in-memory stack, Development with mix of local/remote, Production with pure client packages."

---

## Tags

`in-memory-service` `fastapi` `testclient` `dependency-injection` `request-layer` `type-safe` `service-composition` `hybrid-mode` `starlette` `asgi` `millisecond-tests` `zero-dependencies`

---

## Search Phrases

- "FastAPI in-memory testing pattern"
- "TestClient vs requests.Session injection"
- "service composition dependency injection"
- "request layer abstraction pattern"
- "zero service dependencies remote mode"
- "hybrid in-memory remote development"
- "Type_Safe FastAPI service pattern"
- "millisecond integration tests"
- "Starlette ASGI in-process calls"
- "fluent factory pattern Python"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Implementation Brief |
| **Domain** | Dev Briefs / HTML Graph Service |
| **Sub-domain** | Testing Patterns / Service Architecture |
| **Format** | Markdown + PDF |
| **Version** | v1.5.0 |
| **Date** | January 2025 |
| **Scope** | mgraph_ai_service_cache, mgraph_ai_service_html_graph |
| **Target Audience** | Backend Developers, Test Engineers |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `enables` | v0.8.11 HTML Graph Cache Mode Integration |
| `uses` | Cache Service |
| `uses` | HTML Graph Service |
| `pattern_for` | Type_Safe Architecture |
| `part_of` | MyFeeds.ai Service Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `mode` | An execution mode |
| `class` | A Python class |
| `pattern` | An architectural pattern |
| `benefit` | A positive outcome |
| `phase` | An implementation phase |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `enables` | `enabled_by` | Capability enablement |
| `injects` | `injected_by` | Dependency injection |
| `produces` | `produced_by` | Output generation |
| `precedes` | `follows` | Phase ordering |

### Taxonomy

```
in_memory_service_pattern
├── execution_modes
│   ├── full_remote
│   ├── full_in_memory
│   └── hybrid
├── key_classes
│   ├── service_in_memory
│   ├── client_config
│   ├── client_requests (INJECTION POINT)
│   └── client_factory
├── implementation_phases
│   ├── phase_1_cache_in_memory
│   ├── phase_2_client_refactor
│   └── phase_3_html_graph_in_memory
└── benefits
    ├── millisecond_tests
    ├── no_port_conflicts
    └── deterministic_results
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `in_memory_mode` | `mode` | Full In-Memory Mode |
| `remote_mode` | `mode` | Full Remote Mode |
| `requests_class` | `class` | *__Requests (Injection Point) |
| `service_in_memory` | `class` | *__Service__In_Memory |
| `request_layer` | `pattern` | Request Layer Abstraction |
| `millisecond_tests` | `benefit` | Millisecond Test Execution |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `requests_class` | `enables` | `in_memory_mode` |
| `requests_class` | `enables` | `remote_mode` |
| `request_layer` | `produces` | `millisecond_tests` |
| `service_in_memory` | `injects` | `requests_class` |
| `phase_1` | `precedes` | `phase_2` |
| `phase_2` | `precedes` | `phase_3` |

</details>
