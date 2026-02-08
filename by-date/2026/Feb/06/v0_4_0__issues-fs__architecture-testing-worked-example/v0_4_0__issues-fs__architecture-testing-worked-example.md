# Architecture Testing with Semantic Graphs: A Complete Worked Example

**Document:** issues-fs__architecture-testing-worked-example  
**Version:** v1.0  
**Date:** 2026-02-05  
**Status:** Practical Guide  
**Depends On:** issues-fs__compatibility-through-connectivity v1.0, issues-fs__llm-as-execution-engine v1.0  

---

## Executive Summary

This document demonstrates the complete workflow for architecture compatibility testing using a real-world example: a **Food Delivery Platform**. We show exactly how an architect's artifacts (diagrams, text descriptions, flow charts) are transformed into testable semantic graphs, and how compatibility is assessed across these representations.

This is not theory — it's a step-by-step guide you can follow today using LLM agents as the execution engine. By the end, you'll see how to go from "boxes and arrows on a whiteboard" to "automated compatibility report."

---

## Part 1: What the Architect Creates

### The Architect's Job

An architect produces artifacts that describe how a system should work:

1. **Context Diagrams** — The system and its external actors
2. **Container Diagrams** — The major deployable units inside the system
3. **Component Diagrams** — The internal structure of containers
4. **Flow Diagrams** — How requests move through the system
5. **Data Flow Diagrams** — How data moves and where it's stored
6. **Text Descriptions** — Prose explaining each element
7. **Rules and Constraints** — Invariants that must hold

These artifacts use different "languages":
- Diagrams use visual language (boxes, arrows, containment)
- Text uses natural language (English prose)
- Rules use modal language (must, never, always)

All of them describe the same system. They should agree.

### The Food Delivery Platform

We'll use a food delivery platform as our example. This is complex enough to be realistic:
- Multiple user types (customers, restaurants, drivers)
- Multiple services (user, order, payment, notification)
- External integrations (Stripe, Twilio, Google Maps)
- Security requirements (PII handling, authentication)
- State machines (order lifecycle)

---

## Part 2: The Architect's Artifacts

### Artifact 1: System Context Diagram

```
                                    ┌─────────────────────┐
                                    │                     │
                                    │   Restaurant Staff  │
                                    │                     │
                                    └──────────┬──────────┘
                                               │ manages menu,
                                               │ accepts orders
                                               ▼
┌─────────────────────┐           ┌───────────────────────────────────────┐           ┌─────────────────────┐
│                     │           │                                       │           │                     │
│      Customer       │──────────▶│                                       │◀──────────│   Delivery Driver   │
│    (Mobile App)     │  browses, │                                       │  accepts  │    (Driver App)     │
│                     │  orders   │      FOOD DELIVERY PLATFORM           │  jobs,    │                     │
└─────────────────────┘           │                                       │  updates  └─────────────────────┘
                                  │                                       │  location
                                  └───────────────────────────────────────┘
                                         │           │           │
                                         │           │           │
                          ┌──────────────┘           │           └──────────────┐
                          │                          │                          │
                          ▼                          ▼                          ▼
               ┌─────────────────────┐    ┌─────────────────────┐    ┌─────────────────────┐
               │                     │    │                     │    │                     │
               │   Payment Gateway   │    │   SMS/Push Service  │    │   Maps/Routing      │
               │      (Stripe)       │    │     (Twilio)        │    │   (Google Maps)     │
               │                     │    │                     │    │                     │
               └─────────────────────┘    └─────────────────────┘    └─────────────────────┘
```

### Artifact 2: Container Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                                   FOOD DELIVERY PLATFORM                                 │
│                                                                                          │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐    │
│  │  Customer App   │  │ Restaurant App  │  │   Driver App    │  │   Admin Portal  │    │
│  │  (React Native) │  │   (React Web)   │  │ (React Native)  │  │   (React Web)   │    │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘    │
│           │                    │                    │                    │              │
│           └────────────────────┴────────────────────┴────────────────────┘              │
│                                          │                                              │
│                                          ▼                                              │
│                          ┌───────────────────────────────┐                              │
│                          │        API Gateway            │                              │
│                          │          (Kong)               │                              │
│                          └───────────────┬───────────────┘                              │
│                                          │                                              │
│       ┌──────────────┬──────────────┬────┴────┬──────────────┬──────────────┐          │
│       ▼              ▼              ▼         ▼              ▼              ▼          │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐ ┌─────────┐  ┌─────────┐   ┌─────────┐       │
│  │  User   │   │  Menu   │   │  Order  │ │ Driver  │  │ Payment │   │  Notif  │       │
│  │ Service │   │ Service │   │ Service │ │ Service │  │ Service │   │ Service │       │
│  └────┬────┘   └────┬────┘   └────┬────┘ └────┬────┘  └────┬────┘   └────┬────┘       │
│       │             │             │           │            │             │             │
│       ▼             ▼             ▼           ▼            │             │             │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐ ┌─────────┐       │             │             │
│  │ User DB │   │ Menu DB │   │Order DB │ │  Redis  │       │             │             │
│  │(Postgres)│  │(Postgres)│  │(Postgres)│ │ (Cache) │       │             │             │
│  └─────────┘   └─────────┘   └─────────┘ └─────────┘       │             │             │
│                                                            │             │             │
│                                                      ┌─────┴─────────────┴─────┐       │
│                                                      │        Kafka            │       │
│                                                      │       (Events)          │       │
│                                                      └─────────────────────────┘       │
└─────────────────────────────────────────────────────────────────────────────────────────┘
```

### Artifact 3: Text Description — API Gateway

```
API Gateway (Kong) — The single entry point for all client traffic. Handles 
authentication (JWT validation), rate limiting (100 req/min for normal users, 
1000 for restaurants), and routes requests to appropriate services. All external 
traffic must pass through here.
```

### Artifact 4: Text Description — Order Service

```
Order Service — The heart of the system. Creates orders, manages order state 
(placed → accepted → preparing → ready → picked up → delivered), calculates 
pricing with taxes and fees, and coordinates between restaurants and drivers. 
Orders can only move forward in state — no backward transitions allowed. A 
DELIVERED order cannot be cancelled.
```

### Artifact 5: Order State Machine Diagram

```
                                    ┌──────────────┐
                                    │   CREATED    │
                                    └──────┬───────┘
                                           │ payment succeeds
                                           ▼
                    ┌──────────────────────────────────────────┐
                    │                 PLACED                    │
                    └──────────────────────┬───────────────────┘
                                           │ restaurant accepts
                                           ▼
                    ┌──────────────────────────────────────────┐
                    │               ACCEPTED                    │
                    └──────────────────────┬───────────────────┘
                                           │ restaurant starts cooking
                                           ▼
                    ┌──────────────────────────────────────────┐
                    │               PREPARING                   │
                    └──────────────────────┬───────────────────┘
                                           │ food is ready
                                           ▼
                    ┌──────────────────────────────────────────┐
                    │            READY_FOR_PICKUP              │
                    └──────────────────────┬───────────────────┘
                                           │ driver picks up
                                           ▼
                    ┌──────────────────────────────────────────┐
                    │               PICKED_UP                   │
                    └──────────────────────┬───────────────────┘
                                           │ driver delivers
                                           ▼
                    ┌──────────────────────────────────────────┐
                    │               DELIVERED                   │
                    └──────────────────────────────────────────┘
