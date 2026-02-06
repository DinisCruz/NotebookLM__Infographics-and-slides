# Semantic Testing DSL: Unit Tests for Content

**Document:** issues-fs__semantic-testing-dsl  
**Version:** v1.0  
**Date:** 2026-02-05  
**Status:** Draft (Exploratory)  
**Depends On:** issues-fs__semantic-graph-code-representation v1.0, issues-fs__semantic-text-architecture v1.0  

---

## Executive Summary

This document defines a Domain-Specific Language (DSL) for writing tests against semantic content. The DSL compiles to Python/JavaScript, where tests execute against the code representation of semantic graphs (not against text). The goal is to enable "unit tests for content" — executable assertions about meaning, structure, evidence, and relationships.

This is explicitly **not Gherkin**. Gherkin was English pretending to be code, with a brittle translation layer underneath. This DSL is a precise semantic language that compiles to real typed code. The tests are robust because they test graph structure, not text patterns.

**Note:** This document is exploratory. Several sections are marked `[NEEDS EXPLORATION]` where design decisions are not yet final.

---

## Part 1: The Vision

### What We're Building

A language for expressing semantic assertions about documents:

```
spec "Security Risk Assessment"

  test "Every risk has a remediation"
    for each Risk in document
      assert Risk.remediation exists
      assert Risk.remediation.control_framework is one of ["NIST", "ISO", "CIS"]

  test "Claims have evidence"
    for each Claim in document where Claim.type is "fact"
      assert Claim.evidence exists
      assert Claim.evidence.source is not empty

  test "No unsupported opinions stated as facts"
    for each Claim in document where Claim.type is "opinion"
      assert Claim.stated_as is not "fact"
      assert Claim.attribution exists
```

This compiles to Python:

```python
class SecurityRiskAssessmentSpec(SemanticTestSuite):
    
    def test_every_risk_has_remediation(self):
        for risk in self.document.graph.risks:
            self.assertIsNotNone(risk.remediation, 
                f"Risk '{risk.label}' has no remediation")
            self.assertIn(risk.remediation.control_framework, 
                ["NIST", "ISO", "CIS"],
                f"Remediation '{risk.remediation.label}' uses unknown framework")
    
    def test_claims_have_evidence(self):
        for claim in self.document.graph.claims:
            if claim.type == "fact":
                self.assertIsNotNone(claim.evidence,
                    f"Fact claim '{claim.label}' has no evidence")
                self.assertTrue(claim.evidence.source,
                    f"Evidence for '{claim.label}' has no source")
    
    def test_no_unsupported_opinions_stated_as_facts(self):
        for claim in self.document.graph.claims:
            if claim.type == "opinion":
                self.assertNotEqual(claim.stated_as, "fact",
                    f"Opinion '{claim.label}' is incorrectly stated as fact")
                self.assertIsNotNone(claim.attribution,
                    f"Opinion '{claim.label}' has no attribution")
```

### Why a DSL?

**Not Python/JS directly** because:
- Test intent gets lost in boilerplate
- Non-developers should be able to read and write content specs
- The DSL enforces patterns that prevent brittle tests

**Not Gherkin** because:
- Gherkin's "Given/When/Then" doesn't map to semantic assertions
- Gherkin hides the implementation; we want transparent compilation
- Gherkin tests UI/behavior; we test graph structure

**A purpose-built DSL** because:
- Syntax optimized for semantic assertions
- Compiles to real code (Python/JS) that runs against typed representations
- Readable by content authors, writable by developers, executable by CI

### The Key Insight: Testing Structure, Not Text

The DSL tests the code representation (from Document 2), not the source text. This means:

| Text Change | Graph Change | Test Result |
|-------------|--------------|-------------|
| "remediation" → "mitigation" | None (same node type) | Pass |
| "must" → "should" | Requirement.modal changed | Fail (if modal matters) |
| Added a paragraph | New nodes/edges added | Depends on assertions |
| Deleted a citation | Evidence edge removed | Fail (if evidence required) |

