# OSBot-Utils Safe Primitives Complete Reference Guide

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

OSBot-Utils provides a comprehensive collection of 100+ type-safe primitive classes that enforce validation rules at instantiation time, preventing invalid data from entering your system. These primitives cover common use cases including strings, integers, floats, identifiers, cryptographic hashes, file paths, HTTP headers, LLM parameters, and network types — all with zero performance overhead since validation happens once at creation time.

---

## Key Concepts

- **Safe_Str**: Base type-safe string with regex validation, sanitization, length limits, and case control — the foundation for all domain-specific string types.

- **Safe_Int / Safe_UInt**: Type-safe integers with min/max bounds, string conversion support, and range clamping — Safe_UInt enforces non-negative values automatically.

- **Safe_Float**: Type-safe float with exact Decimal arithmetic support, precision control, and epsilon-based equality comparisons for financial and scientific calculations.

- **Domain-Specific Types**: Over 100 specialized primitives covering identifiers (Guid, Safe_Id), cryptography (SHA1, NaCl keys), files (paths, sizes), HTTP (headers, methods), LLM (prompts, temperature), and web (URLs, emails).

- **Type_Safe Integration**: Primitives integrate seamlessly with Type_Safe classes, enabling automatic validation on creation, JSON serialization with type preservation, and deserialization that recreates typed objects.

- **Validation at Instantiation**: All validation happens once when the object is created — subsequent operations carry zero overhead since the data is guaranteed valid.

---

## Core Arguments

1. Traditional Python primitive types (str, int, float) allow any value, creating entire categories of bugs from SQL injection to buffer overflows — Safe primitives eliminate these by construction.

2. Each Safe primitive encapsulates domain knowledge (max lengths, valid characters, numeric ranges) as code that cannot be bypassed or forgotten.

3. Validation at instantiation time means bugs are caught immediately where bad data originates, not deep in business logic where debugging is difficult.

4. Type annotations with Safe primitives are self-documenting — the type tells you exactly what constraints apply without reading implementation code.

5. Exact decimal arithmetic via `use_decimal=True` eliminates floating-point errors in financial calculations where 0.1 + 0.2 must equal 0.3.

6. The 100+ included primitives cover most common use cases out of the box, while the extensible base classes make creating custom domain types straightforward.

---

## Key Quotes

> "Type-safe integer with validation: min/max values, allow_str, clamp_to_range."

> "Catch invalid data at creation time, not runtime."

> "With Safe_Float and use_decimal=True: Money(0.1) + Money(0.2) == Money(0.3) — True!"

> "Choose the right primitive: Use domain-specific types over generic ones."

---

## Tags

`osbot-utils` `type-safe` `safe-primitives` `python` `validation` `safe-str` `safe-int` `safe-float` `runtime-validation` `domain-types` `cryptography` `identifiers` `http-types` `llm-types` `file-paths`

---

## Search Phrases

- "type-safe primitives for Python"
- "runtime validation Python types"
- "OSBot-Utils safe string validation"
- "exact decimal arithmetic Python"
- "domain-specific type validation"
- "Safe_Str regex validation"
- "LLM parameter validation types"
- "HTTP header type safety"
- "preventing SQL injection through types"
- "Python validation at instantiation"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Reference Guide |
| **Domain** | Software Development |
| **Sub-domain** | Type Safety / Validation |
| **Author** | Dinis Cruz |
| **Date Created** | 11 Nov 2025 |
| **Version** | v3.28.0 |
| **Source Format** | Markdown |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `part_of` | OSBot-Utils Python Framework |
| `uses` | Type_Safe base class |
| `related_to` | Type_Safe Capabilities Guide (v3.63.4) |
| `related_to` | @type_safe Decorator Guide (v3.63.3) |
| `references` | Python decimal module |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `framework` | A software library or framework |
| `primitive_type` | A type-safe primitive class |
| `domain` | A category of domain-specific types |
| `feature` | A capability or feature of the framework |
| `benefit` | A positive outcome from using the framework |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `provides` | `provided_by` | Framework provides primitive types |
| `includes` | `part_of` | Domain includes specific types |
| `enables` | `enabled_by` | Feature enables benefit |
| `validates` | `validated_by` | Type validates data category |
| `extends` | `extended_by` | Type extends base class |

### Taxonomy

```
safe_primitives
├── core_types
│   ├── Safe_Str
│   ├── Safe_Int
│   ├── Safe_UInt
│   └── Safe_Float
├── domain_types
│   ├── identifiers
│   ├── cryptography
│   ├── files
│   ├── http
│   ├── llm
│   ├── network
│   ├── numerical
│   └── web
└── features
    ├── instantiation_validation
    ├── regex_sanitization
    ├── decimal_arithmetic
    └── type_safe_integration
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `osbot_utils` | `framework` | OSBot-Utils |
| `safe_str` | `primitive_type` | Safe_Str |
| `safe_int` | `primitive_type` | Safe_Int |
| `safe_uint` | `primitive_type` | Safe_UInt |
| `safe_float` | `primitive_type` | Safe_Float |
| `identifiers_domain` | `domain` | Identifiers (Guid, Safe_Id, Timestamp) |
| `crypto_domain` | `domain` | Cryptography (SHA1, NaCl Keys) |
| `http_domain` | `domain` | HTTP Types (Headers, Methods) |
| `llm_domain` | `domain` | LLM Types (Prompts, Temperature) |
| `instantiation_validation` | `feature` | Validation at Instantiation |
| `decimal_arithmetic` | `feature` | Exact Decimal Arithmetic |
| `bug_prevention` | `benefit` | Bug Prevention by Construction |
| `self_documenting` | `benefit` | Self-Documenting Type Annotations |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `osbot_utils` | `provides` | `safe_str` |
| `osbot_utils` | `provides` | `safe_int` |
| `osbot_utils` | `provides` | `safe_float` |
| `safe_uint` | `extends` | `safe_int` |
| `identifiers_domain` | `part_of` | `osbot_utils` |
| `crypto_domain` | `part_of` | `osbot_utils` |
| `http_domain` | `part_of` | `osbot_utils` |
| `llm_domain` | `part_of` | `osbot_utils` |
| `instantiation_validation` | `enables` | `bug_prevention` |
| `decimal_arithmetic` | `enables` | `safe_float` |
| `safe_str` | `validates` | `identifiers_domain` |

</details>