```

### Artifact 6: PII Data Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                                    PII DATA FLOW                                         │
│                                                                                          │
│   COLLECTION                    STORAGE                      USAGE                       │
│                                                                                          │
│  ┌───────────────┐         ┌───────────────┐                                            │
│  │  Customer     │────────▶│   User DB     │                                            │
│  │  Registration │         │  - name       │                                            │
│  │  • name       │         │  - email      │         ┌───────────────┐                  │
│  │  • email      │         │  - phone_hash │────────▶│  Order        │                  │
│  │  • phone      │         │  - addresses  │         │  Confirmation │                  │
│  └───────────────┘         └───────────────┘         └───────────────┘                  │
│                                                                                          │
│  ┌───────────────┐         ┌───────────────┐         ┌───────────────┐                  │
│  │  Payment      │         │   Stripe      │         │  We NEVER     │                  │
│  │  Method       │────────▶│  (external)   │         │  store card   │                  │
│  │  • card number│         │  Returns:     │────────▶│  numbers      │                  │
│  │  • expiry     │         │  • token      │         │               │                  │
│  │  • CVV        │         │  • last4      │         │  User DB has  │                  │
│  └───────────────┘         └───────────────┘         │  token only   │                  │
│                                                      └───────────────┘                  │
│                                                                                          │
│   SECURITY RULES:                                                                        │
│   • Card numbers NEVER enter our system - Stripe handles directly                       │
│   • Phone numbers stored hashed in User DB, plain only in active Order DB               │
│   • Delivery address visible to driver only during active delivery                       │
│                                                                                          │
└─────────────────────────────────────────────────────────────────────────────────────────┘
```

### Artifact 7: Architecture Rules (Text)

```
ARCHITECTURE RULES

Traffic Rules:
- All external traffic enters through the Application Load Balancer
- All client requests pass through the API Gateway (no direct service access)
- Services communicate with each other via internal service mesh, not via public gateway

Data Rules:
- Customer PII is stored only in User DB and Order DB
- Card numbers never enter our system — Stripe handles all payment data
- Driver location data is ephemeral (Redis only, not persisted to DB)

Security Rules:
- All inter-service communication is authenticated (mTLS)
- No service has direct internet access (outbound via NAT Gateway only)
- Admin Portal requires MFA

State Machine Rules:
- Orders can only move forward in state (no backward transitions)
- DELIVERED orders cannot be cancelled
- Payment must succeed before order reaches PLACED state
```

---

## Part 3: Extracting Semantic Graphs

Now we transform each artifact into a graph. In practice, we use LLM agents as the execution engine (see "LLM as Execution Engine" document).

### Step 1: Extract from Context Diagram

**Prompt to DiagramExtractor Agent:**

```
You are the DiagramExtractor. Extract a graph from this system context diagram.

DIAGRAM:
[paste context diagram ASCII art]

OUTPUT FORMAT:
{
  "nodes": [...],
  "edges": [...],
  "inferred_rules": [...]
}
```

**Agent Output:**

