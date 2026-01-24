# Vibe Coding Workflow for Rapid Product-Market Fit Discovery

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This workflow document describes a rapid prototyping approach for product-market fit discovery using AI-assisted "vibe coding." Built around a MITM proxy infrastructure that intercepts web traffic, caches pages in S3, and serves multiple transformed versions via browser cookies, the workflow enables CEO/CPO "vibe coders" to quickly create webpage modifications and demo them to pilot customers. Key insight: customer-defined success means letting user reactions determine which features are desirable — the workflow empowers product leaders to dream up an idea and see it realized in a demo the same day, de-risking the product roadmap through validated customer feedback.

---

## Key Concepts

- **Vibe Coding**: Fast, iterative prototyping approach using AI-assisted coding where non-engineers describe changes in natural language and get working code or transformations without writing everything from scratch.

- **MITM Proxy Infrastructure**: Man-in-the-middle proxy intercepting HTTP requests, caching original pages in S3, and serving transformed versions based on browser cookie settings.

- **Dynamic Version Serving**: Proxy checks special cookie to decide which page version to serve — original if not set, or specific transformation if cookie specifies version ID.

- **Offline Page Modification**: Cached pages modified outside live browsing sessions using LLM prompts, creating multiple variants (content removed, elements blurred, highlights added) stored as separate S3 objects.

- **Customer-Defined Success**: Rather than guessing internally, customer reactions ("I love it" vs "Not useful") define which features are desirable — the ultimate validation is "Yes, I want this!"

- **CEO/CPO as Vibe Coders**: Product leads with enough technical background to use AI coding assistants (Cursor, ChatGPT) becoming primary drivers of rapid prototyping, focusing on UX rather than low-level code.

---

## Core Arguments

1. With technical foundation (proxy, caching, APIs) in place, the next step is finding what end-user experience delivers real value — validated directly with customers, not guessed internally.

2. The workflow divides roles so CTO ensures platform stability while CEO/CPO iterate on product ideas without being blocked by heavy engineering cycles — making discovery highly agile.

3. LLM-powered HTML manipulation (via prompts like "Remove negative-sentiment text") combined with AI coding assistants enables rapid transformation of pages without deep coding expertise.

4. Prototypes should feel like live website experiences to customers — taking snapshots and altering them while preserving click-through navigation greatly enhances feedback quality.

5. Ideas can be built and tested in hours/days instead of weeks; engineering investment only comes after validating an idea — cost-effective experimentation through cached pages and offline modifications.

6. Success metrics include: multiple prototypes conducted, live demo capability, customer enthusiasm for at least one feature, learning from failures, and product team empowerment to try ideas on a whim.

---

## Key Quotes

> "The term vibe coding encapsulates this quick, exploratory building approach."

> "Customer reactions define what features are desirable."

> "The CEO and CPO feel confident that they can dream up an idea and see it realized in a demo the same day."

> "It de-risks the product roadmap significantly."

---

## Tags

`vibe-coding` `product-market-fit` `rapid-prototyping` `mitm-proxy` `page-caching` `llm-transformation` `customer-validation` `ai-coding-assistant` `s3-storage` `cookie-versioning` `html-manipulation` `startup-methodology`

---

## Search Phrases

- "vibe coding workflow product discovery"
- "MITM proxy page transformation"
- "rapid prototyping product-market fit"
- "CEO CPO as vibe coders"
- "LLM HTML page modification"
- "customer-defined feature validation"
- "S3 page version caching"
- "cookie-based version serving"
- "AI-assisted prototyping workflow"
- "same-day idea to demo"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Workflow Guide / Methodology |
| **Domain** | GenAI Development / Product Discovery |
| **Sub-domain** | Prototyping / Customer Validation |
| **Format** | PDF (8 pages) |
| **Date** | January 2026 |
| **Authors** | Dinis Cruz, ChatGPT Deep Research |
| **Target Audience** | CEOs, CPOs, Product Leaders |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `related_to` | Comparing Vibe Coding to Traditional Outsourced Development |
| `related_to` | LLM-Assisted Development Workflow |
| `uses` | MitmProxy Service |
| `uses` | HTML Graph Service |
| `part_of` | MyFeeds.ai Architecture |

