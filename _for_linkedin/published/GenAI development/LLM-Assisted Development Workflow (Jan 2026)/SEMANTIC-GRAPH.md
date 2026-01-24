# LLM-Assisted Development Workflow (Jan 2026)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This workflow document describes an AI-augmented software engineering approach where the LLM serves as a powerful pair programmer requiring clear direction, context, and human oversight. The five-stage process emphasizes context engineering (curating relevant information without overloading), iterative planning before coding, comprehensive specifications as blueprints, LLM-driven code generation and test creation, and rigorous human-in-the-loop verification. Key insight: the workflow assumes developer expertise — AI amplifies skilled engineers but isn't a substitute for fundamental development skills; treat the LLM like an "over-confident junior developer" whose work requires mandatory review.

---

## Key Concepts

- **Context Engineering**: Curating prompt content intelligently — giving the model "only what matters, the right information, in the right format, at the right time" rather than dumping entire codebases.

- **Lost in the Middle Phenomenon**: How models miss important details buried in overly long prompts, leading to confused output, token waste, and inconsistent code.

- **Iterative Planning Dialogue**: Back-and-forth conversation with LLM to brainstorm requirements and design before any implementation — "waterfall in 15 minutes."

- **Specification Brief**: Comprehensive document (often 100s-1000+ lines) capturing architecture, data models, interfaces, behaviors, and testing strategy as the contract for implementation.

- **Test-Driven Refinement Loop**: Running AI-generated tests one by one, using failures to iteratively debug and refine code — forcing thorough review of AI output.

- **IFD (Iterative Flow Development)**: Approach where LLM helps build end-to-end slices including backend, tests, and UI prototypes for testing complex data flows.

---

## Core Arguments

1. AI coding assistants require skill, structure, and clear process — the workflow is about the developer's expertise guiding AI at every step, not letting AI take over blindly.

2. Providing too much context hurts performance ("Lost in the Middle"); strategic context selection through phased delivery leads to better results than overwhelming the AI.

3. Planning first prevents wasted cycles — a comprehensive spec/plan is the cornerstone that aligns human and AI before coding begins, like a "waterfall in 15 minutes."

4. The LLM can generate multiple files (10-40+) in one response when given a detailed spec, but output must be treated as a first draft requiring human refinement.

5. LLM-generated tests act as a second pair of eyes on implementation — if the AI writes a test for a scenario it forgot to handle in code, that signals a missing feature.

6. All software engineering practices (design before coding, testing, version control, standards) are MORE important when AI writes half your code — AI amplifies discipline, doesn't replace it.

---

## Key Quotes

> "Context engineering isn't about stuffing more into the prompt — it's about curating smarter."

> "An LLM coding partner is like an over-confident junior developer — you must rigorously review and test everything it produces."

> "Planning first forces you and the AI onto the same page and prevents wasted cycles."

> "AI doesn't replace engineering discipline; it amplifies it."

---

## Tags

`llm-workflow` `ai-pair-programming` `context-engineering` `specification-driven` `test-driven-development` `code-generation` `human-in-the-loop` `developer-productivity` `ai-augmented-engineering` `iterative-development` `prompt-engineering` `software-architecture`

---

## Search Phrases

- "LLM-assisted development workflow 2026"
- "context engineering for AI coding"
- "specification-driven AI development"
- "treating LLM as junior developer"
- "iterative planning with AI pair programmer"
- "test-driven refinement AI code"
- "context window paradox LLM"
- "AI-augmented software engineering"
- "human-in-the-loop code review"
- "multi-file code generation LLM"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Workflow Guide / Best Practices |
| **Domain** | GenAI Development / Software Engineering |
| **Sub-domain** | Development Methodologies |
| **Format** | PDF (10 pages) |
| **Date** | January 2026 |
| **Authors** | Dinis Cruz, ChatGPT Deep Research |
| **Target Audience** | Developers, Engineering Leaders |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `related_to` | Comparing Vibe Coding to Traditional Outsourced Development |
| `related_to` | Vibe Coding Workflow for Rapid Product-Market Fit |
| `references` | Addy Osmani - My LLM Coding Workflow |
| `references` | Thoughtworks - Context Engineering |
| `references` | Assembled - Writing Tests with LLMs |
| `part_of` | GenAI Development Best Practices |

