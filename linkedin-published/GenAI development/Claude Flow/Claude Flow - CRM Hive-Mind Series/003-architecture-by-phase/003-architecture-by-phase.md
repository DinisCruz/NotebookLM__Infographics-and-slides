# Architecture by Phase: CRM Development

**Date:** January 26, 2026
**Document Type:** Technical Architecture & Workflow Diagrams
**Purpose:** Visualize the four development phases and execution modes

---

## Phase Overview

| Phase | Objective | Execution Mode | Agents/Tools |
|-------|-----------|----------------|--------------|
| **Phase 1** | Create CRM API | 🐝 Claude-Flow Swarm | 5 parallel agents |
| **Phase 2** | Create UI + API Docs | 🐝 Claude-Flow Swarm | 3 parallel agents |
| **Phase 3** | Bug Fixing | 🤖 Claude Direct | Edit tool |
| **Phase 4** | New Features | 🤖 Claude Direct | Edit tool |

---

## Phase 1: API Creation (Claude-Flow Swarm Mode)

### Execution Mode: 🐝 SWARM (5 Parallel Agents)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           PHASE 1: API CREATION                              │
│                        Execution: Claude-Flow Swarm                          │
└─────────────────────────────────────────────────────────────────────────────┘

                              ┌───────────────┐
                              │  USER PROMPT  │
                              │ "Implement a  │
                              │  CRM for      │
                              │  customers"   │
                              └───────┬───────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CLAUDE CODE (QUEEN)                                  │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    Coordination Actions                              │   │
│  │  1. mcp__claude-flow__hive-mind_status() → Check swarm health       │   │
│  │  2. mcp__claude-flow__hive-mind_memory(set, "crm-architecture")     │   │
│  │  3. Bash("mkdir -p src/{types,storage,services,api} tests")         │   │
│  │  4. Bash("npx @claude-flow/cli swarm init --topology hierarchical") │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │      SPAWN 5 AGENTS IN PARALLEL   │
                    │      (Single Message, All At Once)│
                    └─────────────────┬─────────────────┘
                                      │
        ┌─────────────┬───────────────┼───────────────┬─────────────┐
        │             │               │               │             │
        ▼             ▼               ▼               ▼             ▼
   ┌─────────┐  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
   │ AGENT 1 │  │ AGENT 2 │    │ AGENT 3 │    │ AGENT 4 │    │ AGENT 5 │
   │Architect│  │ Storage │    │Services │    │   API   │    │ Tester  │
   │ (sonnet)│  │  Coder  │    │  Coder  │    │  Coder  │    │(sonnet) │
   │         │  │(sonnet) │    │(sonnet) │    │(sonnet) │    │         │
   └────┬────┘  └────┬────┘    └────┬────┘    └────┬────┘    └────┬────┘
        │             │               │               │             │
        ▼             ▼               ▼               ▼             ▼
   ┌─────────┐  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
   │ OUTPUT  │  │ OUTPUT  │    │ OUTPUT  │    │ OUTPUT  │    │ OUTPUT  │
   │         │  │         │    │         │    │         │    │         │
   │ types/  │  │storage/ │    │services/│    │  api/   │    │ tests/  │
   │index.ts │  │Store.ts │    │Customer │    │customers│    │*.test.ts│
   │         │  │index.ts │    │ Service │    │  .ts    │    │run-tests│
   │6 entities│ │Generic  │    │Deal Svc │    │deals.ts │    │  .ts    │
   │DTOs     │  │CRUD Map │    │Int. Svc │    │interact │    │         │
   │Filters  │  │         │    │index.ts │    │  .ts    │    │40 tests │
   └─────────┘  └─────────┘    └─────────┘    └─────────┘    └─────────┘
        │             │               │               │             │
        └─────────────┴───────────────┼───────────────┴─────────────┘
                                      │
                                      ▼
                         ┌────────────────────────┐
                         │   QUEEN AGGREGATES     │
                         │   • Verifies completion│
                         │   • Runs tests (40/40) │
                         │   • Updates hive memory│
                         └────────────────────────┘
