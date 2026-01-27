# Semantic Graph: Unified Service Client Architecture (v2)

## Ontology

### Core Domain
The architecture addresses **service client design patterns** in a **FastAPI microservices ecosystem**. The primary innovation is transitioning from **instance-based registry storage** to **config-based registry storage**, enabling **stateless client facades**.

### Fundamental Entities
- **Service Client**: Domain-specific class providing typed access to a microservice
- **Registry**: Centralized storage for service configurations keyed by client type
- **Transport Layer**: Generic HTTP/TestClient execution mechanism
- **Config**: Connection parameters (mode, URL, API keys, FastAPI app reference)
- **Domain Operation**: Grouped methods for specific functionality (store, retrieve, delete)

### Key Relationships
- Clients **extend** base infrastructure classes
- Registry **stores** configs **keyed by** client types
- Domain operations **receive** requests objects (not client references)
- Transport **delegates** execution based on mode (IN_MEMORY vs REMOTE)

---

## Taxonomy

### Architecture Patterns
```
Service Client Architecture
├── Transport Patterns
│   ├── IN_MEMORY Mode (TestClient)
│   └── REMOTE Mode (HTTP requests)
├── Registration Patterns
│   ├── v1: Instance Storage (deprecated)
│   └── v2: Config Storage (current)
├── Client Patterns
│   ├── Stateful Client (v1)
│   └── Stateless Facade (v2)
└── Dependency Patterns
    ├── Circular (_client reference)
    └── Linear (requests injection)
```

### Package Hierarchy
```
Infrastructure Layer (osbot_fast_api)
├── Fast_API__Service__Registry
├── Fast_API__Service__Registry__Client__Base
├── Fast_API__Client__Requests
└── Fast_API__Service__Registry__Client__Config

Service Client Layer (*_client packages)
├── Cache__Service__Client
├── Html_Graph__Service__Client
└── LLM__Service__Client

Service Layer (*_service packages)
└── Registration helpers (functions, not classes)
```

### Evolution Phases
```
Architecture Evolution
├── Phase 1: Registry Pattern Introduction
├── Phase 2: Questioning In_Memory Class
├── Phase 3: Package Dependency Analysis
├── Phase 4: Generic Transport Discovery
├── Phase 5: Wrapper Layer Elimination
├── Phase 6: Naming Cleanup
└── Phase 7: Config Storage (v2 final)
```

---

## Knowledge Graph

### Nodes

| ID | Label | Type | Description |
|----|-------|------|-------------|
| N1 | Fast_API__Service__Registry | Class | Config store keyed by client type |
| N2 | Fast_API__Client__Requests | Class | Generic transport (IN_MEMORY/REMOTE) |
| N3 | Fast_API__Service__Registry__Client__Base | Class | Base class for all service clients |
| N4 | Fast_API__Service__Registry__Client__Config | Class | Shared configuration schema |
| N5 | Cache__Service__Client | Class | Stateless facade for cache service |
| N6 | Cache__Service__Client__Requests | Class | Cache-specific requests extension |
| N7 | Service__Client__File__Store | Class | Domain operation for storing files |
| N8 | register_cache_service | Function | Registration helper for IN_MEMORY mode |
| N9 | IN_MEMORY Mode | Mode | Uses TestClient with FastAPI app |
| N10 | REMOTE Mode | Mode | Uses HTTP requests library |
| N11 | osbot_fast_api | Package | Infrastructure package |
| N12 | mgraph_ai_service_cache_client | Package | Light client package (schemas + client) |
| N13 | mgraph_ai_service_cache | Package | Heavy service package (FastAPI + logic) |
| N14 | Stateless Facade | Pattern | Client without internal config state |
| N15 | Config Store | Pattern | Registry stores configs, not instances |
| N16 | @cache_on_self | Decorator | Caches method result on instance |
| N17 | TestClient | Class | Starlette test client for in-process calls |
| N18 | requests.Session | Class | HTTP session for remote calls |
| N19 | service_type | Attribute | Client type used for registry lookup |
| N20 | Dict__Fast_API__Service__Configs_By_Type | Class | Type-safe dict for config storage |

### Edges