---

## Semantic Knowledge Graph

### The Five-Stage Workflow (Visual)

```mermaid
flowchart LR
    subgraph stage1 ["1️⃣ REQUIREMENTS"]
        REQ["📋 Define Problem"]
        CTX["🎯 Focused Context"]
    end

    subgraph stage2 ["2️⃣ PLANNING"]
        BRAIN["🧠 Brainstorm\nwith LLM"]
        ARCH["📐 Architecture"]
    end

    subgraph stage3 ["3️⃣ SPECIFICATION"]
        SPEC["📝 Detailed Spec\n(100-1000+ lines)"]
    end

    subgraph stage4 ["4️⃣ GENERATION"]
        GEN["🤖 LLM Generates\nCode"]
        FILES["📁 Multiple Files\n(10-40+)"]
    end

    subgraph stage5 ["5️⃣ TESTING"]
        TEST["🧪 Tests"]
        DEBUG["🔧 Debug"]
        REVIEW["👁️ Human Review"]
    end

    stage1 --> stage2
    stage2 --> stage3
    stage3 --> stage4
    stage4 --> stage5
    stage5 -->|"iterate"| stage4

    style stage1 fill:#e3f2fd,stroke:#1565c0
    style stage2 fill:#fff3e0,stroke:#ef6c00
    style stage3 fill:#f3e5f5,stroke:#7b1fa2
    style stage4 fill:#e8f5e9,stroke:#2e7d32
    style stage5 fill:#ffecb3,stroke:#ff8f00
```

### Context Engineering Pattern (Visual)

```mermaid
flowchart TB
    subgraph bad ["❌ BAD: Overloading"]
        DUMP["Dump Entire\nCodebase"]
        MIDDLE["Lost in\nthe Middle"]
        CONFUSED["Confused\nOutput"]
    end

    subgraph good ["✅ GOOD: Curated"]
        SELECT["Select Relevant\nSnippets"]
        PHASED["Phased\nDelivery"]
        FOCUSED["Focused\nOutput"]
    end

    DUMP --> MIDDLE --> CONFUSED
    SELECT --> PHASED --> FOCUSED

    style bad fill:#ffcdd2,stroke:#c62828
    style good fill:#c8e6c9,stroke:#2e7d32
```

### Human-in-the-Loop Model (Visual)

```mermaid
flowchart TB
    subgraph human ["👤 HUMAN (Expert)"]
        ARCH_H["Architect"]
        REVIEWER["Reviewer"]
        PROMPT["Prompt Engineer"]
    end

    subgraph llm ["🤖 LLM (Junior Dev)"]
        GENERATE["Generate Code"]
        SUGGEST["Suggest Solutions"]
        WRITE_TEST["Write Tests"]
    end

    subgraph output ["📦 OUTPUT"]
        CODE["Code"]
        TESTS["Tests"]
    end

    ARCH_H -->|"spec"| GENERATE
    PROMPT -->|"context"| SUGGEST
    GENERATE --> CODE
    WRITE_TEST --> TESTS
    CODE --> REVIEWER
    TESTS --> REVIEWER
    REVIEWER -->|"feedback"| GENERATE

    style human fill:#e3f2fd,stroke:#1565c0
    style llm fill:#fff3e0,stroke:#ef6c00
    style output fill:#c8e6c9,stroke:#2e7d32
```

### Test-Driven Refinement Loop (Visual)