```json
{
  "nodes": [
    {
      "id": "actor-customer",
      "type": "Actor",
      "label": "Customer",
      "properties": {"interface": "Mobile App"}
    },
    {
      "id": "actor-restaurant",
      "type": "Actor", 
      "label": "Restaurant Staff",
      "properties": {}
    },
    {
      "id": "actor-driver",
      "type": "Actor",
      "label": "Delivery Driver",
      "properties": {"interface": "Driver App"}
    },
    {
      "id": "system-platform",
      "type": "System",
      "label": "Food Delivery Platform",
      "properties": {"internal": true}
    },
    {
      "id": "external-stripe",
      "type": "External_System",
      "label": "Payment Gateway (Stripe)",
      "properties": {}
    },
    {
      "id": "external-twilio",
      "type": "External_System",
      "label": "SMS/Push Service (Twilio)",
      "properties": {}
    },
    {
      "id": "external-maps",
      "type": "External_System",
      "label": "Maps/Routing (Google Maps)",
      "properties": {}
    }
  ],
  "edges": [
    {
      "source": "actor-customer",
      "target": "system-platform",
      "type": "uses",
      "label": "browses, orders"
    },
    {
      "source": "actor-restaurant",
      "target": "system-platform",
      "type": "uses",
      "label": "manages menu, accepts orders"
    },
    {
      "source": "actor-driver",
      "target": "system-platform",
      "type": "uses",
      "label": "accepts jobs, updates location"
    },
    {
      "source": "system-platform",
      "target": "external-stripe",
      "type": "integrates_with",
      "label": "payment processing"
    },
    {
      "source": "system-platform",
      "target": "external-twilio",
      "type": "integrates_with",
      "label": "notifications"
    },
    {
      "source": "system-platform",
      "target": "external-maps",
      "type": "integrates_with",
      "label": "routing"
    }
  ],
  "inferred_rules": [
    {
      "id": "context-rule-1",
      "type": "boundary",
      "description": "All actor interactions go through the platform boundary",
      "evidence": "All actor arrows point to the platform, not to external systems directly"
    }
  ]
}
```

### Step 2: Extract from Container Diagram

**Prompt to DiagramExtractor Agent:**

```
You are the DiagramExtractor. Extract a graph from this container diagram.

DIAGRAM:
[paste container diagram ASCII art]

Pay special attention to:
- Which containers are inside the platform boundary
- Which containers connect to which
- Any gateway/entry point patterns
```

**Agent Output:**

```json
{
  "nodes": [
    {
      "id": "app-customer",
      "type": "Container",
      "label": "Customer App",
      "properties": {"technology": "React Native", "category": "frontend"}
    },
    {
      "id": "app-restaurant",
      "type": "Container",
      "label": "Restaurant App",
      "properties": {"technology": "React Web", "category": "frontend"}
    },
    {
      "id": "app-driver",
      "type": "Container",
      "label": "Driver App",
      "properties": {"technology": "React Native", "category": "frontend"}
    },
    {
      "id": "app-admin",
      "type": "Container",
      "label": "Admin Portal",
      "properties": {"technology": "React Web", "category": "frontend"}
    },
    {
      "id": "gateway",
      "type": "Container",
      "label": "API Gateway",
      "properties": {"technology": "Kong", "category": "infrastructure"}
    },
    {
      "id": "svc-user",
      "type": "Container",
      "label": "User Service",
      "properties": {"category": "backend"}
    },
    {
      "id": "svc-menu",
      "type": "Container",
      "label": "Menu Service",
      "properties": {"category": "backend"}
    },
    {
      "id": "svc-order",
      "type": "Container",
      "label": "Order Service",
      "properties": {"category": "backend"}
    },
    {
      "id": "svc-driver",
      "type": "Container",
      "label": "Driver Service",
      "properties": {"category": "backend"}
    },
    {
      "id": "svc-payment",
      "type": "Container",
      "label": "Payment Service",
      "properties": {"category": "backend"}
    },
    {
      "id": "svc-notif",
      "type": "Container",
      "label": "Notification Service",
      "properties": {"category": "backend"}
    },
    {
      "id": "db-user",
      "type": "DataStore",
      "label": "User DB",
      "properties": {"technology": "Postgres"}
    },
    {
      "id": "db-menu",
      "type": "DataStore",
      "label": "Menu DB",
      "properties": {"technology": "Postgres"}
    },
    {
      "id": "db-order",
      "type": "DataStore",
      "label": "Order DB",
      "properties": {"technology": "Postgres"}
    },
    {
      "id": "cache-redis",
      "type": "DataStore",
      "label": "Redis",
      "properties": {"technology": "Redis", "purpose": "Cache"}
    },
    {
      "id": "queue-kafka",
      "type": "MessageQueue",
      "label": "Kafka",
      "properties": {"technology": "Kafka", "purpose": "Events"}
    }
  ],
  "edges": [
    {"source": "app-customer", "target": "gateway", "type": "connects_to"},
    {"source": "app-restaurant", "target": "gateway", "type": "connects_to"},
    {"source": "app-driver", "target": "gateway", "type": "connects_to"},
    {"source": "app-admin", "target": "gateway", "type": "connects_to"},
    {"source": "gateway", "target": "svc-user", "type": "routes_to"},
    {"source": "gateway", "target": "svc-menu", "type": "routes_to"},
    {"source": "gateway", "target": "svc-order", "type": "routes_to"},
    {"source": "gateway", "target": "svc-driver", "type": "routes_to"},
    {"source": "gateway", "target": "svc-payment", "type": "routes_to"},
    {"source": "gateway", "target": "svc-notif", "type": "routes_to"},
    {"source": "svc-user", "target": "db-user", "type": "reads_writes"},
    {"source": "svc-menu", "target": "db-menu", "type": "reads_writes"},
    {"source": "svc-order", "target": "db-order", "type": "reads_writes"},
    {"source": "svc-driver", "target": "cache-redis", "type": "reads_writes"},
    {"source": "svc-payment", "target": "queue-kafka", "type": "publishes_to"},
    {"source": "svc-notif", "target": "queue-kafka", "type": "subscribes_to"}
  ],
  "inferred_rules": [
    {
      "id": "container-rule-1",
      "type": "gateway_pattern",
      "description": "All frontend apps connect only to API Gateway",
      "evidence": "No arrows from frontend apps to backend services directly"
    },
    {
      "id": "container-rule-2",
      "type": "gateway_pattern",
      "description": "API Gateway is the single entry point to backend services",
      "evidence": "All backend services receive connections only from gateway"
    }
  ]
}
```

