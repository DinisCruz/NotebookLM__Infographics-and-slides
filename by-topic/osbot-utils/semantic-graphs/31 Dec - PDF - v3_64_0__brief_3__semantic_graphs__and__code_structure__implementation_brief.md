# (Brief 3) Semantic Graphs & Code Structure - Implementation Brief

**Version:** v3.64.0  
**Status:** New Implementation  
**Target:** OSBot-Utils (`osbot_utils.helpers.semantic_graphs` and `osbot_utils.helpers.python_call_flow.code_structure`)  
**Created:** December 2024  
**Prerequisites:** Brief 2 (Call Flow Analyzer) - Phase 1 Complete  

---

## Executive Summary

This document specifies the implementation of a **generic semantic graph framework** and its first concrete application: a **Python code structure graph**. This work extends the Call Flow Analyzer (Brief 2) by introducing proper graph-based representation of code structure, replacing the current approach of storing structural metadata as flat attributes on call flow nodes.

**The Core Insight:** The current Call Flow Analyzer stores module names, file paths, and qualified names as string attributes on every node. This is redundant, inflexible, and misses the opportunity to model code structure as a first-class graph. By separating concerns into:

1. **Semantic Graphs** — A generic, reusable framework for typed graphs with formal ontologies
2. **Code Structure** — A specific application modeling Python package/module/class/method relationships

We achieve both a powerful reusable abstraction AND a cleaner call flow implementation where behavioral nodes simply reference structural nodes.

**Key Deliverables:**

1. `osbot_utils.helpers.semantic_graphs` — Generic framework (ontology, taxonomy, rules, graph)
2. `osbot_utils.helpers.python_call_flow.code_structure` — Python code structure using semantic graphs
3. Refactored `Schema__Call_Flow__Node` — Lightweight nodes referencing structure

---

## Part 1: Problem Analysis

### 1.1 Current State (Post Brief 2)

The Call Flow Analyzer produces graphs with nodes like:

```python
Schema__Call_Graph__Node(
    node_id      = 'c0000001',
    name         = 'sample_function',
    full_name    = 'test_Call_Flow__Analyzer.sample_function',    # Redundant
    node_type    = 'function',
    module       = 'test_Call_Flow__Analyzer',                    # Repeated everywhere
    file_path    = '/path/to/test_Call_Flow__Analyzer.py',        # Repeated everywhere
    depth        = 0,
    calls        = ['c0000002'],
    called_by    = [],
    line_number  = 57,
    ...
)
```

**Problems with this approach:**

| Issue | Description |
|-------|-------------|
| **Redundancy** | Module name repeated on every node in that module |
| **No relationships** | "Module contains class" is implicit, not queryable |
| **Mixed concerns** | Behavioral (calls) and structural (module) on same node |
| **No hierarchy** | Package→Module→Class→Method not captured |
| **String matching** | Finding "all classes in package X" requires string parsing |
| **No reusability** | Structure is specific to call flow, can't use elsewhere |

### 1.2 The Insight: Two Distinct Graphs

Code analysis actually involves TWO orthogonal concerns:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                                                                                  │
│   CODE STRUCTURE GRAPH                      CALL FLOW GRAPH                      │
│   (Static: What exists, where)              (Dynamic: Who calls whom)            │
│                                                                                  │
│   ┌─────────┐                               ┌─────────────────┐                  │
│   │ package │                               │ Call_Flow_Node  │                  │
│   └────┬────┘                               │ structure_ref ──┼───┐              │
│        │ has                                │ depth: 0        │   │              │
│        ▼                                    │ is_entry: true  │   │              │
│   ┌─────────┐                               └────────┬────────┘   │              │
│   │ module  │                                        │            │              │
│   └────┬────┘                                    calls│            │ references  │
│        │ defines                                     ▼            │              │
│        ▼                                    ┌─────────────────┐   │              │
│   ┌─────────┐                               │ Call_Flow_Node  │   │              │
│   │  class  │◄──────────────────────────────┼─structure_ref   │   │              │
│   └────┬────┘                               │ depth: 1        │   │              │
│        │ has                                └─────────────────┘   │              │
│        ▼                                                          │              │
│   ┌─────────┐◄────────────────────────────────────────────────────┘              │
│   │ method  │                                                                    │
│   └─────────┘                                                                    │
│                                                                                  │
│   "Where does this code live?"              "What does it call?"                 │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 1.3 The Deeper Insight: Generic Semantic Graphs

The code structure graph is just ONE instance of a more general pattern. Many domains need:

- **Typed nodes** — Different kinds of entities
- **Typed edges** — Different kinds of relationships  
- **Formal ontology** — What types exist, how they can connect
- **Bidirectional navigation** — "Class has method" AND "method in class"
- **Validation** — Ensure edges connect valid source→target types
- **Rules** — Domain-specific constraints (transitivity, cardinality)

This pattern applies to:

| Domain | Node Types | Edge Types |
|--------|------------|------------|
| Code Structure | Package, Module, Class, Method | has, defines, inherits_from |
| Organization | Team, Person, Role | manages, reports_to, member_of |
| Knowledge Base | Concept, Fact, Source | relates_to, supports, contradicts |
| File System | Directory, File, Symlink | contains, links_to |

**Solution:** Build a generic **Semantic Graphs** framework, then use it for Code Structure.

---

## Part 2: Architecture Overview

