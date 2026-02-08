# LLM as Execution Engine: Implementing Workflows Before Code Exists

**Document:** issues-fs__llm-as-execution-engine  
**Version:** v1.0  
**Date:** 2026-02-05  
**Status:** Practical Guide  
**Depends On:** issues-fs__compatibility-through-connectivity v1.0  

---

## Executive Summary

This document describes a pragmatic approach to implementing the compatibility testing architecture *today*, without waiting for code to be written. The key insight: **an LLM can act as the execution engine for code that doesn't exist yet**. 

When we describe a function like `extract_semantic_graph(text, ontology)`, we don't need to implement it in Python immediately. We can have an LLM agent *act like* that function — receiving the same inputs, following the same logic, producing the same outputs. The layers above don't know (or care) whether they're calling real code or an LLM acting as code.

This allows us to:
1. **Use the workflow today** — No waiting for MVP
2. **Validate the design** — See if the architecture actually works before coding it
3. **Discover edge cases** — Find what we missed while the cost of change is low
4. **Gradually replace with code** — As patterns solidify, swap LLM execution for real code

The prompt becomes the specification. The LLM becomes the implementation. The interface stays the same.

---

## Part 1: The Problem — Waiting for Code

### The Traditional Approach

```
1. Design the architecture
2. Write detailed specifications
3. Implement the code
4. Test the code
5. Discover the design was wrong
6. Go back to step 1
```

This is slow, expensive, and frustrating. By the time you discover problems, you've invested significant effort in code that needs to be rewritten.

### The Bootstrap Problem

For the compatibility testing system, we need:
- Text extraction (NLP, semantic parsing)
- Diagram extraction (visual parsing, structure extraction)
- Code extraction (AST analysis, call graph building)
- Config extraction (YAML/HCL parsing, schema understanding)
- Trace extraction (OpenTelemetry parsing, pattern detection)
- Compatibility engine (graph comparison, divergence detection)
- Report generation (formatting, recommendations)

That's a lot of code. Each component is non-trivial. If we wait until all of it is implemented before we can test the *workflow*, we'll wait a long time.

### The Insight: LLM as Execution Engine

What if we don't wait?

Every function we've described has:
- **Inputs** — What it receives (text, diagrams, code, etc.)
- **Logic** — What it does (extract, compare, report)
- **Outputs** — What it produces (graphs, compatibility results, reports)

An LLM can understand all three. Given the inputs and a description of the logic, it can produce the outputs. It might not be as fast or as precise as compiled code, but it can *act like* the code.

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   CALLER (upper layer)                                              │
│                                                                     │
│   result = extract_semantic_graph(text, ontology="architecture-v1") │
│                                                                     │
└─────────────────────────────────┬───────────────────────────────────┘
                                  │
                                  │  The caller doesn't know (or care)
                                  │  what's behind this interface
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
          ┌─────────────────┐        ┌─────────────────┐
          │                 │        │                 │
          │   REAL CODE     │   OR   │   LLM AGENT     │
          │   (future)      │        │   (today)       │
          │                 │        │                 │
          │ def extract_... │        │ "Act like the   │
          │   parse(text)   │        │  function that  │
          │   build_graph() │        │  extracts..."   │
          │   return graph  │        │                 │
          │                 │        │                 │
          └─────────────────┘        └─────────────────┘
```

The abstraction layer hides the implementation. Today it's an LLM. Tomorrow it might be code. The caller never changes.

---

## Part 2: The Abstraction Pattern

### Interface Definition

Define what the function *should* do, without implementing it:

```python
class SemanticExtractor(Protocol):
    """Interface for extracting semantic graphs from text."""
    
    def extract(
        self, 
        text: str, 
        ontology: str,
        context: Optional[Dict] = None
    ) -> SemanticGraph:
        """
        Extract a semantic graph from text.
        
        Args:
            text: The source text to analyze
            ontology: The O&T to use for extraction (e.g., "architecture-v1")
            context: Optional context (e.g., related diagrams, definitions)
            
        Returns:
            A SemanticGraph containing:
            - Nodes for entities, concepts, rules
            - Edges for relationships
            - Provenance linking nodes to source text spans
        """
        ...