The test is insulated from cosmetic text changes. It only fails when the semantic structure changes in ways that violate the spec.

---

## Part 2: DSL Syntax

### Spec Declaration

Every test file starts with a spec declaration:

```
spec "Document Type Name"
  ontology: risk-description-v1     # Which O&T applies
  version: 1.0                      # Spec version
  author: security-team             # Who owns this spec
```

### Test Blocks

Tests are named blocks containing assertions:

```
test "Descriptive test name"
  # assertions go here
```

### Iteration

Loop over nodes of a type:

```
for each Risk in document
  # assertions about each risk

for each Claim in document where Claim.type is "fact"
  # filtered iteration

for each Vulnerability in Risk.vulnerabilities
  # nested iteration
```

### Assertions

Core assertion patterns:

```
# Existence
assert Risk.remediation exists
assert Risk.remediation does not exist

# Equality
assert Risk.severity is "critical"
assert Risk.severity is not "low"

# Membership
assert Risk.severity is one of ["high", "critical"]
assert Risk.category is not one of ["deprecated", "archived"]

# Emptiness
assert Risk.vulnerabilities is not empty
assert Risk.notes is empty

# Numeric
assert Risk.control_coverage >= 0.8
assert count(Risk.vulnerabilities) > 0
assert count(Risk.vulnerabilities) <= 10

# String patterns [NEEDS EXPLORATION: regex syntax]
assert Risk.label matches /^RISK-\d+:/
assert Risk.description contains "impact"

# Relationships
assert Risk links to Lexicon:anchor__risk
assert Risk.remediation links to any NIST_Control
assert path exists from Risk to Asset through Vulnerability
```

### Conditional Logic

```
if Risk.severity is "critical"
  assert Risk.remediation exists
  assert Risk.remediation.timeline is "immediate"
else if Risk.severity is "high"
  assert Risk.remediation exists
else
  assert Risk.remediation exists or Risk.accepted_risk is true
```

### Variables and References

```
let unmitigated_risks = filter(document.risks, r => r.remediation does not exist)
assert count(unmitigated_risks) == 0

let critical_without_timeline = filter(document.risks, r =>
  r.severity is "critical" and r.remediation.timeline does not exist
)
assert count(critical_without_timeline) == 0 
  with message "Critical risks must have remediation timelines"
```

### Custom Messages

```
assert Risk.remediation exists
  with message "Risk '{Risk.label}' has no remediation"

assert Claim.evidence exists
  with message "The claim '{Claim.content}' is stated as fact but has no supporting evidence"
```

### Grouping and Organization

```
spec "Security Risk Assessment"

  group "Structural Completeness"
    
    test "Every risk has required fields"
      for each Risk in document
        assert Risk.vulnerability exists
        assert Risk.threat_actor exists
        assert Risk.impact exists

    test "Every risk has remediation"
      for each Risk in document
        assert Risk.remediation exists

  group "Evidence and Attribution"
    
    test "Facts have evidence"
      for each Claim in document where Claim.type is "fact"
        assert Claim.evidence exists
    
    test "Opinions have attribution"
      for each Claim in document where Claim.type is "opinion"
        assert Claim.attribution exists

  group "External Links"
    
    test "Vulnerabilities link to CWE"
      for each Vulnerability in document
        assert Vulnerability links to CWE
    
    test "Remediations link to control framework"
      for each Remediation in document
        assert Remediation.control_framework exists
```

---

## Part 3: Compilation to Python

### Basic Compilation

DSL:
```
test "Every risk has a remediation"
  for each Risk in document
    assert Risk.remediation exists
```

Compiles to:
```python
def test_every_risk_has_remediation(self):
    for risk in self.document.graph.risks:
        self.assertIsNotNone(
            risk.remediation,
            f"Risk '{risk.label}' has no remediation"
        )
```

### Filtered Iteration

