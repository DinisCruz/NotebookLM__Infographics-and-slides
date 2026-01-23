# Standalone (Air-Gapped) MITM Proxy Service

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This technical brief describes a standalone deployment mode for the MITM Proxy platform where all microservice components (caching, HTML processing, graph generation, text transformation) run in a single process or container. The key innovation is using FastAPI's TestClient mechanism to route inter-service calls in-memory rather than over HTTP sockets—the client abstraction layer switches between "live HTTP" and "in-memory" modes transparently. This enables fast integration testing (full stack boots in hundreds of milliseconds), air-gapped deployments without external dependencies, and simplified prototyping. The same codebase supports multiple deployment modes: serverless functions, Kubernetes microservices, or monolithic application.

---

## Key Concepts

- **Single-Process Deployment**: All normally separate microservices (caching, HTML graph, text comprehension, etc.) initialized together in one FastAPI application or Python process, sharing runtime and memory space.

- **In-Memory Service Calls (Test Mode)**: Client abstraction layer configured to use FastAPI's TestClient or HTTPX ASGI client to call target service apps directly in memory, bypassing network sockets while maintaining same HTTP client interface.

- **Client Abstraction Layer**: Each microservice has a client module that can switch between real HTTP requests (production) and TestClient calls (standalone mode) via configuration flag or environment variable.

- **FastAPI TestClient Mechanism**: Starlette's TestClient class allows calling API endpoints as if via HTTP but runs requests entirely in-memory, built on HTTPX with Requests library API compatibility.

- **Air-Gapped Operation**: Complete system functions offline by packaging all components together—inter-service communication occurs internally so the system runs on a single machine without internet access.

- **Deployment Flexibility**: Same codebase supports three deployment modes: independent serverless functions, microservices on Kubernetes, or one-shot monolithic application depending on operational needs.

---

## Core Arguments

1. While microservice architecture offers scalability, it complicates integration testing and deployments in isolated environments—the standalone mode addresses both by embedding all services in one process with in-memory communication.

2. FastAPI's TestClient provides the same interface as an HTTP client, so existing request code works identically whether going to a live server or an in-memory one—the switch is transparent to service logic.

3. Integration tests using the standalone mode exercise real code paths across services rather than mocking them out, providing higher confidence that workflows function correctly across service boundaries.

4. For air-gapped sites, pre-downloading the wheels or using an internal PyPI mirror enables installation without runtime internet access—only Python and required libraries are needed.

5. The standalone mode enables quick prototypes, demos, and MVP deployments where a single executable is easier than managing multiple services, but it's not designed to replace horizontal scaling for production traffic.

6. Services can re-initialize for each test case in tens of milliseconds (negligible overhead), enabling comprehensive test suites that bring up full in-memory stacks repeatedly without performance impact.

---

## Key Quotes

> "This mode allows the entire system to function offline (even in air-gapped environments) by embedding each service in-memory."

> "The code 'thinks' it's making a normal request, but the TestClient catches it and routes it internally."

> "This approach essentially boots up a miniature version of our production environment inside the test suite."

> "The same codebase can be run as independent serverless functions, as microservices on Kubernetes, or as a one-shot monolithic application, depending on the need."

---

## Tags

`standalone-deployment` `air-gapped` `in-memory-services` `fastapi-testclient` `integration-testing` `single-process` `client-abstraction` `offline-operation` `pypi-package` `microservices` `monolithic` `dependency-injection`

---

## Search Phrases

- "standalone MITM proxy single process deployment"
- "air-gapped microservices in-memory"
- "FastAPI TestClient inter-service calls"
- "client abstraction layer HTTP vs in-memory"
- "integration testing without network overhead"
- "offline proxy service deployment"
- "single executable microservice bundle"
- "dependency injection test mode"
- "monolithic deployment from microservices"
- "PyPI package air-gapped installation"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Brief |
| **Domain** | Dev Briefs / MitmProxy Service |
| **Sub-domain** | Deployment Modes / Testing Patterns |
| **Format** | PDF (5 pages) |
| **Date** | January 2026 |
| **Generated By** | ChatGPT |
| **Target Audience** | DevOps Engineers, Test Engineers, Backend Developers |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `mode_of` | MITM Proxy Platform Architecture |
| `uses` | FastAPI TestClient |
| `enables` | Integration Testing |
| `related_to` | In-Memory Service Pattern (v1.5.0) |
| `part_of` | MyFeeds.ai Service Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `deployment_mode` | A way to deploy the system |
| `component` | System component |
| `mechanism` | Technical mechanism |
| `use_case` | Usage scenario |
| `limitation` | Known limitation |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `enables` | `enabled_by` | Capability enablement |
| `uses` | `used_by` | Mechanism usage |
| `combines` | `combined_in` | Component combination |
| `supports` | `supported_by` | Use case support |

### Taxonomy

```
standalone_mitm_proxy
├── deployment_modes
│   ├── single_process
│   ├── single_container
│   └── pypi_package
├── components_combined
│   ├── mitm_proxy_api
│   ├── caching_service
│   ├── html_graph_service
│   └── text_comprehension_service
├── mechanisms
│   ├── fastapi_testclient
│   ├── client_abstraction_layer
│   ├── dependency_injection
│   └── in_memory_data_stores
├── use_cases
│   ├── integration_testing
│   ├── air_gapped_deployment
│   ├── lightweight_demos
│   └── mvp_deployment
└── limitations
    ├── no_horizontal_scaling
    ├── shared_process_resources
    └── some_external_dependencies
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `standalone_mode` | `deployment_mode` | Standalone Deployment |
| `testclient` | `mechanism` | FastAPI TestClient |
| `client_abstraction` | `mechanism` | Client Abstraction Layer |
| `integration_testing` | `use_case` | Integration Testing |
| `air_gapped` | `use_case` | Air-Gapped Deployment |
| `caching_service` | `component` | Caching Service |
| `html_graph` | `component` | HTML Graph Service |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `standalone_mode` | `combines` | `caching_service` |
| `standalone_mode` | `combines` | `html_graph` |
| `standalone_mode` | `uses` | `testclient` |
| `standalone_mode` | `uses` | `client_abstraction` |
| `standalone_mode` | `supports` | `integration_testing` |
| `standalone_mode` | `supports` | `air_gapped` |

</details>
