# Phased Development Methodology

## A Guide to Independent, Composable Development Phases

---

## Overview

This document captures a development methodology for building complex systems through independent, composable phases. Each phase produces testable artifacts that can be validated in isolation before integration.

**Core Principle**: Build systems as a pipeline of independent transformations, where each phase's output becomes the next phase's input, connected via serializable data formats.

---

## The Phase Structure

### Each Phase Contains

```
phase_X__descriptive_name/
├── PHASE_X__brief.md           # Requirements and design decisions
├── Implementation files         # The actual code
├── test_*.py                   # Tests for this phase
├── fixture__*.json             # Test data (often from previous phase)
├── PHASE_X__debrief.md         # Learnings and completion summary
└── *__Generator.py             # Tools to create fixtures for next phase
```

### The Phase Lifecycle

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PHASE LIFECYCLE                              │
└─────────────────────────────────────────────────────────────────────┘

1. BRIEF PHASE
   ├── Define inputs and outputs
   ├── Specify interfaces
   ├── Document design decisions
   └── Create: PHASE_X__brief.md

2. IMPLEMENT PHASE
   ├── Write core implementation
   ├── Use subclass pattern when extending existing code
   ├── Create helper utilities
   └── Create: Implementation files

3. TEST PHASE
   ├── Load fixtures from previous phase
   ├── Validate transformations
   ├── Test edge cases
   └── Create: test_*.py files

4. FIXTURE GENERATION
   ├── Create generator for next phase
   ├── Serialize outputs as JSON
   ├── Enable deterministic IDs for reproducibility
   └── Create: *__Generator.py, fixture__*.json

5. DEBRIEF PHASE
   ├── Document what was built
   ├── Capture bugs fixed and learnings
   ├── Record architectural decisions
   └── Create: PHASE_X__debrief.md
```

---

## Key Patterns

### 1. Phase Independence via JSON Fixtures

**Problem**: Phases depend on each other's classes, creating import chains and tight coupling.

**Solution**: Serialize phase outputs to JSON. Next phase loads JSON, not classes.

```
Phase A                          Phase B
┌─────────────────┐             ┌─────────────────┐
│ Process         │             │ Load JSON       │
│ Transform       │ ──JSON──►   │ Process         │
│ Serialize       │             │ Transform       │
└─────────────────┘             └─────────────────┘

# Phase A generates
fixture__example.json

# Phase B loads
fixture = load_fixture('example')
data = fixture['output_data']
```

**Benefits**:
- Test Phase B without Phase A classes installed
- Clear phase boundaries
- Reproducible test data
- Easy debugging (JSON is human-readable)

### 2. The Subclass Pattern

**Problem**: Need to add functionality without modifying production code.

**Solution**: Create subclass that overrides only what's needed.

```python
# Base class (UNCHANGED)
class Html_Use_Case__3:
    def transform(self, data):
        self.step_1()
        self.step_2()
        self.step_3()
        self.step_4()
    
    def step_1(self): ...
    def step_2(self): ...
    def step_3(self): ...
    def step_4(self): ...

# Subclass (NEW - adds tracking)
class Html_Use_Case__3__Source_Tracking(Html_Use_Case__3):
    def __init__(self):
        super().__init__()
        self.tracking_data = {}
    
    def step_1(self):          # Override with tracking
        # Track before
        result = super().step_1()
        # Track after
        return result
    
    # step_2, step_4 inherited unchanged
```

**Benefits**:
- Zero changes to production code
- Easy rollback (delete subclass files)
- Can use base class when feature not needed
- Clear separation of concerns

### 3. Deterministic IDs for Testing

**Problem**: Random UUIDs make tests non-reproducible and hard to debug.

**Solution**: Use deterministic ID generation in test fixtures.

```python
# Without deterministic IDs
node_ids = {'a7f3b2c1-...', 'e9d4f5a6-...', ...}  # Changes every run

# With deterministic IDs
with graph_deterministic_ids():
    fixture = generate_fixture(data)
# node_ids = {'f0000001', 'f0000002', 'f0000003', ...}  # Same every run
```

**Benefits**:
- Tests are reproducible
- Easy to write assertions on specific IDs
- Debugging is straightforward
- Can track ID relationships across phases
- Enables hardcoded expected values in tests

### 4. Fixture Generator Pattern

Each phase creates a generator for the next phase:

```python
# Phase_B__Test_Data_Generator.py

HTML_SAMPLES = {
    'simple': '<html>...</html>',
    'complex': '<html>...</html>',
}