DSL:
```
for each Claim in document where Claim.type is "fact"
  assert Claim.evidence exists
```

Compiles to:
```python
for claim in self.document.graph.claims:
    if claim.type == "fact":
        self.assertIsNotNone(
            claim.evidence,
            f"Fact claim '{claim.label}' has no evidence"
        )
```

### Complex Assertions

DSL:
```
assert path exists from Risk to Asset through Vulnerability
```

Compiles to:
```python
path = risk.path_to(asset, through=[Vulnerability])
self.assertIsNotNone(
    path,
    f"No path from Risk '{risk.label}' to Asset '{asset.label}' through Vulnerability"
)
```

### Conditional Logic

DSL:
```
if Risk.severity is "critical"
  assert Risk.remediation.timeline is "immediate"
else
  assert Risk.remediation.timeline exists
```

Compiles to:
```python
if risk.severity == "critical":
    self.assertEqual(
        risk.remediation.timeline, 
        "immediate",
        f"Critical risk '{risk.label}' should have immediate timeline"
    )
else:
    self.assertIsNotNone(
        risk.remediation.timeline,
        f"Risk '{risk.label}' should have a remediation timeline"
    )
```

### Full Test Suite Compilation

DSL:
```
spec "Risk Assessment"
  ontology: risk-description-v1

  test "Risks are complete"
    for each Risk in document
      assert Risk.vulnerability exists
      assert Risk.remediation exists

  test "High severity risks have timelines"
    for each Risk in document where Risk.severity is one of ["high", "critical"]
      assert Risk.remediation.timeline exists
```

Compiles to:
```python
# Auto-generated from: risk_assessment.semantic
# Ontology: risk-description-v1
# Do not edit manually

from semantic_testing import SemanticTestSuite
from risk_types import Risk, Vulnerability, Remediation  # From O&T compilation

class RiskAssessmentSpec(SemanticTestSuite):
    """Semantic test suite for Risk Assessment documents."""
    
    ontology = "risk-description-v1"
    
    def test_risks_are_complete(self):
        """Risks are complete"""
        for risk in self.document.graph.risks:
            self.assertIsNotNone(
                risk.vulnerability,
                f"Risk '{risk.label}' has no vulnerability"
            )
            self.assertIsNotNone(
                risk.remediation,
                f"Risk '{risk.label}' has no remediation"
            )
    
    def test_high_severity_risks_have_timelines(self):
        """High severity risks have timelines"""
        for risk in self.document.graph.risks:
            if risk.severity in ["high", "critical"]:
                self.assertIsNotNone(
                    risk.remediation.timeline,
                    f"High/critical risk '{risk.label}' has no remediation timeline"
                )
```

---

## Part 4: Compilation to JavaScript/TypeScript

### TypeScript Output

DSL:
```
test "Every risk has a remediation"
  for each Risk in document
    assert Risk.remediation exists
```

Compiles to:
```typescript
test("Every risk has a remediation", () => {
    for (const risk of document.graph.risks) {
        expect(risk.remediation).toBeDefined();
    }
});
```

### Full Suite in TypeScript

```typescript
// Auto-generated from: risk_assessment.semantic
// Ontology: risk-description-v1

import { SemanticTestSuite } from 'semantic-testing';
import { Risk, Vulnerability, Remediation } from './risk_types';

describe("Risk Assessment", () => {
    let document: SemanticDocument;
    
    beforeAll(async () => {
        document = await loadSemanticDocument("risk-assessment.md", {
            ontology: "risk-description-v1"
        });
    });
    
    test("Risks are complete", () => {
        for (const risk of document.graph.risks) {
            expect(risk.vulnerability).toBeDefined();
            expect(risk.remediation).toBeDefined();
        }
    });
    
    test("High severity risks have timelines", () => {
        for (const risk of document.graph.risks) {
            if (["high", "critical"].includes(risk.severity)) {
                expect(risk.remediation?.timeline).toBeDefined();
            }
        }
    });
});
```

