# Semantic Graph: Unified Service Client Architecture

## Key Concepts

### Registry Pattern
A centralized service discovery mechanism that manages client registration and retrieval. The `Fast_API__Service__Registry` maintains a dictionary of registered service clients, allowing application code to discover and access clients by type without knowing their implementation details.

### Generic Transport
The `Fast_API__Client__Requests` class provides a service-agnostic transport layer that handles HTTP communication. It abstracts away the differences between in-memory and remote execution, providing a unified interface for all service clients.

### IN_MEMORY Mode
A testing mode that uses Starlette's `TestClient` to communicate directly with a FastAPI application instance. This enables fast, isolated testing without network overhead, with ~100ms startup time for a full service stack.

### REMOTE Mode
A production mode that uses Python's `requests` library to communicate with remote HTTP endpoints. Configuration includes base URL, API key name, and API key value for authenticated requests.

### TestClient vs HTTP
- **TestClient**: Starlette's test client that directly invokes FastAPI routes without network I/O
- **HTTP**: Standard HTTP requests using the `requests` library for network communication

---

## Ontology

### Domain: Microservice Client Architecture

#### Core Classes
- **ServiceRegistry** - Central registry for service client management
- **ServiceClient** - Domain-specific client for a microservice
- **TransportLayer** - Abstraction for request execution
- **ClientConfig** - Configuration schema for service clients

#### Properties
- **mode** - Execution mode (IN_MEMORY or REMOTE)
- **base_url** - Remote service endpoint URL
- **fast_api_app** - FastAPI application instance for IN_MEMORY mode
- **api_key_name** - HTTP header name for authentication
- **api_key_value** - Authentication token value

#### Relationships
- ServiceClient **extends** RegistryClientBase
- ServiceClient **uses** TransportLayer
- TransportLayer **reads** ClientConfig
- ServiceRegistry **manages** ServiceClient
- ClientPackage **depends_on** InfrastructurePackage
- ServicePackage **depends_on** ClientPackage

---

## Taxonomy

```
Unified Service Client Architecture
├── Infrastructure Layer (osbot_fast_api)
│   ├── Registry
│   │   ├── Fast_API__Service__Registry
│   │   ├── Fast_API__Service__Registry__Client__Base
│   │   └── Fast_API__Service__Registry__Client__Config
│   ├── Transport
│   │   └── Fast_API__Client__Requests
│   └── Enums
│       └── Enum__Fast_API__Service__Registry__Client__Mode
│           ├── IN_MEMORY
│           └── REMOTE
├── Service Client Layer (*_client packages)
│   ├── Cache Service Client
│   │   ├── Cache__Service__Client
│   │   ├── Cache__Service__Client__Requests
│   │   └── Domain Operations
│   │       ├── store()
│   │       ├── retrieve()
│   │       ├── delete()
│   │       ├── exists()
│   │       ├── namespace()
│   │       └── namespaces()
│   ├── Html Service Client
│   │   └── Html__Service__Client
│   ├── Html Graph Service Client
│   │   └── Html_Graph__Service__Client
│   └── LLM Service Client
│       └── LLM__Service__Client
├── Service Layer (* packages)
│   └── Registration Helpers
│       └── register_*_service()
└── Application Layer
    └── Business Logic
        └── Service Consumers
```

---

## Knowledge Graph

### Nodes

| ID | Label | Type | Description |
|----|-------|------|-------------|
| N1 | Fast_API__Service__Registry | Class | Central registry for service client discovery and management |
| N2 | Fast_API__Service__Registry__Client__Base | Class | Base class that all service clients extend |
| N3 | Fast_API__Service__Registry__Client__Config | Schema | Configuration schema for service client settings |
| N4 | Fast_API__Client__Requests | Class | Generic transport layer for IN_MEMORY and REMOTE modes |
| N5 | Cache__Service__Client | Class | Unified cache service client |
| N6 | Cache__Service__Client__Requests | Class | Cache-specific transport extending generic transport |
| N7 | Html__Service__Client | Class | HTML service client |
| N8 | Html_Graph__Service__Client | Class | HTML graph service client |
| N9 | LLM__Service__Client | Class | LLM service client |
| N10 | IN_MEMORY | Mode | TestClient-based execution mode for testing |
| N11 | REMOTE | Mode | HTTP-based execution mode for production |
| N12 | TestClient | Component | Starlette's test client for direct FastAPI communication |
| N13 | requests.Session | Component | Python requests library for HTTP communication |
| N14 | osbot_fast_api | Package | Infrastructure package containing registry and transport |
| N15 | mgraph_ai_service_cache_client | Package | Cache service client package (light) |
| N16 | mgraph_ai_service_cache | Package | Cache service package (heavy) with FastAPI routes |
| N17 | register_cache_service | Function | Registration helper for IN_MEMORY mode setup |
| N18 | fast_api_app | Property | FastAPI application instance for IN_MEMORY mode |
| N19 | base_url | Property | Remote service endpoint URL |
| N20 | api_key_name | Property | HTTP header name for authentication |
| N21 | api_key_value | Property | Authentication token value |
| N22 | Application_Layer | Layer | Business logic that consumes services |
| N23 | Service_Client_Layer | Layer | Domain-specific clients |
| N24 | Infrastructure_Layer | Layer | Generic transport and registry |

