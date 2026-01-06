# (Brief 3.5) Semantic Graph ID/Ref Architecture Clarification

**Version:** v3.64.0  
**Status:** Preparatory Refactoring  
**Target:** OSBot-Utils (`osbot_utils.type_safe.primitives` and `osbot_utils.helpers.semantic_graphs`)  
**Created:** January 2026  
**Prerequisites:** Brief 3 Complete - Semantic Graphs Framework  
**Enables:** Brief 4 - Call Flow as Semantic Graph  

---

## Executive Summary

This brief specifies a foundational refactoring to clarify the distinction between **Instance IDs** and **Semantic References** in the semantic graphs framework. The current implementation uses `*_Id` suffix for both concepts, causing architectural confusion.

**Key Changes:**

1. **Rename label/reference types:** `*_Id` → `*_Ref` (e.g., `Ontology_Id` → `Ontology_Ref`)
2. **Add deterministic ID creation:** `Obj_Id.from_basis(seed_string)` for URI/seed-based IDs
3. **Add ID provenance tracking:** `Schema__Id__Source` with sidecar pattern `{id}_source`
4. **Update all semantic_graphs code** to use new naming conventions

**Why Now:** Brief 4 (Call Flow as Semantic Graph) will be significantly cleaner with this foundation in place. The current naming confusion would propagate into call flow code and make the architecture harder to understand.

---

## Part 1: The Problem

### 1.1 Current Naming Confusion

The current implementation uses `*_Id` suffix for two fundamentally different concepts:

```python
# GROUP A: These are string labels/references (NOT instance identifiers)
class Node_Type_Id(Semantic_Id): pass    # Values: "class", "method", "function"
class Ontology_Id(Semantic_Id): pass     # Values: "code_structure", "call_flow"
class Taxonomy_Id(Semantic_Id): pass     # Values: "call_flow_taxonomy"
class Category_Id(Semantic_Id): pass     # Values: "container", "callable"
class Rule_Set_Id(Semantic_Id): pass     # Values: "call_flow_rules"

# GROUP B: These ARE instance identifiers
class Node_Id(...): pass                 # Created via Node_Id(Obj_Id())
class Edge_Id(...): pass                 # Created via Edge_Id(Obj_Id())
class Graph_Id(...): pass                # Created via Graph_Id(Obj_Id())
```

### 1.2 Why This Matters

| Aspect | Group A (Labels) | Group B (Instance IDs) |
|--------|------------------|------------------------|
| **Source** | JSON config, dict keys | Created via `*_Id(Obj_Id())` |
| **Default value** | `''` (empty string) | `''` (empty string) |
| **Unique value** | From JSON/config | From `Obj_Id()` |
| **Uniqueness scope** | Per-ontology definition | Per-instance globally |
| **Human readable** | YES ("class", "method") | NO (random/sequential) |

### 1.3 Missing Capability: Deterministic IDs

Currently, instance IDs can only be:
- **Random:** `Node_Id(Obj_Id())` — different each time
- **Sequential (tests):** Inside `graph_deterministic_ids()` context

**Missing:** Deterministic IDs from a stable seed (e.g., URI, qualified name), enabling:
- Cross-session identity
- Semantic Web compatibility
- Reproducible ID generation

---

## Part 2: Solution Architecture

### 2.1 New Naming Convention

```
*_Id  = Instance identifier, unique per instance, created via *_Id(Obj_Id())
*_Ref = Reference by name, string-based, comes from JSON/config
```

### 2.2 Rename Mapping

| Current Name | New Name | Base Class | Purpose |
|--------------|----------|------------|---------|
| `Semantic_Id` | `Semantic_Ref` | `Safe_Str` | Base for string references |
| `Ontology_Id` | `Ontology_Ref` | `Semantic_Ref` | Reference ontology by name |
| `Taxonomy_Id` | `Taxonomy_Ref` | `Semantic_Ref` | Reference taxonomy by name |
| `Category_Id` | `Category_Ref` | `Semantic_Ref` | Reference category by name |
| `Node_Type_Id` | `Node_Type_Ref` | `Semantic_Ref` | Reference node type by name |
| `Rule_Set_Id` | `Rule_Set_Ref` | `Semantic_Ref` | Reference rule set by name |