```

### Agent Details

| Agent | Type | Model | Task | Output Files | Lines |
|-------|------|-------|------|--------------|-------|
| Agent 1 | `system-architect` | sonnet | Design type system | `src/types/index.ts` | 129 |
| Agent 2 | `coder` | sonnet | Build storage layer | `src/storage/Store.ts`, `index.ts` | 116 |
| Agent 3 | `coder` | sonnet | Implement services | `src/services/*.ts` | ~400 |
| Agent 4 | `coder` | sonnet | Create REST API | `src/api/*.ts` | ~700 |
| Agent 5 | `tester` | sonnet | Write unit tests | `tests/*.ts` | ~500 |

### Data Flow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    TYPES     │────▶│   STORAGE    │────▶│   SERVICES   │────▶│     API      │
│              │     │              │     │              │     │              │
│ • Customer   │     │ • Store<T>   │     │ • Customer   │     │ • GET /      │
│ • Deal       │     │ • Map<K,V>   │     │   Service    │     │ • POST /     │
│ • Contact    │     │ • CRUD ops   │     │ • DealService│     │ • PUT /:id   │
│ • Interaction│     │ • Search     │     │ • Interaction│     │ • DELETE /:id│
│ • Company    │     │              │     │   Service    │     │              │
│ • Note       │     │              │     │              │     │              │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
```

### Timeline

```
T=0min   ─────┬───────────────────────────────────────────────────── T=10min
              │
              ├─ Agent 1 (Architect) ████████░░░░░░░░░░░░ Complete @ T=3min
              │
              ├─ Agent 2 (Storage)   ████████████░░░░░░░░ Complete @ T=5min
              │
              ├─ Agent 3 (Services)  ████████████████░░░░ Complete @ T=7min
              │
              ├─ Agent 4 (API)       ██████████████████░░ Complete @ T=8min
              │
              └─ Agent 5 (Tester)    ████████████████████ Complete @ T=10min

              [═══════════ PARALLEL EXECUTION ═══════════]
```

---

## Phase 2: UI + API Docs (Claude-Flow Swarm Mode)

### Execution Mode: 🐝 SWARM (3 Parallel Agents)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      PHASE 2: UI + API DOCUMENTATION                         │
│                        Execution: Claude-Flow Swarm                          │
└─────────────────────────────────────────────────────────────────────────────┘

                              ┌───────────────┐
                              │  USER PROMPT  │
                              │ "add swagger  │
                              │  UI and a     │
                              │  modern UI"   │
                              └───────┬───────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CLAUDE CODE (QUEEN)                                  │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    Coordination Actions                              │   │
│  │  1. mcp__claude-flow__hive-mind_memory(set, "crm-phase2")           │   │
│  │  2. Analyze requirements: Server + Swagger + Frontend + Data        │   │
│  │  3. Select appropriate agent types and models                       │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │      SPAWN 3 AGENTS IN PARALLEL   │
                    └─────────────────┬─────────────────┘
                                      │
              ┌───────────────────────┼───────────────────────┐
              │                       │                       │
              ▼                       ▼                       ▼
      ┌──────────────┐       ┌──────────────┐       ┌──────────────┐
      │   AGENT 1    │       │   AGENT 2    │       │   AGENT 3    │
      │  Backend Dev │       │Frontend Coder│       │  Data Seeder │
      │   (sonnet)   │       │   (sonnet)   │       │   (haiku)    │
      │              │       │              │       │              │
      │ Complex task:│       │ Creative UI: │       │ Simple task: │
      │ Server setup │       │ Modern SaaS  │       │ Generate     │
      │ OpenAPI spec │       │ design       │       │ sample data  │
      └──────┬───────┘       └──────┬───────┘       └──────┬───────┘
             │                      │                      │
             ▼                      ▼                      ▼
      ┌──────────────┐       ┌──────────────┐       ┌──────────────┐
      │   OUTPUT     │       │   OUTPUT     │       │   OUTPUT     │
      │              │       │              │       │              │
      │src/server/   │       │public/       │       │src/server/   │
      │ • index.ts   │       │ • index.html │       │ • seed.ts    │
      │ • routes.ts  │       │ • css/       │       │              │
      │ • swagger.ts │       │   styles.css │       │ • 8 companies│
      │   (36KB!)    │       │ • js/        │       │ • 12 customer│
      │              │       │   app.js     │       │ • 31 contacts│
      │              │       │   components │       │ • 18 deals   │
      └──────────────┘       └──────────────┘       └──────────────┘
```

### Model Selection Strategy

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        INTELLIGENT MODEL ROUTING                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   Task Complexity Assessment:                                                │
│                                                                              │
│   ┌─────────────────┐                                                       │
│   │ Backend Dev     │──▶ HIGH complexity (server, routing, OpenAPI spec)    │
│   │                 │    ──▶ Use SONNET ($0.003/task)                       │
│   └─────────────────┘                                                       │
│                                                                              │
│   ┌─────────────────┐                                                       │
│   │ Frontend Coder  │──▶ HIGH complexity (creative UI, responsive design)   │
│   │                 │    ──▶ Use SONNET ($0.003/task)                       │
│   └─────────────────┘                                                       │
│                                                                              │
│   ┌─────────────────┐                                                       │
│   │ Data Seeder     │──▶ LOW complexity (generate sample records)           │
│   │                 │    ──▶ Use HAIKU ($0.0002/task) ← 15x cheaper!       │
│   └─────────────────┘                                                       │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Deliverables Structure

```
                    ┌─────────────────────────────────────┐
                    │         http://localhost:3000       │
                    └─────────────────┬───────────────────┘
                                      │
          ┌───────────────────────────┼───────────────────────────┐
          │                           │                           │
          ▼                           ▼                           ▼
   ┌─────────────┐            ┌─────────────┐            ┌─────────────┐
   │      /      │            │  /api-docs  │            │    /api/*   │
   │   CRM UI    │            │ Swagger UI  │            │  REST API   │
   │             │            │             │            │             │
   │ ┌─────────┐ │            │ ┌─────────┐ │            │ /customers  │
   │ │Dashboard│ │            │ │Try APIs │ │            │ /deals      │
   │ │Customers│ │            │ │OpenAPI  │ │            │ /interactions│
   │ │Deals    │ │            │ │3.0 Spec │ │            │             │
   │ │Interact.│ │            │ │Examples │ │            │ 18 endpoints│
   │ └─────────┘ │            │ └─────────┘ │            │             │
   └─────────────┘            └─────────────┘            └─────────────┘
          │                           │                           │
          │                           │                           │
          └───────────────────────────┼───────────────────────────┘
                                      │
                                      ▼
                         ┌────────────────────────┐
                         │    Express.js Server   │
                         │    (src/server/)       │
                         │                        │
                         │  • Static file serving │
                         │  • API routes          │
                         │  • Swagger middleware  │
                         │  • Auto-seed on start  │
                         └────────────────────────┘
```

---

## Phase 3: Bug Fixing (Claude Direct Mode)

### Execution Mode: 🤖 DIRECT (Claude Code with Edit Tool)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         PHASE 3: BUG FIXING                                  │
│                        Execution: Claude Direct                              │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                              BUG #1                                          │
│         TypeError: this.deals.reduce is not a function                       │
└─────────────────────────────────────────────────────────────────────────────┘

   ┌─────────────┐         ┌─────────────┐         ┌─────────────┐
   │   USER      │         │   CLAUDE    │         │   FILE      │
   │   REPORT    │────────▶│   DIRECT    │────────▶│   EDIT      │
   │             │         │             │         │             │
   │ "I get this │         │ 1. Read     │         │ app.js      │
   │  error..."  │         │ 2. Diagnose │         │             │
   │             │         │ 3. Fix      │         │ APIClient   │
   └─────────────┘         └─────────────┘         └─────────────┘

   Root Cause Analysis:
   ┌─────────────────────────────────────────────────────────────────────────┐
   │                                                                          │
   │   API Response:        Frontend Expected:                                │
   │   {                    [                                                 │
   │     success: true,       { id: 1, ... },                                │
   │     data: [              { id: 2, ... }                                 │
   │       { id: 1, ... },  ]                                                │
   │       { id: 2, ... }                                                    │
   │     ]                  ← Array directly, not wrapped                    │
   │   }                                                                      │
   │   ↑ Wrapped in object                                                   │
   │                                                                          │
   └─────────────────────────────────────────────────────────────────────────┘

   Fix Applied:
   ┌─────────────────────────────────────────────────────────────────────────┐
   │   // BEFORE                        // AFTER                             │
   │   return await response.json();    const json = await response.json(); │
   │                                    return json.data !== undefined       │
   │                                      ? json.data : json;                │
   └─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                              BUG #2                                          │
│              Field Naming Mismatch (camelCase vs snake_case)                │
└─────────────────────────────────────────────────────────────────────────────┘

   ┌─────────────────────────────────────────────────────────────────────────┐
   │                                                                          │
   │   API Returns:              Frontend Used:                               │
   │   ─────────────             ──────────────                               │
   │   customerId                customer_id         ✗                        │
   │   createdAt                 created_at          ✗                        │
   │   expectedCloseDate         expected_close_date ✗                        │
   │                                                                          │
   └─────────────────────────────────────────────────────────────────────────┘

   Fix Applied (Global Replace):
   ┌─────────────────────────────────────────────────────────────────────────┐
   │   Edit({ file: "app.js", replace_all: true,                             │
   │          old: "customer_id", new: "customerId" })                       │
   │   Edit({ file: "app.js", replace_all: true,                             │
   │          old: "created_at", new: "createdAt" })                         │
   │   Edit({ file: "components.js", ... })                                  │
   └─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                              BUG #3                                          │
│            SyntaxError: Invalid or unexpected token                         │
└─────────────────────────────────────────────────────────────────────────────┘

   Root Cause:
   ┌─────────────────────────────────────────────────────────────────────────┐
   │                                                                          │
   │   // GENERATED HTML (BROKEN)                                            │
   │   onclick="app.editCustomer(48aa3cb6-c640-49a1-94b2-a8e35022c2f7)"     │
   │                             ↑                                           │
   │                             UUID interpreted as math expression!         │
   │                             48aa3cb6 - c640 - 49a1 - ...                │
   │                                                                          │
   │   // FIXED                                                               │
   │   onclick="app.editCustomer('48aa3cb6-c640-49a1-94b2-a8e35022c2f7')"   │
   │                             ↑   Quoted string                     ↑     │
   │                                                                          │
   └─────────────────────────────────────────────────────────────────────────┘

   Fix Applied:
   ┌─────────────────────────────────────────────────────────────────────────┐
   │   // BEFORE                                                              │
   │   onclick="app.editCustomer(${customer.id})"                            │
   │                                                                          │
   │   // AFTER                                                               │
   │   onclick="app.editCustomer('${customer.id}')"                          │
   │                              ↑              ↑                            │
   │                              Added quotes                                │
   └─────────────────────────────────────────────────────────────────────────┘
```

### Direct Mode Workflow

```
┌────────────────────────────────────────────────────────────────────────────┐
│                      DIRECT MODE EDIT CYCLE                                 │
└────────────────────────────────────────────────────────────────────────────┘

         ┌─────────┐      ┌─────────┐      ┌─────────┐      ┌─────────┐
         │  READ   │─────▶│ ANALYZE │─────▶│  EDIT   │─────▶│ VERIFY  │
         │         │      │         │      │         │      │         │
         │Read()   │      │Diagnose │      │Edit()   │      │Bash()   │
         │tool     │      │root     │      │tool     │      │curl     │
         │         │      │cause    │      │         │      │         │
         └─────────┘      └─────────┘      └─────────┘      └─────────┘
              │                                                   │
              │                                                   │
              └───────────────────────────────────────────────────┘
                              Iterate if needed

         Time per bug: 2-5 minutes (immediate feedback loop)
```

---

## Phase 4: New Features (Claude Direct Mode)

### Execution Mode: 🤖 DIRECT (Claude Code with Edit Tool)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     PHASE 4: DRAG-AND-DROP FEATURE                           │
│                        Execution: Claude Direct                              │
└─────────────────────────────────────────────────────────────────────────────┘

                              ┌───────────────┐
                              │  USER PROMPT  │
                              │ "add drag and │
                              │  drop between │
                              │  stages"      │
                              └───────┬───────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CLAUDE CODE (DIRECT)                                 │
│                                                                              │
│  Decision: Feature is FOCUSED (4 files) → Use DIRECT mode, not swarm        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
              ┌───────────────────────┼───────────────────────┐
              │                       │                       │
              ▼                       ▼                       ▼
      ┌──────────────┐       ┌──────────────┐       ┌──────────────┐
      │   STEP 1     │       │   STEP 2     │       │   STEP 3     │
      │  JavaScript  │       │     CSS      │       │    HTML      │
      │              │       │              │       │              │
      │ app.js:      │       │ styles.css:  │       │ index.html:  │
      │ • Drag start │       │ • .dragging  │       │ • Add closed │
      │ • Drag end   │       │ • .drag-over │       │   -won column│
      │ • Drop zone  │       │ • Animations │       │ • Add closed │
      │ • Update API │       │              │       │   -lost col  │
      └──────────────┘       └──────────────┘       └──────────────┘
              │                       │                       │
              ▼                       ▼                       ▼
      ┌──────────────┐       ┌──────────────┐       ┌──────────────┐
      │   STEP 4     │       │   STEP 5     │       │   STEP 6     │
      │  API Client  │       │  Components  │       │   VERIFY     │
      │              │       │              │       │              │
      │ app.js:      │       │components.js:│       │ • Test in    │
      │ • PATCH      │       │ • Stage      │       │   browser    │
      │   /deals/:id │       │   options    │       │ • Drag works │
      │   /stage     │       │   update     │       │ • API called │
      └──────────────┘       └──────────────┘       └──────────────┘
```

### Feature Implementation Details

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     DRAG-AND-DROP ARCHITECTURE                               │
└─────────────────────────────────────────────────────────────────────────────┘

   BEFORE (Click only):
   ┌─────────────────────────────────────────────────────────────────────────┐
   │                                                                          │
   │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐       │
   │  │  LEAD   │  │QUALIFIED│  │PROPOSAL │  │ NEGOT.  │  │ CLOSED  │       │
   │  │         │  │         │  │         │  │         │  │         │       │
   │  │ [Deal1] │  │ [Deal3] │  │ [Deal5] │  │ [Deal7] │  │ [Deal9] │       │
   │  │ [Deal2] │  │ [Deal4] │  │ [Deal6] │  │ [Deal8] │  │         │       │
   │  │         │  │         │  │         │  │         │  │         │       │
   │  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘       │
   │                                                                          │
   │  Interaction: Click deal → Open modal → Change stage → Save              │
   │                                                                          │
   └─────────────────────────────────────────────────────────────────────────┘

   AFTER (Drag-and-Drop):
   ┌─────────────────────────────────────────────────────────────────────────┐
   │                                                                          │
   │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌──────┐ ┌──────┐ │
   │  │  LEAD   │  │QUALIFIED│  │PROPOSAL │  │ NEGOT.  │  │CLOSED│ │CLOSED│ │
   │  │         │  │         │  │         │  │         │  │ WON  │ │ LOST │ │
   │  │ ┌─────┐ │  │         │  │         │  │         │  │      │ │      │ │
   │  │ │Deal1│◄┼──┼─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─│─ ─ ─ ─│ │
   │  │ └─────┘ │  │  DRAG   │  │         │  │         │  │      │ │      │ │
   │  │         │  │ ┌─────┐ │  │         │  │         │  │      │ │      │ │
   │  │         │  │ │Deal3│─┼─▶DROP     │  │         │  │      │ │      │ │
   │  │         │  │ └─────┘ │  │ ┌─────┐ │  │         │  │      │ │      │ │
   │  │         │  │         │  │ │Deal3│ │  │         │  │      │ │      │ │
   │  │         │  │         │  │ └─────┘ │  │         │  │      │ │      │ │
   │  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └──────┘ └──────┘ │
   │                                                                          │
   │  Interaction: Grab deal → Drag to column → Drop → Auto-update API        │
   │                                                                          │
   └─────────────────────────────────────────────────────────────────────────┘
```

### Event Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DRAG EVENT SEQUENCE                                  │
└─────────────────────────────────────────────────────────────────────────────┘

   User Action          Browser Event          Handler                API Call
   ───────────          ─────────────          ───────                ────────
       │                     │                    │                      │
       │  Grab card          │                    │                      │
       │─────────────────────▶                    │                      │
       │                     │  dragstart         │                      │
       │                     │───────────────────▶│                      │
       │                     │                    │ setData(dealId)      │
       │                     │                    │ add .dragging class  │
       │                     │                    │                      │
       │  Move over column   │                    │                      │
       │─────────────────────▶                    │                      │
       │                     │  dragover          │                      │
       │                     │───────────────────▶│                      │
       │                     │                    │ preventDefault()     │
       │                     │                    │ add .drag-over class │
       │                     │                    │                      │
       │  Release            │                    │                      │
       │─────────────────────▶                    │                      │
       │                     │  drop              │                      │
       │                     │───────────────────▶│                      │
       │                     │                    │ getData(dealId)      │
       │                     │                    │─────────────────────▶│
       │                     │                    │     PATCH /deals/:id/stage
       │                     │                    │◀─────────────────────│
       │                     │                    │ updateDealStage()    │
       │                     │                    │ renderDealsBoard()   │
       │                     │                    │ Toast.success()      │
       │                     │                    │                      │
```

### Why Direct Mode (Not Swarm)?

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      MODE SELECTION DECISION                                 │
└─────────────────────────────────────────────────────────────────────────────┘

   Feature: Drag-and-Drop Pipeline

   Evaluation:
   ┌─────────────────────────────────────────────────────┐
   │  Criterion              │ Value      │ Score       │
   ├─────────────────────────────────────────────────────┤
   │  Files affected         │ 4          │ LOW         │
   │  Parallelizable?        │ No         │ -           │
   │  Sequential dependency? │ Yes        │ DIRECT      │
   │  Iteration expected?    │ Yes        │ DIRECT      │
   │  Complexity             │ Medium     │ EITHER      │
   └─────────────────────────────────────────────────────┘

   Result: ────▶ DIRECT MODE

   Reasoning:
   • Changes are interconnected (JS events → CSS styles → HTML structure)
   • Likely to need iteration based on testing
   • 4 files is below swarm threshold (~10+ files)
   • No benefit from parallelization (sequential dependencies)
```

---

## Summary: Who Did What

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    EXECUTION MODE BY PHASE                                   │
└─────────────────────────────────────────────────────────────────────────────┘

   Phase 1: API Creation
   ════════════════════════════════════════════════════════════════════════════
   │ CLAUDE-FLOW SWARM │ 5 agents │ ~10 min │ 1,500+ lines │ Types/Store/Svc/API/Tests
   ════════════════════════════════════════════════════════════════════════════

   Phase 2: UI + Docs
   ════════════════════════════════════════════════════════════════════════════
   │ CLAUDE-FLOW SWARM │ 3 agents │ ~15 min │ 1,500+ lines │ Server/UI/Seed
   ════════════════════════════════════════════════════════════════════════════

   Phase 3: Bug Fixing
   ════════════════════════════════════════════════════════════════════════════
   │  CLAUDE DIRECT    │ 1 instance│ ~10 min │ ~50 lines   │ API format/Names/Quotes
   ════════════════════════════════════════════════════════════════════════════

   Phase 4: New Features
   ════════════════════════════════════════════════════════════════════════════
   │  CLAUDE DIRECT    │ 1 instance│ ~10 min │ ~150 lines  │ Drag-drop/CSS/HTML
   ════════════════════════════════════════════════════════════════════════════


   ┌─────────────────┐                      ┌─────────────────┐
   │  SWARM MODE     │                      │  DIRECT MODE    │
   │  🐝🐝🐝🐝🐝     │                      │       🤖        │
   │                 │                      │                 │
   │  8 agents total │                      │  1 instance     │
   │  ~25 min        │                      │  ~20 min        │
   │  ~3,000 lines   │                      │  ~200 lines     │
   │                 │                      │                 │
   │  Best for:      │                      │  Best for:      │
   │  • Greenfield   │                      │  • Bug fixes    │
   │  • Parallel work│                      │  • Small features│
   │  • 10+ files    │                      │  • Iteration    │
   └─────────────────┘                      └─────────────────┘
```

---

## Visual Timeline

```
═══════════════════════════════════════════════════════════════════════════════
                              DEVELOPMENT TIMELINE
═══════════════════════════════════════════════════════════════════════════════

T=0         T=10        T=25        T=35        T=45
│           │           │           │           │
▼           ▼           ▼           ▼           ▼
┌───────────┬───────────┬───────────┬───────────┐
│  PHASE 1  │  PHASE 2  │  PHASE 3  │  PHASE 4  │
│    API    │    UI     │   BUGS    │ FEATURES  │
│           │           │           │           │
│  🐝 SWARM │  🐝 SWARM │  🤖 DIRECT│  🤖 DIRECT│
│  5 agents │  3 agents │   fixes   │  drag-drop│
│           │           │           │           │
│ ████████  │ ██████████│ ████████  │ ████████  │
└───────────┴───────────┴───────────┴───────────┘

Legend:
  🐝 = Claude-Flow Swarm (parallel agents)
  🤖 = Claude Direct (single instance)
  █  = Active development time

═══════════════════════════════════════════════════════════════════════════════
```

---

*Document generated as part of the Claude-Flow development debrief series.*