```mermaid
flowchart LR
    subgraph cycle ["🔄 REFINEMENT CYCLE"]
        GEN["LLM Generates\nCode + Tests"]
        RUN["Run Tests\nOne by One"]
        FAIL["Test Fails?"]
        DEBUG["Debug with\nLLM"]
        PASS["All Pass"]
    end

    GEN --> RUN
    RUN --> FAIL
    FAIL -->|"yes"| DEBUG
    DEBUG --> GEN
    FAIL -->|"no"| PASS

    style GEN fill:#e3f2fd,stroke:#1565c0
    style RUN fill:#fff3e0,stroke:#ef6c00
    style FAIL fill:#ffcdd2,stroke:#c62828
    style DEBUG fill:#f3e5f5,stroke:#7b1fa2
    style PASS fill:#c8e6c9,stroke:#2e7d32
```

---

### Ontology

> The ontology defines the **types of entities** (nodes) and **relationships** (predicates) in this knowledge domain.

#### Node Types

```mermaid
classDiagram
    class Stage {
        <<workflow>>
        A workflow stage
    }
    class Practice {
        <<method>>
        A recommended practice
    }
    class Artifact {
        <<output>>
        A produced artifact
    }
    class Role {
        <<team>>
        A job function
    }
    class Antipattern {
        <<warning>>
        Something to avoid
    }

    Stage -- Artifact : produces
    Practice -- Antipattern : avoids
    Role -- Stage : performs
```

| Ref | Description | Examples |
|-----|-------------|----------|
| `stage` | A workflow stage | Requirements, Planning, Specification, Generation, Testing |
| `practice` | A recommended practice | Context Engineering, Human-in-the-Loop |
| `artifact` | A produced artifact | Specification Document, Generated Code, Tests |
| `role` | A job function | Architect, Reviewer, Prompt Engineer |
| `antipattern` | Something to avoid | Context Overloading, Blind Trust |

#### Predicates (Relationships)

```mermaid
graph LR
    A[Stage] -->|precedes| B[Stage]
    B -->|follows| A

    C[Stage] -->|produces| D[Artifact]
    D -->|produced_by| C

    E[Stage] -->|requires| F[Practice]
    F -->|required_by| E

    G[Practice] -->|avoids| H[Antipattern]
    H -->|avoided_by| G
```

| Ref | Inverse | Description |
|-----|---------|-------------|
| `precedes` | `follows` | Workflow stage order |
| `produces` | `produced_by` | Stage producing artifact |
| `requires` | `required_by` | Dependency relationship |
| `avoids` | `avoided_by` | Antipattern prevention |

---

### Taxonomy

> Hierarchical classification of concepts in this domain.

```mermaid
mindmap
  root((LLM-Assisted\nWorkflow))
    Stages
      1 Requirements & Context
      2 Iterative Planning
      3 Specification Brief
      4 Code Generation
      5 Testing & Refinement
    Practices
      Context Engineering
        Curated Snippets
        Phased Delivery
        Right Information Right Time
      Human Oversight
        Review All Output
        Test Everything
        Verify Logic
      Test-Driven Refinement
        Run Tests One by One
        Debug with LLM
        Iterate Until Pass
    Artifacts
      Focused Context
      Design Spec
      Generated Code
      Test Suite
    Roles
      Architect
      Code Reviewer
      Prompt Engineer
```

**ASCII Tree View:**

```
llm_workflow
├── stages
│   ├── requirements_context
│   ├── iterative_planning
│   ├── specification_brief
│   ├── code_generation
│   └── testing_refinement
├── practices
│   ├── context_engineering
│   ├── human_oversight
│   └── test_driven_refinement
├── artifacts
│   ├── focused_context
│   ├── design_spec
│   ├── generated_code
│   └── test_suite
└── roles
    ├── architect
    ├── code_reviewer
    └── prompt_engineer
```

---

### Knowledge Graph

> Visual representation of entities and their relationships.