**Keep unchanged:**
- `Node_Id` — Instance ID for graph nodes
- `Edge_Id` — Instance ID for graph edges
- `Graph_Id` — Instance ID for graphs
- `Safe_Str__Ontology__Verb` — Primitive with regex constraints

### 2.3 Type Hierarchy After Refactoring

```
Safe_Str (core primitive)
│
├── Semantic_Ref                          # String references from config
│   ├── Ontology_Ref                      # "call_flow", "code_structure"
│   ├── Taxonomy_Ref                      # "call_flow_taxonomy"
│   ├── Category_Ref                      # "container", "callable"
│   ├── Node_Type_Ref                     # "class", "method", "function"
│   └── Rule_Set_Ref                      # "call_flow_rules"
│
├── Safe_Str__Ontology__Verb              # Verb primitive: "calls", "contains"
│
└── Safe_Str__Id__Source                  # Basis/seed for deterministic IDs

Obj_Id (auto-generating base)
│
├── Node_Id                               # Per-node instance ID
├── Edge_Id                               # Per-edge instance ID
└── Graph_Id                              # Per-graph instance ID
```

---

## Part 3: Deterministic ID Creation

### 3.1 The `from_basis()` Method

Add to `Obj_Id` class:

```python
import hashlib
from osbot_utils.type_safe.primitives.domains.identifiers.Obj_Id import Obj_Id


class Obj_Id(Safe_Id):
    
    @classmethod
    def from_basis(cls, basis: str) -> 'Obj_Id':
        """Create deterministic ID from basis string.
        
        Same basis string always produces same ID.
        Enables:
        - Cross-session identity (same seed → same ID)
        - Semantic Web compatibility (URI as basis)
        - Reproducible ID generation
        
        Args:
            basis: Seed string (URI, qualified name, any stable identifier)
            
        Returns:
            Deterministic Obj_Id derived from basis
        """
        if not basis:
            raise ValueError("Basis string cannot be empty")
        hash_bytes = hashlib.sha256(basis.encode('utf-8')).digest()
        deterministic_id = hash_bytes.hex()[:8]                      # Match Obj_Id format
        return cls(deterministic_id)
```

### 3.2 ID Creation Modes

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         ID CREATION MODES                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  MODE 1: Random                                                          │
│  ─────────────────                                                       │
│  node_id = Node_Id(Obj_Id())                                             │
│  → Random unique ID each call                                            │
│  → Use for: runtime instances, session-specific entities                 │
│                                                                          │
│  MODE 2: Deterministic from basis (NEW)                                  │
│  ──────────────────────────────────────                                  │
│  ontology_id = Ontology_Id(Obj_Id.from_basis(uri))                       │
│  → Same basis → same ID, always                                          │
│  → Use for: entities with stable identity (URIs, qualified names)        │
│                                                                          │
│  MODE 3: Sequential (tests)                                              │
│  ──────────────────────────                                              │
│  with graph_deterministic_ids():                                         │
│      node_id = Node_Id(Obj_Id())  # → 'c0000001', 'c0000002', ...        │
│  → Use for: deterministic test assertions                                │
│                                                                          │
│  MODE 4: Explicit                                                        │
│  ────────────────                                                        │
│  node_id = Node_Id('existing-id-from-storage')                           │
│  → Use for: loading from persisted data                                  │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Part 4: ID Provenance Tracking

### 4.1 Source Type Enum

**File:** `osbot_utils/type_safe/primitives/domains/identifiers/enums/Enum__Id__Source_Type.py`

```python
from enum import Enum


class Enum__Id__Source_Type(str, Enum):
    DETERMINISTIC = 'deterministic'                  # From basis string (URI, seed, etc.)
    RANDOM        = 'random'                         # Via Obj_Id()
    SEQUENTIAL    = 'sequential'                     # Via graph_deterministic_ids context
    EXPLICIT      = 'explicit'                       # Directly assigned (loaded from storage)
```

### 4.2 Source Primitive

**File:** `osbot_utils/type_safe/primitives/domains/identifiers/safe_str/Safe_Str__Id__Source.py`

```python
from osbot_utils.type_safe.primitives.core.Safe_Str import Safe_Str


class Safe_Str__Id__Source(Safe_Str):                # Basis/seed string for deterministic IDs
    pass                                             # URI, qualified name, or any stable identifier
```