### Edges

| Source | Target | Relationship | Description |
|--------|--------|--------------|-------------|
| N5 | N2 | EXTENDS | Cache__Service__Client extends registry client base |
| N7 | N2 | EXTENDS | Html__Service__Client extends registry client base |
| N8 | N2 | EXTENDS | Html_Graph__Service__Client extends registry client base |
| N9 | N2 | EXTENDS | LLM__Service__Client extends registry client base |
| N6 | N4 | EXTENDS | Cache requests extends generic requests |
| N5 | N6 | USES | Cache client uses its requests class |
| N1 | N5 | MANAGES | Registry manages cache service client |
| N1 | N7 | MANAGES | Registry manages HTML service client |
| N1 | N8 | MANAGES | Registry manages HTML graph service client |
| N1 | N9 | MANAGES | Registry manages LLM service client |
| N4 | N3 | READS | Transport layer reads configuration |
| N4 | N10 | SUPPORTS | Transport supports IN_MEMORY mode |
| N4 | N11 | SUPPORTS | Transport supports REMOTE mode |
| N10 | N12 | USES | IN_MEMORY mode uses TestClient |
| N11 | N13 | USES | REMOTE mode uses requests.Session |
| N3 | N18 | HAS_PROPERTY | Config has fast_api_app property |
| N3 | N19 | HAS_PROPERTY | Config has base_url property |
| N3 | N20 | HAS_PROPERTY | Config has api_key_name property |
| N3 | N21 | HAS_PROPERTY | Config has api_key_value property |
| N15 | N14 | DEPENDS_ON | Client package depends on infrastructure |
| N16 | N15 | DEPENDS_ON | Service package depends on client package |
| N17 | N16 | BELONGS_TO | Registration function belongs to service package |
| N22 | N23 | CALLS | Application layer calls service client layer |
| N23 | N24 | CALLS | Service client layer calls infrastructure layer |
| N14 | N24 | IMPLEMENTS | osbot_fast_api implements infrastructure layer |
| N15 | N23 | IMPLEMENTS | Client packages implement service client layer |

---

## Cypher Export