def generate_fixture(html: str, name: str) -> dict:
    """Generate fixture for Phase C from HTML."""
    # Phase A processing
    phase_a_output = PhaseA(html).process()
    
    # Phase B processing  
    phase_b_output = PhaseB(phase_a_output).process()
    
    return {
        'name': name,
        'input': html,
        'phase_a_output': phase_a_output,
        'phase_b_output': phase_b_output.to_json()  # Serialize!
    }

def generate_all_fixtures(output_dir: str):
    """Generate all fixtures for next phase."""
    for name, html in HTML_SAMPLES.items():
        with deterministic_ids():
            fixture = generate_fixture(html, name)
        
        path = f'{output_dir}/fixture__{name}.json'
        with open(path, 'w') as f:
            json.dump(fixture, f, indent=2)
```

### 5. Fixture Loader Pattern

Each phase has a loader for its fixtures:

```python
# Phase_C__Test_Fixtures.py

FIXTURES_DIR = Path(__file__).parent
FIXTURE_NAMES = ['simple', 'complex', 'edge_case']

def _load_all_fixtures() -> dict:
    fixtures = {}
    for name in FIXTURE_NAMES:
        path = FIXTURES_DIR / f'fixture__{name}.json'
        if path.exists():
            with open(path) as f:
                fixtures[name] = json.load(f)
    return fixtures

FIXTURES = _load_all_fixtures()

def get_fixture(name: str) -> dict:
    return FIXTURES.get(name)

def list_fixtures() -> list:
    return list(FIXTURES.keys())

def print_fixtures_status():
    """Diagnostic: show which fixtures are loaded."""
    ...
```

### 6. Graceful Test Skipping

Tests skip gracefully when fixtures aren't available:

```python
class test_Phase_C(TestCase):
    
    def setUp(self):
        if len(FIXTURES) == 0:
            self.skipTest("No fixtures loaded - run Phase B generator first")
    
    def test_something(self):
        fixture = get_fixture('simple')
        # ... test code
```

---

## Performance Benchmarking Methodology

### The "Follow the Rabbit Hole" Pattern

When investigating performance bottlenecks, use a systematic drilling approach: start at the highest level of the pipeline, identify the dominant cost, then drill into that function. Repeat this process at each level until you reach the root cause.

The pattern produces a "rabbit hole path" that documents exactly where time is spent. For example: `Pipeline → convert_from_dict (95%) → _process_body (99%) → _process_body_children (100%) → _process_body__element (100%) → @type_safe decorator (77%)`. Each level narrows the focus until the root cause is identified. A separate detailed methodology document covers this technique in depth.

### Benchmark Infrastructure

#### Standard Benchmark Structure

```python
class test_perf__Phase_X__Benchmark__Name(TestCase):
    
    @classmethod
    def setUpClass(cls):
        # Pre-generate ALL test data once
        with graph_deterministic_ids():
            cls.html_100  = cls.generator.generate__100()
            cls.html_dict = Html__To__Html_Dict(html=cls.html_100).convert()
        
        # Pre-extract data needed by benchmarks
        cls.nodes = cls.body_dict.get('nodes', [])
    
    def benchmarks(self, timing: Perf_Benchmark__Timing):
        # Register all benchmark stages here
        timing.benchmark('A_01__stage_name', stage_function)
        timing.benchmark('A_02__next_stage', next_function)
    
    @type_safe_fast_create
    def test__benchmark_name(self):
        builder = Perf_Report__Builder(...)
        report = builder.run(self.benchmarks)
        self.storage.save(report, key=REPORT_KEY, formats=['txt'])
```

#### Naming Convention for Benchmarks

```
Section Letter + Number + Descriptive Name

A_01__setup_phase           # Section A, first benchmark
A_02__extract_data          # Section A, second benchmark
B_01__process_single        # Section B, first benchmark
B_02__process_all           # Section B, second benchmark
```

### Object Reuse Strategy

**Problem**: Creating fresh objects inside benchmark lambdas measures setup overhead, not target code.

**Solution**: Create objects ONCE before benchmark registration, outside the lambda.

```python
# WRONG - measures document creation on every iteration
def stage_A_05():
    document  = create_fresh_document()  # Inside lambda = measured!
    converter = Converter()
    result    = converter.process(document)

# CORRECT - measures only processing
document_A_05  = create_fresh_document()   # Outside lambda
converter_A_05 = Converter()

def stage_A_05():
    result = converter_A_05.process(document_A_05)  # Only this is measured