### 2.1 Two-Project Structure

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              IMPLEMENTATION STRUCTURE                            │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  osbot_utils/helpers/                                                            │
│  │                                                                               │
│  ├── semantic_graphs/              ◄─── GENERIC FRAMEWORK (New)                  │
│  │   ├── ontology/                      Reusable across ANY domain               │
│  │   ├── taxonomy/                      No Python-specific knowledge             │
│  │   ├── rules/                         Pure graph structure + typing            │
│  │   └── graph/                                                                  │
│  │                                                                               │
│  └── python_call_flow/             ◄─── PYTHON-SPECIFIC (Existing + Extended)    │
│      ├── code_structure/                Uses semantic_graphs framework           │
│      │   └── data/                      Contains code_structure ontology         │
│      │       └── ontology__code_structure.json                                   │
│      │                                                                           │
│      └── call_flow/                     Existing analyzer (refactored)           │
│          └── (existing files)           Nodes now reference structure_ref        │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Dependency Flow

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              DEPENDENCY HIERARCHY                                │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│                        ┌──────────────────────────┐                              │
│                        │   osbot_utils.type_safe  │                              │
│                        │   (Foundation Layer)     │                              │
│                        └────────────┬─────────────┘                              │
│                                     │                                            │
│                                     │ provides Type_Safe, Safe_* primitives      │
│                                     ▼                                            │
│                        ┌──────────────────────────┐                              │
│                        │    semantic_graphs       │                              │
│                        │   (Generic Framework)    │                              │
│                        │                          │                              │
│                        │  • Ontology schemas      │                              │
│                        │  • Taxonomy schemas      │                              │
│                        │  • Rule schemas          │                              │
│                        │  • Graph schemas         │                              │
│                        └────────────┬─────────────┘                              │
│                                     │                                            │
│                                     │ provides graph infrastructure              │
│                                     ▼                                            │
│            ┌────────────────────────┴────────────────────────┐                   │
│            │                                                 │                   │
│            ▼                                                 ▼                   │
│  ┌───────────────────────┐                     ┌───────────────────────┐         │
│  │ python_call_flow/     │                     │ (future domains)      │         │
│  │ code_structure        │                     │                       │         │
│  │                       │                     │ • organization_graph  │         │
│  │ • Code structure      │                     │ • knowledge_base      │         │
│  │   ontology (JSON)     │                     │ • dependency_graph    │         │
│  │ • Python-specific     │                     │ • ...                 │         │
│  │   rules (JSON)        │                     │                       │         │
│  └───────────┬───────────┘                     └───────────────────────┘         │
│              │                                                                   │
│              │ uses structure nodes                                              │
│              ▼                                                                   │
│  ┌───────────────────────┐                                                       │
│  │ python_call_flow/     │                                                       │
│  │ call_flow             │                                                       │
│  │                       │                                                       │
│  │ • Behavioral edges    │                                                       │
│  │ • Lightweight nodes   │                                                       │
│  │ • References to       │                                                       │
│  │   structure_ref       │                                                       │
│  └───────────────────────┘                                                       │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 2.3 Semantic Graphs Internal Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         SEMANTIC GRAPHS FRAMEWORK                                │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                           ONTOLOGY                                       │    │
│  │                    "What types exist?"                                   │    │
│  │                    "How can they connect?"                               │    │
│  │                                                                          │    │
│  │  ┌──────────────────────────────────────────────────────────────────┐   │    │
│  │  │  Node Types                     Relationships (per node type)     │   │    │
│  │  │  ═══════════                    ═════════════════════════════     │   │    │
│  │  │                                                                   │   │    │
│  │  │  "package" ─────────────────►   has ──────► [package, module]     │   │    │
│  │  │  "module"  ─────────────────►   defines ──► [class, function]     │   │    │
│  │  │                                 imports ──► [module]              │   │    │
│  │  │  "class"   ─────────────────►   has ──────► [method]              │   │    │
│  │  │                                 inherits_from ► [class]           │   │    │
│  │  │  "method"  ─────────────────►   calls ────► [method, function]    │   │    │
│  │  │  "function"─────────────────►   calls ────► [method, function]    │   │    │
│  │  │                                                                   │   │    │
│  │  └──────────────────────────────────────────────────────────────────┘   │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                          │                                       │
│                                          │ references                            │
│                                          ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                           TAXONOMY                                       │    │
│  │                    "How to classify types?"                              │    │
│  │                                                                          │    │
│  │                        code_element                                      │    │
│  │                             │                                            │    │
│  │              ┌──────────────┼──────────────┐                             │    │
│  │              ▼              ▼              ▼                             │    │
│  │         container       code_unit      physical                         │    │
│  │         (package,       (class,        (file)                           │    │
│  │          module)         method,                                        │    │
│  │                          function)                                      │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                          │                                       │
│                                          │ applies to                            │
│                                          ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                            RULES                                         │    │
│  │                    "What constraints apply?"                             │    │
│  │                    (Domain-specific, separate from ontology)             │    │
│  │                                                                          │    │
│  │  Transitivity:  class ──inherits_from──► class  (chains)                │    │
│  │  Cardinality:   method can be "in" only ONE class                       │    │
│  │  Validation:    custom constraint functions                              │    │
│  │                                                                          │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                          │                                       │
│                                          │ validates                             │
│                                          ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                       SEMANTIC GRAPH                                     │    │
│  │                    (Instance Data)                                       │    │
│  │                                                                          │    │
│  │  ontology_ref: "code_structure"                                         │    │
│  │  rule_set_ref: "python_rules"                                           │    │
│  │                                                                          │    │
│  │  nodes: {                                                                │    │
│  │    "n001": { node_type: "module", name: "my_module" },                  │    │
│  │    "n002": { node_type: "class",  name: "MyClass" },                    │    │
│  │    ...                                                                   │    │
│  │  }                                                                       │    │
│  │                                                                          │    │
│  │  edges: [                                                                │    │
│  │    { from: "n001", verb: "defines", to: "n002" },                       │    │
│  │    ...                                                                   │    │
│  │  ]                                                                       │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Part 3: Design Principles

### 3.1 Bidirectional Relationships with Explicit Names

**Principle:** Every edge has both a forward and inverse name. No ambiguous terms like "contains" or "has_parent".

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                       BIDIRECTIONAL EDGE NAMING                                  │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   STORED (minimal):                                                              │
│   ─────────────────                                                              │
│                                                                                  │
│   source_type: "module"                                                          │
│   verb:        "defines"                                                         │
│   inverse:     "defined_in"                                                      │
│   targets:     ["class", "function"]                                             │
│                                                                                  │
│   COMPUTED (full names):                                                         │
│   ──────────────────────                                                         │
│                                                                                  │
│   Forward: {source}_{verb}_{target}     →  "module_defines_class"               │
│   Inverse: {target}_{inverse}_{source}  →  "class_defined_in_module"            │
│                                                                                  │
│   NAVIGATION:                                                                    │
│   ───────────                                                                    │
│                                                                                  │
│   ┌────────┐    module_defines_class    ┌────────┐                              │
│   │ module │ ─────────────────────────► │ class  │                              │
│   │        │ ◄───────────────────────── │        │                              │
│   └────────┘   class_defined_in_module  └────────┘                              │
│                                                                                  │
│   Both directions are ALWAYS available for graph traversal.                     │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Relationships as Verbs