### 4.3 ID Source Schema

**File:** `osbot_utils/type_safe/primitives/domains/identifiers/schemas/Schema__Id__Source.py`

```python
from osbot_utils.type_safe.Type_Safe                                                         import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.enums.Enum__Id__Source_Type        import Enum__Id__Source_Type
from osbot_utils.type_safe.primitives.domains.identifiers.safe_str.Safe_Str__Id__Source      import Safe_Str__Id__Source


class Schema__Id__Source(Type_Safe):                 # Provenance information for an ID
    source_type : Enum__Id__Source_Type              # How the ID was created
    source      : Safe_Str__Id__Source               # The basis string (if deterministic)
```

### 4.4 Domain-Specific Source Subclasses

```python
# In semantic_graphs/schemas/identifier/

class Ontology_Id__Source(Schema__Id__Source):       # Source for Ontology_Id
    pass

class Node_Id__Source(Schema__Id__Source):           # Source for Node_Id
    pass

class Edge_Id__Source(Schema__Id__Source):           # Source for Edge_Id
    pass

class Graph_Id__Source(Schema__Id__Source):          # Source for Graph_Id
    pass
```

### 4.5 Sidecar Pattern Usage

```python
class Schema__Ontology(Type_Safe):
    ontology_id        : Ontology_Id                 # The compact ID
    ontology_id_source : Ontology_Id__Source = None  # How it was created (optional)
    name               : Ontology_Ref                # Human label
    ...
```

**Key points:**
- Naming convention: `{id_field}_source`
- Default to `None` — only populate when provenance matters
- No overhead when not needed
- Full flexibility for complex cases

---

## Part 5: Schema Updates

### 5.1 Schema__Semantic_Graph

**Before:**
```python
class Schema__Semantic_Graph(Type_Safe):
    graph_id     : Graph_Id
    ontology_ref : Ontology_Id                       # Confusing!
    rule_set_ref : Rule_Set_Id                       # Confusing!
    nodes        : Dict[str, Schema__Semantic_Graph__Node]
    edges        : List[Schema__Semantic_Graph__Edge]
```

**After:**
```python
class Schema__Semantic_Graph(Type_Safe):
    graph_id          : Graph_Id
    graph_id_source   : Graph_Id__Source = None      # Optional provenance
    ontology_ref      : Ontology_Ref                 # Clear! Reference by name
    rule_set_ref      : Rule_Set_Ref                 # Clear! Reference by name
    nodes             : Dict__Nodes__By_Id           # Typed collection
    edges             : List__Edges                  # Typed collection

    def __init__(self, **kwargs):
        if kwargs.get('graph_id') is None:
            kwargs['graph_id'] = Graph_Id(Obj_Id())
            kwargs['graph_id_source'] = Graph_Id__Source(
                source_type = Enum__Id__Source_Type.RANDOM,
                source      = Safe_Str__Id__Source('')
            )
        super().__init__(**kwargs)
```

### 5.2 Schema__Semantic_Graph__Node

**Before:**
```python
class Schema__Semantic_Graph__Node(Type_Safe):
    node_id     : Node_Id
    node_type   : Node_Type_Id                       # Confusing!
    name        : Safe_Str__Id
    line_number : Safe_UInt
```

**After:**
```python
class Schema__Semantic_Graph__Node(Type_Safe):
    node_id        : Node_Id
    node_id_source : Node_Id__Source = None          # Optional provenance
    node_type      : Node_Type_Ref                   # Clear! Reference to type name
    name           : Safe_Str__Id
    line_number    : Safe_UInt
```

### 5.3 Schema__Semantic_Graph__Edge

**Before:**
```python
class Schema__Semantic_Graph__Edge(Type_Safe):
    edge_id     : Edge_Id
    from_node   : Node_Id
    verb        : Safe_Str__Ontology__Verb
    to_node     : Node_Id
    line_number : Safe_UInt
```

**After:**
```python
class Schema__Semantic_Graph__Edge(Type_Safe):
    edge_id        : Edge_Id
    edge_id_source : Edge_Id__Source = None          # Optional provenance
    from_node      : Node_Id
    verb           : Safe_Str__Ontology__Verb
    to_node        : Node_Id
    line_number    : Safe_UInt
```