```

### LLM Implementation

The LLM agent implements this interface by *acting like* the function:

```python
class LLMSemanticExtractor(SemanticExtractor):
    """LLM-based implementation of semantic extraction."""
    
    def __init__(self, agent: Agent):
        self.agent = agent
    
    def extract(
        self, 
        text: str, 
        ontology: str,
        context: Optional[Dict] = None
    ) -> SemanticGraph:
        
        prompt = f"""
You are acting as a semantic extraction function. Your task is to extract
a semantic graph from the given text, following the specified ontology.

## Your Role
You ARE the function `extract_semantic_graph(text, ontology)`. 
Execute this function and return the result.

## Input: Text
{text}

## Input: Ontology
{ontology}

## Instructions
1. Identify all entities (components, technologies, actors) in the text
2. Identify all relationships between entities
3. Identify all rules, constraints, and requirements stated in the text
4. For each extracted item, note which part of the text it came from
5. Structure the output as a graph with nodes and edges

## Output Format
Return a JSON object with this structure:
{{
  "nodes": [
    {{
      "id": "unique-id",
      "type": "Component|Rule|Relationship|...",
      "label": "human readable name",
      "source_span": [start_char, end_char],
      "properties": {{...}}
    }}
  ],
  "edges": [
    {{
      "source": "node-id",
      "target": "node-id", 
      "type": "relationship type",
      "source_span": [start_char, end_char]
    }}
  ]
}}

Execute the extraction now.
"""
        
        response = self.agent.execute(prompt)
        return SemanticGraph.from_json(response)
```

### Code Implementation (Future)

When we're ready, we implement with real code:

```python
class CodeSemanticExtractor(SemanticExtractor):
    """Code-based implementation of semantic extraction."""
    
    def __init__(self, nlp_model, ontology_registry):
        self.nlp = nlp_model
        self.ontologies = ontology_registry
    
    def extract(
        self, 
        text: str, 
        ontology: str,
        context: Optional[Dict] = None
    ) -> SemanticGraph:
        
        # Real NLP pipeline
        doc = self.nlp(text)
        ont = self.ontologies.get(ontology)
        
        graph = SemanticGraph()
        
        # Entity extraction
        for ent in doc.ents:
            if ont.matches_node_type(ent.label_):
                graph.add_node(Node(
                    type=ent.label_,
                    label=ent.text,
                    source_span=(ent.start_char, ent.end_char)
                ))
        
        # Relationship extraction
        for sent in doc.sents:
            relations = self.extract_relations(sent, ont)
            for rel in relations:
                graph.add_edge(rel)
        
        return graph
```

### The Swap Is Invisible

```python
# Configuration determines which implementation to use
if config.use_llm_execution:
    extractor = LLMSemanticExtractor(agent=claude_agent)
else:
    extractor = CodeSemanticExtractor(nlp_model, ontology_registry)

# The caller doesn't change
graph = extractor.extract(text, ontology="architecture-v1")
```

---

## Part 3: Using This Today with Role Agents

### The Issues-FS Role Agents

Issues-FS already has role-based agents:
- **Architect** — Designs systems, creates diagrams, writes documentation
- **Dev** — Implements code, writes tests
- **QA** — Validates quality, finds issues
- **Librarian** — Manages knowledge, curates vocabulary

Each agent can be given a role to *act like* a component of the compatibility testing system.

### Example: Text Extraction Agent

**Prompt to Dev Agent:**

```
You are now acting as the TextExtractor component of the compatibility 
testing system.

## Your Function Signature
```python
def extract_semantic_graph(text: str, ontology: str) -> SemanticGraph
```

## Your Behavior
When given architecture documentation text, you will:

1. Parse the text to identify:
   - Components (systems, services, modules)
   - Technologies (languages, frameworks, databases)
   - Relationships (uses, contains, connects to)
   - Rules (must, never, always statements)
   - Constraints (limits, boundaries, requirements)

2. For each item, record:
   - A unique ID
   - The type (from the ontology)
   - A human-readable label
   - The character span in the source text
   - Any properties or attributes

3. Build edges for relationships between nodes

4. Output as JSON following the SemanticGraph schema

## Ontology: architecture-v1
Node types: Component, Technology, Rule, Constraint, Zone, Actor
Edge types: uses, contains, connects_to, enforces, requires