**Principle:** Edge types are verbs describing the relationship, stored on the source node type.

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                       NODE-CENTRIC RELATIONSHIP MODEL                            │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   Instead of flat edge list:           Use node-centric verbs:                  │
│   ──────────────────────────           ───────────────────────                  │
│                                                                                  │
│   edges: [                              node_types:                              │
│     { id: "e1",                           "module":                              │
│       source: "module",                      relationships:                      │
│       verb: "defines",        ───►            "defines":                         │
│       target: "class" },                         inverse: "defined_in"           │
│     { id: "e2",                                  targets: [class, function]      │
│       source: "module",                   "imports":                             │
│       verb: "imports",                       inverse: "imported_by"              │
│       target: "module" },                    targets: [module]                   │
│     ...                                                                          │
│   ]                                                                              │
│                                                                                  │
│   Benefits:                                                                      │
│   • Source type inferred from parent key                                        │
│   • No redundant storage of source type                                         │
│   • Clear "what can this type do?" view                                         │
│   • Easy to extend with new targets                                             │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 3.3 Separation of Structure and Rules

**Principle:** Ontology defines structure (what CAN exist). Rules define behavior (constraints on instances).

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                       ONTOLOGY vs RULES SEPARATION                               │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   ONTOLOGY (Generic Structure)              RULES (Domain-Specific Behavior)    │
│   ════════════════════════════              ════════════════════════════════    │
│                                                                                  │
│   "A class CAN have methods"                "In Python, inheritance is          │
│   "A class CAN inherit from class"           transitive"                        │
│                                                                                  │
│   • What node types exist                   • Transitivity constraints           │
│   • What verbs are valid                    • Cardinality limits                 │
│   • What source→target pairs allowed        • Domain validation rules            │
│                                                                                  │
│   SAME ontology, DIFFERENT rules:                                               │
│   ───────────────────────────────                                               │
│                                                                                  │
│   ontology: "code_structure"                ontology: "code_structure"          │
│   rules: "python_rules"                     rules: "java_rules"                 │
│                                                                                  │
│   • Single inheritance                      • Single class inheritance          │
│   • Multiple via mixins                     • Multiple interface inheritance    │
│   • Duck typing                             • Strict typing                     │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 3.4 Domain-Specific Primitives

**Principle:** Use precise Safe_* types, never generic strings.

```python
# ✗ BAD - Generic primitives
module_allowlist : List[Safe_Str__Label]                 # What IS a label?
class_blocklist  : List[Safe_Str__Label]                 # No validation

# ✓ GOOD - Domain-specific primitives  
module_allowlist : List[Safe_Str__Python__Module]        # Validated module name
class_blocklist  : List[Safe_Str__Python__Class]         # Validated class name
```

**Required new primitives:**

| Primitive | Purpose | Validation |
|-----------|---------|------------|
| `Safe_Str__Python__Class` | Python class name | PascalCase, valid identifier |
| `Safe_Str__Python__Method` | Python method name | Valid identifier, allows dunder |
| `Safe_Str__Python__Function` | Python function name | Valid identifier |
| `Safe_Str__Python__Package` | Python package name | Lowercase, valid identifier |
| `Safe_Str__Python__Qualified_Name` | Full path `a.b.C.method` | Dot-separated identifiers |
| `Safe_Str__Ontology__Verb` | Relationship verb | Lowercase with underscores |
| `Node_Type_Id` | Ontology node type ID | Safe identifier |
| `Ontology_Id` | Ontology identifier | Safe identifier |
| `Taxonomy_Id` | Taxonomy identifier | Safe identifier |
| `Category_Id` | Taxonomy category ID | Safe identifier |
| `Rule_Set_Id` | Rule set identifier | Safe identifier |

---

## Part 4: Schema Specifications

### 4.1 Ontology Schemas

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/ontology/schemas/Schema__Ontology__Relationship.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import List
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.helpers.semantic_graphs.primitives.Node_Type_Id                     import Node_Type_Id
from osbot_utils.helpers.semantic_graphs.primitives.safe_str.Safe_Str__Ontology__Verb import Safe_Str__Ontology__Verb


class Schema__Ontology__Relationship(Type_Safe):                                     # Defines a verb with targets
    inverse : Safe_Str__Ontology__Verb                                               # Inverse verb (e.g., "in")
    targets : List[Node_Type_Id]                                                     # Valid target node types


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/ontology/schemas/Schema__Ontology__Node_Type.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import Dict
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Text         import Safe_Str__Text
from osbot_utils.helpers.semantic_graphs.primitives.Category_Id                      import Category_Id
from osbot_utils.helpers.semantic_graphs.primitives.safe_str.Safe_Str__Ontology__Verb import Safe_Str__Ontology__Verb
from osbot_utils.helpers.semantic_graphs.ontology.schemas.Schema__Ontology__Relationship import Schema__Ontology__Relationship


class Schema__Ontology__Node_Type(Type_Safe):                                        # Defines a node type
    description   : Safe_Str__Text                                                   # Human-readable description
    relationships : Dict[Safe_Str__Ontology__Verb, Schema__Ontology__Relationship]   # verb → relationship
    taxonomy_ref  : Category_Id                  = Category_Id('')                   # Optional taxonomy link


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/ontology/schemas/Schema__Ontology.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import Dict, List, Tuple
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Text         import Safe_Str__Text
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Version      import Safe_Str__Version
from osbot_utils.helpers.semantic_graphs.primitives.Ontology_Id                      import Ontology_Id
from osbot_utils.helpers.semantic_graphs.primitives.Taxonomy_Id                      import Taxonomy_Id
from osbot_utils.helpers.semantic_graphs.primitives.Node_Type_Id                     import Node_Type_Id
from osbot_utils.helpers.semantic_graphs.primitives.safe_str.Safe_Str__Ontology__Verb import Safe_Str__Ontology__Verb
from osbot_utils.helpers.semantic_graphs.ontology.schemas.Schema__Ontology__Node_Type import Schema__Ontology__Node_Type