### 5.4 Schema__Ontology

**Before:**
```python
class Schema__Ontology(Type_Safe):
    ontology_id  : Ontology_Id
    version      : Safe_Str__Version
    description  : Safe_Str__Text
    taxonomy_ref : Taxonomy_Id
    node_types   : Dict__Node_Types__By_Id
```

**After:**
```python
class Schema__Ontology(Type_Safe):
    ontology_id        : Ontology_Id
    ontology_id_source : Ontology_Id__Source = None
    version            : Safe_Str__Version
    description        : Safe_Str__Text
    taxonomy_ref       : Taxonomy_Ref                # Clear!
    node_types         : Dict__Node_Types__By_Ref    # Updated collection name

    def __init__(self, **kwargs):
        if kwargs.get('ontology_id') is None:
            name = kwargs.get('name') or kwargs.get('ontology_ref')
            if name:
                kwargs['ontology_id'] = Ontology_Id(Obj_Id.from_basis(str(name)))
                kwargs['ontology_id_source'] = Ontology_Id__Source(
                    source_type = Enum__Id__Source_Type.DETERMINISTIC,
                    source      = Safe_Str__Id__Source(str(name))
                )
            else:
                kwargs['ontology_id'] = Ontology_Id(Obj_Id())
                kwargs['ontology_id_source'] = Ontology_Id__Source(
                    source_type = Enum__Id__Source_Type.RANDOM,
                    source      = Safe_Str__Id__Source('')
                )
        super().__init__(**kwargs)
```

---

## Part 6: Collection Subclass Updates

### 6.1 Rename Mapping

| Current Name | New Name |
|--------------|----------|
| `Dict__Node_Types__By_Id` | `Dict__Node_Types__By_Ref` |
| `Dict__Ontologies__By_Id` | `Dict__Ontologies__By_Ref` |
| `Dict__Taxonomies__By_Id` | `Dict__Taxonomies__By_Ref` |
| `Dict__Categories__By_Id` | `Dict__Categories__By_Ref` |
| `List__Node_Type_Ids` | `List__Node_Type_Refs` |

### 6.2 Updated Definitions

```python
class Dict__Node_Types__By_Ref(Type_Safe__Dict):
    expected_key_type   = Node_Type_Ref
    expected_value_type = Schema__Ontology__Node_Type

class Dict__Ontologies__By_Ref(Type_Safe__Dict):
    expected_key_type   = Ontology_Ref
    expected_value_type = Schema__Ontology

class List__Node_Type_Refs(Type_Safe__List):
    expected_type = Node_Type_Ref
```

---

## Part 7: File Structure

### 7.1 New Files in `osbot_utils/type_safe/primitives/`

```
osbot_utils/type_safe/primitives/domains/identifiers/
├── Obj_Id.py                                        # Add from_basis() method
├── enums/
│   └── Enum__Id__Source_Type.py                     # NEW
├── safe_str/
│   └── Safe_Str__Id__Source.py                      # NEW
└── schemas/
    └── Schema__Id__Source.py                        # NEW
```

### 7.2 Renamed Files in `osbot_utils/helpers/semantic_graphs/`

```
osbot_utils/helpers/semantic_graphs/schemas/
├── identifier/
│   ├── Semantic_Id.py        → Semantic_Ref.py
│   ├── Ontology_Id.py        → Ontology_Ref.py
│   ├── Taxonomy_Id.py        → Taxonomy_Ref.py
│   ├── Category_Id.py        → Category_Ref.py
│   ├── Node_Type_Id.py       → Node_Type_Ref.py
│   ├── Rule_Set_Id.py        → Rule_Set_Ref.py
│   ├── Ontology_Id__Source.py                       # NEW
│   ├── Node_Id__Source.py                           # NEW
│   ├── Edge_Id__Source.py                           # NEW
│   └── Graph_Id__Source.py                          # NEW
├── collection/
│   ├── Dict__Node_Types__By_Id.py    → Dict__Node_Types__By_Ref.py
│   ├── Dict__Ontologies__By_Id.py    → Dict__Ontologies__By_Ref.py
│   └── List__Node_Type_Ids.py        → List__Node_Type_Refs.py
├── graph/
│   ├── Schema__Semantic_Graph.py                    # Update annotations
│   ├── Schema__Semantic_Graph__Node.py              # Update annotations
│   └── Schema__Semantic_Graph__Edge.py              # Update annotations
├── ontology/
│   └── Schema__Ontology*.py                         # Update annotations
├── taxonomy/
│   └── Schema__Taxonomy*.py                         # Update annotations
└── rule/
    └── Schema__Rule*.py                             # Update annotations
```

