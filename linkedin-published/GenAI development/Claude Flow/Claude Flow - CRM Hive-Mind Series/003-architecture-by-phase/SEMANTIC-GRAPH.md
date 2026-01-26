[🏠 Home](../../../../../README.md) / [LinkedIn Published](../../../../README.md) / [GenAI Development](../../../README.md) / [Claude Flow](../../README.md) / [CRM Hive-Mind Series](../README.md) / [Architecture by Phase](./README.md) / **Semantic Graph**

# Semantic Knowledge Graph: Architecture by Phase

---

## Summary

This document presents comprehensive technical architecture diagrams for a four-phase CRM development project using Claude-Flow. It demonstrates the strategic use of swarm execution mode with parallel agents for greenfield development (Phases 1-2) versus direct mode with single-instance Claude for bug fixes and small features (Phases 3-4). The architecture includes intelligent model routing, with Sonnet used for complex tasks and Haiku for simple operations, achieving optimal cost-effectiveness while maintaining development velocity.

---

## Key Concepts

1. **Phase-Based Development Timeline**: A structured four-phase approach dividing CRM development into API creation, UI/documentation, bug fixing, and feature enhancement, each with appropriate execution strategies.

2. **Swarm vs Direct Mode Selection**: Decision criteria for choosing between parallel agent swarm execution (10+ files, parallelizable tasks) and single-instance direct mode (bug fixes, sequential dependencies, iteration required).

3. **Parallel Agent Architecture**: Claude-Flow swarm topology where a Queen coordinator spawns multiple specialized agents (Architect, Coder, Tester) working simultaneously on different system components.

4. **Intelligent Model Routing**: Task complexity assessment that routes high-complexity work to Sonnet and simple tasks like data seeding to Haiku, achieving 15x cost reduction on appropriate workloads.

5. **Drag-and-Drop Event Flow**: Implementation architecture showing the browser event sequence from dragstart through drop, including API integration for deal stage updates.

6. **Bug Fix Root Cause Analysis**: Systematic debugging methodology addressing API response wrapper mismatches, field naming conventions (camelCase vs snake_case), and UUID quoting in HTML attributes.

---

## Core Arguments

1. **Execution mode should match task characteristics**: Swarm mode excels at greenfield parallel development (generating 3,000+ lines across 8 agents in 25 minutes), while direct mode provides the immediate feedback loops necessary for bug fixes and iterative feature work.

2. **Agent specialization improves output quality**: Dedicated agent types (system-architect, coder, tester) with appropriate models produce higher-quality code than generalist approaches, with the Queen aggregating and verifying all outputs.

3. **Model selection impacts cost without sacrificing quality**: Using Haiku for simple data seeding tasks (0.0002 USD/task) versus Sonnet for complex UI/backend work (0.003 USD/task) optimizes the cost-quality tradeoff.

4. **Sequential dependencies require direct mode**: Features like drag-and-drop where JavaScript events, CSS styles, and HTML structure are interconnected benefit from single-instance execution that can iterate quickly based on testing feedback.

5. **Parallel execution accelerates greenfield development**: Five agents completing API creation in 10 minutes demonstrates how swarm architecture compresses development timelines for independent workstreams.

6. **Clear handoff points between phases**: Each phase produces well-defined outputs (types, storage, services, API, tests) that subsequent phases can build upon, enabling clean separation of concerns.

---

## Tags

`claude-flow` `swarm-architecture` `parallel-agents` `direct-mode` `crm-development` `phase-based-development` `model-routing` `sonnet` `haiku` `drag-and-drop` `api-design` `bug-fixing` `root-cause-analysis` `typescript` `express-server`

---

## Mermaid Diagrams

### Development Phase Flowchart