class Schema__Ontology(Type_Safe):                                                   # Complete ontology definition
    ontology_id  : Ontology_Id                                                       # Unique identifier
    version      : Safe_Str__Version                                                 # Semantic version
    description  : Safe_Str__Text                                                    # What this ontology models
    taxonomy_ref : Taxonomy_Id                    = Taxonomy_Id('')                  # Optional taxonomy reference
    node_types   : Dict[Node_Type_Id, Schema__Ontology__Node_Type]                   # type_id → definition

    def valid_edge(self, source_type : Node_Type_Id,                                 # Check if edge is valid
                   verb        : Safe_Str__Ontology__Verb,
                   target_type : Node_Type_Id) -> bool:
        node_type = self.node_types.get(source_type)
        if not node_type:
            return False
        relationship = node_type.relationships.get(verb)
        if not relationship:
            return False
        return target_type in relationship.targets

    def edge_forward_name(self, source_type : Node_Type_Id,                          # Compute forward name
                          verb        : Safe_Str__Ontology__Verb,
                          target_type : Node_Type_Id) -> str:
        return f"{source_type}_{verb}_{target_type}"

    def edge_inverse_name(self, source_type : Node_Type_Id,                          # Compute inverse name
                          verb        : Safe_Str__Ontology__Verb,
                          target_type : Node_Type_Id) -> str:
        inverse_verb = self.node_types[source_type].relationships[verb].inverse
        return f"{target_type}_{inverse_verb}_{source_type}"

    def all_valid_edges(self) -> List[Tuple[Node_Type_Id, str, Node_Type_Id]]:       # Enumerate all valid edges
        edges = []
        for source_id, node_type in self.node_types.items():
            for verb, rel in node_type.relationships.items():
                for target_id in rel.targets:
                    edges.append((source_id, str(verb), target_id))
        return edges
```

### 4.2 Taxonomy Schemas

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/taxonomy/schemas/Schema__Taxonomy__Category.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import List
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Text         import Safe_Str__Text
from osbot_utils.type_safe.primitives.domains.identifiers.safe_str.Safe_Str__Id      import Safe_Str__Id
from osbot_utils.helpers.semantic_graphs.primitives.Category_Id                      import Category_Id


class Schema__Taxonomy__Category(Type_Safe):                                         # Category in hierarchy
    category_id : Category_Id                                                        # Unique identifier
    name        : Safe_Str__Id                                                       # Category name
    description : Safe_Str__Text                                                     # What this category represents
    parent_ref  : Category_Id                     = Category_Id('')                  # Parent (empty if root)
    child_refs  : List[Category_Id]                                                  # Child categories


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/taxonomy/schemas/Schema__Taxonomy.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import Dict
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Text         import Safe_Str__Text
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Version      import Safe_Str__Version
from osbot_utils.helpers.semantic_graphs.primitives.Taxonomy_Id                      import Taxonomy_Id
from osbot_utils.helpers.semantic_graphs.primitives.Category_Id                      import Category_Id
from osbot_utils.helpers.semantic_graphs.taxonomy.schemas.Schema__Taxonomy__Category import Schema__Taxonomy__Category


class Schema__Taxonomy(Type_Safe):                                                   # Complete taxonomy
    taxonomy_id   : Taxonomy_Id                                                      # Unique identifier
    version       : Safe_Str__Version                                                # Semantic version
    description   : Safe_Str__Text                                                   # What this taxonomy classifies
    root_category : Category_Id                                                      # Top-level category
    categories    : Dict[Category_Id, Schema__Taxonomy__Category]                    # id → category
```

### 4.3 Rules Schemas

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/rules/schemas/Schema__Rule__Transitivity.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.helpers.semantic_graphs.primitives.Node_Type_Id                     import Node_Type_Id
from osbot_utils.helpers.semantic_graphs.primitives.safe_str.Safe_Str__Ontology__Verb import Safe_Str__Ontology__Verb


class Schema__Rule__Transitivity(Type_Safe):                                         # Transitivity rule
    source_type : Node_Type_Id                                                       # e.g., "class"
    verb        : Safe_Str__Ontology__Verb                                           # e.g., "inherits_from"
    target_type : Node_Type_Id                                                       # e.g., "class"


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/rules/schemas/Schema__Rule__Cardinality.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import Optional
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Text         import Safe_Str__Text
from osbot_utils.type_safe.primitives.core.Safe_UInt                                 import Safe_UInt
from osbot_utils.helpers.semantic_graphs.primitives.Node_Type_Id                     import Node_Type_Id
from osbot_utils.helpers.semantic_graphs.primitives.safe_str.Safe_Str__Ontology__Verb import Safe_Str__Ontology__Verb


class Schema__Rule__Cardinality(Type_Safe):                                          # Cardinality constraint
    source_type : Node_Type_Id                                                       # e.g., "method"
    verb        : Safe_Str__Ontology__Verb                                           # e.g., "in"
    target_type : Node_Type_Id                                                       # e.g., "class"
    min_targets : Safe_UInt                       = Safe_UInt(0)                     # Minimum required
    max_targets : Optional[Safe_UInt]             = None                             # Maximum allowed (None = unlimited)
    description : Safe_Str__Text                  = Safe_Str__Text('')               # Human explanation


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/rules/schemas/Schema__Rule_Set.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import List
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Text         import Safe_Str__Text
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Version      import Safe_Str__Version
from osbot_utils.helpers.semantic_graphs.primitives.Rule_Set_Id                      import Rule_Set_Id
from osbot_utils.helpers.semantic_graphs.primitives.Ontology_Id                      import Ontology_Id
from osbot_utils.helpers.semantic_graphs.rules.schemas.Schema__Rule__Transitivity    import Schema__Rule__Transitivity
from osbot_utils.helpers.semantic_graphs.rules.schemas.Schema__Rule__Cardinality     import Schema__Rule__Cardinality