## Ready
You are now the TextExtractor. Await input text.
```

**Input:**
```
API Gateway (Kong) — The single entry point for all client traffic. 
Handles authentication (JWT validation), rate limiting (100 req/min 
for normal users, 1000 for restaurants), and routes requests to 
appropriate services. All external traffic must pass through here.
```

**Output (from agent acting as TextExtractor):**
```json
{
  "nodes": [
    {
      "id": "comp-1",
      "type": "Component",
      "label": "API Gateway",
      "source_span": [0, 11],
      "properties": {
        "role": "entry point",
        "scope": "all client traffic"
      }
    },
    {
      "id": "tech-1",
      "type": "Technology",
      "label": "Kong",
      "source_span": [13, 17],
      "properties": {}
    },
    {
      "id": "comp-2",
      "type": "Component",
      "label": "Authentication",
      "source_span": [76, 90],
      "properties": {
        "method": "JWT validation"
      }
    },
    {
      "id": "constraint-1",
      "type": "Constraint",
      "label": "Rate Limit (Normal Users)",
      "source_span": [107, 142],
      "properties": {
        "limit": "100 req/min",
        "applies_to": "normal users"
      }
    },
    {
      "id": "constraint-2",
      "type": "Constraint",
      "label": "Rate Limit (Restaurants)",
      "source_span": [144, 167],
      "properties": {
        "limit": "1000 req/min",
        "applies_to": "restaurants"
      }
    },
    {
      "id": "rule-1",
      "type": "Rule",
      "label": "All external traffic through gateway",
      "source_span": [217, 260],
      "properties": {
        "modal": "must",
        "subject": "external traffic",
        "action": "pass through",
        "object": "here (API Gateway)"
      }
    }
  ],
  "edges": [
    {
      "source": "comp-1",
      "target": "tech-1",
      "type": "uses",
      "source_span": [13, 17]
    },
    {
      "source": "comp-1",
      "target": "comp-2",
      "type": "contains",
      "source_span": [67, 104]
    },
    {
      "source": "comp-1",
      "target": "constraint-1",
      "type": "enforces",
      "source_span": [107, 167]
    },
    {
      "source": "comp-1",
      "target": "constraint-2",
      "type": "enforces",
      "source_span": [107, 167]
    },
    {
      "source": "comp-1",
      "target": "rule-1",
      "type": "enforces",
      "source_span": [217, 260]
    }
  ]
}
```

The agent *acted like* the TextExtractor function. It received input, followed the logic, and produced output in the expected format.

### Example: Diagram Extraction Agent

**Prompt to Dev Agent:**

```
You are now acting as the DiagramExtractor component of the compatibility 
testing system.

## Your Function Signature
```python
def extract_diagram_graph(diagram: str) -> DiagramGraph
```

## Your Behavior
When given an ASCII architecture diagram, you will:

1. Identify all boxes (components, services, systems)
2. Identify all arrows (relationships, data flows)
3. Identify containment (boxes inside boxes)
4. Identify zones/boundaries (groupings)

5. For each element, extract:
   - ID, type, label
   - Position hints (for layout understanding)
   - Connection points

6. Output as JSON following the DiagramGraph schema

## Ready
You are now the DiagramExtractor. Await input diagram.
```

**Input:**
```
┌──────────┐     ┌──────────┐     ┌──────────┐
│ External │────▶│ Gateway  │────▶│ Service  │
└──────────┘     └──────────┘     └──────────┘
```

**Output (from agent acting as DiagramExtractor):**
```json
{
  "nodes": [
    {
      "id": "box-1",
      "type": "Box",
      "label": "External",
      "properties": {
        "position": "left"
      }
    },
    {
      "id": "box-2",
      "type": "Box",
      "label": "Gateway",
      "properties": {
        "position": "center"
      }
    },
    {
      "id": "box-3",
      "type": "Box",
      "label": "Service",
      "properties": {
        "position": "right"
      }
    }
  ],
  "edges": [
    {
      "source": "box-1",
      "target": "box-2",
      "type": "arrow",
      "properties": {
        "direction": "right"
      }
    },
    {
      "source": "box-2",
      "target": "box-3",
      "type": "arrow",
      "properties": {
        "direction": "right"
      }
    }
  ],
  "inferred_rules": [
    {
      "type": "path_constraint",
      "description": "No direct path from External to Service",
      "evidence": "All paths from External go through Gateway"
    }
  ]
}
```

### Example: Compatibility Engine Agent

**Prompt to QA Agent:**

```
You are now acting as the CompatibilityEngine component of the 
compatibility testing system.

