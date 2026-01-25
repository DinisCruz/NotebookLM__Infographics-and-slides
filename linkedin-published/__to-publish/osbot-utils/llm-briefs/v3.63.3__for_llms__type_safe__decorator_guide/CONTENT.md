# @type_safe Decorator: Comprehensive Guide for LLMs

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

The @type_safe decorator provides runtime type enforcement for Python method parameters and return values, solving the critical problem that Python's type hints are completely ignored at runtime. The decorator validates at two critical points: call-time (all parameters checked, auto-converted to Safe_* types) and return-time (return value validated and auto-converted). This enables catching type errors immediately at the point of entry rather than deep in execution where debugging is difficult.

---

## Key Concepts

- **Two-Phase Validation**: The decorator validates at call-time (all parameters) and return-time (validates return value against annotation), providing complete contract enforcement.

- **Auto-Conversion**: Parameters and return values are automatically converted to Safe_* types when compatible — passing a string where Safe_Id is expected triggers automatic conversion with validation.

- **List Element Validation**: `List[T]` validates every element, not just the list itself — catching wrong-type elements with the exact index in the error message.

- **Optional via Default = None**: Parameters become optional by setting `= None` as default — no need for verbose `Optional[T]` declarations. None returns are always allowed.

- **Return Collection Auto-Conversion**: Plain lists, dicts, and sets returned from methods are automatically converted to Type_Safe__List, Type_Safe__Dict, Type_Safe__Set with type enforcement.

- **Performance Optimization**: Methods with no parameters (or only `self`) bypass most validation overhead — about 5x overhead vs undecorated, while full parameter validation has variable overhead.

---

## Core Arguments

1. Python's type hints are documentation only — the interpreter completely ignores them, allowing wrong types to flow through the system and crash far from where bad data entered.

2. Silent type failures lead to delayed errors and debugging nightmares — @type_safe catches errors immediately at the call site with clear messages naming the parameter and expected vs. actual types.

3. List element validation is critical — without it, `['a', 'b', 123]` passes validation for `List[str]` in pure Python, but @type_safe catches the integer at index 2 immediately.

4. Auto-conversion enables ergonomic APIs where callers can pass compatible types (strings for Safe_Id) while the method body always works with properly typed values.

5. None returns are always valid regardless of return type annotation — this avoids cluttering every method with Optional[T] and follows the principle that None means "not found" with caller responsibility.

6. Collection auto-conversion ensures that even methods returning plain `[]` or `{}` produce type-safe collections that will validate future operations.

---

## Key Quotes

> "Python's type hints for function parameters and return values are completely ignored at runtime. They're just documentation."

> "The error message says 'NoneType has no attribute name' — but the actual bug was passing int instead of str THREE calls earlier!"

> "List item at index 2 expected type str, but got int — identifies WHICH element failed."

> "None is always allowed regardless of return type — None means 'not found' or 'no result'."

---

## Tags

`type-safe` `decorator` `python` `runtime-validation` `type-checking` `parameter-validation` `return-validation` `auto-conversion` `list-validation` `callable-validation` `optional-parameters` `osbot-utils` `llm-guide` `method-contracts` `type-enforcement`

---

## Search Phrases

- "@type_safe decorator Python"
- "runtime parameter validation Python"
- "return type validation decorator"
- "List element type validation"
- "auto-convert to Safe types"
- "Optional parameters without Optional"
- "Python type hints not enforced"
- "method contract enforcement Python"
- "Type_Safe__List auto-conversion"
- "callable validation in lists"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Guide / LLM Brief |
| **Domain** | Software Development |
| **Sub-domain** | Type Safety / Python Decorators |
| **Author** | Dinis Cruz |
| **Date Created** | Jan 2026 |
| **Version** | v3.63.3 |
| **Source Format** | Markdown |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `part_of` | OSBot-Utils Python Framework |
| `prerequisite` | Type_Safe Capabilities Guide (v3.63.4) |
| `related_to` | Safe Primitives Reference Guide (v3.28.0) |
| `related_to` | Type_Safe Collections Subclassing Guide |
| `contrasts_with` | Python static type hints (mypy, PyCharm) |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `decorator` | A Python decorator that wraps functions |
| `validation_phase` | A point where validation occurs |
| `feature` | A capability provided by the decorator |
| `problem` | An issue the decorator solves |
| `conversion` | An automatic type conversion |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `performs` | `performed_by` | Decorator performs validation |
| `solves` | `solved_by` | Decorator solves problem |
| `enables` | `enabled_by` | Feature enables capability |
| `converts` | `converted_by` | Auto-converts types |
| `occurs_at` | `timing_of` | Validation occurs at phase |

### Taxonomy

```
type_safe_decorator
├── validation_phases
│   ├── call_time_validation
│   │   ├── parameter_type_check
│   │   ├── union_type_matching
│   │   ├── list_element_validation
│   │   └── callable_validation
│   └── return_time_validation
│       ├── return_type_check
│       └── collection_auto_conversion
├── auto_conversions
│   ├── primitive_to_safe_type
│   ├── list_to_type_safe_list
│   ├── dict_to_type_safe_dict
│   └── set_to_type_safe_set
└── optimizations
    ├── no_params_fast_path
    └── only_self_fast_path
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `type_safe_decorator` | `decorator` | @type_safe Decorator |
| `call_time` | `validation_phase` | Call-Time Validation |
| `return_time` | `validation_phase` | Return-Time Validation |
| `list_element_validation` | `feature` | List Element Validation |
| `callable_validation` | `feature` | Callable List Validation |
| `auto_conversion` | `feature` | Auto-Conversion to Safe Types |
| `collection_conversion` | `conversion` | Collection Auto-Conversion |
| `silent_type_failures` | `problem` | Silent Type Failures in Python |
| `delayed_errors` | `problem` | Delayed Error Discovery |
| `optional_via_default` | `feature` | Optional via = None Default |
| `none_always_valid` | `feature` | None Always Valid Return |
| `performance_optimization` | `feature` | Fast Path for No-Param Methods |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `type_safe_decorator` | `performs` | `call_time` |
| `type_safe_decorator` | `performs` | `return_time` |
| `type_safe_decorator` | `solves` | `silent_type_failures` |
| `type_safe_decorator` | `solves` | `delayed_errors` |
| `type_safe_decorator` | `enables` | `list_element_validation` |
| `type_safe_decorator` | `enables` | `callable_validation` |
| `type_safe_decorator` | `enables` | `auto_conversion` |
| `return_time` | `enables` | `collection_conversion` |
| `call_time` | `enables` | `optional_via_default` |
| `return_time` | `enables` | `none_always_valid` |
| `type_safe_decorator` | `enables` | `performance_optimization` |

</details>
