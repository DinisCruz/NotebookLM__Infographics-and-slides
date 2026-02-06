# Code Representations for Semantic Graphs

**Document:** issues-fs__semantic-graph-code-representation  
**Version:** v1.0  
**Date:** 2026-02-05  
**Status:** Draft  
**Depends On:** issues-fs__semantic-text-architecture v1.0, issues-fs__thinking-in-graphs v1.0  

---

## Executive Summary

This document defines how semantic graphs are represented as code — specifically, how Issues-FS graphs compile to Python/JavaScript classes and how those classes become the subject of testing and analysis. The core insight is that **tests should operate on code representations, not on text or raw graph structures**. Text is fragile (wording changes break tests). Raw graphs are awkward to test (traversal code obscures intent). Code is robust, type-checked, and IDE-friendly.

The architecture defines: graph-to-code compilation, code-to-graph round-tripping, type system integration, and the foundation for semantic testing (covered in a separate document).

---

## Part 1: Why Code Representation?

### The Testing Problem

Consider testing a semantic graph extracted from a risk description. With raw graph traversal:

```python
# Testing the raw graph — awkward and fragile
def test_risk_has_remediation():
    graph = extract_semantic_graph(risk_description)
    
    # Find the risk node
    risk_nodes = [n for n in graph.nodes if n.type == "Risk"]
    assert len(risk_nodes) > 0, "No risk found"
    
    risk = risk_nodes[0]
    
    # Find edges from risk to remediation
    remediation_edges = [e for e in graph.edges 
                         if e.source == risk.id 
                         and e.type == "mitigated_by"]
    
    assert len(remediation_edges) > 0, "Risk has no remediation"
    
    # Get the remediation node
    remediation_id = remediation_edges[0].target
    remediation = graph.get_node(remediation_id)
    
    # Check the remediation links to a control framework
    control_edges = [e for e in graph.edges
                     if e.source == remediation.id
                     and e.type == "links_to"]
    
    assert any(e.target.startswith("NIST:") for e in control_edges), \
        "Remediation not linked to NIST control"
```

This is verbose, error-prone, and obscures the intent. The test is about graph traversal, not about meaning.

### The Text-Matching Problem

Alternatively, you might test against text patterns:

```python
# Testing via text matching — brittle
def test_risk_mentions_remediation():
    assert "remediation" in risk_description.lower()
    assert "two-factor" in risk_description.lower() or "2FA" in risk_description
```

This is even worse. It breaks when wording changes. "Remediation" might become "mitigation." "Two-factor" might become "multi-factor." The meaning is the same; the test fails.

### The Solution: Code Representation

If the semantic graph compiles to typed code, tests become natural:

```python
# Testing the code representation — clean and robust
def test_risk_has_remediation():
    risk = document.graph.Risk[0]
    
    assert risk.remediation is not None, "Risk has no remediation"
    assert risk.remediation.control_framework == "NIST", \
        "Remediation not linked to NIST"
```

The code representation **is** the graph. `risk.remediation` **is** the edge traversal. Type hints tell you what's expected. The IDE autocompletes. Refactoring tools work. The test expresses intent, not mechanics.

---

## Part 2: The Compilation Model

### Nodes Become Classes

Every node type in the Ontology/Taxonomy becomes a class:

```python
# O&T Definition
ontology:
  node_types:
    - Risk
    - Vulnerability  
    - Threat_Actor
    - Remediation

# Compiled to Python
class Risk(Semantic_Node):
    """A risk being described in the document."""
    pass

class Vulnerability(Semantic_Node):
    """A technical weakness that can be exploited."""
    pass

class Threat_Actor(Semantic_Node):
    """An entity that might exploit a vulnerability."""
    pass

class Remediation(Semantic_Node):
    """A control or fix that addresses a vulnerability."""
    pass
```

### Edges Become Typed Properties

Edge types become properties on the source node class:

```python
# O&T Definition
ontology:
  edge_types:
    - exploits: Threat_Actor → Vulnerability
    - mitigated_by: Risk → Remediation
    - affects: Vulnerability → Asset
    - links_to: Remediation → Control_Framework

# Compiled to Python
class Risk(Semantic_Node):
    vulnerabilities : List[Vulnerability]      # edges to vulnerabilities
    remediation     : Optional[Remediation]    # edge to remediation
    threat_actors   : List[Threat_Actor]       # edges to threat actors

class Vulnerability(Semantic_Node):
    exploited_by    : List[Threat_Actor]       # reverse edge
    affects         : List[Asset]              # forward edge
    remediations    : List[Remediation]        # edges to remediations

class Remediation(Semantic_Node):
    mitigates       : List[Vulnerability]      # reverse edge  
    control_framework: Optional[str]           # link to external reference
    nist_control    : Optional[NIST_Control]   # typed external link
```

The type annotations **are** the edge definitions. `risk.remediation` returns a `Remediation` object (or None). `risk.vulnerabilities` returns a list of `Vulnerability` objects. The edges are navigable as properties.

### Instance Data from Extraction

When a semantic graph is extracted from text, the code representation is instantiated:

```python
# Extracted graph (conceptual)
{
  "nodes": [
    {"id": "risk-1", "type": "Risk", "label": "Weak Password Risk"},
    {"id": "vuln-1", "type": "Vulnerability", "label": "Weak Passwords"},
    {"id": "threat-1", "type": "Threat_Actor", "label": "External Attacker"},
    {"id": "rem-1", "type": "Remediation", "label": "Implement 2FA"}
  ],
  "edges": [
    {"source": "risk-1", "type": "has_vulnerability", "target": "vuln-1"},
    {"source": "risk-1", "type": "mitigated_by", "target": "rem-1"},
    {"source": "vuln-1", "type": "exploited_by", "target": "threat-1"},
    {"source": "rem-1", "type": "links_to", "target": "NIST:IA-2"}
  ]
}

# Compiled code representation
risk = Risk(
    id              = "risk-1",
    label           = "Weak Password Risk",
    vulnerabilities = [vuln_1],              # Vulnerability instance
    remediation     = rem_1,                 # Remediation instance
)

vuln_1 = Vulnerability(
    id          = "vuln-1",
    label       = "Weak Passwords",
    exploited_by = [threat_1],               # Threat_Actor instance
)

rem_1 = Remediation(
    id               = "rem-1",
    label            = "Implement 2FA",
    mitigates        = [vuln_1],
    control_framework = "NIST",
    nist_control     = NIST_Control("IA-2"),
)

threat_1 = Threat_Actor(
    id    = "threat-1",
    label = "External Attacker",
)
```

Now you can write: `risk.remediation.nist_control.id` → `"IA-2"`

The graph is fully navigable as Python objects.

---

## Part 3: The Base Class: Semantic_Node

### Core Structure

All node classes inherit from `Semantic_Node`:

```python
from osbot_utils.type_safe import Type_Safe
from typing import List, Optional, Any

class Semantic_Node(Type_Safe):
    """Base class for all semantic graph nodes."""
    
    # Identity
    id          : str                           # Unique node ID
    label       : str                           # Human-readable label
    node_type   : str                           # Type name from O&T
    
    # Provenance
    source_text : Optional[str]                 # Original text this came from
    source_span : Optional[tuple[int, int]]     # Character span in source
    extracted_by: Optional[str]                 # Service/version that extracted
    confidence  : float = 1.0                   # Extraction confidence
    
    # Graph position
    _graph      : Optional['Semantic_Graph']    # Parent graph reference
    
    def edges_out(self, edge_type: str = None) -> List['Semantic_Node']:
        """Get all nodes this node has outgoing edges to."""
        ...
    
    def edges_in(self, edge_type: str = None) -> List['Semantic_Node']:
        """Get all nodes that have edges to this node."""
        ...
    
    def path_to(self, target: 'Semantic_Node') -> List['Semantic_Node']:
        """Find shortest path to another node."""
        ...
    
    def linked_to_anchor(self, anchor: str) -> bool:
        """Check if this node links (directly or transitively) to a Lexicon anchor."""
        ...
```

### Generated Subclasses

The O&T compiler generates typed subclasses:

```python
# Generated from Risk-Description-v1 O&T
class Risk(Semantic_Node):
    """A risk identified in the document."""
    
    # Typed edges (from O&T edge definitions)
    vulnerabilities : List[Vulnerability] = []
    threat_actors   : List[Threat_Actor]  = []
    remediation     : Optional[Remediation] = None
    impacts         : List[Impact] = []
    
    # Computed properties
    @property
    def is_mitigated(self) -> bool:
        """Check if this risk has a remediation."""
        return self.remediation is not None
    
    @property
    def severity(self) -> str:
        """Compute severity from impacts."""
        if not self.impacts:
            return "unknown"
        return max(i.severity for i in self.impacts)
    
    # Validation
    def validate(self) -> List[str]:
        """Check O&T constraints."""
        errors = []
        if not self.vulnerabilities:
            errors.append("Risk must have at least one vulnerability")
        if not self.remediation:
            errors.append("Risk should have a remediation")
        return errors
```

The type annotations are the edge schema. The properties compute derived values. The `validate()` method checks O&T constraints.

---

## Part 4: The Container: Semantic_Graph

### Graph as Typed Container

The extracted graph becomes a typed container:

```python
class Semantic_Graph(Type_Safe):
    """Container for a semantic graph with typed access."""
    
    # All nodes, keyed by type
    risks           : List[Risk] = []
    vulnerabilities : List[Vulnerability] = []
    threat_actors   : List[Threat_Actor] = []
    remediations    : List[Remediation] = []
    
    # All nodes, keyed by ID
    _nodes_by_id    : Dict[str, Semantic_Node] = {}
    
    # Metadata
    source_document : Optional[str] = None
    ontology        : Optional[str] = None
    extracted_at    : Optional[str] = None
    
    def get(self, node_id: str) -> Optional[Semantic_Node]:
        """Get a node by ID."""
        return self._nodes_by_id.get(node_id)
    
    def find(self, node_type: type, **filters) -> List[Semantic_Node]:
        """Find nodes matching criteria."""
        nodes = getattr(self, node_type.__name__.lower() + 's', [])
        for key, value in filters.items():
            nodes = [n for n in nodes if getattr(n, key, None) == value]
        return nodes
    
    def validate(self) -> Dict[str, List[str]]:
        """Validate all nodes against O&T constraints."""
        results = {}
        for node in self._nodes_by_id.values():
            errors = node.validate()
            if errors:
                results[node.id] = errors
        return results
```

### Usage Pattern

```python
# Load a document's semantic graph
graph = semantic_service.extract("risk-assessment.md", ontology="risk-v1")

# Access by type
for risk in graph.risks:
    print(f"Risk: {risk.label}")
    print(f"  Vulnerabilities: {[v.label for v in risk.vulnerabilities]}")
    print(f"  Remediation: {risk.remediation.label if risk.remediation else 'MISSING'}")

# Access by ID
specific_node = graph.get("risk-1")

# Find with filters
critical_risks = graph.find(Risk, severity="critical")
unmitigated = [r for r in graph.risks if not r.is_mitigated]

# Validate
validation_errors = graph.validate()
if validation_errors:
    print("Validation failed:")
    for node_id, errors in validation_errors.items():
        print(f"  {node_id}: {errors}")
```

---

## Part 5: Round-Trip: Code ↔ Graph

### Graph to Code (Compilation)

```python
def compile_graph_to_code(
    graph_data: dict,
    ontology: Ontology
) -> Semantic_Graph:
    """
    Compile a raw graph (nodes + edges) into typed code representation.
    """
    # Create the container
    result = Semantic_Graph(
        source_document = graph_data.get("source"),
        ontology        = ontology.name,
        extracted_at    = graph_data.get("extracted_at"),
    )
    
    # First pass: create all nodes
    for node_data in graph_data["nodes"]:
        node_class = ontology.get_node_class(node_data["type"])
        node = node_class(
            id          = node_data["id"],
            label       = node_data.get("label", ""),
            source_text = node_data.get("source_text"),
            source_span = node_data.get("source_span"),
            confidence  = node_data.get("confidence", 1.0),
        )
        node._graph = result
        result._nodes_by_id[node.id] = node
        
        # Add to typed list
        type_list = getattr(result, node_data["type"].lower() + "s", None)
        if type_list is not None:
            type_list.append(node)
    
    # Second pass: wire up edges
    for edge_data in graph_data["edges"]:
        source = result.get(edge_data["source"])
        target = result.get(edge_data["target"])
        edge_type = edge_data["type"]
        
        # Set the property on the source node
        edge_prop = ontology.get_edge_property(source.__class__, edge_type)
        current = getattr(source, edge_prop.name)
        
        if edge_prop.is_list:
            current.append(target)
        else:
            setattr(source, edge_prop.name, target)
    
    return result
```