## Your Function Signature
```python
def assess_compatibility(
    rule: Rule,
    text_graph: SemanticGraph,
    diagram_graph: DiagramGraph,
    code_graph: Optional[CodeGraph] = None
) -> CompatibilityResult
```

## Your Behavior
When given a rule and graphs from multiple artifact types, you will:

1. Find how the rule is expressed in each graph
2. Compare the expressions for agreement/divergence
3. Identify specific points of divergence
4. Generate a compatibility assessment
5. Provide recommendations

## Ready
You are now the CompatibilityEngine. Await inputs.
```

**Input:**
```
Rule: "All external traffic must pass through API Gateway"

Text Graph: (the JSON from TextExtractor above)

Diagram Graph: (the JSON from DiagramExtractor above)
```

**Output (from agent acting as CompatibilityEngine):**
```json
{
  "rule": "All external traffic must pass through API Gateway",
  "assessments": {
    "text": {
      "status": "FOUND",
      "representation": "rule-1: modal=must, subject=external traffic, action=pass through, object=API Gateway",
      "confidence": "high",
      "source": "nodes[rule-1]"
    },
    "diagram": {
      "status": "FOUND",
      "representation": "Path External→Gateway→Service with no bypass path",
      "confidence": "high",
      "source": "inferred_rules[0]"
    }
  },
  "compatibility": {
    "status": "COMPATIBLE",
    "all_agree": true,
    "divergences": []
  },
  "summary": "Text and diagram agree: all external traffic flows through the gateway with no bypass paths shown."
}
```

---

## Part 4: The Full Workflow with Agents

### Step-by-Step Process

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     COMPATIBILITY TESTING WORKFLOW                       │
│                        (Using LLM Agents Today)                          │
└─────────────────────────────────────────────────────────────────────────┘

STEP 1: Prepare Artifacts
─────────────────────────
Human provides:
  • Architecture documentation (text)
  • Architecture diagrams (ASCII/Mermaid)
  • Code (if available)
  • Configs (if available)


STEP 2: Extract from Text
─────────────────────────
Prompt to Agent:
  "You are the TextExtractor. Here is the architecture doc.
   Extract the semantic graph using ontology architecture-v1."
   
Agent returns:
  SemanticGraph JSON


STEP 3: Extract from Diagram
────────────────────────────
Prompt to Agent:
  "You are the DiagramExtractor. Here is the ASCII diagram.
   Extract the structural graph."
   
Agent returns:
  DiagramGraph JSON


STEP 4: Extract from Code (if available)
────────────────────────────────────────
Prompt to Agent:
  "You are the CodeExtractor. Here is the source code.
   Extract the behavioral graph (modules, calls, patterns)."
   
Agent returns:
  CodeGraph JSON


STEP 5: Identify Rules
──────────────────────
Prompt to Agent:
  "You are the RuleIdentifier. Given this TextGraph,
   list all testable rules (must, never, always statements)."
   
Agent returns:
  List of Rule objects


STEP 6: Assess Compatibility (for each rule)
────────────────────────────────────────────
Prompt to Agent:
  "You are the CompatibilityEngine. 
   Rule: {rule}
   Text Graph: {text_graph}
   Diagram Graph: {diagram_graph}
   Code Graph: {code_graph}
   
   Assess compatibility across all layers."
   
Agent returns:
  CompatibilityResult JSON


STEP 7: Generate Report
───────────────────────
Prompt to Agent:
  "You are the ReportGenerator.
   Given these CompatibilityResults, generate the full
   compatibility report in the standard format."
   
Agent returns:
  Formatted compatibility report
```

### Orchestration

The human (or a conductor agent) orchestrates the workflow:

```python
# This could be run by a human or automated
async def run_compatibility_check(artifacts: Artifacts) -> Report:
    
    # Step 2: Extract from text
    text_graph = await agent.execute(
        role="TextExtractor",
        input=artifacts.text,
        ontology="architecture-v1"
    )
    
    # Step 3: Extract from diagram
    diagram_graph = await agent.execute(
        role="DiagramExtractor",
        input=artifacts.diagram
    )
    
    # Step 4: Extract from code (if available)
    code_graph = None
    if artifacts.code:
        code_graph = await agent.execute(
            role="CodeExtractor",
            input=artifacts.code
        )
    
    # Step 5: Identify rules
    rules = await agent.execute(
        role="RuleIdentifier",
        input=text_graph
    )
    
    # Step 6: Assess each rule
    results = []
    for rule in rules:
        result = await agent.execute(
            role="CompatibilityEngine",
            rule=rule,
            text_graph=text_graph,
            diagram_graph=diagram_graph,
            code_graph=code_graph
        )
        results.append(result)
    
    # Step 7: Generate report
    report = await agent.execute(
        role="ReportGenerator",
        results=results
    )
    
    return report
```