| Source | Target | Relationship | Description |
|--------|--------|--------------|-------------|
| N1 | N20 | contains | Registry contains configs dict |
| N1 | N4 | stores | Registry stores config objects |
| N2 | N1 | looks_up | Requests looks up config from registry |
| N2 | N17 | uses_for | Uses TestClient for IN_MEMORY mode |
| N2 | N18 | uses_for | Uses Session for REMOTE mode |
| N3 | N2 | depends_on | Base client uses requests transport |
| N5 | N3 | extends | Cache client extends base class |
| N5 | N6 | creates | Cache client creates its requests |
| N5 | N14 | implements | Cache client implements stateless facade |
| N6 | N2 | extends | Cache requests extends generic transport |
| N6 | N19 | has | Requests has service_type attribute |
| N7 | N6 | receives | Domain op receives requests object |
| N8 | N1 | registers_with | Helper registers config with registry |
| N8 | N5 | registers | Helper registers Cache client type |
| N9 | N17 | implemented_by | IN_MEMORY uses TestClient |
| N10 | N18 | implemented_by | REMOTE uses requests.Session |
| N11 | N1 | contains | osbot_fast_api contains registry |
| N11 | N2 | contains | osbot_fast_api contains transport |
| N12 | N5 | contains | Client package contains client facade |
| N12 | N11 | depends_on | Client package depends on infrastructure |
| N13 | N8 | contains | Service package contains registration |
| N13 | N12 | depends_on | Service depends on client package |
| N14 | N15 | enabled_by | Stateless facades enabled by config store |
| N16 | N2 | decorates | Caches config lookup in requests |

---

## Concept Relationships

### v1 to v2 Evolution

```
v1 Architecture                      v2 Architecture
─────────────────                    ─────────────────
Registry stores instances     →      Registry stores configs
                                     keyed by client type

Client holds config           →      Client is stateless facade
                                     (looks up config at request time)

Domain ops receive _client    →      Domain ops receive requests
(circular dependency)                (linear dependency)

Multiple wrapper classes      →      Single unified client class
(In_Memory, Registry_Client)
```

### Dependency Flow

```
Application Code
      │
      ▼
Cache__Service__Client (stateless facade)
      │
      ├─── requests() ──→ Cache__Service__Client__Requests
      │                          │
      │                          ├─── service_type = Cache__Service__Client
      │                          │
      │                          └─── config() ──→ Fast_API__Service__Registry
      │                                                   │
      │                                                   └─── configs[Cache__Service__Client]
      │
      └─── store() ──→ Service__Client__File__Store(requests=...)
                              │
                              └─── requests.execute("POST", path, body)
```

---

## Cypher Export