### Code to Graph (Decompilation)

```python
def decompile_code_to_graph(
    semantic_graph: Semantic_Graph,
    ontology: Ontology
) -> dict:
    """
    Decompile typed code representation back to raw graph format.
    """
    nodes = []
    edges = []
    
    for node_id, node in semantic_graph._nodes_by_id.items():
        # Export node
        nodes.append({
            "id": node.id,
            "type": node.node_type,
            "label": node.label,
            "source_text": node.source_text,
            "source_span": node.source_span,
            "confidence": node.confidence,
        })
        
        # Export edges from this node
        for edge_prop in ontology.get_edge_properties(node.__class__):
            targets = getattr(node, edge_prop.name)
            if targets is None:
                continue
            if not isinstance(targets, list):
                targets = [targets]
            
            for target in targets:
                edges.append({
                    "source": node.id,
                    "type": edge_prop.edge_type,
                    "target": target.id,
                })
    
    return {
        "nodes": nodes,
        "edges": edges,
        "source": semantic_graph.source_document,
        "ontology": semantic_graph.ontology,
        "extracted_at": semantic_graph.extracted_at,
    }
```

### Round-Trip Guarantee

The compilation is lossless. You can:

1. Extract a graph from text
2. Compile it to code
3. Decompile it back to graph format
4. The result equals the original (modulo ordering)

This enables:
- Editing in code form, persisting in graph form
- Testing in code form, storing in graph form
- Migration between representations

---

## Part 6: Type System Integration

### Python Type Hints

The generated classes use Python's type system:

```python
from typing import List, Optional, Union
from typing_extensions import Annotated

class Risk(Semantic_Node):
    # Required edge (list, at least one expected)
    vulnerabilities: Annotated[List[Vulnerability], MinLength(1)]
    
    # Optional edge (may be None)
    remediation: Optional[Remediation]
    
    # Union type (can link to multiple types)
    related_items: List[Union[Risk, Vulnerability, Asset]]
    
    # Literal for constrained values
    severity: Literal["low", "medium", "high", "critical"]
```

### TypeScript Types (for JS representation)

```typescript
interface Risk extends SemanticNode {
    vulnerabilities: Vulnerability[];  // Required, non-empty
    remediation?: Remediation;         // Optional
    relatedItems: (Risk | Vulnerability | Asset)[];
    severity: "low" | "medium" | "high" | "critical";
}

interface Vulnerability extends SemanticNode {
    exploitedBy: ThreatActor[];
    affects: Asset[];
    remediations: Remediation[];
}
```

### IDE Support

Because the representation uses standard types:

- **Autocomplete:** `risk.` shows all available properties
- **Type checking:** `risk.remediation.xyz` errors if `xyz` isn't on `Remediation`
- **Go to definition:** Jump from usage to class definition
- **Find references:** Find all places a property is accessed
- **Refactoring:** Rename a property across the codebase

This is development ergonomics that raw graph traversal can never provide.

### Static Analysis

Tools like mypy (Python) or tsc (TypeScript) can verify:

```python
# mypy catches this at development time
risk: Risk = graph.risks[0]
print(risk.remediaton)  # Error: "remediaton" is not a member of "Risk"
                        # Did you mean "remediation"?

# mypy catches type mismatches
risk.severity = "very high"  # Error: Literal["low", "medium", "high", "critical"] expected
```

Errors caught at development time, not at runtime.

---

## Part 7: Advanced Patterns

### Computed Properties

Some properties aren't stored — they're computed from the graph:

```python
class Risk(Semantic_Node):
    vulnerabilities: List[Vulnerability]
    remediation: Optional[Remediation]
    
    @property
    def is_mitigated(self) -> bool:
        """True if this risk has a remediation."""
        return self.remediation is not None
    
    @property
    def exploit_paths(self) -> List[List[Semantic_Node]]:
        """All paths from threat actors to assets through this risk."""
        paths = []
        for vuln in self.vulnerabilities:
            for threat in vuln.exploited_by:
                for asset in vuln.affects:
                    paths.append([threat, vuln, self, asset])
        return paths
    
    @property
    def control_coverage(self) -> float:
        """Percentage of vulnerabilities with remediations."""
        if not self.vulnerabilities:
            return 0.0
        mitigated = sum(1 for v in self.vulnerabilities if v.remediations)
        return mitigated / len(self.vulnerabilities)
```

### Validation Methods

O&T constraints become validation methods:

```python
class Risk(Semantic_Node):
    def validate(self) -> List[ValidationError]:
        errors = []
        
        # From O&T: "every Risk must have at least one Vulnerability"
        if not self.vulnerabilities:
            errors.append(ValidationError(
                node=self,
                constraint="min_vulnerabilities",
                message="Risk must have at least one vulnerability",
                severity="error",
            ))
        
        # From O&T: "every Risk should have a Remediation"
        if not self.remediation:
            errors.append(ValidationError(
                node=self,
                constraint="should_have_remediation",
                message="Risk should have a remediation",
                severity="warning",
            ))
        
        # From O&T: "Remediation must link to control framework"
        if self.remediation and not self.remediation.control_framework:
            errors.append(ValidationError(
                node=self.remediation,
                constraint="control_framework_required",
                message="Remediation must link to a control framework",
                severity="error",
            ))
        
        return errors
```

### Traversal Methods

Common graph traversals become methods:

```python
class Semantic_Node(Type_Safe):
    
    def ancestors(self, edge_type: str = None) -> List['Semantic_Node']:
        """Get all nodes that lead to this node."""
        result = []
        visited = set()
        queue = list(self.edges_in(edge_type))
        
        while queue:
            node = queue.pop(0)
            if node.id not in visited:
                visited.add(node.id)
                result.append(node)
                queue.extend(node.edges_in(edge_type))
        
        return result
    
    def descendants(self, edge_type: str = None) -> List['Semantic_Node']:
        """Get all nodes reachable from this node."""
        # Similar implementation
        ...
    
    def connectivity_to(self, anchor: str) -> int:
        """Count hops to a Lexicon anchor node."""
        path = self.path_to_anchor(anchor)
        return len(path) if path else -1
```

---

## Part 8: The O&T Compiler

### From O&T to Code

The O&T compiler takes an ontology definition and generates Python/TypeScript classes:

```python
class OT_Compiler:
    """Compiles Ontology/Taxonomy definitions to typed code."""
    
    def compile(self, ontology: Ontology, target: str = "python") -> str:
        """Generate code for an ontology."""
        if target == "python":
            return self._compile_python(ontology)
        elif target == "typescript":
            return self._compile_typescript(ontology)
        else:
            raise ValueError(f"Unknown target: {target}")
    
    def _compile_python(self, ontology: Ontology) -> str:
        lines = [
            "# Auto-generated from O&T: " + ontology.name,
            "# Do not edit manually",
            "",
            "from semantic_graph import Semantic_Node, Semantic_Graph",
            "from typing import List, Optional, Literal",
            "",
        ]
        
        # Generate node classes
        for node_type in ontology.node_types:
            lines.extend(self._generate_node_class(node_type, ontology))
            lines.append("")
        
        # Generate graph container
        lines.extend(self._generate_graph_class(ontology))
        
        return "\n".join(lines)
    
    def _generate_node_class(self, node_type: NodeType, ontology: Ontology) -> List[str]:
        lines = [
            f"class {node_type.name}(Semantic_Node):",
            f'    """{node_type.description}"""',
        ]
        
        # Add edge properties
        for edge in ontology.edges_from(node_type):
            target_type = edge.target_type.name
            if edge.cardinality == "many":
                type_hint = f"List[{target_type}]"
                default = "[]"
            else:
                type_hint = f"Optional[{target_type}]"
                default = "None"
            
            lines.append(f"    {edge.property_name}: {type_hint} = {default}")
        
        # Add validation method
        lines.extend(self._generate_validation(node_type, ontology))
        
        return lines
```