class Schema__Rule_Set(Type_Safe):                                                   # Collection of rules
    rule_set_id        : Rule_Set_Id                                                 # Unique identifier
    ontology_ref       : Ontology_Id                                                 # Which ontology this applies to
    version            : Safe_Str__Version                                           # Semantic version
    description        : Safe_Str__Text                                              # What rules this contains
    transitivity_rules : List[Schema__Rule__Transitivity]                            # Transitive relationships
    cardinality_rules  : List[Schema__Rule__Cardinality]                             # Cardinality constraints
```

### 4.4 Semantic Graph Instance Schemas

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/graph/schemas/Schema__Semantic_Graph__Node.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.Node_Id                    import Node_Id
from osbot_utils.type_safe.primitives.domains.identifiers.safe_str.Safe_Str__Id      import Safe_Str__Id
from osbot_utils.type_safe.primitives.core.Safe_UInt                                 import Safe_UInt
from osbot_utils.helpers.semantic_graphs.primitives.Node_Type_Id                     import Node_Type_Id


class Schema__Semantic_Graph__Node(Type_Safe):                                       # Instance node
    node_id     : Node_Id                                                            # Unique identifier
    node_type   : Node_Type_Id                                                       # Reference to ontology type
    name        : Safe_Str__Id                                                       # Display name
    line_number : Safe_UInt                       = Safe_UInt(0)                     # Optional source location


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/graph/schemas/Schema__Semantic_Graph__Edge.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.Node_Id                    import Node_Id
from osbot_utils.type_safe.primitives.domains.identifiers.Edge_Id                    import Edge_Id
from osbot_utils.type_safe.primitives.core.Safe_UInt                                 import Safe_UInt
from osbot_utils.helpers.semantic_graphs.primitives.safe_str.Safe_Str__Ontology__Verb import Safe_Str__Ontology__Verb


class Schema__Semantic_Graph__Edge(Type_Safe):                                       # Instance edge
    edge_id     : Edge_Id                                                            # Unique identifier
    from_node   : Node_Id                                                            # Source node
    verb        : Safe_Str__Ontology__Verb                                           # Relationship verb
    to_node     : Node_Id                                                            # Target node
    line_number : Safe_UInt                       = Safe_UInt(0)                     # Optional source location


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/graph/schemas/Schema__Semantic_Graph.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import Dict, List
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.Graph_Id                   import Graph_Id
from osbot_utils.type_safe.primitives.domains.common.safe_str.Safe_Str__Version      import Safe_Str__Version
from osbot_utils.helpers.semantic_graphs.primitives.Ontology_Id                      import Ontology_Id
from osbot_utils.helpers.semantic_graphs.primitives.Rule_Set_Id                      import Rule_Set_Id
from osbot_utils.helpers.semantic_graphs.graph.schemas.Schema__Semantic_Graph__Node  import Schema__Semantic_Graph__Node
from osbot_utils.helpers.semantic_graphs.graph.schemas.Schema__Semantic_Graph__Edge  import Schema__Semantic_Graph__Edge


class Schema__Semantic_Graph(Type_Safe):                                             # Complete instance graph
    graph_id     : Graph_Id                                                          # Unique identifier
    version      : Safe_Str__Version              = Safe_Str__Version('1.0.0')       # Graph version
    ontology_ref : Ontology_Id                                                       # Which ontology defines types
    rule_set_ref : Rule_Set_Id                    = Rule_Set_Id('')                  # Optional rule set
    nodes        : Dict[str, Schema__Semantic_Graph__Node]                           # node_id → node
    edges        : List[Schema__Semantic_Graph__Edge]                                # All edges

    def add_node(self, node: Schema__Semantic_Graph__Node) -> 'Schema__Semantic_Graph':
        self.nodes[str(node.node_id)] = node
        return self

    def add_edge(self, edge: Schema__Semantic_Graph__Edge) -> 'Schema__Semantic_Graph':
        self.edges.append(edge)
        return self

    def node_count(self) -> int:
        return len(self.nodes)

    def edge_count(self) -> int:
        return len(self.edges)
```

---

## Part 5: Ontology Data Files

### 5.1 Code Structure Ontology (JSON)

```json
{
  "ontology_id": "code_structure",
  "version": "1.0.0",
  "description": "Ontology for Python code structure elements",
  "taxonomy_ref": "code_elements",

  "node_types": {
    "package": {
      "description": "Python package (directory with __init__.py)",
      "taxonomy_ref": "container",
      "relationships": {
        "has": {
          "inverse": "in",
          "targets": ["package", "module"]
        }
      }
    },

    "module": {
      "description": "Python module (.py file)",
      "taxonomy_ref": "container",
      "relationships": {
        "defines": {
          "inverse": "defined_in",
          "targets": ["class", "function"]
        },
        "imports": {
          "inverse": "imported_by",
          "targets": ["module"]
        }
      }
    },

    "class": {
      "description": "Python class definition",
      "taxonomy_ref": "code_unit",
      "relationships": {
        "has": {
          "inverse": "in",
          "targets": ["method"]
        },
        "inherits_from": {
          "inverse": "inherited_by",
          "targets": ["class"]
        }
      }
    },

    "method": {
      "description": "Method within a class",
      "taxonomy_ref": "code_unit",
      "relationships": {
        "calls": {
          "inverse": "called_by",
          "targets": ["method", "function"]
        }
      }
    },

    "function": {
      "description": "Standalone function",
      "taxonomy_ref": "code_unit",
      "relationships": {
        "calls": {
          "inverse": "called_by",
          "targets": ["method", "function"]
        }
      }
    }
  }
}
```

### 5.2 Python Rules (JSON)

```json
{
  "rule_set_id": "python_rules",
  "ontology_ref": "code_structure",
  "version": "1.0.0",
  "description": "Python-specific structural rules",

  "transitivity_rules": [
    {
      "source_type": "class",
      "verb": "inherits_from",
      "target_type": "class"
    },
    {
      "source_type": "package",
      "verb": "has",
      "target_type": "package"
    }
  ],

  "cardinality_rules": [
    {
      "source_type": "method",
      "verb": "in",
      "target_type": "class",
      "min_targets": 1,
      "max_targets": 1,
      "description": "A method belongs to exactly one class"
    },
    {
      "source_type": "function",
      "verb": "defined_in",
      "target_type": "module",
      "min_targets": 1,
      "max_targets": 1,
      "description": "A function is defined in exactly one module"
    }
  ]
}
```

