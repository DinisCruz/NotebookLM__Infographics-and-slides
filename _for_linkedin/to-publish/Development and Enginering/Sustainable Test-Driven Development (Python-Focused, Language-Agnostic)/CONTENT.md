# Sustainable Test-Driven Development (Python-Focused, Language-Agnostic)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This guide presents a refined approach to test-driven development that emphasizes fast feedback loops, keeping tests close to code, and an "always-be-passing" philosophy where the test suite remains green throughout development. Rather than strictly following traditional TDD's "red, green, refactor" cycle, this methodology focuses on writing tests for every code change — whether before or immediately after — yielding 90-100% coverage as a natural byproduct. The approach favors real code over mocks, treats test infrastructure as a first-class concern, and leverages AI to accelerate test creation.

---

## Key Concepts

- **Fast Feedback Loop**: Tests should run in milliseconds (sub-200ms for unit tests), keeping developers in flow by avoiding context switches and enabling frequent validation of code changes.

- **Always-Be-Passing Philosophy**: The main branch never contains deliberately failing tests; code and tests evolve together in small increments so the codebase is always in a valid, deployable state.

- **No Manual Step Left Behind**: Any manual verification is immediately converted into an automated test, ensuring behaviors are continuously checked and preventing regression drift.

- **Prefer Real Code Over Mocks**: Tests run on actual code units working together rather than heavily mocked components, catching integration issues and enabling fearless refactoring.

- **Bug Reproduction Tests**: When a bug is discovered, the first response is writing a test that reproduces it — creating a chain of tests from high-level to unit tests that document how the bug manifests.

- **Test Infrastructure Investment**: Senior developers lead in building robust test frameworks (factories, fixtures, helpers) that make writing tests the path of least resistance.

---

## Core Arguments

1. The faster your feedback loop, the less context switching needed — developers should be able to run tests in milliseconds and immediately know if code changes work.

2. Writing tests for every change (whether before or after) achieves the same outcome as strict TDD while avoiding extended "red" periods that break CI pipelines.

3. Turning every manual verification into an automated test prevents regression, forces testability and good design, and naturally leads to 90%+ code coverage.

4. Over-reliance on mocks creates tests that pass while the real system breaks; preferring integration-style tests validates actual functionality and enables refactoring.

5. High test coverage transforms fear into freedom — developers can refactor mercilessly because the test suite immediately reveals unintended side effects.

6. AI (LLMs) can accelerate testing by generating boilerplate tests, explaining code through tests, and helping maintain test assertions when code changes.

---

## Key Quotes

> "Code without tests is broken by design."

> "The faster your feedback loop, the less need there is for context switching — and the faster you'll be able to ship features and bug fixes."

> "Stop mocking so much stuff… most of the time you can avoid mocking and you'll be better for it."

> "No programming episode is complete without working code and the tests to keep it working."

---

## Tags

`tdd` `test-driven-development` `testing` `python` `pytest` `fast-feedback` `code-coverage` `refactoring` `unit-testing` `integration-testing` `mocking` `ci-cd` `regression-testing` `llm-testing` `software-engineering`

---

## Search Phrases

- "sustainable test-driven development practices"
- "TDD always-be-passing approach"
- "fast feedback loop in testing"
- "why avoid mocking in tests"
- "high code coverage without chasing metrics"
- "writing tests for bug reproduction"
- "test infrastructure best practices"
- "using AI to write unit tests"
- "alternative to traditional TDD red green refactor"
- "how to achieve 100% test coverage naturally"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Methodology / Best Practices Guide |
| **Domain** | Software Development |
| **Sub-domain** | Testing Practices |
| **Author** | Dinis Cruz |
| **Date Created** | 06 Jan 2025 |
| **Source Format** | PDF |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `references` | Martin Fowler - Self-Testing Code |
| `references` | Kent C. Dodds - Write tests, not too many, mostly integration |
| `related_to` | Pass-Driven Development (PDD) |
| `contrasts_with` | Traditional TDD (Red-Green-Refactor) |
| `uses` | pytest (Python testing framework) |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `methodology` | A software development approach or practice |
| `principle` | A guiding concept or rule |
| `practice` | A specific technique or habit |
| `benefit` | A positive outcome from following practices |
| `tool` | A software tool supporting the methodology |
| `anti_pattern` | A practice to avoid |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `includes` | `part_of` | Methodology contains practice |
| `enables` | `enabled_by` | Practice enables benefit |
| `addresses` | `addressed_by` | Practice solves problem |
| `contrasts_with` | `contrasts_with` | Opposing approaches |
| `uses` | `used_by` | Methodology uses tool |

### Taxonomy

```
testing_methodologies
├── test_driven_development
│   ├── traditional_tdd
│   └── sustainable_tdd
│       ├── always_be_passing
│       └── fast_feedback
└── behavior_driven_development

testing_practices
├── unit_testing
├── integration_testing
├── regression_testing
└── bug_reproduction_testing

test_approaches
├── real_code_testing
└── mock_heavy_testing
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `sustainable_tdd` | `methodology` | Sustainable TDD |
| `fast_feedback` | `principle` | Fast Feedback Loop |
| `always_passing` | `principle` | Always-Be-Passing |
| `no_manual_behind` | `practice` | No Manual Step Left Behind |
| `prefer_real_code` | `practice` | Prefer Real Code Over Mocks |
| `bug_reproduction` | `practice` | Bug Reproduction Tests |
| `test_infrastructure` | `practice` | Test Infrastructure Investment |
| `fearless_refactoring` | `benefit` | Fearless Refactoring |
| `high_coverage` | `benefit` | High Coverage as Byproduct |
| `excessive_mocking` | `anti_pattern` | Excessive Mocking |
| `pytest` | `tool` | pytest |
| `ai_test_generation` | `practice` | AI-Assisted Test Generation |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `sustainable_tdd` | `includes` | `fast_feedback` |
| `sustainable_tdd` | `includes` | `always_passing` |
| `sustainable_tdd` | `includes` | `no_manual_behind` |
| `sustainable_tdd` | `includes` | `prefer_real_code` |
| `fast_feedback` | `enables` | `fearless_refactoring` |
| `no_manual_behind` | `enables` | `high_coverage` |
| `prefer_real_code` | `contrasts_with` | `excessive_mocking` |
| `test_infrastructure` | `enables` | `fast_feedback` |
| `sustainable_tdd` | `uses` | `pytest` |
| `ai_test_generation` | `enables` | `high_coverage` |

</details>