---

## Part 8: Implementation Checklist

### Phase 1: Core Primitives (osbot_utils/type_safe/primitives/)

- [ ] Add `from_basis()` method to `Obj_Id`
- [ ] Create `Enum__Id__Source_Type` enum
- [ ] Create `Safe_Str__Id__Source` primitive
- [ ] Create `Schema__Id__Source` schema
- [ ] Add tests for `from_basis()` determinism
- [ ] Add tests for `Schema__Id__Source`

### Phase 2: Identifier Renames (semantic_graphs/schemas/identifier/)

- [ ] Rename `Semantic_Id` → `Semantic_Ref`
- [ ] Rename `Ontology_Id` → `Ontology_Ref`
- [ ] Rename `Taxonomy_Id` → `Taxonomy_Ref`
- [ ] Rename `Category_Id` → `Category_Ref`
- [ ] Rename `Node_Type_Id` → `Node_Type_Ref`
- [ ] Rename `Rule_Set_Id` → `Rule_Set_Ref`
- [ ] Create `Ontology_Id__Source`, `Node_Id__Source`, `Edge_Id__Source`, `Graph_Id__Source`

### Phase 3: Collection Renames (semantic_graphs/schemas/collection/)

- [ ] Rename `Dict__Node_Types__By_Id` → `Dict__Node_Types__By_Ref`
- [ ] Rename `Dict__Ontologies__By_Id` → `Dict__Ontologies__By_Ref`
- [ ] Rename `Dict__Taxonomies__By_Id` → `Dict__Taxonomies__By_Ref`
- [ ] Rename `Dict__Categories__By_Id` → `Dict__Categories__By_Ref`
- [ ] Rename `List__Node_Type_Ids` → `List__Node_Type_Refs`

### Phase 4: Schema Updates (semantic_graphs/schemas/)

- [ ] Update `Schema__Semantic_Graph` — add sidecar, update refs
- [ ] Update `Schema__Semantic_Graph__Node` — add sidecar, update refs
- [ ] Update `Schema__Semantic_Graph__Edge` — add sidecar
- [ ] Update `Schema__Ontology` — add sidecar, update refs, add `__init__`
- [ ] Update `Schema__Ontology__Node_Type` — update refs
- [ ] Update `Schema__Ontology__Relationship` — update refs
- [ ] Update `Schema__Taxonomy` — add sidecar, update refs
- [ ] Update `Schema__Taxonomy__Category` — update refs
- [ ] Update `Schema__Rule_Set` — add sidecar, update refs

### Phase 5: Component Updates (semantic_graphs/)

- [ ] Update `Ontology__Registry` — update type annotations and usages
- [ ] Update `Taxonomy__Registry` — update type annotations and usages
- [ ] Update `Rule__Engine` — update type annotations and usages
- [ ] Update `Semantic_Graph__Builder` — update type annotations and usages
- [ ] Update `Semantic_Graph__Validator` — update type annotations and usages

### Phase 6: Test Updates

- [ ] Update all identifier tests for new names
- [ ] Update all collection tests for new names
- [ ] Update all schema tests for sidecar fields
- [ ] Add tests for deterministic ID creation via `from_basis()`
- [ ] Add tests for ID source tracking

### Phase 7: Code Structure Analyzer

- [ ] Update `osbot_utils/helpers/python_call_flow/code_structure/` to use `*_Ref` types

---

## Part 9: Usage Examples

### 9.1 Creating Ontology with Deterministic ID

```python
# From JSON (via from_json)
data = {
    "name": "call_flow",
    "version": "1.0.0",
    "node_types": { ... }
}
ontology = Schema__Ontology.from_json(data)
# ontology_id is deterministically derived from name "call_flow"
# ontology_id_source.source_type == DETERMINISTIC
# ontology_id_source.source == "call_flow"
```