### 5.3 Code Elements Taxonomy (JSON)

```json
{
  "taxonomy_id": "code_elements",
  "version": "1.0.0",
  "description": "Classification hierarchy for Python code elements",
  "root_category": "code_element",

  "categories": {
    "code_element": {
      "category_id": "code_element",
      "name": "code_element",
      "description": "Root category for all code elements",
      "parent_ref": "",
      "child_refs": ["container", "code_unit"]
    },
    "container": {
      "category_id": "container",
      "name": "container",
      "description": "Elements that contain other elements (package, module)",
      "parent_ref": "code_element",
      "child_refs": []
    },
    "code_unit": {
      "category_id": "code_unit",
      "name": "code_unit",
      "description": "Executable code units (class, method, function)",
      "parent_ref": "code_element",
      "child_refs": []
    }
  }
}
```

---

## Part 6: Primitive Types

### 6.1 Semantic Graph Primitives

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/primitives/Ontology_Id.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.identifiers.Safe_Id import Safe_Id

class Ontology_Id(Safe_Id):                                                          # Ontology identifier
    pass


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/primitives/Taxonomy_Id.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.identifiers.Safe_Id import Safe_Id

class Taxonomy_Id(Safe_Id):                                                          # Taxonomy identifier
    pass


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/primitives/Category_Id.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.identifiers.Safe_Id import Safe_Id

class Category_Id(Safe_Id):                                                          # Taxonomy category identifier
    pass


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/primitives/Node_Type_Id.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.identifiers.Safe_Id import Safe_Id

class Node_Type_Id(Safe_Id):                                                         # Ontology node type identifier
    pass


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/primitives/Rule_Set_Id.py
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.identifiers.Safe_Id import Safe_Id

class Rule_Set_Id(Safe_Id):                                                          # Rule set identifier
    pass


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/semantic_graphs/primitives/safe_str/Safe_Str__Ontology__Verb.py
# ═══════════════════════════════════════════════════════════════════════════════

import re
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str

class Safe_Str__Ontology__Verb(Safe_Str):                                            # Relationship verb
    max_length = 64                                                                  # e.g., "inherits_from"
    regex      = re.compile(r'[^a-z_]')                                              # Lowercase + underscores only
```

### 6.2 Python Domain Primitives

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/type_safe/primitives/domains/python/safe_str/Safe_Str__Python__Class.py
# ═══════════════════════════════════════════════════════════════════════════════

import re
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str

class Safe_Str__Python__Class(Safe_Str):                                             # Python class name
    max_length = 128                                                                 # Reasonable limit
    regex      = re.compile(r'[^a-zA-Z0-9_]')                                        # Valid identifier chars


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/type_safe/primitives/domains/python/safe_str/Safe_Str__Python__Method.py
# ═══════════════════════════════════════════════════════════════════════════════

import re
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str

class Safe_Str__Python__Method(Safe_Str):                                            # Python method name
    max_length = 128
    regex      = re.compile(r'[^a-zA-Z0-9_]')                                        # Allows __dunder__


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/type_safe/primitives/domains/python/safe_str/Safe_Str__Python__Function.py
# ═══════════════════════════════════════════════════════════════════════════════

import re
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str

class Safe_Str__Python__Function(Safe_Str):                                          # Python function name
    max_length = 128
    regex      = re.compile(r'[^a-zA-Z0-9_]')


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/type_safe/primitives/domains/python/safe_str/Safe_Str__Python__Package.py
# ═══════════════════════════════════════════════════════════════════════════════

import re
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str

class Safe_Str__Python__Package(Safe_Str):                                           # Python package name
    max_length = 256
    regex      = re.compile(r'[^a-z0-9_]')                                           # Lowercase by convention


# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/type_safe/primitives/domains/python/safe_str/Safe_Str__Python__Qualified_Name.py
# ═══════════════════════════════════════════════════════════════════════════════

import re
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str

class Safe_Str__Python__Qualified_Name(Safe_Str):                                    # Full path: module.Class.method
    max_length = 512
    regex      = re.compile(r'[^a-zA-Z0-9_.]')                                       # Allows dots
```

---

## Part 7: Integration with Call Flow

### 7.1 Refactored Call Flow Node

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/python_call_flow/call_flow/schemas/Schema__Call_Flow__Node.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import List
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.Node_Id                    import Node_Id
from osbot_utils.type_safe.primitives.core.Safe_UInt                                 import Safe_UInt


class Schema__Call_Flow__Node(Type_Safe):                                            # Lightweight behavioral node
    node_id       : Node_Id                                                          # Unique identifier
    structure_ref : Node_Id                                                          # Reference to Code Structure node
    depth         : Safe_UInt                                                        # Distance from entry point
    calls         : List[Node_Id]                                                    # Outgoing call targets
    called_by     : List[Node_Id]                                                    # Incoming callers
    is_entry      : bool                          = False                            # Is this the entry point?
    is_recursive  : bool                          = False                            # Calls itself?

    # REMOVED: name, full_name, module, file_path, node_type
    # These are now accessed via structure_ref → Code Structure Graph
```

### 7.2 Updated Call Flow Graph

```python
# ═══════════════════════════════════════════════════════════════════════════════
# File: osbot_utils/helpers/python_call_flow/call_flow/schemas/Schema__Call_Flow.py
# ═══════════════════════════════════════════════════════════════════════════════

from typing                                                                          import Dict, List
from osbot_utils.type_safe.Type_Safe                                                 import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.Graph_Id                   import Graph_Id
from osbot_utils.type_safe.primitives.domains.identifiers.Node_Id                    import Node_Id
from osbot_utils.type_safe.primitives.core.Safe_UInt                                 import Safe_UInt
from osbot_utils.helpers.python_call_flow.call_flow.schemas.Schema__Call_Flow__Node  import Schema__Call_Flow__Node
from osbot_utils.helpers.python_call_flow.call_flow.schemas.Schema__Call_Flow__Edge  import Schema__Call_Flow__Edge
from osbot_utils.helpers.python_call_flow.schemas.Schema__Call_Graph__Config         import Schema__Call_Graph__Config


