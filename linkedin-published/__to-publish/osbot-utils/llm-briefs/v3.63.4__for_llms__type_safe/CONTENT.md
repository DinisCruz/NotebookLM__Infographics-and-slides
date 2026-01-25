# Type_Safe Capabilities Guide for LLMs

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

Type_Safe is a runtime type checking framework for Python that enforces type constraints during execution, catching errors at assignment rather than deep in execution. Unlike Python's type hints (which are ignored at runtime), Type_Safe validates every operation, auto-initializes attributes, provides domain-specific primitive types, and offers JSON serialization with full type preservation. The framework enforces a strict "ban raw primitives" philosophy where raw str, int, and float are never used — only domain-specific Safe_* types.

---

## Key Concepts

- **Runtime Type Enforcement**: Type_Safe validates every attribute assignment at runtime, catching type errors immediately at the point of entry rather than deep in business logic where debugging is difficult.

- **Ban Raw Primitives Philosophy**: Never use raw `str`, `int`, or `float` in Type_Safe classes — always use domain-specific Safe_* types that encapsulate validation rules and prevent entire categories of bugs.

- **Schemas as Pure Data Containers**: Schema classes contain ONLY type annotations with no methods or business logic — behavior belongs in separate service/helper classes for clean serialization and single responsibility.

- **Type_Safe Collections**: Type_Safe__List, Type_Safe__Dict, Type_Safe__Set validate element types on every operation — not just at creation but on every insert, update, or access.

- **@type_safe Decorator**: Provides runtime validation for method parameters and return values with automatic conversion to Safe_* types, Union support, and collection element validation.

- **Automatic JSON Round-Trip**: `.json()` serializes to dict with type information, `.from_json()` deserializes with full type coercion — recreating typed objects from raw JSON data.

---

## Core Arguments

1. Python's type hints are completely ignored at runtime — they're just documentation for static analyzers, not actual enforcement. Type_Safe makes types enforceable.

2. Raw primitives (str, int, float) enable entire categories of bugs: SQL injection, XSS, buffer overflows, integer overflows, floating-point precision errors — Safe_* types eliminate these by construction.

3. Schemas must be pure data containers with no methods because mixing data structure with behavior breaks clean serialization and violates single responsibility principle.

4. Collection subclasses (Dict__, List__, Set__) should be pure type definitions — just specify expected_key_type and expected_value_type, no custom methods allowed.

5. The @type_safe decorator on methods provides runtime validation at two critical points: call-time (all parameters) and return-time (validates and auto-converts return values).

6. Returning None is always allowed regardless of return type annotation — None means "not found" and the caller handles it. Don't clutter code with Optional[T] declarations.

---

## Key Quotes

> "NEVER use raw str, int, or float in Type_Safe classes."

> "Raw primitives have caused major bugs and security issues: SQL injection, XSS, buffer overflows, command injection."

> "Schemas are PURE DATA - no methods."

> "Caller handles None — it means 'not found' or 'no result'."

---

## Tags

`type-safe` `osbot-utils` `python` `runtime-validation` `type-checking` `safe-primitives` `json-serialization` `schema-design` `type-safe-collections` `type-safe-decorator` `domain-types` `data-validation` `llm-guide` `python-typing` `clean-architecture`

---

## Search Phrases

- "runtime type checking Python"
- "Type_Safe framework Python"
- "ban raw primitives Python"
- "type safe collections Python"
- "schema pure data container pattern"
- "@type_safe decorator usage"
- "JSON serialization with type preservation"
- "Safe_Str Safe_Int Safe_Float alternatives"
- "Python runtime type enforcement"
- "Type_Safe vs type hints"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Guide / LLM Brief |
| **Domain** | Software Development |
| **Sub-domain** | Type Safety / Python Frameworks |
| **Author** | Dinis Cruz |
| **Date Created** | Jan 2026 |
| **Version** | v3.63.4 |
| **Source Format** | Markdown |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `part_of` | OSBot-Utils Python Framework |
| `related_to` | Safe Primitives Reference Guide (v3.28.0) |
| `related_to` | @type_safe Decorator Guide (v3.63.3) |
| `related_to` | Python Formatting Guide (v3.63.4) |
| `references` | Type_Safe Collections Subclassing Guide |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `framework` | A software framework or library |
| `principle` | A guiding design principle |
| `class_type` | A type of class in the framework |
| `feature` | A capability of the framework |
| `anti_pattern` | A practice to avoid |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `enforces` | `enforced_by` | Framework enforces principle |
| `provides` | `provided_by` | Framework provides feature |
| `replaces` | `replaced_by` | Safe type replaces raw primitive |
| `contrasts_with` | `contrasts_with` | Opposing approaches |
| `includes` | `part_of` | Framework includes class type |

### Taxonomy

```
type_safe_framework
├── core_classes
│   ├── Type_Safe__Base
│   ├── Type_Safe__Primitive
│   └── Type_Safe
├── collections
│   ├── Type_Safe__List
│   ├── Type_Safe__Dict
│   ├── Type_Safe__Set
│   └── Type_Safe__Tuple
├── decorators
│   └── @type_safe
├── principles
│   ├── ban_raw_primitives
│   ├── pure_data_schemas
│   └── runtime_enforcement
└── safe_types
    ├── Safe_Str
    ├── Safe_Int
    ├── Safe_UInt
    └── Safe_Float
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `type_safe` | `framework` | Type_Safe Framework |
| `ban_raw_primitives` | `principle` | Ban Raw Primitives |
| `pure_data_schemas` | `principle` | Schemas as Pure Data |
| `runtime_enforcement` | `feature` | Runtime Type Enforcement |
| `type_safe_collections` | `feature` | Type_Safe Collections |
| `type_safe_decorator` | `feature` | @type_safe Decorator |
| `json_serialization` | `feature` | JSON Round-Trip Serialization |
| `raw_str` | `anti_pattern` | Raw str Usage |
| `raw_int` | `anti_pattern` | Raw int Usage |
| `safe_str` | `class_type` | Safe_Str Domain Types |
| `safe_int` | `class_type` | Safe_Int Domain Types |
| `python_type_hints` | `framework` | Python Type Hints (static only) |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `type_safe` | `enforces` | `ban_raw_primitives` |
| `type_safe` | `enforces` | `pure_data_schemas` |
| `type_safe` | `provides` | `runtime_enforcement` |
| `type_safe` | `provides` | `type_safe_collections` |
| `type_safe` | `provides` | `type_safe_decorator` |
| `type_safe` | `provides` | `json_serialization` |
| `safe_str` | `replaces` | `raw_str` |
| `safe_int` | `replaces` | `raw_int` |
| `type_safe` | `contrasts_with` | `python_type_hints` |
| `runtime_enforcement` | `part_of` | `type_safe` |

</details>
