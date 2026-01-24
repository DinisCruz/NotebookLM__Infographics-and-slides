# LLM Brief: Mermaid Diagram Templates

> Copy-paste templates for Mermaid diagrams in SEMANTIC-GRAPH.md files

---

## Quick Reference

| Diagram Type | Use For | Section |
|--------------|---------|---------|
| `flowchart LR/TB` | Workflows, pipelines, architectures | Domain-specific visual |
| `classDiagram` | Entity types, schema | Ontology |
| `mindmap` | Hierarchies, taxonomies | Taxonomy |
| `graph TB` | Relationships, knowledge graph | Knowledge Graph |

---

## 1. Flowchart Templates

### Left-to-Right Pipeline

```mermaid
flowchart LR
    subgraph input ["📥 INPUT"]
        A["Source Document"]
    end
    subgraph process ["⚙️ PROCESS"]
        B["Processing Step"]
    end
    subgraph output ["📤 OUTPUT"]
        C["Result 1"]
        D["Result 2"]
    end

    A --> B --> C
    B --> D

    style input fill:#e8f5e9,stroke:#4caf50
    style process fill:#e3f2fd,stroke:#2196f3
    style output fill:#fff3e0,stroke:#ff9800
```

### Top-to-Bottom Workflow

```mermaid
flowchart TB
    subgraph phase1 ["🔵 PHASE 1"]
        A["Step 1"]
        B["Step 2"]
    end
    subgraph phase2 ["🟢 PHASE 2"]
        C["Step 3"]
        D["Step 4"]
    end
    subgraph phase3 ["🟠 PHASE 3"]
        E["Step 5"]
    end

    A --> B --> C
    B --> D
    C --> E
    D --> E

    style phase1 fill:#e3f2fd,stroke:#1976d2
    style phase2 fill:#e8f5e9,stroke:#388e3c
    style phase3 fill:#fff3e0,stroke:#f57c00
```

### Decision Flow

```mermaid
flowchart TB
    A["Start"] --> B{"Decision?"}
    B -->|Yes| C["Path A"]
    B -->|No| D["Path B"]
    C --> E["End"]
    D --> E

    style A fill:#e8f5e9,stroke:#4caf50
    style B fill:#fff9c4,stroke:#f9a825
    style E fill:#e3f2fd,stroke:#1976d2
```

### Architecture Diagram

```mermaid
flowchart TB
    subgraph frontend ["🖥️ FRONTEND"]
        UI["Web UI"]
    end
    subgraph backend ["⚙️ BACKEND"]
        API["API Server"]
        DB["Database"]
    end
    subgraph external ["☁️ EXTERNAL"]
        S3["S3 Storage"]
    end

    UI <--> API
    API <--> DB
    API --> S3

    style frontend fill:#e3f2fd,stroke:#1976d2
    style backend fill:#e8f5e9,stroke:#388e3c
    style external fill:#fff3e0,stroke:#f57c00
```

---

## 2. Class Diagram Templates (Ontology)

### Basic Ontology

```mermaid
classDiagram
    class Concept {
        <<type>>
        An abstract idea or notion
    }
    class Practice {
        <<type>>
        A specific technique or method
    }
    class Challenge {
        <<type>>
        A problem or difficulty
    }
    class Tool {
        <<type>>
        Software or technology
    }

    Concept --> Practice : includes
    Practice --> Challenge : addresses
    Tool --> Practice : enables
```

### Methodology Ontology

```mermaid
classDiagram
    class Methodology {
        <<type>>
        Systematic approach to work
    }
    class Principle {
        <<type>>
        Guiding rule or belief
    }
    class Practice {
        <<type>>
        Specific technique
    }
    class Benefit {
        <<type>>
        Positive outcome
    }
    class Limitation {
        <<type>>
        Constraint or drawback
    }

    Methodology --> Principle : based_on
    Methodology --> Practice : includes
    Practice --> Benefit : produces
    Practice --> Limitation : has
```

### Technical Ontology

```mermaid
classDiagram
    class System {
        <<type>>
        A complete solution
    }
    class Component {
        <<type>>
        Part of a system
    }
    class Interface {
        <<type>>
        Connection point
    }
    class DataFlow {
        <<type>>
        Information movement
    }

    System --> Component : contains
    Component --> Interface : exposes
    Interface --> DataFlow : enables
```

---

## 3. Mindmap Templates (Taxonomy)

### Topic Hierarchy

```mermaid
mindmap
  root((Main Topic))
    Category A
      Subcategory A1
      Subcategory A2
    Category B
      Subcategory B1
      Subcategory B2
      Subcategory B3
    Category C
      Subcategory C1
```

### Methodology Breakdown