```

**Exception**: When you specifically need to measure object creation, or when the benchmark requires a fresh object each iteration (e.g., mutating operations).

### Post-Benchmark Score Adjustment

**Problem**: Some operations have dependencies - you can't call B without first calling A. But measuring them together gives cumulative cost, not individual cost.

**Solution**: Measure cumulatively, then adjust scores programmatically.

```python
# B_02 measures create_in_graph only
timing.benchmark('B_02__create_in_graph_all', stage_B_02)
benchmark_b_02__result = timing.results.get('B_02__create_in_graph_all')

# B_03 must call create_in_graph THEN register_attrs (dependency!)
def stage_B_03__register_attrs_all():
    for node in nodes:
        converter.create_in_graph(...)      # Must call this first
        converter.register_attrs(...)       # Then this

timing.benchmark('B_03__register_attrs_all', stage_B_03)

# Adjust B_03 to get isolated register_attrs cost
benchmark_b_03__result = timing.results.get('B_03__register_attrs_all')
benchmark_b_03__result.final_score -= benchmark_b_02__result.final_score
benchmark_b_03__result.raw_score   -= benchmark_b_02__result.raw_score
```

**Why this works**:
- `timing.results` dictionary gives access to benchmark result objects
- Score properties (`final_score`, `raw_score`) are mutable
- Verification: Sum of adjusted parts should equal full measurement

**Key principle**: Benchmark code should mirror production code as closely as possible. Run the real flow, adjust mathematically afterward.

### Scaling Validation

**Purpose**: Confirm that per-element cost is consistent and operations scale linearly.

```python
# Generate test data at multiple sizes
cls.html_100   = generator.generate__100()
cls.html_200   = generator.generate__200()
cls.html_500   = generator.generate__500()
cls.html_1000  = generator.generate__1_000()
```

**Expected results table**:

| Size | Total Time | Per-element | Scaling |
|------|------------|-------------|---------|
| 100 | 3.20ms | 32µs | baseline |
| 200 | 6.40ms | 32µs | 2x ✓ |
| 500 | 16.00ms | 32µs | 5x ✓ |
| 1000 | 32.00ms | 32µs | 10x ✓ |

**Non-linear scaling indicates**: Algorithm issues, cache effects, or measurement problems.

### Layer Bypassing

**Purpose**: Isolate which abstraction layer is causing overhead.

```python
# High level (full stack) - 137µs
document.body_graph.create_element(...)

# Mid level (edit layer) - 30µs  
mgraph.edit().new_node(...)

# Low level (model only) - 10µs
model.new_node(...)

# Overhead calculation:
# High - Mid = 107µs (Html_MGraph layer)
# Mid - Low = 20µs (edit/index layer)
```

### Elimination Testing

**Purpose**: Prove causality by removing suspected bottleneck.

```python
def stage_A_05__process_all_elements():
    for node in nodes:
        # Comment out suspected bottleneck
        # converter._process_body__element(...)
        pass

# Results:
# WITH _process_body__element:     45.40ms
# WITHOUT _process_body__element:  0.03ms (1,500x faster!)
# Conclusion: 100% of cost is in _process_body__element
```

### Cumulative vs Independent Measurement

**Cumulative**: Each stage includes all previous work.

```
A_01: step1                    → 10µs
A_02: step1 + step2            → 50µs  (step2 = 40µs)
A_03: step1 + step2 + step3    → 80µs  (step3 = 30µs)
```

**Independent**: Each stage measured in isolation.

```
A_01: step1 only               → 10µs
A_02: step2 only               → 40µs
A_03: step3 only               → 30µs
```

**When to use each**:
- **Cumulative**: When operations must run in sequence, shows realistic pipeline cost
- **Independent**: When you need to isolate individual costs, requires score adjustment for dependencies

### Performance Context Managers

**`type_safe_fast_create`**: Decorator that enables fast object creation mode.

```python
@type_safe_fast_create
def test__benchmark(self):
    # All Type_Safe object creation in this method uses fast path
    ...
```

**`graph_deterministic_ids()`**: Context manager for reproducible IDs.

```python
with graph_deterministic_ids():
    # All node/edge IDs are deterministic: f0000001, f0000002, ...
    fixture = generate_test_data()
```

### Benchmark Result Storage

Store results to files for historical comparison and regression detection:

```python
self.storage = Perf_Report__Storage__File_System(storage_path=cls.storage_path)
self.storage.save(report, key=REPORT_KEY, formats=['txt'])
```

Benefits:
- Git can track performance changes via diffs
- Easy to detect regressions
- Historical record of optimizations

---

## The Brief Document

### Structure

```markdown
# Phase X: [Descriptive Name]