---

## Part 5: Starting with Hello World

### The Simplest Possible System

Don't start with the Food Delivery Platform. Start with Hello World:

```
┌──────────┐     ┌──────────┐
│  Client  │────▶│  Server  │
└──────────┘     └──────────┘
```

**Text description:**
```
The system consists of a Client and a Server. 
The Client sends requests to the Server.
All client requests must go to the Server (there is no other path).
```

**The rule:**
"All client requests must go to the Server"

### Run the Workflow

**Step 1: TextExtractor**
```json
{
  "nodes": [
    {"id": "client", "type": "Component", "label": "Client"},
    {"id": "server", "type": "Component", "label": "Server"},
    {"id": "rule-1", "type": "Rule", "label": "All requests to server"}
  ],
  "edges": [
    {"source": "client", "target": "server", "type": "sends_requests_to"}
  ]
}
```

**Step 2: DiagramExtractor**
```json
{
  "nodes": [
    {"id": "box-1", "type": "Box", "label": "Client"},
    {"id": "box-2", "type": "Box", "label": "Server"}
  ],
  "edges": [
    {"source": "box-1", "target": "box-2", "type": "arrow"}
  ]
}
```

**Step 3: CompatibilityEngine**
```json
{
  "rule": "All client requests must go to the Server",
  "compatibility": {
    "status": "COMPATIBLE",
    "text": "States the rule explicitly",
    "diagram": "Shows single path from Client to Server, no alternatives"
  }
}
```

**Result:** The workflow works. Now scale up.

### Scaling Up Incrementally

```
Hello World (2 boxes, 1 rule)
    ↓ (add complexity)
Simple Gateway (3 boxes, 2 rules)
    ↓
Service with Database (4 boxes, 3 rules)
    ↓
Microservices (10 boxes, 10 rules)
    ↓
Full Food Delivery Platform (20+ boxes, 20+ rules)
```

At each step, the same workflow runs. The agents handle increasing complexity. You discover what breaks and fix it before writing code.

---

## Part 6: What the Prompt *Is*

### The Prompt Is the Specification

When you write a prompt like:

```
You are the TextExtractor. Given architecture documentation,
extract a semantic graph with nodes for components, rules, and
relationships. Output as JSON following this schema...
```

You've written a specification. It defines:
- **Inputs:** Architecture documentation
- **Outputs:** SemanticGraph JSON
- **Behavior:** What to extract and how to structure it

This specification can be:
1. **Executed by an LLM** (today)
2. **Implemented as code** (tomorrow)
3. **Tested against** (always)

### The Human as Architect

When a person writes a prompt to an LLM:
- They describe intent (what they want)
- They describe structure (how it should work)
- They describe constraints (what must be true)

**This is architecture.** The prompt-writer is an architect. The LLM is the implementation.

The problem today: there's no connection between that "architecture" (the prompt) and any persistent system. Once the conversation ends, the architecture is lost. Hallucinations and drift occur because there's no verification loop.

The compatibility testing approach fixes this: the prompt (architecture) is connected to the output (implementation), and we can verify they match.

---

## Part 7: Gradual Replacement

### The Replacement Path

```
PHASE 1: All LLM
─────────────────
TextExtractor      = LLM Agent
DiagramExtractor   = LLM Agent
CodeExtractor      = LLM Agent
CompatibilityEngine = LLM Agent
ReportGenerator    = LLM Agent

(Everything works, but slow and expensive)


PHASE 2: High-Volume Components → Code
──────────────────────────────────────
TextExtractor      = Code (NLP pipeline, runs often)
DiagramExtractor   = Code (Parser, runs often)
CodeExtractor      = LLM Agent (complex, runs less often)
CompatibilityEngine = LLM Agent (reasoning-heavy)
ReportGenerator    = Code (templating, runs often)

(Hybrid: code where it's stable, LLM where it needs reasoning)


PHASE 3: Most → Code
────────────────────
TextExtractor      = Code
DiagramExtractor   = Code
CodeExtractor      = Code
CompatibilityEngine = Code (rules-based) + LLM (edge cases)
ReportGenerator    = Code

(Mostly code, LLM for genuinely hard cases)


PHASE 4: Code with LLM Fallback
───────────────────────────────
All components are code.
LLM is fallback for:
  - Unusual ontologies
  - Ambiguous text
  - Complex reasoning
  - Edge cases code can't handle
```