---

## Semantic Knowledge Graph

### The Vibe Coding Workflow (Visual)

```mermaid
flowchart TB
    subgraph step1 ["1️⃣ HYPOTHESIS"]
        IDEA["💡 Define Idea"]
    end

    subgraph step2 ["2️⃣ RETRIEVE"]
        CACHE["📥 Get Cached Pages"]
    end

    subgraph step3 ["3️⃣ TRANSFORM"]
        LLM["🤖 LLM Modification"]
    end

    subgraph step4 ["4️⃣ PREVIEW"]
        BEFORE["Before"]
        AFTER["After"]
    end

    subgraph step5 ["5️⃣ TEST"]
        BROWSER["🌐 Browser via Proxy"]
    end

    subgraph step6 ["6️⃣ SCALE"]
        MULTI["📄 Multiple Pages"]
    end

    subgraph step7 ["7️⃣ DEMO"]
        CUSTOMER["👤 Customer Demo"]
    end

    subgraph step8 ["8️⃣ ITERATE"]
        PIVOT["🔄 Iterate or Pivot"]
    end

    step1 --> step2
    step2 --> step3
    step3 --> step4
    step4 --> step5
    step5 --> step6
    step6 --> step7
    step7 --> step8
    step8 -->|"new idea"| step1

    style step1 fill:#e3f2fd,stroke:#1565c0
    style step2 fill:#e8f5e9,stroke:#2e7d32
    style step3 fill:#fff3e0,stroke:#ef6c00
    style step4 fill:#f3e5f5,stroke:#7b1fa2
    style step5 fill:#e0f7fa,stroke:#00838f
    style step6 fill:#fce4ec,stroke:#c2185b
    style step7 fill:#c8e6c9,stroke:#2e7d32
    style step8 fill:#ffecb3,stroke:#ff8f00
```

### Infrastructure Architecture (Visual)

```mermaid
flowchart LR
    subgraph user ["👤 USER"]
        BROWSER["Browser"]
    end

    subgraph proxy ["🔀 MITM PROXY"]
        INTERCEPT["Intercept\nRequests"]
        COOKIE["Check\nCookie"]
    end

    subgraph storage ["☁️ S3 STORAGE"]
        ORIGINAL["Original\nPages"]
        V1["Version 1\n(Modified)"]
        V2["Version 2\n(Modified)"]
        V3["Version 3\n(Modified)"]
    end

    subgraph tools ["🛠️ TRANSFORMATION"]
        LLM["LLM API"]
        HTML["HTML\nManipulation"]
    end

    BROWSER -->|"request"| INTERCEPT
    INTERCEPT --> COOKIE
    COOKIE -->|"no cookie"| ORIGINAL
    COOKIE -->|"version=1"| V1
    COOKIE -->|"version=2"| V2
    COOKIE -->|"version=3"| V3

    LLM --> HTML
    HTML --> V1
    HTML --> V2
    HTML --> V3

    style user fill:#e3f2fd,stroke:#1565c0
    style proxy fill:#fff3e0,stroke:#ef6c00
    style storage fill:#e8f5e9,stroke:#2e7d32
    style tools fill:#f3e5f5,stroke:#7b1fa2
```

### Role Division (Visual)