### Step 3: Extract from Text Descriptions

**Prompt to TextExtractor Agent:**

```
You are the TextExtractor. Extract a semantic graph from this architecture description.

TEXT:
API Gateway (Kong) — The single entry point for all client traffic. Handles 
authentication (JWT validation), rate limiting (100 req/min for normal users, 
1000 for restaurants), and routes requests to appropriate services. All external 
traffic must pass through here.

ONTOLOGY: architecture-v1
Node types: Component, Technology, Rule, Constraint, Responsibility
Edge types: uses, implements, enforces, contains

Extract all entities, relationships, and especially RULES (must/never/always statements).
```

**Agent Output:**

```json
{
  "nodes": [
    {
      "id": "text-comp-gateway",
      "type": "Component",
      "label": "API Gateway",
      "source_span": [0, 11],
      "properties": {
        "role": "single entry point",
        "scope": "all client traffic"
      }
    },
    {
      "id": "text-tech-kong",
      "type": "Technology",
      "label": "Kong",
      "source_span": [13, 17],
      "properties": {}
    },
    {
      "id": "text-resp-auth",
      "type": "Responsibility",
      "label": "Authentication",
      "source_span": [76, 90],
      "properties": {"method": "JWT validation"}
    },
    {
      "id": "text-resp-ratelimit",
      "type": "Responsibility",
      "label": "Rate Limiting",
      "source_span": [107, 120],
      "properties": {}
    },
    {
      "id": "text-constraint-ratelimit-normal",
      "type": "Constraint",
      "label": "Rate Limit Normal Users",
      "source_span": [122, 145],
      "properties": {
        "limit": "100 req/min",
        "applies_to": "normal users"
      }
    },
    {
      "id": "text-constraint-ratelimit-restaurant",
      "type": "Constraint",
      "label": "Rate Limit Restaurants",
      "source_span": [147, 170],
      "properties": {
        "limit": "1000 req/min",
        "applies_to": "restaurants"
      }
    },
    {
      "id": "text-resp-routing",
      "type": "Responsibility",
      "label": "Request Routing",
      "source_span": [177, 212],
      "properties": {}
    },
    {
      "id": "text-rule-gateway",
      "type": "Rule",
      "label": "All external traffic through gateway",
      "source_span": [214, 257],
      "properties": {
        "modal": "must",
        "subject": "external traffic",
        "action": "pass through",
        "object": "here (API Gateway)",
        "full_text": "All external traffic must pass through here"
      }
    }
  ],
  "edges": [
    {"source": "text-comp-gateway", "target": "text-tech-kong", "type": "uses"},
    {"source": "text-comp-gateway", "target": "text-resp-auth", "type": "implements"},
    {"source": "text-comp-gateway", "target": "text-resp-ratelimit", "type": "implements"},
    {"source": "text-resp-ratelimit", "target": "text-constraint-ratelimit-normal", "type": "contains"},
    {"source": "text-resp-ratelimit", "target": "text-constraint-ratelimit-restaurant", "type": "contains"},
    {"source": "text-comp-gateway", "target": "text-resp-routing", "type": "implements"},
    {"source": "text-comp-gateway", "target": "text-rule-gateway", "type": "enforces"}
  ]
}
```

### Step 4: Extract from State Machine Diagram

**Prompt to DiagramExtractor Agent:**

```
You are the DiagramExtractor. Extract a state machine graph from this diagram.

DIAGRAM:
[paste state machine ASCII art]

Extract:
- All states
- All transitions (with triggers)
- Any rules about valid/invalid transitions
```

**Agent Output:**

```json
{
  "nodes": [
    {"id": "state-created", "type": "State", "label": "CREATED", "properties": {"initial": true}},
    {"id": "state-placed", "type": "State", "label": "PLACED", "properties": {}},
    {"id": "state-accepted", "type": "State", "label": "ACCEPTED", "properties": {}},
    {"id": "state-preparing", "type": "State", "label": "PREPARING", "properties": {}},
    {"id": "state-ready", "type": "State", "label": "READY_FOR_PICKUP", "properties": {}},
    {"id": "state-pickedup", "type": "State", "label": "PICKED_UP", "properties": {}},
    {"id": "state-delivered", "type": "State", "label": "DELIVERED", "properties": {"terminal": true}}
  ],
  "edges": [
    {"source": "state-created", "target": "state-placed", "type": "transition", "label": "payment succeeds"},
    {"source": "state-placed", "target": "state-accepted", "type": "transition", "label": "restaurant accepts"},
    {"source": "state-accepted", "target": "state-preparing", "type": "transition", "label": "restaurant starts cooking"},
    {"source": "state-preparing", "target": "state-ready", "type": "transition", "label": "food is ready"},
    {"source": "state-ready", "target": "state-pickedup", "type": "transition", "label": "driver picks up"},
    {"source": "state-pickedup", "target": "state-delivered", "type": "transition", "label": "driver delivers"}
  ],
  "inferred_rules": [
    {
      "id": "sm-rule-1",
      "type": "state_machine_constraint",
      "description": "All transitions are forward-only (no backward arrows)",
      "evidence": "Every transition arrow points downward in the sequence"
    },
    {
      "id": "sm-rule-2",
      "type": "state_machine_constraint", 
      "description": "DELIVERED is a terminal state (no outgoing transitions)",
      "evidence": "No arrows leave the DELIVERED state"
    }
  ]
}
```

