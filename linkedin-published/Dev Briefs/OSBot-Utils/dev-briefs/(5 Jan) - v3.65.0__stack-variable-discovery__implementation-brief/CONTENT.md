# Stack Variable Discovery - Implementation Brief (v3.65.0)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

Stack Variable Discovery is a technique for finding specially-named variables in the call stack with frame injection caching to make repeated lookups O(1). This enables "ambient context" - configuration or state that is automatically available to all code within a certain scope without passing it through every function signature.

---

## Key Concepts

- **Stack Variable Discovery**: Walking the call stack to find a variable, with frame injection for O(1) subsequent lookups
- **Frame Injection Caching**: When the variable is found, inject it into all frames walked through for future lookups
- **Ambient Context**: Configuration or state automatically available to all code within a scope without explicit parameter passing
- **Magic Variable Names**: Using underscore-wrapped names like `_my_context_` for uniqueness and intentionality
- **Checked Variable Marker**: Separate variable to cache "not found" state, avoiding repeated full walks

---

## Core Arguments

1. Explicit parameter passing pollutes every function signature in deep call stacks
2. Thread-local storage has cleanup issues and can leak on exceptions - stack walk auto-cleans when frame exits
3. Frame injection turns O(n) repeated lookups into O(1) hits via `frame.f_locals` injection
4. No global state required - everything lives in stack frames and auto-cleans
5. Context managers provide clear scope boundaries for when context is active
6. Measured performance: cache hit (1-2 frames) takes only ~300-500 nanoseconds

---

## Key Quotes

> "Stack Variable Discovery provides ambient context without explicit parameter passing"

> "No global state - everything in stack frames with automatic cleanup when frames exit"

> "Zero heap allocations for caching - only stack frame locals modified"

---

## Performance Characteristics

| Scenario | First Call | Subsequent Calls |
|----------|------------|------------------|
| Context at depth d | O(d) | O(1-2) |
| No context | O(MAX_DEPTH) | O(1-2) |

---

## Tags

`stack-variable-discovery` `osbot-utils` `context-propagation` `frame-injection` `python-internals` `ambient-context` `performance-optimization` `call-stack` `type-safe` `configuration-pattern`

---

## Search Phrases

- "stack variable discovery Python"
- "ambient context pattern"
- "frame injection caching"
- "context propagation without parameters"
- "Python call stack inspection"
- "thread-local alternative"
- "O(1) context lookup"
- "state-free context propagation"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Documentation / Implementation Brief |
| **Domain** | Software Development |
| **Sub-domain** | Python Internals, Design Patterns |
| **Author** | Dinis Cruz |
| **Version** | v3.65.0 |
| **Date Created** | 5 Jan 2026 |
| **Source Format** | Markdown |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |
| **Repository** | https://github.com/owasp-sbot/OSBot-Utils |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `part_of` | OSBot-Utils framework |
| `uses` | Python `inspect` module |
| `related_to` | [Type_Safe runtime primitives](../../features/(16%20Jan)%20-%20v3.71.1__semantic-graphs__llm-usage-brief/) |
| `alternative_to` | Thread-local storage |
| `alternative_to` | Explicit parameter passing |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `technique` | Implementation approach |
| `component` | Part of the system |
| `benefit` | Positive outcome |
| `alternative` | Other approaches |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `uses` | `used_by` | Technique employs component |
| `produces` | `produced_by` | Technique creates benefit |
| `replaces` | `replaced_by` | Technique is alternative to another |
| `enables` | `enabled_by` | Component makes capability possible |

### Taxonomy

```
stack_variable_discovery (root)
├── technique
│   ├── stack_walk
│   ├── frame_injection
│   └── checked_marker
├── component
│   ├── magic_variable_name
│   ├── context_manager
│   └── max_depth_limit
├── benefit
│   ├── o1_lookup
│   ├── no_global_state
│   ├── auto_cleanup
│   └── thread_safety
└── alternative
    ├── thread_local
    ├── explicit_params
    └── global_cache
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `stack_discovery` | `technique` | Stack Variable Discovery |
| `frame_injection` | `technique` | Frame Injection Caching |
| `checked_marker` | `technique` | Checked Variable Marker |
| `magic_name` | `component` | Magic Variable Name |
| `context_mgr` | `component` | Context Manager Pattern |
| `o1_lookup` | `benefit` | O(1) Subsequent Lookups |
| `no_global` | `benefit` | No Global State |
| `auto_cleanup` | `benefit` | Automatic Cleanup |
| `thread_safe` | `benefit` | Thread Safety by Design |
| `thread_local` | `alternative` | Thread-Local Storage |
| `explicit_params` | `alternative` | Explicit Parameters |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `stack_discovery` | `uses` | `frame_injection` |
| `stack_discovery` | `uses` | `checked_marker` |
| `stack_discovery` | `uses` | `magic_name` |
| `frame_injection` | `produces` | `o1_lookup` |
| `stack_discovery` | `produces` | `no_global` |
| `stack_discovery` | `produces` | `auto_cleanup` |
| `stack_discovery` | `produces` | `thread_safe` |
| `context_mgr` | `enables` | `auto_cleanup` |
| `stack_discovery` | `replaces` | `thread_local` |
| `stack_discovery` | `replaces` | `explicit_params` |

</details>