**Status**: 🔄 In Progress | ✅ Complete
**Depends On**: Phase A ✅, Phase B ✅

---

## Objective

> One sentence describing what this phase achieves.

---

## The Problem

What problem does this phase solve? Include:
- Current state
- Desired state
- Why existing approaches don't work

---

## Implementation Approach

### Architecture

```
Diagram showing components and data flow
```

### Key Design Decisions

1. **Decision 1**: Rationale
2. **Decision 2**: Rationale

---

## Interfaces

### Input
- What this phase receives
- Format and structure

### Output
- What this phase produces
- Format and structure

---

## Files

| File | Purpose |
|------|---------|
| file1.py | Description |
| file2.py | Description |

---

## Test Plan

| Test | Description |
|------|-------------|
| test_1 | What it validates |
| test_2 | What it validates |
```

---

## The Debrief Document

### Structure

```markdown
# Phase X Debrief: [Descriptive Name]

**Date**: [Completion date]
**Status**: ✅ Complete
**Tests**: X/Y passing

---

## Executive Summary

Brief description of what was built and the key achievement.

---

## Architecture Decisions

Document the decisions made and why.

---

## Files Created

| File | Purpose |
|------|---------|
| ... | ... |

---

## Key Learnings & Bugs Fixed

### Bug 1: [Name]

**Problem**: What went wrong
**Symptom**: How it manifested
**Root Cause**: Why it happened
**Fix**: How it was resolved

---

## Test Coverage

Summary of tests and what they validate.

---

## Next Steps

What comes after this phase.
```

---

## JSON Serialization Guidelines

### DO serialize with full type information

```python
# Good - preserves type info for deserialization
data = obj.json()  # Full format

# Bad - loses type info
data = obj.to_json()  # Compressed format
```

### DO use standard reconstruction methods

```python
# Good
obj = MyClass.from_json(json_data)

# Bad - manual reconstruction
obj = MyClass()
for key, value in json_data['nodes'].items():
    obj.add_node(...)  # Fragile, may miss edge cases
```

### DO validate JSON format in tests

```python
def test_json_roundtrip(self):
    original = create_object()
    json_data = original.json()
    restored = MyClass.from_json(json_data)
    
    assert restored == original  # or equivalent validation
```

---

## Test Organization

### Unit Tests (per file)

```python
# test_MyClass.py
class test_MyClass(TestCase):
    
    def test_method_1__basic(self):
        ...
    
    def test_method_1__edge_case(self):
        ...
    
    def test_method_2__basic(self):
        ...
```

### Integration Tests (per phase)

```python
# test_Phase_C__Full_Pipeline_Integration.py
class test_Phase_C__Full_Pipeline_Integration(TestCase):
    
    def setUp(self):
        # Skip if fixtures not available
        if len(FIXTURES) == 0:
            self.skipTest("...")
    
    def test_1__first_stage(self):
        """Validate input processing."""
        ...
    
    def test_2__transformation(self):
        """Validate core transformation."""
        ...
    
    def test_3__output(self):
        """Validate output format."""
        ...
    
    def test_4__end_to_end(self):
        """Full pipeline validation."""
        ...
```

### Performance Benchmark Tests

```python
# test_perf__Phase_X__Benchmark__Name.py
class test_perf__Phase_X__Benchmark__Name(TestCase):
    
    @classmethod
    def setUpClass(cls):
        # Heavy setup done once for all benchmarks
        ...
    
    def benchmarks(self, timing):
        # Register benchmark stages
        ...
    
    @type_safe_fast_create
    def test__benchmark_name(self):
        # Run and store benchmark
        ...
```

---

## Common Pitfalls

### 1. Importing Previous Phase Classes

**Wrong**:
```python
from Phase_A import PhaseAProcessor  # Creates dependency!
from Phase_B import PhaseBConverter
```

**Right**:
```python
from Phase_C__Test_Fixtures import get_fixture  # Load JSON instead
```

### 2. Non-Deterministic Test Data

**Wrong**:
```python
def test_something(self):
    data = generate_random_data()  # Different every run
    assert process(data) == expected  # May fail randomly
```

**Right**:
```python
def test_something(self):
    fixture = get_fixture('known_case')  # Same every run
    assert process(fixture) == expected
```

### 3. Modifying Core Classes

**Wrong**:
```python
# In production code
class CoreProcessor:
    def process(self):
        # Added tracking here for Phase C
        self.track()  # Pollutes core with phase-specific code