```mermaid
graph TB
    subgraph stages ["📋 Workflow Stages"]
        REQ["Requirements\n(stage)"]
        PLAN["Planning\n(stage)"]
        SPEC["Specification\n(stage)"]
        GEN["Generation\n(stage)"]
        TEST["Testing\n(stage)"]
    end

    subgraph practices ["🎯 Practices"]
        CTXENG["Context Engineering\n(practice)"]
        HUMAN["Human-in-the-Loop\n(practice)"]
    end

    subgraph antipatterns ["⚠️ Antipatterns"]
        OVERLOAD["Context Overload\n(antipattern)"]
        BLIND["Blind Trust\n(antipattern)"]
    end

    subgraph artifacts ["📦 Artifacts"]
        SPECDOC["Spec Document\n(artifact)"]
        CODE["Generated Code\n(artifact)"]
    end

    REQ -->|precedes| PLAN
    PLAN -->|precedes| SPEC
    SPEC -->|precedes| GEN
    GEN -->|precedes| TEST
    PLAN -->|produces| SPECDOC
    GEN -->|produces| CODE
    CTXENG -->|avoids| OVERLOAD
    HUMAN -->|avoids| BLIND
    TEST -->|requires| HUMAN

    style REQ fill:#e3f2fd,stroke:#1565c0
    style PLAN fill:#fff3e0,stroke:#ef6c00
    style SPEC fill:#f3e5f5,stroke:#7b1fa2
    style GEN fill:#e8f5e9,stroke:#2e7d32
    style TEST fill:#ffecb3,stroke:#ff8f00
    style CTXENG fill:#e1bee7,stroke:#7b1fa2
    style HUMAN fill:#e1bee7,stroke:#7b1fa2
    style OVERLOAD fill:#ffcdd2,stroke:#c62828
    style BLIND fill:#ffcdd2,stroke:#c62828
    style SPECDOC fill:#e8eaf6,stroke:#3f51b5
    style CODE fill:#e8eaf6,stroke:#3f51b5
```

#### Nodes (for database import)

| ID | Type | Name |
|----|------|------|
| `requirements` | `stage` | Requirements & Context |
| `planning` | `stage` | Iterative Planning |
| `specification` | `stage` | Specification Brief |
| `generation` | `stage` | Code Generation |
| `testing` | `stage` | Testing & Refinement |
| `context_engineering` | `practice` | Context Engineering |
| `human_loop` | `practice` | Human-in-the-Loop |
| `context_overload` | `antipattern` | Context Overloading |
| `spec_document` | `artifact` | Specification Document |

#### Edges (for database import)

| From | Predicate | To |
|------|-----------|-----|
| `requirements` | `precedes` | `planning` |
| `planning` | `precedes` | `specification` |
| `specification` | `precedes` | `generation` |
| `generation` | `precedes` | `testing` |
| `context_engineering` | `avoids` | `context_overload` |
| `planning` | `produces` | `spec_document` |
| `testing` | `requires` | `human_loop` |

---

### Cypher Import (Neo4j)

```cypher
// Create nodes
CREATE (req:Stage {id: 'requirements', name: 'Requirements & Context', order: 1})
CREATE (plan:Stage {id: 'planning', name: 'Iterative Planning', order: 2})
CREATE (spec:Stage {id: 'specification', name: 'Specification Brief', order: 3})
CREATE (gen:Stage {id: 'generation', name: 'Code Generation', order: 4})
CREATE (test:Stage {id: 'testing', name: 'Testing & Refinement', order: 5})
CREATE (ctxeng:Practice {id: 'context_engineering', name: 'Context Engineering'})
CREATE (human:Practice {id: 'human_loop', name: 'Human-in-the-Loop'})
CREATE (overload:Antipattern {id: 'context_overload', name: 'Context Overloading'})
CREATE (specdoc:Artifact {id: 'spec_document', name: 'Specification Document'})
CREATE (code:Artifact {id: 'generated_code', name: 'Generated Code'})

// Create relationships
CREATE (req)-[:PRECEDES]->(plan)
CREATE (plan)-[:PRECEDES]->(spec)
CREATE (spec)-[:PRECEDES]->(gen)
CREATE (gen)-[:PRECEDES]->(test)
CREATE (ctxeng)-[:AVOIDS]->(overload)
CREATE (plan)-[:PRODUCES]->(specdoc)
CREATE (gen)-[:PRODUCES]->(code)
CREATE (test)-[:REQUIRES]->(human)
```
