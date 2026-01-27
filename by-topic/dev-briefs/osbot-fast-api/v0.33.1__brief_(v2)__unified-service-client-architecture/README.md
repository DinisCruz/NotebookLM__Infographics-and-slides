# v0.33.1 - Unified Service Client Architecture (v2)

[Home](../../../../../README.md) / [By Topic](../../../../README.md) / [Dev Briefs](../../../README.md) / [OSBot Fast API](../../README.md) / **v0.33.1 - Unified Service Client (v2)**

---

## Overview

This development brief describes a unified architecture for FastAPI service clients that enables seamless switching between IN_MEMORY (TestClient) and REMOTE (HTTP) modes without code changes.

**Key Insight (v2)**: The registry stores **configs keyed by client type**, not client instances. Client classes become **stateless facades** that look up their config from the registry at request time.

## Evolution from v1 to v2

| Aspect | v1 (Instance Storage) | v2 (Config Storage) |
|--------|----------------------|---------------------|
| Registry stores | Client instances | Config objects keyed by client type |
| Client classes | Stateful (hold config) | Stateless facades |
| Domain operations receive | `_client` reference | `requests` object |
| Mental model | "Get the client for this service" | "What config does this service type use?" |

## Key Concepts

### Stateless Facades
Client classes (e.g., `Cache__Service__Client`) are now stateless facades. They don't hold configuration - they look it up from the registry when needed via `@cache_on_self` decorated methods.

### Config Store Pattern
```python
# Registration (v2)
registry.register(Cache__Service__Client, config)

# Lookup (v2)
config = registry.config(Cache__Service__Client)
```

### Domain Operations Pattern
Domain operation classes receive the `requests` object directly, breaking circular dependencies:
```python
def store(self):
    return Service__Client__File__Store(requests=self.requests())
```

## Architecture Layers

1. **Application Layer** - Business logic uses stateless client facades
2. **Service Client Layer** - Domain-specific clients extending base class
3. **Infrastructure Layer** - Generic transport in osbot_fast_api

## Files

| File | Description |
|------|-------------|
| `v0.33.1__brief_(v2)__unified-service-client-architecture.md.md` | Full development brief |
| `26 Jan - Stateless_Facades_Unified_Transport.pdf` | Presentation slides |
| `26 Jan - Unified Service Client Architecture.jpg` | Architecture diagram |
| `slides_mosaic.png` | Mosaic of all presentation slides |
| `SEMANTIC-GRAPH.md` | Knowledge graph and ontology |

## Affected Projects

- `osbot_fast_api`
- `mgraph_ai_service_cache_client`
- `mgraph_ai_service_cache`
- `mgraph_ai_service_html_graph_client`
- `mgraph_ai_service_html_graph`
- Future service clients

## Benefits

- **Reduced Complexity**: 1 class per service client (vs 3+)
- **Shared Transport**: Generic `Fast_API__Client__Requests`
- **Unified Config**: Single `Fast_API__Service__Registry__Client__Config`
- **No Circular Dependencies**: Domain ops receive `requests`, not `_client`
- **Testability**: IN_MEMORY mode uses real FastAPI app, ~100ms startup

---

*Status: Proposed | Version: v0.33.1 | Date: January 2025*