---

## Part 5: Test Patterns

### Pattern 1: Structural Completeness

Verify that all required elements exist:

```
spec "Bug Report Completeness"

  test "Bug has required sections"
    assert document.symptoms exists
    assert document.steps_to_reproduce exists
    assert document.expected_behavior exists
    assert document.actual_behavior exists
  
  test "Steps to reproduce are actionable"
    for each Step in document.steps_to_reproduce
      assert Step.action exists
      assert Step.action is not empty
```

### Pattern 2: Relationship Validity

Verify that relationships are correctly formed:

```
spec "Risk Relationships"

  test "Vulnerabilities are linked to threats"
    for each Vulnerability in document
      assert count(Vulnerability.exploited_by) > 0
        with message "Vulnerability '{Vulnerability.label}' has no threat actor"
  
  test "Remediations address vulnerabilities"
    for each Remediation in document
      assert count(Remediation.mitigates) > 0
        with message "Remediation '{Remediation.label}' doesn't mitigate any vulnerability"
  
  test "No orphan nodes"
    for each node in document.all_nodes
      assert count(node.edges_in) > 0 or count(node.edges_out) > 0
        with message "Node '{node.label}' is orphaned (no connections)"
```

### Pattern 3: Evidence Chains

Verify that claims are supported:

```
spec "Research Paper Evidence"

  test "Abstract claims are supported in body"
    for each Claim in document.abstract
      assert Claim.supported_by exists
      assert Claim.supported_by.section is not "abstract"
        with message "Abstract claim '{Claim.content}' is self-referential"
  
  test "Conclusions trace to methodology"
    for each Conclusion in document.conclusions
      let evidence_chain = path from Conclusion to document.methodology
      assert evidence_chain exists
        with message "Conclusion '{Conclusion.content}' has no path to methodology"
  
  test "Statistics have sources"
    for each Claim in document where Claim.contains_statistic is true
      assert Claim.citation exists
        with message "Statistical claim '{Claim.content}' has no citation"
```

### Pattern 4: Consistency Checks

Verify internal consistency:

```
spec "Document Consistency"

  test "Title matches content"
    let title_concepts = document.title.concepts
    let body_concepts = document.body.concepts
    
    for each Concept in title_concepts
      assert Concept in body_concepts
        with message "Title mentions '{Concept.label}' but body doesn't discuss it"
  
  test "Issue type matches content"
    if document.issue_type is "bug"
      assert document contains Symptom
      assert document contains Affected_Component
    else if document.issue_type is "feature"
      assert document contains Requested_Capability
      assert document contains User_Benefit
  
  test "Severity matches impact"
    if document.severity is "critical"
      assert document.impact.scope is one of ["system-wide", "data-loss", "security-breach"]
```

### Pattern 5: External Link Validation

Verify links to external references:

```
spec "Security Standards Compliance"

  test "Vulnerabilities link to CWE"
    for each Vulnerability in document
      assert Vulnerability links to CWE
        with message "Vulnerability '{Vulnerability.label}' not linked to CWE"
  
  test "Threat actors link to MITRE ATT&CK"
    for each Threat_Actor in document
      assert Threat_Actor links to MITRE_ATTACK
        with message "Threat actor '{Threat_Actor.label}' not linked to ATT&CK"
  
  test "Remediations link to NIST controls"
    for each Remediation in document
      assert Remediation.nist_control exists
      assert Remediation.nist_control.id matches /^[A-Z]{2}-\d+/
```

### Pattern 6: Cross-Version Regression

Verify that changes don't break expectations:

```
spec "Version Regression"
  compare: previous_version

  test "No claims removed without documentation"
    for each Claim in previous_version
      if Claim not in current_version
        assert document.changelog mentions Claim
          with message "Claim '{Claim.content}' was removed without changelog entry"
  
  test "Requirements not weakened"
    for each Requirement in previous_version
      let current = find Requirement in current_version by Requirement.id
      if current exists
        assert current.modal is not weaker than Requirement.modal
          with message "Requirement '{Requirement.id}' was weakened from '{Requirement.modal}' to '{current.modal}'"
  
  test "No new unsubstantiated claims"
    for each Claim in current_version where Claim not in previous_version
      assert Claim.evidence exists
        with message "New claim '{Claim.content}' has no evidence"
```

---

## Part 6: CLI Integration

### Running Tests

```bash
# Run semantic tests on a document
issues-fs semantic test document.md --spec document_spec.semantic

# Run with specific test
issues-fs semantic test document.md --spec document_spec.semantic --test "Risks are complete"

# Run all specs in a directory
issues-fs semantic test document.md --spec-dir ./specs/

# Output formats
issues-fs semantic test document.md --spec spec.semantic --output json
issues-fs semantic test document.md --spec spec.semantic --output junit
issues-fs semantic test document.md --spec spec.semantic --output markdown
```

### Compiling Specs

```bash
# Compile DSL to Python
issues-fs semantic compile spec.semantic --target python --output spec_test.py

# Compile to TypeScript
issues-fs semantic compile spec.semantic --target typescript --output spec_test.ts

# Validate DSL syntax
issues-fs semantic validate spec.semantic

# Watch mode (recompile on change)
issues-fs semantic compile spec.semantic --watch
```

### CI/CD Integration

```yaml
# GitHub Actions example
name: Semantic Tests

on: [push, pull_request]

jobs:
  semantic-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Install Issues-FS
        run: pip install issues-fs
      
      - name: Run semantic tests
        run: |
          issues-fs semantic test docs/*.md \
            --spec-dir ./specs/ \
            --output junit \
            --output-file test-results.xml
      
      - name: Upload results
        uses: actions/upload-artifact@v2
        with:
          name: test-results
          path: test-results.xml
```

---

## Part 7: Test Lifecycle

### The TDD Flow for Content

1. **Write the spec first** — Define what the content should contain
2. **Create/edit content** — Write the document
3. **Run tests** — Check if content meets spec
4. **Fix failures** — Update content or update spec
5. **Commit together** — Content and spec evolve together

### Example Lifecycle

**Step 1: Write spec**
```
spec "API Documentation"

  test "Every endpoint has description"
    for each Endpoint in document
      assert Endpoint.description exists
      assert length(Endpoint.description) >= 20
  
  test "Every endpoint has example"
    for each Endpoint in document
      assert Endpoint.example exists
      assert Endpoint.example.request exists
      assert Endpoint.example.response exists
```

**Step 2: Run tests (fail)**
```bash
$ issues-fs semantic test api-docs.md --spec api_doc_spec.semantic

FAIL: Every endpoint has description
  - Endpoint 'POST /users' has no description

FAIL: Every endpoint has example
  - Endpoint 'POST /users' has no example
  - Endpoint 'GET /users/{id}' example missing response

2 tests, 2 failed
```

**Step 3: Fix content**
```markdown
### POST /users

Creates a new user account with the specified details.

**Example:**
Request:
```json
{"name": "Alice", "email": "alice@example.com"}
```

Response:
```json
{"id": "123", "name": "Alice", "email": "alice@example.com"}
```
```

**Step 4: Run tests (pass)**
```bash
$ issues-fs semantic test api-docs.md --spec api_doc_spec.semantic

PASS: Every endpoint has description
PASS: Every endpoint has example

2 tests, 2 passed
```

**Step 5: Commit together**
```bash
git add api-docs.md api_doc_spec.semantic
git commit -m "Add POST /users endpoint with tests"
```

---

## Part 8: Advanced Features

### Parameterized Tests

```
spec "Severity-Specific Requirements"

  for severity in ["low", "medium", "high", "critical"]
    test "Risks with {severity} severity have appropriate response time"
      for each Risk in document where Risk.severity is severity
        if severity is "critical"
          assert Risk.response_time <= 24 hours
        else if severity is "high"
          assert Risk.response_time <= 72 hours
        else
          assert Risk.response_time <= 168 hours
```