### Step 5: Extract Rules from Architecture Rules Text

**Prompt to TextExtractor Agent:**

```
You are the TextExtractor. Extract ALL rules from this architecture rules document.

TEXT:
[paste Architecture Rules text]

Focus on extracting every rule statement. For each rule, identify:
- The subject (what the rule is about)
- The modal (must, never, always, etc.)
- The constraint (what must/must not happen)
- The category (traffic, data, security, state machine)
```

**Agent Output:**

```json
{
  "rules": [
    {
      "id": "rule-traffic-1",
      "category": "traffic",
      "text": "All external traffic enters through the Application Load Balancer",
      "subject": "external traffic",
      "modal": "must",
      "constraint": "enter through ALB"
    },
    {
      "id": "rule-traffic-2",
      "category": "traffic",
      "text": "All client requests pass through the API Gateway",
      "subject": "client requests",
      "modal": "must",
      "constraint": "pass through API Gateway"
    },
    {
      "id": "rule-traffic-3",
      "category": "traffic",
      "text": "Services communicate via internal service mesh, not via public gateway",
      "subject": "service-to-service communication",
      "modal": "must",
      "constraint": "use internal mesh, not public gateway"
    },
    {
      "id": "rule-data-1",
      "category": "data",
      "text": "Customer PII is stored only in User DB and Order DB",
      "subject": "customer PII",
      "modal": "must",
      "constraint": "stored only in User DB and Order DB"
    },
    {
      "id": "rule-data-2",
      "category": "data",
      "text": "Card numbers never enter our system",
      "subject": "card numbers",
      "modal": "never",
      "constraint": "enter our system"
    },
    {
      "id": "rule-data-3",
      "category": "data",
      "text": "Driver location data is ephemeral (Redis only)",
      "subject": "driver location data",
      "modal": "must",
      "constraint": "be ephemeral, Redis only"
    },
    {
      "id": "rule-security-1",
      "category": "security",
      "text": "All inter-service communication is authenticated (mTLS)",
      "subject": "inter-service communication",
      "modal": "must",
      "constraint": "be authenticated with mTLS"
    },
    {
      "id": "rule-security-2",
      "category": "security",
      "text": "No service has direct internet access",
      "subject": "services",
      "modal": "never",
      "constraint": "have direct internet access"
    },
    {
      "id": "rule-security-3",
      "category": "security",
      "text": "Admin Portal requires MFA",
      "subject": "Admin Portal",
      "modal": "must",
      "constraint": "require MFA"
    },
    {
      "id": "rule-statemachine-1",
      "category": "state_machine",
      "text": "Orders can only move forward in state",
      "subject": "orders",
      "modal": "must",
      "constraint": "move forward only"
    },
    {
      "id": "rule-statemachine-2",
      "category": "state_machine",
      "text": "DELIVERED orders cannot be cancelled",
      "subject": "DELIVERED orders",
      "modal": "cannot",
      "constraint": "be cancelled"
    },
    {
      "id": "rule-statemachine-3",
      "category": "state_machine",
      "text": "Payment must succeed before order reaches PLACED state",
      "subject": "payment",
      "modal": "must",
      "constraint": "succeed before PLACED"
    }
  ]
}
```

---

## Part 4: Running Compatibility Checks

Now we have graphs from:
- Context Diagram
- Container Diagram
- Text Descriptions
- State Machine Diagram
- Rules Document

We can check compatibility for each rule.

### Compatibility Check 1: Gateway Rule

**Rule:** "All external traffic must pass through API Gateway"

**Prompt to CompatibilityEngine Agent:**

```
You are the CompatibilityEngine. Assess compatibility for this rule across the extracted graphs.

RULE: "All external traffic must pass through API Gateway"

CONTEXT_DIAGRAM_GRAPH:
[paste context diagram JSON]

CONTAINER_DIAGRAM_GRAPH:
[paste container diagram JSON]

TEXT_GRAPH:
[paste text extraction JSON]

Assess:
1. How is this rule expressed in each artifact?
2. Do they agree?
3. Any divergences?
```

**Agent Output:**

```json
{
  "rule": "All external traffic must pass through API Gateway",
  "rule_id": "rule-traffic-2",
  
  "assessments": {
    "context_diagram": {
      "status": "IMPLICIT",
      "finding": "Actors connect to 'Food Delivery Platform' as a single box. Gateway not visible at this level.",
      "supports_rule": "NEUTRAL",
      "note": "Context level doesn't show internal routing - expected"
    },
    "container_diagram": {
      "status": "FOUND",
      "finding": "All frontend apps (Customer, Restaurant, Driver, Admin) connect to API Gateway. API Gateway connects to all backend services. No frontend→backend direct connections.",
      "supports_rule": "YES",
      "evidence": [
        "Edge: app-customer → gateway",
        "Edge: app-restaurant → gateway", 
        "Edge: app-driver → gateway",
        "Edge: app-admin → gateway",
        "No edges from app-* to svc-* directly"
      ]
    },
    "text_description": {
      "status": "FOUND",
      "finding": "Explicitly states: 'All external traffic must pass through here'",
      "supports_rule": "YES",
      "evidence": ["Node: text-rule-gateway with modal='must', action='pass through'"]
    }
  },
  
  "compatibility": {
    "status": "COMPATIBLE",
    "summary": "Container diagram structure enforces what text description states. Context diagram is neutral (appropriate for that zoom level).",
    "divergences": [],
    "confidence": "HIGH"
  }
}
```