class Schema__Call_Flow(Type_Safe):                                                  # Call flow graph (behavioral)
    graph_id            : Graph_Id                                                   # Unique identifier
    code_structure_ref  : Graph_Id                                                   # Reference to structure graph
    entry_point         : Node_Id                                                    # Starting node
    config              : Schema__Call_Graph__Config                                 # Analysis configuration
    nodes               : Dict[str, Schema__Call_Flow__Node]                         # node_id → node
    edges               : List[Schema__Call_Flow__Edge]                              # Behavioral edges only
    max_depth_found     : Safe_UInt              = Safe_UInt(0)                      # Deepest level reached
```

### 7.3 Data Flow Between Graphs

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         DATA FLOW BETWEEN GRAPHS                                 │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   ANALYSIS INPUT:                                                                │
│   ═══════════════                                                                │
│                                                                                  │
│   analyzer.analyze(Call_Flow__Analyzer)                                          │
│                    │                                                             │
│                    ▼                                                             │
│   ┌────────────────────────────────────────────────────────────────────────┐    │
│   │                     Code_Structure__Analyzer                            │    │
│   │                                                                         │    │
│   │  1. Parse class/module/package hierarchy                               │    │
│   │  2. Create structure nodes (package, module, class, method)            │    │
│   │  3. Create structure edges (has, defines, inherits_from)               │    │
│   │  4. Validate against ontology                                          │    │
│   │                                                                         │    │
│   │  Output: Schema__Semantic_Graph (code structure)                       │    │
│   │          graph_id = "cs_001"                                           │    │
│   └────────────────────────────────────────────────────────────────────────┘    │
│                    │                                                             │
│                    │ structure nodes created                                     │
│                    ▼                                                             │
│   ┌────────────────────────────────────────────────────────────────────────┐    │
│   │                     Call_Flow__Analyzer                                 │    │
│   │                                                                         │    │
│   │  1. Walk methods/functions (from structure graph)                      │    │
│   │  2. Extract calls from AST                                             │    │
│   │  3. Create lightweight call flow nodes (structure_ref points to        │    │
│   │     corresponding structure node)                                      │    │
│   │  4. Create behavioral edges (calls, self, chain)                       │    │
│   │                                                                         │    │
│   │  Output: Schema__Call_Flow                                             │    │
│   │          code_structure_ref = "cs_001"                                 │    │
│   └────────────────────────────────────────────────────────────────────────┘    │
│                    │                                                             │
│                    ▼                                                             │
│   QUERY EXAMPLES:                                                                │
│   ═══════════════                                                                │
│                                                                                  │
│   # Get module for a call flow node:                                            │
│   call_node = call_flow.nodes["cf_001"]                                         │
│   structure_node = code_structure.nodes[call_node.structure_ref]                │
│   module_edge = find_edge(structure_node, verb="defined_in")                    │
│   module_node = code_structure.nodes[module_edge.to_node]                       │
│                                                                                  │
│   # Find all methods in a class:                                                │
│   class_node = find_node(code_structure, name="MyClass")                        │
│   method_edges = find_edges(code_structure, from=class_node, verb="has")        │
│   methods = [code_structure.nodes[e.to_node] for e in method_edges]            │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Part 8: File Structure

```
osbot_utils/
├── helpers/
│   │
│   ├── semantic_graphs/                           # GENERIC FRAMEWORK
│   │   │
│   │   ├── __init__.py
│   │   │
│   │   ├── primitives/                            # Domain identifiers
│   │   │   ├── __init__.py
│   │   │   ├── Ontology_Id.py
│   │   │   ├── Taxonomy_Id.py
│   │   │   ├── Category_Id.py
│   │   │   ├── Node_Type_Id.py
│   │   │   ├── Rule_Set_Id.py
│   │   │   └── safe_str/
│   │   │       ├── __init__.py
│   │   │       └── Safe_Str__Ontology__Verb.py
│   │   │
│   │   ├── ontology/                              # Ontology system
│   │   │   ├── __init__.py
│   │   │   ├── schemas/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── Schema__Ontology.py
│   │   │   │   ├── Schema__Ontology__Node_Type.py
│   │   │   │   └── Schema__Ontology__Relationship.py
│   │   │   └── Ontology__Registry.py              # Load/cache ontologies
│   │   │
│   │   ├── taxonomy/                              # Taxonomy system
│   │   │   ├── __init__.py
│   │   │   ├── schemas/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── Schema__Taxonomy.py
│   │   │   │   └── Schema__Taxonomy__Category.py
│   │   │   └── Taxonomy__Registry.py
│   │   │
│   │   ├── rules/                                 # Rules system
│   │   │   ├── __init__.py
│   │   │   ├── schemas/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── Schema__Rule_Set.py
│   │   │   │   ├── Schema__Rule__Transitivity.py
│   │   │   │   └── Schema__Rule__Cardinality.py
│   │   │   └── Rule__Engine.py                    # Apply rules to graphs
│   │   │
│   │   └── graph/                                 # Instance graphs
│   │       ├── __init__.py
│   │       ├── schemas/
│   │       │   ├── __init__.py
│   │       │   ├── Schema__Semantic_Graph.py
│   │       │   ├── Schema__Semantic_Graph__Node.py
│   │       │   └── Schema__Semantic_Graph__Edge.py
│   │       ├── Semantic_Graph__Builder.py         # Fluent API
│   │       └── Semantic_Graph__Validator.py       # Validate against ontology
│   │
│   └── python_call_flow/                          # PYTHON-SPECIFIC
│       │
│       ├── __init__.py
│       │
│       ├── code_structure/                        # Code structure graph
│       │   ├── __init__.py
│       │   ├── Code_Structure__Analyzer.py        # Build structure graph
│       │   ├── Code_Structure__Builder.py         # Fluent API
│       │   └── data/
│       │       ├── ontology__code_structure.json
│       │       ├── taxonomy__code_elements.json
│       │       └── rules__python.json
│       │
│       ├── call_flow/                             # Behavioral graph (refactored)
│       │   ├── __init__.py
│       │   ├── schemas/
│       │   │   ├── __init__.py
│       │   │   ├── Schema__Call_Flow.py           # Renamed, references structure
│       │   │   ├── Schema__Call_Flow__Node.py     # Lightweight
│       │   │   └── Schema__Call_Flow__Edge.py
│       │   └── Call_Flow__Analyzer.py             # Updated
│       │
│       └── (existing files - updated)
│           ├── Call_Flow__AST__Extractor.py
│           ├── Call_Flow__Call__Filter.py
│           ├── Call_Flow__Call__Resolver.py
│           ├── Call_Flow__Edge__Factory.py
│           ├── Call_Flow__Node__Factory.py
│           ├── Call_Flow__Node__Registry.py
│           └── Call_Flow__Exporter__Mermaid.py
│
└── type_safe/primitives/domains/
    └── python/                                    # NEW: Python primitives
        ├── __init__.py
        └── safe_str/
            ├── __init__.py
            ├── Safe_Str__Python__Class.py
            ├── Safe_Str__Python__Method.py
            ├── Safe_Str__Python__Function.py
            ├── Safe_Str__Python__Package.py
            └── Safe_Str__Python__Qualified_Name.py