```mermaid
flowchart TD
    subgraph Phase1["Phase 1: API Creation"]
        P1_Start[User Prompt] --> P1_Queen[Claude Code Queen]
        P1_Queen --> P1_Spawn{Spawn 5 Agents}
        P1_Spawn --> P1_A1[Agent 1: Architect]
        P1_Spawn --> P1_A2[Agent 2: Storage]
        P1_Spawn --> P1_A3[Agent 3: Services]
        P1_Spawn --> P1_A4[Agent 4: API]
        P1_Spawn --> P1_A5[Agent 5: Tester]
        P1_A1 --> P1_Agg[Queen Aggregates]
        P1_A2 --> P1_Agg
        P1_A3 --> P1_Agg
        P1_A4 --> P1_Agg
        P1_A5 --> P1_Agg
    end

    subgraph Phase2["Phase 2: UI + Docs"]
        P2_Start[User Prompt] --> P2_Queen[Claude Code Queen]
        P2_Queen --> P2_Spawn{Spawn 3 Agents}
        P2_Spawn --> P2_A1[Backend Dev<br/>Sonnet]
        P2_Spawn --> P2_A2[Frontend Coder<br/>Sonnet]
        P2_Spawn --> P2_A3[Data Seeder<br/>Haiku]
    end

    subgraph Phase3["Phase 3: Bug Fixing"]
        P3_Report[Bug Report] --> P3_Read[Read File]
        P3_Read --> P3_Analyze[Analyze Root Cause]
        P3_Analyze --> P3_Edit[Edit Tool Fix]
        P3_Edit --> P3_Verify[Verify with curl]
        P3_Verify -->|More bugs| P3_Read
    end

    subgraph Phase4["Phase 4: Features"]
        P4_Prompt[Feature Request] --> P4_Decide{Files Affected?}
        P4_Decide -->|< 10 files| P4_Direct[Direct Mode]
        P4_Direct --> P4_JS[JavaScript Events]
        P4_JS --> P4_CSS[CSS Styles]
        P4_CSS --> P4_HTML[HTML Structure]
        P4_HTML --> P4_Test[Browser Test]
    end

    Phase1 --> Phase2
    Phase2 --> Phase3
    Phase3 --> Phase4
```

### Agent Architecture Class Diagram

```mermaid
classDiagram
    class ClaudeCodeQueen {
        +checkSwarmHealth()
        +setHiveMemory()
        +spawnAgents()
        +aggregateOutputs()
        +runTests()
    }

    class SwarmAgent {
        <<abstract>>
        +String agentType
        +String model
        +execute()
        +writeOutput()
    }

    class SystemArchitect {
        +model: sonnet
        +designTypeSystem()
        +outputFiles: types/index.ts
    }

    class StorageCoder {
        +model: sonnet
        +buildStorageLayer()
        +outputFiles: storage/*.ts
    }

    class ServicesCoder {
        +model: sonnet
        +implementServices()
        +outputFiles: services/*.ts
    }

    class APICoder {
        +model: sonnet
        +createRESTAPI()
        +outputFiles: api/*.ts
    }

    class Tester {
        +model: sonnet
        +writeUnitTests()
        +outputFiles: tests/*.ts
    }

    class DataSeeder {
        +model: haiku
        +generateSampleData()
        +outputFiles: server/seed.ts
    }

    class DirectModeInstance {
        +readFile()
        +analyzeCode()
        +editFile()
        +verifyChanges()
    }

    ClaudeCodeQueen --> SwarmAgent : spawns
    SwarmAgent <|-- SystemArchitect
    SwarmAgent <|-- StorageCoder
    SwarmAgent <|-- ServicesCoder
    SwarmAgent <|-- APICoder
    SwarmAgent <|-- Tester
    SwarmAgent <|-- DataSeeder

    ClaudeCodeQueen --> DirectModeInstance : switches to
```

### Concept Mindmap

```mermaid
mindmap
    root((Architecture by Phase))
        Swarm Mode
            Phase 1 API
                5 Parallel Agents
                Types Layer
                Storage Layer
                Services Layer
                API Layer
                Test Suite
            Phase 2 UI
                3 Parallel Agents
                Backend Dev
                Frontend Coder
                Data Seeder
            Model Routing
                Sonnet for Complex
                Haiku for Simple
                15x Cost Savings
        Direct Mode
            Phase 3 Bugs
                Read Analyze Edit
                Root Cause Analysis
                API Response Wrapper
                Field Naming
                UUID Quoting
            Phase 4 Features
                Drag and Drop
                Event Flow
                CSS Animations
                API Integration
            Selection Criteria
                Under 10 Files
                Sequential Deps
                Iteration Expected
        Timeline
            Total 45 Minutes
            3000+ Lines Swarm
            200 Lines Direct
            8 Agents Used
```