### Compatibility Check 2: Forward-Only State Machine

**Rule:** "Orders can only move forward in state"

**Prompt to CompatibilityEngine Agent:**

```
You are the CompatibilityEngine. Assess compatibility for this rule.

RULE: "Orders can only move forward in state"

STATE_MACHINE_GRAPH:
[paste state machine JSON]

TEXT_GRAPH (Order Service description):
[paste order service text extraction]

RULES_GRAPH:
[paste rules extraction JSON]

Assess compatibility.
```

**Agent Output:**

```json
{
  "rule": "Orders can only move forward in state",
  "rule_id": "rule-statemachine-1",
  
  "assessments": {
    "state_machine_diagram": {
      "status": "FOUND",
      "finding": "All transitions go in one direction (CREATED→PLACED→ACCEPTED→PREPARING→READY→PICKED_UP→DELIVERED). No backward arrows.",
      "supports_rule": "YES",
      "evidence": [
        "Inferred rule sm-rule-1: 'All transitions are forward-only'",
        "7 states, 6 transitions, all unidirectional"
      ]
    },
    "text_description": {
      "status": "FOUND",
      "finding": "Text states 'manages order state (placed → accepted → preparing → ready → picked up → delivered)' and 'Orders can only move forward in state — no backward transitions allowed'",
      "supports_rule": "YES",
      "evidence": ["Explicit statement with modal 'can only'"]
    },
    "rules_document": {
      "status": "FOUND",
      "finding": "Rule explicitly listed: 'Orders can only move forward in state (no backward transitions)'",
      "supports_rule": "YES",
      "evidence": ["rule-statemachine-1"]
    }
  },
  
  "compatibility": {
    "status": "COMPATIBLE",
    "summary": "All three artifacts agree: state machine shows forward-only transitions, text describes it, rules document mandates it.",
    "divergences": [],
    "confidence": "HIGH"
  }
}
```

### Compatibility Check 3: Card Numbers Never Stored

**Rule:** "Card numbers never enter our system"

**Prompt to CompatibilityEngine Agent:**

```
You are the CompatibilityEngine. Assess compatibility for this rule.

RULE: "Card numbers never enter our system"

PII_DATAFLOW_GRAPH:
[extract from PII diagram]

CONTAINER_GRAPH:
[paste container diagram JSON]

RULES_GRAPH:
[paste rules extraction JSON]

Assess compatibility.
```

**Agent Output:**

```json
{
  "rule": "Card numbers never enter our system",
  "rule_id": "rule-data-2",
  
  "assessments": {
    "pii_dataflow_diagram": {
      "status": "FOUND",
      "finding": "Diagram shows: Payment Method (card number, expiry, CVV) → Stripe (external) → Returns: token, last4. Explicit note: 'We NEVER store card numbers' and 'User DB has token only'",
      "supports_rule": "YES",
      "evidence": [
        "Card data flow goes to external Stripe, not to internal DB",
        "Only token returns to our system"
      ]
    },
    "container_diagram": {
      "status": "IMPLICIT",
      "finding": "Payment Service exists but diagram doesn't show data content. Stripe is shown as external integration.",
      "supports_rule": "NEUTRAL",
      "note": "Container diagram doesn't show data fields - need PII diagram or code for verification"
    },
    "rules_document": {
      "status": "FOUND",
      "finding": "Rule explicitly listed: 'Card numbers never enter our system — Stripe handles all payment data'",
      "supports_rule": "YES",
      "evidence": ["rule-data-2 with modal='never'"]
    }
  },
  
  "compatibility": {
    "status": "COMPATIBLE",
    "summary": "PII diagram and rules document explicitly agree. Container diagram is neutral (doesn't show data content, which is expected at that level).",
    "divergences": [],
    "confidence": "HIGH"
  },
  
  "recommendations": [
    "To fully verify, would need to check code: Payment Service should never have card_number field",
    "Runtime verification: scan logs for card number patterns"
  ]
}
```

---

## Part 5: The Full Compatibility Report

**Prompt to ReportGenerator Agent:**

```
You are the ReportGenerator. Generate the full compatibility report from these assessment results.

ASSESSMENTS:
[paste all compatibility check results]

Generate a comprehensive report in the standard format.
```

**Agent Output:**