### What Triggers Replacement

Replace an LLM component with code when:

1. **Pattern is stable** — The inputs, logic, and outputs are well-understood
2. **Volume is high** — The component runs frequently, LLM cost adds up
3. **Latency matters** — Real-time use cases need faster execution
4. **Accuracy is critical** — Code is more deterministic than LLM
5. **Edge cases are known** — You've seen enough examples to handle them

Keep an LLM component when:

1. **Pattern is evolving** — Still learning what the component should do
2. **Volume is low** — Runs infrequently, LLM cost is acceptable
3. **Reasoning is complex** — Requires understanding, not just parsing
4. **Flexibility is needed** — New ontologies, new formats, new domains

---

## Part 8: Using This in Issues-FS Today

### The Role Agent Prompts

For the Issues-FS project, you can create role prompts that turn agents into components:

**TextExtractor Role:**
```
# Role: TextExtractor
# System: Compatibility Testing

You are the TextExtractor component. When activated, you will:

1. Receive architecture documentation text
2. Extract a semantic graph following the specified ontology
3. Return JSON in the SemanticGraph format

## Ontology (default: architecture-v1)
Node types: Component, Technology, Rule, Constraint, Zone, Actor, Relationship
Edge types: uses, contains, connects_to, enforces, requires, produces, consumes

## Output Schema
{
  "nodes": [...],
  "edges": [...],
  "metadata": {
    "source": "...",
    "ontology": "...",
    "extracted_at": "..."
  }
}

## Activation
When you see "EXTRACT:" followed by text, execute your function.
```

**DiagramExtractor Role:**
```
# Role: DiagramExtractor
# System: Compatibility Testing

You are the DiagramExtractor component. When activated, you will:

1. Receive an architecture diagram (ASCII, Mermaid, or description)
2. Extract the structural graph (boxes, arrows, containment)
3. Infer implicit rules from the structure
4. Return JSON in the DiagramGraph format

## Output Schema
{
  "nodes": [...],
  "edges": [...],
  "inferred_rules": [...],
  "metadata": {...}
}

## Activation
When you see "DIAGRAM:" followed by diagram content, execute your function.
```

**CompatibilityEngine Role:**
```
# Role: CompatibilityEngine
# System: Compatibility Testing

You are the CompatibilityEngine component. When activated, you will:

1. Receive a rule and graphs from multiple artifact types
2. Find the rule's representation in each graph
3. Compare representations for agreement/divergence
4. Generate compatibility assessment with details
5. Provide recommendations

## Input Format
RULE: {rule statement}
TEXT_GRAPH: {json}
DIAGRAM_GRAPH: {json}
CODE_GRAPH: {json or "not provided"}

## Output Schema
{
  "rule": "...",
  "assessments": {
    "text": {...},
    "diagram": {...},
    "code": {...}
  },
  "compatibility": {
    "status": "COMPATIBLE|PARTIAL|INCOMPATIBLE",
    "divergences": [...],
    "recommendations": [...]
  }
}

## Activation
When you see "ASSESS:" followed by the inputs, execute your function.
```

### Running a Check

**Human to Conductor Agent:**
```
I need to run a compatibility check on the API Gateway section of our
architecture documentation.

Here's the text:
[paste architecture text]

Here's the diagram:
[paste ASCII diagram]

Please:
1. Act as TextExtractor and extract the semantic graph
2. Act as DiagramExtractor and extract the diagram graph  
3. Act as RuleIdentifier and list all testable rules
4. Act as CompatibilityEngine and assess each rule
5. Act as ReportGenerator and give me the full report
```