```mermaid
mindmap
  root((Methodology))
    Principles
      Principle 1
      Principle 2
    Practices
      Practice A
      Practice B
      Practice C
    Tools
      Tool 1
      Tool 2
    Benefits
      Benefit X
      Benefit Y
```

### Technical Domain

```mermaid
mindmap
  root((Software Architecture))
    Frontend
      React
      Vue
      Angular
    Backend
      API Design
      Authentication
      Database
    Infrastructure
      Cloud
      Containers
      CI/CD
```

---

## 4. Knowledge Graph Templates

### Basic Relationship Graph

```mermaid
graph TB
    subgraph concepts ["📚 CONCEPTS"]
        A["Concept A\n(type)"]
        B["Concept B\n(type)"]
    end
    subgraph processes ["⚙️ PROCESSES"]
        C["Process C\n(type)"]
    end

    A -->|relates_to| B
    B -->|enables| C
    A -.->|influences| C

    style A fill:#e3f2fd,stroke:#1976d2
    style B fill:#e3f2fd,stroke:#1976d2
    style C fill:#fff3e0,stroke:#f57c00
```

### Problem-Solution Graph

```mermaid
graph TB
    subgraph problems ["❌ PROBLEMS"]
        P1["Problem 1\n(challenge)"]
        P2["Problem 2\n(challenge)"]
    end
    subgraph solutions ["✅ SOLUTIONS"]
        S1["Solution A\n(methodology)"]
    end
    subgraph outcomes ["🎯 OUTCOMES"]
        O1["Benefit 1\n(benefit)"]
        O2["Benefit 2\n(benefit)"]
    end

    P1 -.->|addressed_by| S1
    P2 -.->|addressed_by| S1
    S1 -->|produces| O1
    S1 -->|produces| O2

    style P1 fill:#ffcdd2,stroke:#c62828
    style P2 fill:#ffcdd2,stroke:#c62828
    style S1 fill:#c8e6c9,stroke:#2e7d32
    style O1 fill:#e3f2fd,stroke:#1976d2
    style O2 fill:#e3f2fd,stroke:#1976d2
```

### Multi-Category Graph

```mermaid
graph TB
    subgraph data ["📊 DATA"]
        D1["Input Data\n(artifact)"]
        D2["Output Data\n(artifact)"]
    end
    subgraph tools ["🔧 TOOLS"]
        T1["Tool A\n(tool)"]
        T2["Tool B\n(tool)"]
    end
    subgraph people ["👥 ROLES"]
        R1["Developer\n(role)"]
    end

    D1 -->|processed_by| T1
    T1 -->|produces| D2
    T1 -->|integrated_with| T2
    R1 -->|uses| T1
    R1 -->|uses| T2

    style D1 fill:#e1bee7,stroke:#7b1fa2
    style D2 fill:#e1bee7,stroke:#7b1fa2
    style T1 fill:#fff3e0,stroke:#f57c00
    style T2 fill:#fff3e0,stroke:#f57c00
    style R1 fill:#e8f5e9,stroke:#388e3c
```

---

## Color Palette Reference

| Category | Fill | Stroke | CSS |
|----------|------|--------|-----|
| Problems | `#ffcdd2` | `#c62828` | Light/Dark Red |
| Solutions | `#c8e6c9` | `#2e7d32` | Light/Dark Green |
| Concepts | `#e3f2fd` | `#1976d2` | Light/Dark Blue |
| Processes | `#fff3e0` | `#f57c00` | Light/Dark Orange |
| Data | `#e1bee7` | `#7b1fa2` | Light/Dark Purple |
| Neutral | `#f5f5f5` | `#616161` | Light/Dark Gray |
| Warning | `#fff9c4` | `#f9a825` | Light/Dark Yellow |

---

## Edge Styles

| Style | Syntax | Use For |
|-------|--------|---------|
| Solid arrow | `-->` | Direct relationship |
| Dashed arrow | `-.->` | Indirect/weak relationship |
| Thick arrow | `==>` | Strong relationship |
| Labeled | `-->|label|` | Named relationship |
| Bidirectional | `<-->` | Two-way relationship |

---

## Node Shapes

| Shape | Syntax | Use For |
|-------|--------|---------|
| Rectangle | `A["Text"]` | Default, concepts |
| Round | `A("Text")` | Processes, actions |
| Stadium | `A(["Text"])` | Start/end points |
| Diamond | `A{"Text"}` | Decisions |
| Circle | `A(("Text"))` | Central concepts |
| Hexagon | `A{{"Text"}}` | External systems |

---

## Tips

1. **Keep it simple** — 8-15 nodes maximum
2. **Use subgraphs** — Group related nodes
3. **Consistent colors** — Same type = same color
4. **Readable labels** — Use `\n` for multi-line
5. **Meaningful edges** — Label relationships
6. **Test rendering** — Check in GitHub preview