```mermaid
flowchart TB
    subgraph cto ["🔧 CTO"]
        INFRA["Platform Stability"]
        API["API Development"]
        SECURITY["Security"]
    end

    subgraph ceo_cpo ["💡 CEO/CPO (Vibe Coders)"]
        IDEAS["Feature Ideas"]
        PROTO["Rapid Prototypes"]
        DEMOS["Customer Demos"]
    end

    subgraph tools ["🤖 AI TOOLS"]
        CURSOR["Cursor"]
        CHATGPT["ChatGPT"]
        CLAUDE["Claude"]
    end

    subgraph outcome ["✅ OUTCOME"]
        VALIDATED["Validated Features"]
        ROADMAP["De-risked Roadmap"]
    end

    cto -->|"enables"| ceo_cpo
    tools -->|"empowers"| ceo_cpo
    ceo_cpo --> outcome

    style cto fill:#e8f5e9,stroke:#2e7d32
    style ceo_cpo fill:#fff3e0,stroke:#ef6c00
    style tools fill:#f3e5f5,stroke:#7b1fa2
    style outcome fill:#c8e6c9,stroke:#2e7d32
```

---

### Ontology

> The ontology defines the **types of entities** (nodes) and **relationships** (predicates) in this knowledge domain.

#### Node Types

```mermaid
classDiagram
    class Component {
        <<infrastructure>>
        Infrastructure component
    }
    class Role {
        <<team>>
        Team role
    }
    class Step {
        <<workflow>>
        Workflow step
    }
    class Tool {
        <<development>>
        Development tool
    }
    class Artifact {
        <<output>>
        Produced output
    }
    class Metric {
        <<measurement>>
        Success indicator
    }

    Role -- Step : performs
    Step -- Artifact : produces
    Tool -- Role : empowers
    Component -- Artifact : stores
```

| Ref | Description | Examples |
|-----|-------------|----------|
| `component` | Infrastructure component | MITM Proxy, S3 Cache, HTML API |
| `role` | Team role | CTO, CEO/CPO (Vibe Coder) |
| `step` | Workflow step | Define Hypothesis, Customer Demo |
| `tool` | Development tool | Cursor, ChatGPT, Claude |
| `artifact` | Produced output | Transformed Page, Prototype |
| `metric` | Success indicator | Customer Enthusiasm, Validated Feature |

#### Predicates (Relationships)

```mermaid
graph LR
    A[Component] -->|stores| B[Artifact]
    B -->|stored_in| A

    C[Role] -->|performs| D[Step]
    D -->|performed_by| C

    E[Step] -->|produces| F[Artifact]
    F -->|produced_by| E

    G[Step] -->|precedes| H[Step]
    H -->|follows| G
```

| Ref | Inverse | Description |
|-----|---------|-------------|
| `uses` | `used_by` | Component usage |
| `performs` | `performed_by` | Role performing step |
| `produces` | `produced_by` | Step producing artifact |
| `precedes` | `follows` | Workflow order |

---

### Taxonomy

> Hierarchical classification of concepts in this domain.

```mermaid
mindmap
  root((Vibe Coding\nWorkflow))
    Infrastructure
      MITM Proxy
      S3 Cache
      HTML Manipulation API
    Roles
      CTO
        Technical Lead
        Platform Stability
      CEO/CPO
        Vibe Coders
        Product Vision
    Workflow Steps
      1 Define Hypothesis
      2 Retrieve Pages
      3 LLM Modify
      4 Preview
      5 Browser Test
      6 Scale Prototype
      7 Customer Demo
      8 Iterate/Pivot
    Tools
      AI Coding Assistants
        Cursor
        ChatGPT
        Claude
      LLM APIs
```

**ASCII Tree View:**

```
vibe_coding_workflow
├── infrastructure
│   ├── mitm_proxy
│   ├── s3_cache
│   └── html_manipulation_api
├── roles
│   ├── cto (technical lead)
│   └── ceo_cpo (vibe coders)
├── workflow_steps
│   ├── define_hypothesis
│   ├── retrieve_pages
│   ├── llm_modify
│   ├── preview
│   ├── browser_test
│   ├── scale_prototype
│   ├── customer_demo
│   └── iterate_pivot
└── tools
    ├── ai_coding_assistant
    └── llm_api
```

---

### Knowledge Graph

> Visual representation of entities and their relationships.