### Shared Helpers

```
spec "Complex Document"

  helper is_fully_linked(node)
    return node links to Lexicon and count(node.edges_out) > 0

  helper has_evidence_chain(claim)
    return claim.evidence exists and claim.evidence.source exists

  test "All nodes are fully linked"
    for each node in document.all_nodes
      assert is_fully_linked(node)
  
  test "All claims have evidence chains"
    for each Claim in document
      assert has_evidence_chain(Claim)
```

### Importing Shared Specs

```
spec "Security Document"
  import "common/evidence_spec.semantic"
  import "common/linking_spec.semantic"

  test "Security-specific requirement"
    # This spec includes all tests from imported specs
    # plus its own additional tests
    for each Threat_Actor in document
      assert Threat_Actor.sophistication exists
```

### Snapshot Testing [NEEDS EXPLORATION]

```
spec "Document Structure Snapshot"

  test "Structure matches baseline"
    let current_structure = document.graph.structure_hash
    assert current_structure matches snapshot "baseline_structure"
      with message "Document structure has changed from baseline"
  
  test "Claim count stable"
    assert count(document.claims) matches snapshot "claim_count" +/- 10%
```

**[NEEDS EXPLORATION]:** How do snapshots work? Where are baselines stored? How do you update them? How do you handle intentional changes vs regressions?

---

## Part 9: Areas Needing Exploration

### Syntax Design [NEEDS EXPLORATION]

Several syntax questions remain open:

1. **Regex syntax:** Should we use `/pattern/` or `matches("pattern")` or something else?
   
2. **Temporal expressions:** How to express `<= 24 hours`? Parse natural language? Require ISO duration?

3. **Path expressions:** `path from A to B through C` is intuitive but may need more complex patterns (optional nodes, multiple paths, etc.)

4. **Negation:** `assert X does not exist` vs `assert not exists(X)` vs `assertNot X exists`

5. **Collection operations:** How verbose should `filter`, `map`, `any`, `all` be?

### Compilation Targets [NEEDS EXPLORATION]

1. **Python frameworks:** Should we compile to unittest, pytest, or custom framework?

2. **JavaScript frameworks:** Jest, Mocha, Vitest, or custom?

3. **Other languages:** Go, Rust, Java — worth supporting?

### Error Messages [NEEDS EXPLORATION]

1. **Localization:** Should error messages be localizable?

2. **Context:** How much context to include? Full path to failing node? Excerpt of source text?

3. **Suggestions:** Can we suggest fixes? "Claim has no evidence. Did you mean to add a citation?"

### IDE Support [NEEDS EXPLORATION]

1. **Syntax highlighting:** VS Code extension, JetBrains plugin?

2. **Autocomplete:** Can we provide completion for node types, properties, Lexicon anchors?

3. **Inline errors:** Show DSL errors before compilation?

4. **Test running:** Run tests from IDE, show results inline?

### Inheritance and Composition [NEEDS EXPLORATION]

1. **Spec inheritance:** Can one spec extend another?

2. **Mixin patterns:** Reusable test fragments that aren't full specs?

3. **Conditional inclusion:** Include tests only if certain conditions are met?

### Integration with Graph Changes [NEEDS EXPLORATION]

1. **Diff-aware tests:** Tests that only run on changed portions of the graph?

2. **Regression detection:** Auto-generate tests from graph structure?

3. **Test generation:** Can we suggest tests based on O&T constraints?

---

## Part 10: Comparison to Gherkin

### What Gherkin Got Right

- Readable by non-developers
- Structured test organization (Feature/Scenario/Given/When/Then)
- Parameterized scenarios (Scenario Outline)
- Tagging and filtering

### What Gherkin Got Wrong