### Knowledge Graph

```mermaid
graph TB
    subgraph Execution_Modes["Execution Modes"]
        SWARM[Swarm Mode]
        DIRECT[Direct Mode]
    end

    subgraph Phases["Development Phases"]
        P1[Phase 1: API Creation]
        P2[Phase 2: UI + Docs]
        P3[Phase 3: Bug Fixing]
        P4[Phase 4: Features]
    end

    subgraph Agents["Agent Types"]
        ARCH[System Architect]
        STORE[Storage Coder]
        SVC[Services Coder]
        API[API Coder]
        TEST[Tester]
        SEED[Data Seeder]
    end

    subgraph Models["AI Models"]
        SONNET[Claude Sonnet]
        HAIKU[Claude Haiku]
    end

    subgraph Outputs["System Outputs"]
        TYPES[Types Layer]
        STORAGE[Storage Layer]
        SERVICES[Services Layer]
        RESTAPI[REST API]
        TESTS[Test Suite]
        UI[Frontend UI]
        SWAGGER[Swagger Docs]
        DRAGDROP[Drag Drop Feature]
    end

    subgraph Bugs["Bug Categories"]
        BUG1[API Response Wrapper]
        BUG2[Field Naming Mismatch]
        BUG3[UUID Quoting Error]
    end

    SWARM --> P1
    SWARM --> P2
    DIRECT --> P3
    DIRECT --> P4

    P1 --> ARCH
    P1 --> STORE
    P1 --> SVC
    P1 --> API
    P1 --> TEST

    P2 --> SEED

    ARCH --> SONNET
    STORE --> SONNET
    SVC --> SONNET
    API --> SONNET
    TEST --> SONNET
    SEED --> HAIKU

    ARCH --> TYPES
    STORE --> STORAGE
    SVC --> SERVICES
    API --> RESTAPI
    TEST --> TESTS

    P2 --> UI
    P2 --> SWAGGER

    P3 --> BUG1
    P3 --> BUG2
    P3 --> BUG3

    P4 --> DRAGDROP

    TYPES --> STORAGE
    STORAGE --> SERVICES
    SERVICES --> RESTAPI
```

---

## Cypher Export