```mermaid
graph TB
    subgraph components ["🔧 Components"]
        PROXY["MITM Proxy\n(component)"]
        S3["S3 Cache\n(component)"]
    end

    subgraph roles ["👥 Roles"]
        VIBE["CEO/CPO Vibe Coder\n(role)"]
        CTO["CTO\n(role)"]
    end

    subgraph steps ["📋 Steps"]
        HYPO["Define Hypothesis\n(step)"]
        DEMO["Customer Demo\n(step)"]
    end

    subgraph tools ["🛠️ Tools"]
        AI["AI Coding Assistant\n(tool)"]
    end

    subgraph artifacts ["📦 Artifacts"]
        PAGE["Transformed Page\n(artifact)"]
    end

    subgraph metrics ["📊 Metrics"]
        VALID["Customer Validation\n(metric)"]
    end

    VIBE -->|performs| HYPO
    VIBE -->|uses| AI
    CTO -->|maintains| PROXY
    PROXY -->|serves| PAGE
    S3 -->|stores| PAGE
    HYPO -->|precedes| DEMO
    DEMO -->|produces| VALID

    style PROXY fill:#fff3e0,stroke:#ef6c00
    style S3 fill:#e8f5e9,stroke:#2e7d32
    style VIBE fill:#e3f2fd,stroke:#1565c0
    style CTO fill:#e3f2fd,stroke:#1565c0
    style HYPO fill:#f3e5f5,stroke:#7b1fa2
    style DEMO fill:#f3e5f5,stroke:#7b1fa2
    style AI fill:#ffecb3,stroke:#ff8f00
    style PAGE fill:#fce4ec,stroke:#c2185b
    style VALID fill:#c8e6c9,stroke:#2e7d32
```

#### Nodes (for database import)

| ID | Type | Name |
|----|------|------|
| `mitm_proxy` | `component` | MITM Proxy |
| `s3_cache` | `component` | S3 Page Cache |
| `vibe_coder` | `role` | CEO/CPO Vibe Coder |
| `define_hypothesis` | `step` | Define Hypothesis |
| `customer_demo` | `step` | Demo to Customer |
| `ai_assistant` | `tool` | AI Coding Assistant |
| `transformed_page` | `artifact` | Transformed Page Version |
| `customer_validation` | `metric` | Customer Enthusiasm |

#### Edges (for database import)

| From | Predicate | To |
|------|-----------|-----|
| `vibe_coder` | `performs` | `define_hypothesis` |
| `vibe_coder` | `uses` | `ai_assistant` |
| `mitm_proxy` | `serves` | `transformed_page` |
| `s3_cache` | `stores` | `transformed_page` |
| `define_hypothesis` | `precedes` | `customer_demo` |
| `customer_demo` | `produces` | `customer_validation` |

---

### Cypher Import (Neo4j)

```cypher
// Create nodes
CREATE (proxy:Component {id: 'mitm_proxy', name: 'MITM Proxy'})
CREATE (s3:Component {id: 's3_cache', name: 'S3 Page Cache'})
CREATE (vibe:Role {id: 'vibe_coder', name: 'CEO/CPO Vibe Coder'})
CREATE (cto:Role {id: 'cto', name: 'CTO'})
CREATE (hypothesis:Step {id: 'define_hypothesis', name: 'Define Hypothesis', order: 1})
CREATE (demo:Step {id: 'customer_demo', name: 'Demo to Customer', order: 7})
CREATE (ai:Tool {id: 'ai_assistant', name: 'AI Coding Assistant'})
CREATE (page:Artifact {id: 'transformed_page', name: 'Transformed Page Version'})
CREATE (validation:Metric {id: 'customer_validation', name: 'Customer Enthusiasm'})

// Create relationships
CREATE (vibe)-[:PERFORMS]->(hypothesis)
CREATE (vibe)-[:USES]->(ai)
CREATE (cto)-[:MAINTAINS]->(proxy)
CREATE (proxy)-[:SERVES]->(page)
CREATE (s3)-[:STORES]->(page)
CREATE (hypothesis)-[:PRECEDES]->(demo)
CREATE (demo)-[:PRODUCES]->(validation)
```