- **English as code:** "Given I am on the login page" looks like English but requires exact matching. Change a word and the step definition breaks.

- **Hidden implementation:** The step definitions (actual code) are separate from the feature files. You can't understand what a test does without reading both.

- **Behavior, not structure:** Gherkin tests behaviors ("When I click..."). We're testing structure ("Risk has Remediation"). Different paradigm.

- **Brittle glue code:** Step definitions are regex matchers on natural language. Constant maintenance.

### How This DSL Is Different

| Aspect | Gherkin | Semantic DSL |
|--------|---------|--------------|
| Tests what | UI behavior | Graph structure |
| Compilation | Regex to step definitions | Direct to typed code |
| Brittleness | High (text matching) | Low (structure matching) |
| Transparency | Hidden (step defs elsewhere) | Visible (compilation is deterministic) |
| Type safety | None | Full (from O&T) |
| IDE support | Limited | Full (via compiled code) |

### The Key Difference

Gherkin: Test fails because someone wrote "login page" instead of "log in page"

Semantic DSL: Test fails because Risk node has no Remediation edge — regardless of what words were used to describe it

---

## Part 11: Implementation Roadmap

### Phase 1: Core DSL

1. Define grammar for basic assertions (`exists`, `is`, `is one of`)
2. Implement Python code generator
3. Basic iteration (`for each X in document`)
4. Custom error messages
5. CLI for compilation and execution

### Phase 2: Advanced Assertions

1. Relationship assertions (`links to`, `path exists`)
2. Numeric comparisons (`>=`, `count()`)
3. Conditional logic (`if`/`else`)
4. String patterns (regex or simplified matching)

### Phase 3: Test Organization

1. Groups and naming
2. Imports and shared specs
3. Helpers and custom functions
4. Parameterized tests

### Phase 4: Developer Experience

1. VS Code extension (syntax highlighting, completion)
2. Watch mode (recompile on save)
3. Better error messages with suggestions
4. Test result visualization

### Phase 5: CI/CD Integration

1. JUnit/xUnit output format
2. GitHub Actions integration
3. Merge request comments with test results
4. Trend tracking (test pass rate over time)

---

## Decisions Log

| # | Decision | Rationale | Status |
|---|----------|-----------|--------|
| TD1 | **Custom DSL, not pure Python** | DSL enforces patterns, is more readable, prevents brittle tests | Decided |
| TD2 | **Compiles to real code** | Unlike Gherkin, compilation is transparent and type-safe | Decided |
| TD3 | **Tests graph structure, not text** | Structure is stable across wording changes | Decided |
| TD4 | **Python as primary target** | Ecosystem is Python-first; TypeScript secondary | Decided |
| TD5 | **Syntax: `assert X exists`** | Reads naturally, explicit about existence checks | Tentative |
| TD6 | **Syntax: `for each X in Y`** | Clear iteration, familiar pattern | Tentative |
| TD7 | **Groups for organization** | Logical grouping of related tests | Tentative |
| TD8 | **Regex syntax** | TBD — several options | Open |
| TD9 | **Snapshot testing** | TBD — unclear semantics | Open |
| TD10 | **IDE support approach** | TBD — VS Code first, then others | Open |

---

## References

- [Code Representations for Semantic Graphs](./v0_4_0__issues-fs__semantic-graph-code-representation.md) — What tests run against
- [Semantic Text Architecture](./v0_4_0__issues-fs__semantic-text-architecture.md) — How text becomes graphs
- [Thinking in Graphs](./v0_4_0__issues-fs__thinking-in-graphs.md) — Foundational philosophy
- [Gherkin Reference](https://cucumber.io/docs/gherkin/reference/) — What we're NOT doing
- [CoffeeScript](https://coffeescript.org/) — Inspiration for clean syntax
- [Groovy](https://groovy-lang.org/) — Inspiration for DSL capabilities

---

*Semantic Testing DSL v1.0 (Exploratory Draft)*  
*Date: 2026-02-05*