```cypher
// Execution Modes
CREATE (swarm:ExecutionMode {name: 'Swarm Mode', description: 'Parallel agent execution via Claude-Flow', bestFor: 'Greenfield development, 10+ files'})
CREATE (direct:ExecutionMode {name: 'Direct Mode', description: 'Single Claude instance with Edit tool', bestFor: 'Bug fixes, small features, iteration'})

// Development Phases
CREATE (p1:Phase {name: 'Phase 1: API Creation', duration: '10 min', outputLines: 1500, agentCount: 5})
CREATE (p2:Phase {name: 'Phase 2: UI + Docs', duration: '15 min', outputLines: 1500, agentCount: 3})
CREATE (p3:Phase {name: 'Phase 3: Bug Fixing', duration: '10 min', outputLines: 50, agentCount: 1})
CREATE (p4:Phase {name: 'Phase 4: Features', duration: '10 min', outputLines: 150, agentCount: 1})

// AI Models
CREATE (sonnet:Model {name: 'Claude Sonnet', costPerTask: 0.003, complexity: 'high'})
CREATE (haiku:Model {name: 'Claude Haiku', costPerTask: 0.0002, complexity: 'low'})

// Agent Types
CREATE (architect:AgentType {name: 'System Architect', task: 'Design type system', outputFiles: 'types/index.ts'})
CREATE (storageCoder:AgentType {name: 'Storage Coder', task: 'Build storage layer', outputFiles: 'storage/*.ts'})
CREATE (servicesCoder:AgentType {name: 'Services Coder', task: 'Implement services', outputFiles: 'services/*.ts'})
CREATE (apiCoder:AgentType {name: 'API Coder', task: 'Create REST API', outputFiles: 'api/*.ts'})
CREATE (tester:AgentType {name: 'Tester', task: 'Write unit tests', outputFiles: 'tests/*.ts'})
CREATE (seeder:AgentType {name: 'Data Seeder', task: 'Generate sample data', outputFiles: 'server/seed.ts'})

// System Components
CREATE (types:Component {name: 'Types Layer', entities: 6, includes: 'Customer, Deal, Contact, Interaction, Company, Note'})
CREATE (storage:Component {name: 'Storage Layer', pattern: 'Generic CRUD Map'})
CREATE (services:Component {name: 'Services Layer', includes: 'CustomerService, DealService, InteractionService'})
CREATE (api:Component {name: 'REST API', endpoints: 18})
CREATE (tests:Component {name: 'Test Suite', testCount: 40})
CREATE (ui:Component {name: 'Frontend UI', style: 'Modern SaaS'})
CREATE (swagger:Component {name: 'Swagger Docs', spec: 'OpenAPI 3.0'})

// Bug Fixes
CREATE (bug1:Bug {name: 'API Response Wrapper', rootCause: 'Frontend expected array, API returned wrapped object'})
CREATE (bug2:Bug {name: 'Field Naming Mismatch', rootCause: 'camelCase vs snake_case inconsistency'})
CREATE (bug3:Bug {name: 'UUID Quoting Error', rootCause: 'UUID in onclick interpreted as math expression'})

// Features
CREATE (dragdrop:Feature {name: 'Drag-and-Drop Pipeline', filesAffected: 4, events: 'dragstart, dragover, drop'})

// Relationships - Execution Mode to Phase
CREATE (swarm)-[:EXECUTES]->(p1)
CREATE (swarm)-[:EXECUTES]->(p2)
CREATE (direct)-[:EXECUTES]->(p3)
CREATE (direct)-[:EXECUTES]->(p4)

// Relationships - Phase to Agent
CREATE (p1)-[:USES_AGENT]->(architect)
CREATE (p1)-[:USES_AGENT]->(storageCoder)
CREATE (p1)-[:USES_AGENT]->(servicesCoder)
CREATE (p1)-[:USES_AGENT]->(apiCoder)
CREATE (p1)-[:USES_AGENT]->(tester)
CREATE (p2)-[:USES_AGENT]->(seeder)

// Relationships - Agent to Model
CREATE (architect)-[:RUNS_ON]->(sonnet)
CREATE (storageCoder)-[:RUNS_ON]->(sonnet)
CREATE (servicesCoder)-[:RUNS_ON]->(sonnet)
CREATE (apiCoder)-[:RUNS_ON]->(sonnet)
CREATE (tester)-[:RUNS_ON]->(sonnet)
CREATE (seeder)-[:RUNS_ON]->(haiku)

// Relationships - Agent to Component
CREATE (architect)-[:PRODUCES]->(types)
CREATE (storageCoder)-[:PRODUCES]->(storage)
CREATE (servicesCoder)-[:PRODUCES]->(services)
CREATE (apiCoder)-[:PRODUCES]->(api)
CREATE (tester)-[:PRODUCES]->(tests)

// Relationships - Component Dependencies
CREATE (storage)-[:DEPENDS_ON]->(types)
CREATE (services)-[:DEPENDS_ON]->(storage)
CREATE (api)-[:DEPENDS_ON]->(services)

// Relationships - Phase to Output
CREATE (p2)-[:PRODUCES]->(ui)
CREATE (p2)-[:PRODUCES]->(swagger)
CREATE (p3)-[:FIXES]->(bug1)
CREATE (p3)-[:FIXES]->(bug2)
CREATE (p3)-[:FIXES]->(bug3)
CREATE (p4)-[:IMPLEMENTS]->(dragdrop)

// Relationships - Phase Sequence
CREATE (p1)-[:FOLLOWED_BY]->(p2)
CREATE (p2)-[:FOLLOWED_BY]->(p3)
CREATE (p3)-[:FOLLOWED_BY]->(p4)
```

---

*Generated from source: 003-architecture-by-phase.md*