```

**Right**:
```python
# In Phase C
class CoreProcessor__WithTracking(CoreProcessor):
    def process(self):
        self.track()
        return super().process()
```

### 4. Compressed JSON for Serialization

**Wrong**:
```python
json_data = obj.to_json()  # Compressed, loses type info
restored = MyClass()
restored.load(json_data)  # May fail or lose data
```

**Right**:
```python
json_data = obj.json()  # Full format
restored = MyClass.from_json(json_data)  # Proper reconstruction
```

### 5. Substring Assertions

**Wrong**:
```python
assert '<b' not in html  # Catches <body> too!
```

**Right**:
```python
assert '<b>' not in html  # Specific tag
# Or use regex for more control
```

### 6. Measuring Setup Inside Benchmark Lambda

**Wrong**:
```python
def benchmark_stage():
    document = create_document()  # This is measured too!
    result = process(document)
```

**Right**:
```python
document = create_document()  # Outside - not measured

def benchmark_stage():
    result = process(document)  # Only this is measured
```

### 7. Forgetting to Verify Benchmark Math

**Wrong**:
```python
# Adjusted B_03 without verification
benchmark_b_03.final_score -= benchmark_b_02.final_score
# Hope it's correct...
```

**Right**:
```python
# Adjusted B_03 with verification
benchmark_b_03.final_score -= benchmark_b_02.final_score

# Verify: B_01 + B_02 + B_03(adjusted) + recursive ≈ B_04
total = b_01 + b_02 + b_03_adjusted + recursive
assert abs(total - b_04) < b_04 * 0.05  # Within 5%
```

### 8. Not Using Scaling Validation

**Wrong**:
```python
# Only tested at one size
cls.html = generator.generate__100()
# Assumed it scales linearly...
```

**Right**:
```python
# Test at multiple sizes to confirm scaling
cls.html_100  = generator.generate__100()
cls.html_500  = generator.generate__500()
cls.html_1000 = generator.generate__1_000()

# Verify: 500 nodes should take ~5x longer than 100 nodes
```

---

## Workflow Summary

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DEVELOPMENT WORKFLOW                              │
└─────────────────────────────────────────────────────────────────────┘

1. PLAN
   └── Create PHASE_X__brief.md with requirements and design

2. GENERATE FIXTURES (if depending on previous phase)
   ├── Run: python Phase_[X-1]__Test_Data_Generator.py
   └── Copy: fixture__*.json to current phase folder

3. IMPLEMENT
   ├── Create implementation files
   ├── Use subclass pattern for extensions
   └── Create helper utilities as needed

4. TEST
   ├── Write unit tests for each file
   ├── Write integration tests for phase
   ├── Write performance benchmarks (if applicable)
   └── Ensure all tests pass

5. CREATE GENERATOR (for next phase)
   ├── Create Phase_X__Test_Data_Generator.py
   ├── Generate fixture__*.json files
   └── Test that fixtures are valid

6. DOCUMENT
   └── Create PHASE_X__debrief.md with learnings

7. REPEAT for next phase
```

---

## Benefits of This Approach

| Benefit | Description |
|---------|-------------|
| **Isolation** | Each phase can be developed and tested independently |
| **Reproducibility** | Deterministic fixtures ensure consistent test results |
| **Maintainability** | Subclass pattern keeps core code clean |
| **Debuggability** | JSON fixtures are human-readable |
| **Rollback** | Easy to remove a phase without affecting others |
| **Parallelization** | Multiple phases can be worked on simultaneously |
| **Documentation** | Briefs and debriefs capture decisions and learnings |
| **Performance Visibility** | Benchmarks provide ongoing regression detection |
| **Root Cause Analysis** | Systematic drilling identifies true bottlenecks |

---

## Checklist: Phase Complete

- [ ] Brief document exists and is current
- [ ] All implementation files created
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Performance benchmarks pass (if applicable)
- [ ] Fixtures generated for next phase
- [ ] Fixtures validated (can be loaded and used)
- [ ] Debrief document captures learnings
- [ ] No imports from previous phase classes (only fixtures)
- [ ] Performance results stored for regression tracking

---

## Checklist: Performance Investigation Complete

- [ ] High-level benchmark identifies bottleneck area
- [ ] Systematic drilling reaches root cause
- [ ] Scaling validation confirms linear behavior
- [ ] Elimination testing proves causality
- [ ] Results stored and git-trackable
- [ ] Debrief documents findings and techniques used
- [ ] Optimization achieves target improvement
- [ ] Regression tests prevent future slowdowns