### CLI Integration

```bash
# Compile an O&T to Python
issues-fs ontology compile risk-description-v1.yaml --target python --output risk_types.py

# Compile to TypeScript
issues-fs ontology compile risk-description-v1.yaml --target typescript --output risk_types.ts

# Validate O&T definition
issues-fs ontology validate risk-description-v1.yaml

# Generate from Lexicon anchors
issues-fs ontology from-lexicon anchor__risk anchor__vulnerability --output risk_types.py
```

---

## Part 9: Why This Matters for Testing

### The Foundation for Semantic Tests

With code representations, tests become natural Python/JS:

```python
# Test that a risk is properly structured
def test_risk_structure():
    risk = document.graph.risks[0]
    
    # These are property accesses, not graph traversals
    assert risk.vulnerabilities, "Risk must have vulnerabilities"
    assert risk.remediation, "Risk should have remediation"
    assert risk.remediation.control_framework == "NIST"

# Test relationships
def test_remediation_covers_vulnerabilities():
    for risk in document.graph.risks:
        for vuln in risk.vulnerabilities:
            assert vuln.remediations, f"Vulnerability {vuln.label} has no remediation"

# Test computed properties
def test_control_coverage():
    for risk in document.graph.risks:
        assert risk.control_coverage >= 0.8, \
            f"Risk {risk.label} has low control coverage: {risk.control_coverage}"
```

### Not Testing Text — Testing Meaning

The crucial difference:

**Bad (testing text):**
```python
def test_risk_mentions_remediation():
    assert "remediation" in risk_text.lower()  # Breaks if wording changes
```

**Good (testing code representation):**
```python
def test_risk_has_remediation():
    assert risk.remediation is not None  # Tests structure, not wording
```

The text can change ("remediation" → "mitigation" → "control" → "countermeasure"). As long as the extracted graph has the right structure, the test passes.

### IDE-Friendly Test Development

Writing tests against typed code:

```python
def test_risk_assessment():
    risk = document.graph.risks[0]
    
    # IDE shows: risk.vulnerabilities, risk.remediation, risk.threat_actors, ...
    # Autocomplete works
    # Type errors are caught before running
    
    risk.remediation.  # IDE shows: label, control_framework, nist_control, mitigates, ...
```

This is a dramatically better development experience than writing graph traversal code.

---

## Decisions Log

| # | Decision | Rationale |
|---|----------|-----------|
| CR1 | **Nodes become classes** | Classes provide type checking, IDE support, and natural property access. |
| CR2 | **Edges become typed properties** | Properties are more natural than traversal methods. Types constrain valid relationships. |
| CR3 | **Round-trip compilation** | Graph ↔ code conversion must be lossless. Edit in either form, store in either form. |
| CR4 | **Use standard type systems** | Python type hints and TypeScript types enable static analysis and IDE support. |
| CR5 | **Validation from O&T** | O&T constraints compile to validation methods. Runtime checking matches schema. |
| CR6 | **Computed properties for derived values** | Not everything needs to be stored. Compute severity, coverage, paths on demand. |
| CR7 | **Tests target code, not text** | Code representations are stable across wording changes. Text tests are brittle. |

---

## References

- [Semantic Text Architecture](./v0_4_0__issues-fs__semantic-text-architecture.md) — How text becomes graphs
- [Thinking in Graphs](./v0_4_0__issues-fs__thinking-in-graphs.md) — Foundational philosophy
- [Issues-FS Lexicon Architecture](./v0_4_0__issues-fs__lexicon-architecture-v2.md) — O&T definitions
- [Type_Safe Capabilities Guide](./v3_63_4__for_llms__type_safe.md) — Base class patterns

---

*Code Representations for Semantic Graphs v1.0*  
*Date: 2026-02-05*