```cypher
// Create nodes
CREATE (n1:Class {id: 'N1', name: 'Fast_API__Service__Registry', description: 'Central registry for service client discovery and management'})
CREATE (n2:Class {id: 'N2', name: 'Fast_API__Service__Registry__Client__Base', description: 'Base class that all service clients extend'})
CREATE (n3:Schema {id: 'N3', name: 'Fast_API__Service__Registry__Client__Config', description: 'Configuration schema for service client settings'})
CREATE (n4:Class {id: 'N4', name: 'Fast_API__Client__Requests', description: 'Generic transport layer for IN_MEMORY and REMOTE modes'})
CREATE (n5:Class {id: 'N5', name: 'Cache__Service__Client', description: 'Unified cache service client'})
CREATE (n6:Class {id: 'N6', name: 'Cache__Service__Client__Requests', description: 'Cache-specific transport extending generic transport'})
CREATE (n7:Class {id: 'N7', name: 'Html__Service__Client', description: 'HTML service client'})
CREATE (n8:Class {id: 'N8', name: 'Html_Graph__Service__Client', description: 'HTML graph service client'})
CREATE (n9:Class {id: 'N9', name: 'LLM__Service__Client', description: 'LLM service client'})
CREATE (n10:Mode {id: 'N10', name: 'IN_MEMORY', description: 'TestClient-based execution mode for testing'})
CREATE (n11:Mode {id: 'N11', name: 'REMOTE', description: 'HTTP-based execution mode for production'})
CREATE (n12:Component {id: 'N12', name: 'TestClient', description: 'Starlette test client for direct FastAPI communication'})
CREATE (n13:Component {id: 'N13', name: 'requests.Session', description: 'Python requests library for HTTP communication'})
CREATE (n14:Package {id: 'N14', name: 'osbot_fast_api', description: 'Infrastructure package containing registry and transport'})
CREATE (n15:Package {id: 'N15', name: 'mgraph_ai_service_cache_client', description: 'Cache service client package (light)'})
CREATE (n16:Package {id: 'N16', name: 'mgraph_ai_service_cache', description: 'Cache service package (heavy) with FastAPI routes'})
CREATE (n17:Function {id: 'N17', name: 'register_cache_service', description: 'Registration helper for IN_MEMORY mode setup'})
CREATE (n18:Property {id: 'N18', name: 'fast_api_app', description: 'FastAPI application instance for IN_MEMORY mode'})
CREATE (n19:Property {id: 'N19', name: 'base_url', description: 'Remote service endpoint URL'})
CREATE (n20:Property {id: 'N20', name: 'api_key_name', description: 'HTTP header name for authentication'})
CREATE (n21:Property {id: 'N21', name: 'api_key_value', description: 'Authentication token value'})
CREATE (n22:Layer {id: 'N22', name: 'Application_Layer', description: 'Business logic that consumes services'})
CREATE (n23:Layer {id: 'N23', name: 'Service_Client_Layer', description: 'Domain-specific clients'})
CREATE (n24:Layer {id: 'N24', name: 'Infrastructure_Layer', description: 'Generic transport and registry'})

// Create relationships - Class inheritance
CREATE (n5)-[:EXTENDS]->(n2)
CREATE (n7)-[:EXTENDS]->(n2)
CREATE (n8)-[:EXTENDS]->(n2)
CREATE (n9)-[:EXTENDS]->(n2)
CREATE (n6)-[:EXTENDS]->(n4)

// Create relationships - Usage
CREATE (n5)-[:USES]->(n6)
CREATE (n4)-[:READS]->(n3)

// Create relationships - Registry management
CREATE (n1)-[:MANAGES]->(n5)
CREATE (n1)-[:MANAGES]->(n7)
CREATE (n1)-[:MANAGES]->(n8)
CREATE (n1)-[:MANAGES]->(n9)

// Create relationships - Mode support
CREATE (n4)-[:SUPPORTS]->(n10)
CREATE (n4)-[:SUPPORTS]->(n11)
CREATE (n10)-[:USES]->(n12)
CREATE (n11)-[:USES]->(n13)

// Create relationships - Properties
CREATE (n3)-[:HAS_PROPERTY]->(n18)
CREATE (n3)-[:HAS_PROPERTY]->(n19)
CREATE (n3)-[:HAS_PROPERTY]->(n20)
CREATE (n3)-[:HAS_PROPERTY]->(n21)

// Create relationships - Package dependencies
CREATE (n15)-[:DEPENDS_ON]->(n14)
CREATE (n16)-[:DEPENDS_ON]->(n15)
CREATE (n17)-[:BELONGS_TO]->(n16)

// Create relationships - Layer architecture
CREATE (n22)-[:CALLS]->(n23)
CREATE (n23)-[:CALLS]->(n24)
CREATE (n14)-[:IMPLEMENTS]->(n24)
CREATE (n15)-[:IMPLEMENTS]->(n23)
```

---

## Query Examples

### Find all service clients
```cypher
MATCH (client:Class)-[:EXTENDS]->(base:Class {name: 'Fast_API__Service__Registry__Client__Base'})
RETURN client.name AS ServiceClient
```

### Find transport mode dependencies
```cypher
MATCH (transport:Class {name: 'Fast_API__Client__Requests'})-[:SUPPORTS]->(mode:Mode)-[:USES]->(component:Component)
RETURN mode.name AS Mode, component.name AS Component
```

### Find package dependency chain
```cypher
MATCH path = (heavy:Package)-[:DEPENDS_ON*]->(infra:Package {name: 'osbot_fast_api'})
RETURN [node in nodes(path) | node.name] AS DependencyChain
```

### Find all properties of client config
```cypher
MATCH (config:Schema {name: 'Fast_API__Service__Registry__Client__Config'})-[:HAS_PROPERTY]->(prop:Property)
RETURN prop.name AS Property, prop.description AS Description
```