### 9.2 Creating Node with Random ID

```python
node_id = Node_Id(Obj_Id())
node = Schema__Semantic_Graph__Node(
    node_id   = node_id,
    node_type = Node_Type_Ref('class'),
    name      = Safe_Str__Id('MyClass'),
)
# node_id_source is None (we don't care about provenance here)
```

### 9.3 Creating Node with Deterministic ID

```python
qualified_name = "mymodule.MyClass"
node_id = Node_Id(Obj_Id.from_basis(qualified_name))
node = Schema__Semantic_Graph__Node(
    node_id        = node_id,
    node_id_source = Node_Id__Source(
        source_type = Enum__Id__Source_Type.DETERMINISTIC,
        source      = Safe_Str__Id__Source(qualified_name)
    ),
    node_type      = Node_Type_Ref('class'),
    name           = Safe_Str__Id('MyClass'),
)
# Same qualified_name → same node_id, always
```

### 9.4 Cross-Session Identity

```python
# Session 1
uri = "http://osbot.dev/ontologies/call_flow/1.0"
ontology_id_1 = Ontology_Id(Obj_Id.from_basis(uri))
print(ontology_id_1)  # → "a3f2b8c1"

# Session 2 (different process, different day)
ontology_id_2 = Ontology_Id(Obj_Id.from_basis(uri))
print(ontology_id_2)  # → "a3f2b8c1" (SAME!)

assert ontology_id_1 == ontology_id_2  # ✓ Always passes
```

### 9.5 Semantic Web Compatibility

```python
# Our ontology can reference Semantic Web URIs
owl_class_id = Node_Type_Ref(Obj_Id.from_basis("http://www.w3.org/2002/07/owl#Class"))
foaf_person_id = Node_Type_Ref(Obj_Id.from_basis("http://xmlns.com/foaf/0.1/Person"))

# Pre-computed registry for common URIs
class Semantic_Web__Common_Ids:
    OWL_CLASS    = Obj_Id.from_basis("http://www.w3.org/2002/07/owl#Class")
    OWL_PROPERTY = Obj_Id.from_basis("http://www.w3.org/2002/07/owl#ObjectProperty")
    RDFS_LABEL   = Obj_Id.from_basis("http://www.w3.org/2000/01/rdf-schema#label")
```

---

## Part 10: Success Criteria

### Minimum Viable Product (MVP)

- [ ] All `*_Id` → `*_Ref` renames complete
- [ ] `Obj_Id.from_basis()` implemented and tested
- [ ] `Schema__Id__Source` implemented
- [ ] All semantic_graphs tests pass
- [ ] No regressions in functionality

### Full Implementation

- [ ] All Phase 1-7 checklist items complete
- [ ] Sidecar pattern (`*_source`) on all ID fields in schemas
- [ ] `__init__` methods for automatic ID generation where needed
- [ ] Documentation updated

### Verification

```python
def test__naming_clarity(self):
    """After refactoring, naming is self-documenting."""
    
    # References are clearly references
    node_type = Node_Type_Ref('class')               # Obviously a reference
    ontology  = Ontology_Ref('call_flow')            # Obviously a reference
    
    # IDs are clearly instance IDs
    node_id = Node_Id(Obj_Id())                      # Obviously an instance ID
    
    # Deterministic IDs work
    uri = "http://example.com/ontology"
    id_1 = Ontology_Id(Obj_Id.from_basis(uri))
    id_2 = Ontology_Id(Obj_Id.from_basis(uri))
    assert id_1 == id_2                              # Same basis → same ID
```

---

## Part 11: Migration Notes

### 11.1 No External Impact

Since no external code is currently using the semantic_graphs framework (per project notes), this refactoring has no backward compatibility concerns.

### 11.2 IDE Support

After renaming, IDEs will show clear type distinctions:
- `node_type: Node_Type_Ref` — obviously a reference/label
- `node_id: Node_Id` — obviously an instance ID

### 11.3 Grep-Friendly

```bash
# Find all references
grep -r "Ontology_Ref" osbot_utils/

# Find all instance IDs
grep -r "Ontology_Id" osbot_utils/

# Find all ID source tracking
grep -r "_source" osbot_utils/helpers/semantic_graphs/
```

---

*End of Implementation Brief*