```

---

## Part 9: Implementation Checklist

### Phase 1: Semantic Graphs Framework

#### 1.1 Primitives
- [ ] Create `osbot_utils/helpers/semantic_graphs/primitives/` directory
- [ ] Implement `Ontology_Id`
- [ ] Implement `Taxonomy_Id`
- [ ] Implement `Category_Id`
- [ ] Implement `Node_Type_Id`
- [ ] Implement `Rule_Set_Id`
- [ ] Implement `Safe_Str__Ontology__Verb`

#### 1.2 Ontology Schemas
- [ ] Implement `Schema__Ontology__Relationship`
- [ ] Implement `Schema__Ontology__Node_Type`
- [ ] Implement `Schema__Ontology`
- [ ] Add helper methods (`valid_edge`, `edge_forward_name`, `edge_inverse_name`)

#### 1.3 Taxonomy Schemas
- [ ] Implement `Schema__Taxonomy__Category`
- [ ] Implement `Schema__Taxonomy`

#### 1.4 Rules Schemas
- [ ] Implement `Schema__Rule__Transitivity`
- [ ] Implement `Schema__Rule__Cardinality`
- [ ] Implement `Schema__Rule_Set`

#### 1.5 Graph Schemas
- [ ] Implement `Schema__Semantic_Graph__Node`
- [ ] Implement `Schema__Semantic_Graph__Edge`
- [ ] Implement `Schema__Semantic_Graph`

#### 1.6 Registries and Utilities
- [ ] Implement `Ontology__Registry` (load from JSON)
- [ ] Implement `Taxonomy__Registry`
- [ ] Implement `Semantic_Graph__Validator`
- [ ] Implement `Semantic_Graph__Builder`

### Phase 2: Python Primitives

- [ ] Create `osbot_utils/type_safe/primitives/domains/python/` directory
- [ ] Implement `Safe_Str__Python__Class`
- [ ] Implement `Safe_Str__Python__Method`
- [ ] Implement `Safe_Str__Python__Function`
- [ ] Implement `Safe_Str__Python__Package`
- [ ] Implement `Safe_Str__Python__Qualified_Name`

### Phase 3: Code Structure

#### 3.1 Data Files
- [ ] Create `ontology__code_structure.json`
- [ ] Create `taxonomy__code_elements.json`
- [ ] Create `rules__python.json`

#### 3.2 Analyzer
- [ ] Implement `Code_Structure__Analyzer`
- [ ] Implement `Code_Structure__Builder`
- [ ] Write tests with deterministic IDs

### Phase 4: Call Flow Refactoring

- [ ] Create new `Schema__Call_Flow__Node` (lightweight)
- [ ] Create new `Schema__Call_Flow` (with `code_structure_ref`)
- [ ] Update `Call_Flow__Analyzer` to:
  - [ ] Create code structure graph first
  - [ ] Create call flow nodes with `structure_ref`
  - [ ] Remove redundant fields from nodes
- [ ] Update `Call_Flow__Exporter__Mermaid` to resolve structure refs
- [ ] Update tests

### Phase 5: Testing

- [ ] Unit tests for all ontology schemas
- [ ] Unit tests for all taxonomy schemas
- [ ] Unit tests for all rule schemas
- [ ] Unit tests for semantic graph schemas
- [ ] Integration tests for code structure analyzer
- [ ] Integration tests for updated call flow analyzer
- [ ] Meta-test: analyzer analyzing itself with new structure

---

## Part 10: Success Criteria

### Minimum Viable Product (MVP)

- [ ] Semantic Graphs framework fully functional
- [ ] Code structure ontology defined in JSON
- [ ] Code structure analyzer produces valid semantic graph
- [ ] Call flow nodes reference structure nodes
- [ ] All existing call flow tests pass (with updates)

### Full Implementation

- [ ] Ontology validation working
- [ ] Rule engine applying transitivity/cardinality
- [ ] Taxonomy classification working
- [ ] Registries caching loaded definitions
- [ ] Documentation complete
- [ ] Ready for other domain ontologies

---

## Part 11: Reference

### Related Documents

1. **Brief 2** - Call Flow Analyzer Implementation Brief (prerequisite)
2. **Implementation Debrief Phase 1** - Current state documentation
3. **Type_Safe & Python Formatting Guide** - Coding standards
4. **OSBot-Utils Safe Primitives Reference Guide** - Available primitives

### Key Concepts

| Term | Definition |
|------|------------|
| **Ontology** | Formal definition of what types exist and how they can connect |
| **Taxonomy** | Hierarchical classification of types into categories |
| **Rules** | Domain-specific constraints (transitivity, cardinality) |
| **Semantic Graph** | Instance graph validated against an ontology |
| **Structure Graph** | What code exists and where (static) |
| **Call Flow Graph** | Who calls whom (behavioral) |
| **Verb** | Relationship type between nodes (e.g., "has", "defines") |
| **Forward/Inverse** | Bidirectional edge naming ("has"/"in") |

---

*End of Implementation Brief*