```
═══════════════════════════════════════════════════════════════════════════════════════
                        ARCHITECTURE COMPATIBILITY REPORT
                        Food Delivery Platform v2.1
                        Generated: 2026-02-05 15:45:00 UTC
═══════════════════════════════════════════════════════════════════════════════════════

ARTIFACTS ANALYZED
──────────────────────────────────────────────────────────────────────────────────────
  • System Context Diagram (context-v1.ascii)
  • Container Diagram (containers-v1.ascii)
  • API Gateway Description (architecture.md, Section 2)
  • Order Service Description (architecture.md, Section 3)
  • Order State Machine Diagram (state-machine-v1.ascii)
  • PII Data Flow Diagram (pii-flow-v1.ascii)
  • Architecture Rules Document (architecture.md, Section 7)

RULES EXTRACTED: 12
──────────────────────────────────────────────────────────────────────────────────────

═══════════════════════════════════════════════════════════════════════════════════════
                                COMPATIBILITY RESULTS
═══════════════════════════════════════════════════════════════════════════════════════

RULE 1: "All external traffic enters through Application Load Balancer"
Category: Traffic
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Context Diagram       NEUTRAL     Shows actors → platform, ALB not visible at this level
  Container Diagram     IMPLICIT    Shows gateway but not ALB (infrastructure detail)
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: PARTIAL - Rule stated but not visually shown in diagrams
  Recommendation: Add deployment diagram showing ALB → Gateway → Services
───────────────────────────────────────────────────────────────────────────────────────

RULE 2: "All client requests pass through API Gateway"
Category: Traffic
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Container Diagram     FOUND       All apps → Gateway → Services, no bypass paths
  Text Description      EXPLICIT    "single entry point for all client traffic"
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: HIGH
───────────────────────────────────────────────────────────────────────────────────────

RULE 3: "Services communicate via internal mesh, not public gateway"
Category: Traffic
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Container Diagram     IMPLICIT    No service→gateway→service paths shown
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: PARTIAL - Rule stated but service mesh not visualized
  Recommendation: Add component diagram showing service mesh connections
───────────────────────────────────────────────────────────────────────────────────────

RULE 4: "Customer PII stored only in User DB and Order DB"
Category: Data
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  PII Diagram           FOUND       Shows PII flows to User DB, Order DB only
  Container Diagram     IMPLICIT    Shows DBs exist but not data content
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: HIGH
───────────────────────────────────────────────────────────────────────────────────────

RULE 5: "Card numbers never enter our system"
Category: Data
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  PII Diagram           FOUND       Card data → Stripe (external), token returns
  Container Diagram     NEUTRAL     Shows Stripe integration but not data content
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: HIGH
  Note: Recommend code-level verification (no card_number fields in models)
───────────────────────────────────────────────────────────────────────────────────────

RULE 6: "Driver location data is ephemeral (Redis only)"
Category: Data
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Container Diagram     FOUND       Driver Service → Redis connection shown
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: MEDIUM (need to verify no Postgres connection for location)
───────────────────────────────────────────────────────────────────────────────────────

RULE 7: "All inter-service communication authenticated (mTLS)"
Category: Security
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Container Diagram     NOT SHOWN   mTLS not visible in diagram (infrastructure detail)
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: PARTIAL - Rule stated but not visualized
  Recommendation: Add security overlay diagram or annotations
───────────────────────────────────────────────────────────────────────────────────────

RULE 8: "No service has direct internet access"
Category: Security
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Container Diagram     IMPLICIT    No outbound arrows from services to internet
  Context Diagram       IMPLICIT    Platform → External systems, not services directly
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: MEDIUM (diagrams support implicitly, need deployment config verification)
───────────────────────────────────────────────────────────────────────────────────────

RULE 9: "Admin Portal requires MFA"
Category: Security
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  Container Diagram     NOT SHOWN   MFA not visible (authentication detail)
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: PARTIAL - Rule stated but not visualized
  Recommendation: Add security annotations to container diagram
───────────────────────────────────────────────────────────────────────────────────────

RULE 10: "Orders can only move forward in state"
Category: State Machine
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  State Machine Diagram FOUND       All transitions unidirectional, no backward arrows
  Text Description      EXPLICIT    "can only move forward in state"
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: HIGH
───────────────────────────────────────────────────────────────────────────────────────

RULE 11: "DELIVERED orders cannot be cancelled"
Category: State Machine
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  State Machine Diagram FOUND       DELIVERED is terminal state (no outgoing transitions)
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: HIGH
───────────────────────────────────────────────────────────────────────────────────────

RULE 12: "Payment must succeed before PLACED state"
Category: State Machine
───────────────────────────────────────────────────────────────────────────────────────
  Artifact              Status      Finding
  ─────────────────────────────────────────────────────────────────────────────────────
  State Machine Diagram FOUND       Transition CREATED→PLACED labeled "payment succeeds"
  Rules Document        EXPLICIT    Rule stated explicitly
  
  Compatibility: FULL ✓
  Confidence: HIGH
───────────────────────────────────────────────────────────────────────────────────────

═══════════════════════════════════════════════════════════════════════════════════════
                                    SUMMARY
═══════════════════════════════════════════════════════════════════════════════════════

  Total Rules:              12
  Fully Compatible:         8 (67%)
  Partially Compatible:     4 (33%)
  Incompatible:             0 (0%)
  
  OVERALL STATUS: GOOD
  
  The architecture artifacts are largely consistent. Rules are well-documented.
  Some rules are not visually represented in diagrams but are stated in the rules
  document - this is acceptable for infrastructure/security details that don't
  fit cleanly into container diagrams.

═══════════════════════════════════════════════════════════════════════════════════════
                                 RECOMMENDATIONS
═══════════════════════════════════════════════════════════════════════════════════════

  PRIORITY  ACTION
  ────────────────────────────────────────────────────────────────────────────────────
  MEDIUM    Add deployment diagram showing ALB → Gateway → Services topology
  
  LOW       Add security overlay or annotations showing mTLS, MFA requirements
  
  LOW       Add component diagram for service mesh inter-service communication
  
  FUTURE    Add code extraction to verify:
            • No card_number fields in models
            • OrderStateMachine enforces forward-only transitions
            • Driver Service only writes location to Redis

═══════════════════════════════════════════════════════════════════════════════════════
                                  NEXT STEPS
═══════════════════════════════════════════════════════════════════════════════════════

  1. When code is available, run code extraction and add as verification layer
  2. When deployed, extract Kubernetes configs and verify deployment rules
  3. When running, analyze traces to verify runtime behavior matches design
  
  Current coverage: 2 of 5 layers (Text, Diagrams)
  Target coverage: 5 of 5 layers (Text, Diagrams, Code, Config, Traces)

═══════════════════════════════════════════════════════════════════════════════════════
```