```cypher
// Create Infrastructure Layer Nodes
CREATE (registry:Class {name: 'Fast_API__Service__Registry', layer: 'infrastructure', description: 'Config store keyed by client type'})
CREATE (transport:Class {name: 'Fast_API__Client__Requests', layer: 'infrastructure', description: 'Generic transport for IN_MEMORY/REMOTE'})
CREATE (baseClient:Class {name: 'Fast_API__Service__Registry__Client__Base', layer: 'infrastructure', description: 'Base class for service clients'})
CREATE (config:Class {name: 'Fast_API__Service__Registry__Client__Config', layer: 'infrastructure', description: 'Shared configuration schema'})
CREATE (configsDict:Class {name: 'Dict__Fast_API__Service__Configs_By_Type', layer: 'infrastructure', description: 'Type-safe dict mapping client type to config'})

// Create Service Client Layer Nodes
CREATE (cacheClient:Class {name: 'Cache__Service__Client', layer: 'service_client', description: 'Stateless facade for cache service'})
CREATE (cacheRequests:Class {name: 'Cache__Service__Client__Requests', layer: 'service_client', description: 'Cache-specific requests extension'})
CREATE (fileStore:Class {name: 'Service__Client__File__Store', layer: 'service_client', description: 'Domain operation for storing files'})

// Create Service Layer Nodes
CREATE (registerCache:Function {name: 'register_cache_service', layer: 'service', description: 'Registration helper for IN_MEMORY mode'})

// Create Mode Nodes
CREATE (inMemory:Mode {name: 'IN_MEMORY', description: 'Uses TestClient with FastAPI app'})
CREATE (remote:Mode {name: 'REMOTE', description: 'Uses HTTP requests library'})

// Create Pattern Nodes
CREATE (statelessFacade:Pattern {name: 'Stateless Facade', description: 'Client without internal config state'})
CREATE (configStore:Pattern {name: 'Config Store', description: 'Registry stores configs keyed by client type'})

// Create Package Nodes
CREATE (osbotFastApi:Package {name: 'osbot_fast_api', type: 'infrastructure'})
CREATE (cacheClientPkg:Package {name: 'mgraph_ai_service_cache_client', type: 'client'})
CREATE (cachePkg:Package {name: 'mgraph_ai_service_cache', type: 'service'})

// Create Utility Nodes
CREATE (testClient:Class {name: 'TestClient', layer: 'external', description: 'Starlette test client'})
CREATE (session:Class {name: 'requests.Session', layer: 'external', description: 'HTTP session for remote calls'})
CREATE (cacheOnSelf:Decorator {name: '@cache_on_self', description: 'Caches method result on instance'})

// Infrastructure Relationships
CREATE (registry)-[:CONTAINS]->(configsDict)
CREATE (registry)-[:STORES]->(config)
CREATE (transport)-[:LOOKS_UP_CONFIG_FROM]->(registry)
CREATE (transport)-[:USES_FOR {mode: 'IN_MEMORY'}]->(testClient)
CREATE (transport)-[:USES_FOR {mode: 'REMOTE'}]->(session)
CREATE (baseClient)-[:DEPENDS_ON]->(transport)

// Service Client Relationships
CREATE (cacheClient)-[:EXTENDS]->(baseClient)
CREATE (cacheClient)-[:CREATES]->(cacheRequests)
CREATE (cacheClient)-[:IMPLEMENTS]->(statelessFacade)
CREATE (cacheRequests)-[:EXTENDS]->(transport)
CREATE (fileStore)-[:RECEIVES]->(cacheRequests)

// Service Layer Relationships
CREATE (registerCache)-[:REGISTERS_WITH]->(registry)
CREATE (registerCache)-[:REGISTERS_CONFIG_FOR]->(cacheClient)

// Mode Relationships
CREATE (inMemory)-[:IMPLEMENTED_BY]->(testClient)
CREATE (remote)-[:IMPLEMENTED_BY]->(session)

// Package Relationships
CREATE (osbotFastApi)-[:CONTAINS]->(registry)
CREATE (osbotFastApi)-[:CONTAINS]->(transport)
CREATE (osbotFastApi)-[:CONTAINS]->(baseClient)
CREATE (cacheClientPkg)-[:CONTAINS]->(cacheClient)
CREATE (cacheClientPkg)-[:DEPENDS_ON]->(osbotFastApi)
CREATE (cachePkg)-[:CONTAINS]->(registerCache)
CREATE (cachePkg)-[:DEPENDS_ON]->(cacheClientPkg)

// Pattern Relationships
CREATE (statelessFacade)-[:ENABLED_BY]->(configStore)
CREATE (configStore)-[:IMPLEMENTED_BY]->(registry)

// Decorator Relationships
CREATE (cacheOnSelf)-[:DECORATES]->(transport)
CREATE (cacheOnSelf)-[:DECORATES]->(cacheClient)

// Evolution Relationships (v1 -> v2)
CREATE (v1:ArchitectureVersion {name: 'v1', description: 'Instance storage in registry'})
CREATE (v2:ArchitectureVersion {name: 'v2', description: 'Config storage keyed by client type'})
CREATE (v1)-[:EVOLVED_TO {reason: 'Eliminated circular dependencies, enabled stateless clients'}]->(v2)
CREATE (v2)-[:IMPLEMENTS]->(configStore)
CREATE (v2)-[:ENABLES]->(statelessFacade)
```

---

## Key Insights Summary

1. **Registry stores CONFIGS keyed by client type** - not instances
2. **Client classes become STATELESS FACADES** - no internal config state
3. **Domain operations receive `requests` not `_client`** - breaks circular dependencies
4. **`@cache_on_self` on config()** - single registry lookup per request chain
5. **Registration is a function, not a class** - no state needed, just wiring
6. **Package dependencies unchanged** - architecture works within existing constraints