**Conductor Agent:**
```
I'll run the compatibility check workflow.

## Step 1: Text Extraction
[Executes TextExtractor role, outputs JSON]

## Step 2: Diagram Extraction
[Executes DiagramExtractor role, outputs JSON]

## Step 3: Rule Identification
Found 3 testable rules:
1. "All external traffic must pass through API Gateway"
2. "Rate limiting: 100 req/min normal, 1000 restaurants"
3. "Authentication required for all requests"

## Step 4: Compatibility Assessment

### Rule 1: External traffic through gateway
[Executes CompatibilityEngine role]
Result: COMPATIBLE
- Text: Explicitly states the rule
- Diagram: Shows all paths through gateway

### Rule 2: Rate limiting
[Executes CompatibilityEngine role]
Result: PARTIAL
- Text: Specifies limits
- Diagram: Does not show rate limiting (expected, diagrams don't show this)

### Rule 3: Authentication required
[Executes CompatibilityEngine role]
Result: COMPATIBLE
- Text: States JWT validation
- Diagram: Shows auth as part of gateway

## Step 5: Compatibility Report

[Full formatted report]
```

---

## Part 9: Benefits of This Approach

### Immediate Use

No waiting for code. The workflow runs today. You can:
- Validate architecture documentation
- Check diagram/text consistency
- Identify missing rules
- Find ambiguities in descriptions

### Design Validation

By running the workflow with LLMs, you discover:
- Missing pieces in the architecture
- Ambiguous specifications
- Edge cases you hadn't considered
- Better ways to structure the components

This is cheaper than discovering these issues after writing code.

### Living Documentation

The role prompts *are* documentation. They specify:
- What each component does
- What inputs it expects
- What outputs it produces
- How it behaves

When you eventually write code, these prompts become the specification to implement against.

### Testable Prompts

You can test the prompts themselves:
- Give the same input to the prompt and the code
- Compare outputs
- If they differ, either the prompt or the code is wrong

This creates a feedback loop between specification (prompt) and implementation (code).

---

## Part 10: The Bigger Picture

### What We're Really Doing

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│   TRADITIONAL SOFTWARE DEVELOPMENT                                      │
│                                                                         │
│   Requirements → Design → Code → Test → Deploy                          │
│                                                                         │
│   (Long cycle, expensive mistakes, drift accumulates)                   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘

                              ↓ transforms into ↓

┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│   LLM-ACCELERATED DEVELOPMENT                                           │
│                                                                         │
│   Specify (prompt) → Execute (LLM) → Validate → Iterate                 │
│        ↓                                  ↓                             │
│   When stable:                      Feedback into                       │
│   Implement as code                 better prompts                      │
│                                                                         │
│   (Short cycle, cheap experiments, continuous alignment)                │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

The prompt is the new unit of specification. The LLM is the universal execution engine. Code is an optimization for stable patterns.

### The Prompt-to-Code Pipeline

```
1. PROMPT (Specification)
   "You are the TextExtractor. Given text, extract..."
   
2. LLM EXECUTION (Prototype)
   LLM acts as the function, produces outputs
   
3. VALIDATION (Testing)
   Outputs are checked, edge cases discovered
   
4. ITERATION (Refinement)
   Prompt is refined, behavior improves
   
5. STABILIZATION (Pattern Recognition)
   Inputs, outputs, and logic are well-understood
   
6. CODE (Implementation)
   Prompt becomes specification for real code
   
7. VERIFICATION (Alignment Check)
   Code outputs match LLM outputs for test cases
   
8. DEPLOYMENT (Production)
   Code runs in production, LLM is fallback
```

---

## Decisions Log

| # | Decision | Rationale |
|---|----------|-----------|
| LE1 | **LLM as execution engine, not just assistant** | Enables immediate use of workflows before code exists |
| LE2 | **Interface abstraction hides implementation** | Callers don't know if they're using LLM or code |
| LE3 | **Start with Hello World** | Validate the workflow on simple cases before scaling |
| LE4 | **Role prompts as specifications** | Prompts define what components do, become specs for code |
| LE5 | **Gradual replacement** | Move from all-LLM to all-code as patterns stabilize |
| LE6 | **Keep LLM for reasoning-heavy components** | Some things are better suited to LLM than code |
| LE7 | **Prompts are testable** | Compare LLM output to code output for validation |

---

## References

- [Compatibility Through Connectivity](./v0_4_0__issues-fs__compatibility-through-connectivity.md) — The architecture being implemented
- [Thinking in Graphs](./v0_4_0__issues-fs__thinking-in-graphs.md) — Foundational philosophy
- [Role-Based Agent Coordination](./v0_4_0__issues-fs__role-based-agent-coordination.md) — The agent framework

---

*LLM as Execution Engine v1.0*  
*Practical Guide for Issues-FS*  
*Date: 2026-02-05*