---

## Part 6: Key Takeaways

### What We Demonstrated

1. **Architects create artifacts naturally** — Diagrams, text, rules. They don't need to learn a new language.

2. **Each artifact becomes a graph** — Using LLM agents as extractors, we transform visual and textual content into structured graphs.

3. **Graphs are comparable** — Once everything is a graph, we can assess compatibility across artifact types.

4. **Rules are testable** — Every "must/never/always" statement becomes a testable assertion.

5. **Divergence is surfaced** — When artifacts don't agree, the report shows exactly where and why.

6. **This works today** — Using LLM agents as the execution engine, no custom code needed.

### The Workflow

```
ARCHITECT CREATES              AGENTS EXTRACT              ENGINE ASSESSES
─────────────────              ──────────────              ───────────────

Context Diagram    ─────▶    Context Graph      ─┐
                                                 │
Container Diagram  ─────▶    Container Graph    ─┤
                                                 │
Text Descriptions  ─────▶    Text Graphs        ─┼────▶  COMPATIBILITY
                                                 │         REPORT
State Machine      ─────▶    State Machine      ─┤
                            Graph                │
                                                 │
PII Flow Diagram   ─────▶    PII Flow Graph     ─┤
                                                 │
Rules Document     ─────▶    Rules Graph        ─┘
```

### Scaling Down: Start with Hello World

You don't need a complex system to use this approach. Start with:

```
┌──────────┐     ┌──────────┐
│  Client  │────▶│  Server  │
└──────────┘     └──────────┘

"The Client sends requests to the Server. 
All requests must go through the Server."
```

One diagram, one text description, one rule. Run the workflow. See it work. Then scale up.

### Scaling Up: Add More Layers

As the system matures, add more artifact types:

| Phase | Artifacts | Layers Compared |
|-------|-----------|-----------------|
| Design | Diagrams, Text | 2 layers |
| Development | + Code | 3 layers |
| Deployment | + Config (K8s, Terraform) | 4 layers |
| Production | + Traces (OpenTelemetry) | 5 layers |

Each layer adds confidence. Full compatibility across 5 layers means: what was designed is what was built is what was deployed is what is running.

---

## Part 7: Using This with Issues-FS Role Agents

### Practical Commands

**To the Conductor Agent:**

```
I need to run architecture compatibility analysis on the Food Delivery Platform.

Artifacts:
1. [paste context diagram]
2. [paste container diagram]  
3. [paste text descriptions]
4. [paste state machine]
5. [paste rules document]

Please:
1. Extract graphs from each artifact (use DiagramExtractor and TextExtractor roles)
2. Identify all testable rules
3. Assess compatibility for each rule
4. Generate the full compatibility report
```

**The Conductor orchestrates:**

```
Starting Architecture Compatibility Analysis...

[Activating DiagramExtractor role]
Extracting from Context Diagram... done (7 nodes, 6 edges)
Extracting from Container Diagram... done (17 nodes, 16 edges, 2 inferred rules)
Extracting from State Machine... done (7 states, 6 transitions, 2 inferred rules)
Extracting from PII Flow Diagram... done (8 nodes, 5 edges)

[Activating TextExtractor role]
Extracting from API Gateway description... done (8 nodes, 1 rule)
Extracting from Order Service description... done (5 nodes, 2 rules)
Extracting from Rules Document... done (12 rules)

[Activating RuleIdentifier role]
Total testable rules identified: 12

[Activating CompatibilityEngine role]
Assessing Rule 1/12: "All external traffic through ALB"... PARTIAL
Assessing Rule 2/12: "All client requests through Gateway"... FULL ✓
...
Assessing Rule 12/12: "Payment before PLACED"... FULL ✓

[Activating ReportGenerator role]
Generating compatibility report...

REPORT COMPLETE
──────────────
8/12 rules fully compatible
4/12 rules partially compatible (documented but not visualized)
0/12 rules incompatible

Overall Status: GOOD
```

---

## Conclusion

This document demonstrated the complete workflow for architecture compatibility testing:

1. **What architects create** — Natural artifacts (diagrams, text, rules)
2. **How we extract graphs** — LLM agents acting as extractors
3. **How we assess compatibility** — Comparing graphs across artifact types
4. **What the output looks like** — Detailed compatibility reports

The key insight: **architects don't need to change how they work**. They create diagrams and write descriptions. The system transforms these into testable graphs and verifies consistency.

This is architecture testing that works with architects, not against them.

---

## References

- [Compatibility Through Connectivity](./v0_4_0__issues-fs__compatibility-through-connectivity.md) — The foundational theory
- [LLM as Execution Engine](./v0_4_0__issues-fs__llm-as-execution-engine.md) — How to use this today
- [Thinking in Graphs](./v0_4_0__issues-fs__thinking-in-graphs.md) — The graph-first philosophy
- [Semantic Text Architecture](./v0_4_0__issues-fs__semantic-text-architecture.md) — Text extraction details

---

*Architecture Testing with Semantic Graphs v1.0*  
*A Complete Worked Example*  
*Date: 2026-02-05*
