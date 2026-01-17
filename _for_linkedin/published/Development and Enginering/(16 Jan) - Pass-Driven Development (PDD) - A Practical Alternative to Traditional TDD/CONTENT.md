# Pass-Driven Development (PDD)

> *Semantic content index for search, discovery, and knowledge graph integration*

---

## Summary

Pass-Driven Development (PDD) is a practical alternative to Test-Driven Development that starts with passing tests rather than failing ones. This "green bar first" approach reduces psychological friction and accelerates adoption while maintaining test-first discipline.

---

## Key Concepts

- **Green Bar Psychology**: Starting with passing tests provides immediate positive reinforcement
- **Practical Adoption**: Focus on what actually gets developers writing tests
- **Iterative Refinement**: Tests evolve from passing stubs to meaningful assertions
- **Coverage-Driven Design**: Let coverage metrics guide test expansion

---

## Core Arguments

1. TDD's "red-green-refactor" creates adoption barriers
2. Most developers abandon TDD due to initial frustration
3. PDD maintains test-first benefits with lower friction
4. Passing tests create momentum for continued testing

---

## Key Quotes

> "The best test is one that exists and runs"

> "Start green, refine to red, fix to green"

---

## Tags

`testing` `tdd` `pdd` `test-driven-development` `pass-driven-development` `software-engineering` `best-practices` `developer-experience` `methodology` `green-bar` `test-first`

---

## Search Phrases

- "alternative to TDD"
- "test-driven development problems"
- "starting with passing tests"
- "green bar psychology"
- "test adoption barriers"
- "practical testing approach"
- "developer-friendly testing"
- "iterative test refinement"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Methodology |
| **Domain** | Software Development |
| **Sub-domain** | Testing Practices |
| **Author** | Dinis Cruz |
| **Date Created** | 16 Jan 2026 |
| **Source Format** | PDF White Paper |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `related_to` | [Quadratic Incident](/by-topic/osbot-utils/Performance%20Profiling/Quadratic%20Incident/) |
| `contrasts_with` | Traditional TDD Literature |
| `builds_on` | Test-First Principles |

---

## Semantic Graph

<details>
<summary>Click to expand graph structure (for programmatic consumption)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `methodology` | Development approach or practice |
| `concept` | Key idea or principle |
| `practice` | Concrete technique or activity |
| `benefit` | Positive outcome or advantage |
| `challenge` | Difficulty or problem addressed |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `addresses` | `addressed_by` | Methodology solves a challenge |
| `includes` | `part_of` | Methodology contains practice |
| `produces` | `produced_by` | Practice creates benefit |
| `requires` | `required_by` | Practice needs concept |
| `contrasts_with` | `contrasts_with` | Compares to alternative |

### Taxonomy

```
content_element (root)
├── methodology
│   ├── pdd (Pass-Driven Development)
│   └── tdd (Test-Driven Development)
├── practice
│   ├── writing_passing_tests
│   ├── iterative_refinement
│   └── coverage_driven_design
├── concept
│   ├── green_bar_psychology
│   └── practical_adoption
└── benefit
    ├── lower_barrier_to_entry
    ├── immediate_feedback
    └── measurable_progress
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `pdd` | `methodology` | Pass-Driven Development |
| `tdd` | `methodology` | Test-Driven Development |
| `passing_tests` | `practice` | Start with Passing Tests |
| `iterative` | `practice` | Iterative Test Refinement |
| `coverage` | `practice` | Coverage-Driven Design |
| `green_bar` | `concept` | Green Bar Psychology |
| `adoption` | `concept` | Practical Adoption |
| `low_barrier` | `benefit` | Lower Barrier to Entry |
| `feedback` | `benefit` | Immediate Feedback Loop |
| `progress` | `benefit` | Measurable Progress |
| `tdd_difficulty` | `challenge` | TDD Adoption Difficulty |
| `red_bar` | `challenge` | Red Bar Frustration |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `pdd` | `contrasts_with` | `tdd` |
| `pdd` | `addresses` | `tdd_difficulty` |
| `pdd` | `addresses` | `red_bar` |
| `pdd` | `includes` | `passing_tests` |
| `pdd` | `includes` | `iterative` |
| `pdd` | `includes` | `coverage` |
| `passing_tests` | `produces` | `low_barrier` |
| `passing_tests` | `produces` | `feedback` |
| `iterative` | `produces` | `progress` |
| `passing_tests` | `requires` | `green_bar` |
| `pdd` | `requires` | `adoption` |

</details>
