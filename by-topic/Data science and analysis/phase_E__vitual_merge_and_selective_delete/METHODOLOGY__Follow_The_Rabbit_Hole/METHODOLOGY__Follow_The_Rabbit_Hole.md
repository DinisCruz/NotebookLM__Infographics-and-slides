# METHODOLOGY: Follow the Rabbit Hole

## A Systematic Approach to Performance Root Cause Analysis

---

## Overview

"Follow the Rabbit Hole" is a systematic methodology for discovering the root cause of performance issues, particularly those that only manifest at scale. The core principle is simple: **start from the outermost layer where you can observe the problem, drill inward level by level, and leave a test behind at each level**.

This creates a trail of validated checkpoints that documents exactly where time is spent, ultimately leading to the true root cause.

---

## The Core Concept: Follow the Bug with Your Tests

When investigating a performance issue (or any bug), you don't jump to conclusions. Instead:

1. **Start from the outside** - Find the first place where you can reliably observe the problem
2. **Create a test** - Write a benchmark that confirms and quantifies the issue
3. **Identify the dominant cost** - Find which sub-component takes the most time
4. **Drill into that component** - Create a new benchmark focused on its internals
5. **Repeat** - Continue until you reach the root cause
6. **Leave tests behind** - Each level has its own benchmark that remains valid

```
┌─────────────────────────────────────────────────────────────────────┐
│                    THE RABBIT HOLE PATH                              │
└─────────────────────────────────────────────────────────────────────┘

Level 1: Full Pipeline
    │
    ├── Test confirms: "Pipeline takes 76ms for 100 nodes"
    │
    └── Finding: convert_from_dict is 95% of cost
            │
            ├── Test confirms: "convert_from_dict takes 72ms"
            │
            └── Finding: _process_body is 99% of that
                    │
                    ├── Test confirms: "_process_body takes 71ms"
                    │
                    └── Finding: _process_body_children is 100%
                            │
                            ├── Test confirms: "children processing takes 71ms"
                            │
                            └── Finding: _process_body__element is 100%
                                    │
                                    ├── Test confirms: "element processing takes 71ms"
                                    │
                                    └── Finding: @type_safe decorator is 77%
                                            │
                                            └── ROOT CAUSE FOUND!
```

Each level leaves behind a passing test that validates your understanding.

---

## Why This Approach Works

### 1. No Assumptions

You don't guess where the problem is. You measure, confirm, then drill deeper. Every finding is backed by data.

### 2. Cross-Layer Visibility

Performance issues often span multiple abstraction layers. This approach systematically crosses those layers while maintaining understanding at each one.

### 3. Validated Understanding

The tests you leave behind prove your understanding is correct. If `Level N + 1` costs don't add up to `Level N`, you know something is wrong.

### 4. Regression Prevention

Once you find and fix the root cause, all those tests become regression tests. Any future change that reintroduces the problem will be caught.

### 5. Documentation

The rabbit hole path itself becomes documentation. Future developers can see exactly why certain optimizations were made.

---

## The Challenge: State Replication

The hardest part of this methodology is **generating the correct state at each level without polluting the measurement**.

### The Problem

When you're measuring `_process_body_children()`, you need:
- A fully initialized document
- A body_dict with the right structure
- All lazy-initialized objects already initialized
- The exact state that exists when this method is normally called

But you can't just call all the preceding code - that would measure the wrong thing!

### The Solution: State Extraction

Extract the state ONCE, outside the benchmark, then reuse it:

```python
@classmethod
def setUpClass(cls):
    # Run the FULL pipeline once to generate correct state
    with graph_deterministic_ids():
        cls.html_dict = Html__To__Html_Dict(html=cls.html).convert()
    
    # Extract the specific state needed for our target method
    converter = Html__To__Html_MGraph__Document__Node_Id_Reuse()
    _, cls.body_dict = converter._extract_head_body(cls.html_dict)
    cls.nodes = cls.body_dict.get('nodes', [])

def benchmarks(self, timing):
    # Now we can measure JUST the target method with correct state
    body_dict = self.body_dict  # Pre-computed state
    
    def stage_A_05__process_body_children():
        converter._process_body_children(document, node_id, body_dict, 'body')
    
    timing.benchmark('A_05', stage_A_05__process_body_children)
```

### Lazy Initialization Gotcha

In optimized codebases, objects often defer expensive work until first access:

```python
class MyGraph:
    def __init__(self):
        self._index = None  # Lazy!
    
    @property
    def index(self):
        if self._index is None:
            self._index = self._build_expensive_index()  # First access = slow
        return self._index
```

If your target method calls `self.index`, and you create a fresh object for each benchmark iteration, you're measuring index creation, not the actual work!

**Solution**: Create objects outside the benchmark lambda, or explicitly trigger lazy initialization in setup.

---

## The Line-by-Line Performance Review

At the deepest levels, you need to measure individual lines of code within a method. This is the "line-by-line performance review" pattern.

### The Target Method

```python
def _process_body__element(self, document, parent_id, node, position, tag, node_path):
    node_id = self._generate_node_id()                              # Line 1
    self._process_body__create_in_graph(document, parent_id, ...)   # Line 2
    self._process_body__register_attrs(document, node_id, ...)      # Line 3
    self._process_body_children(document, node_id, node, node_path) # Line 4
```

### The Benchmark Structure

```python
# Measure each line cumulatively
timing.benchmark('A_01__generate_node_id',    measure_line_1)
timing.benchmark('A_02__create_in_graph',     measure_lines_1_and_2)
timing.benchmark('A_03__register_attrs',      measure_lines_1_2_and_3)
timing.benchmark('A_04__process_children',    measure_lines_1_2_3_and_4)
timing.benchmark('A_05__full_method',         measure_full_method)

# Calculate incremental costs
Line 1 cost = A_01
Line 2 cost = A_02 - A_01
Line 3 cost = A_03 - A_02
Line 4 cost = A_04 - A_03

# Validation
A_01 + (A_02-A_01) + (A_03-A_02) + (A_04-A_03) ≈ A_05 ✓
```

### Handling Dependencies

Sometimes Line 3 requires Line 2 to have run first (e.g., node must exist before registering attributes). You can't measure Line 3 in isolation.

**Solution**: Post-benchmark score adjustment

```python
timing.benchmark('B_02__create_in_graph_all', stage_b_02)
benchmark_b_02 = timing.results.get('B_02__create_in_graph_all')

# B_03 must include B_02's work due to dependency
timing.benchmark('B_03__register_attrs_all', stage_b_03)

# Adjust to get isolated cost
benchmark_b_03 = timing.results.get('B_03__register_attrs_all')
benchmark_b_03.final_score -= benchmark_b_02.final_score
benchmark_b_03.raw_score   -= benchmark_b_02.raw_score
```

---

## Tooling Evolution During Investigation

A key insight from this methodology: **tooling development is not separate from debugging - it's part of the process**.

### The Productization Cycle

Each investigation improves your capabilities:

```
Investigation 1: Create basic benchmark framework
Investigation 2: Add report storage for comparisons
Investigation 3: Build score adjustment mechanism
Investigation 4: Create HTML test data generator with scaling
Investigation 5: Add deterministic ID support
...

Each "detour" to improve tooling accelerates future work.
```

### Wardley Map Analogy

Think of it as commoditizing your debugging capabilities:

```
Genesis → Custom → Product → Commodity

Investigation 1: Genesis (ad-hoc timing code)
Investigation 2: Custom (reusable benchmark class)
Investigation 3: Product (configurable framework)
Investigation 4: Commodity (standard patterns, generators)
```

Each evolution makes the next investigation faster and more reliable.

---

## A Complete Example: The perf_1 to perf_9 Journey

### perf_1: Pipeline Overview

**Question**: Where in the full pipeline is time spent?

**Method**: Measure converter creation vs conversion

**Finding**: Conversion is 95%+ of cost, not object creation

```
Converter creation: 5%
Conversion:         95%  ← DRILL HERE
```

### perf_2 & perf_3: Fast Create Validation

**Question**: Does `fast_create` mode help?

**Method**: Compare default vs fast_create mode

**Finding**: ~2x improvement, but still slow. Problem is deeper.

```
Default mode:     20ms
fast_create mode: 9ms  ← Better, but why still 9ms?
```

### perf_4: convert_from_dict Breakdown

**Question**: Which stage of conversion is slowest?

**Method**: Measure each stage (setup, extract, head, body)

**Finding**: `_process_body` is 99%+ of conversion cost

```
document_setup:    4ms   (3%)
extract_head_body: 1µs   (0%)
process_head:      700µs (0.5%)
process_body:      47ms  (96%)  ← DRILL HERE
```

### perf_5: _process_body Line-by-Line

**Question**: Which part of _process_body is slow?

**Method**: Measure each operation in the method

**Finding**: `_process_body_children` is 100% of the cost

```
generate_node_id:       1µs  (0%)
register_element:     300µs  (0.6%)
_process_body_children: 46ms (99%+)  ← DRILL HERE
```

### perf_6: _process_body_children Line-by-Line

**Question**: What inside children processing is slow?

**Method**: Measure loop overhead vs element processing

**Finding**: `_process_body__element` is 100% of cost

```
get_nodes:          100ns  (0%)
count_tags:         9µs    (0%)
loop overhead:      20µs   (0%)
process_elements:   46ms   (100%)  ← DRILL HERE
```

**Mystery Solved**: TEXT nodes are processed recursively inside element processing, not at the body level. This explained why A_04 showed no text processing cost.

### perf_7: _process_body__element Line-by-Line

**Question**: Which operation per element is slowest?

**Method**: Measure create_in_graph vs register_attrs

**Finding**: Cost is split ~50/50 between two operations

```
_generate_node_id:     1µs   (0.2%)
_create_in_graph:    206µs   (44%)   ← BOTTLENECK
_register_attrs:     198µs   (43%)   ← BOTTLENECK
Recursive children:   62µs   (13%)
```

### perf_8: Graph Operations Breakdown

**Question**: Which specific graph operation is slowest?

**Method**: Measure create_element, add_child, register_element separately

**Finding**: All three MGraph operations are expensive

```
Node_Path creation:    1µs   (0%)
create_element:      137µs   (34%)  ← MGraph operation
add_child:            78µs   (19%)  ← MGraph operation
register_element:    182µs   (45%)  ← MGraph operation
```

### perf_9: MGraph Internals

**Question**: What inside MGraph operations is slow?

**Method**: Bypass abstraction layers, measure raw operations

**Finding**: `@type_safe` decorator is 77% of the cost!

```
WITH @type_safe:     137µs per create_element
WITHOUT @type_safe:   32µs per create_element
─────────────────────────────────────────────
@type_safe overhead: 105µs (77% of total!)  ← ROOT CAUSE
```

### The Complete Rabbit Hole Path

```
Full Pipeline (76ms for 100 nodes)
  └── convert_from_dict: 95%
        └── _process_body: 99%
              └── _process_body_children: 100%
                    └── _process_body__element: 100%
                          ├── _create_in_graph: 44%
                          │     ├── create_element (34%)
                          │     └── add_child (19%)
                          └── _register_attrs: 42%
                                └── register_element (45%)
                                      └── @type_safe decorator: 77%
                                            └── ROOT CAUSE FOUND!
```

---

## Validation Techniques

### 1. Sum of Parts ≈ Whole

At each level, verify that sub-components add up to the total:

```python
# perf_7 validation
B_01 + B_02 + B_03 + recursive ≈ B_04 ✓
90µs + 20.6ms + 19.8ms + 6.2ms ≈ 46.7ms ✓
```

### 2. Scaling Validation

Test at multiple sizes to confirm linear behavior:

| Size | Time | Per-element | Scaling |
|------|------|-------------|---------|
| 100 | 3.2ms | 32µs | baseline |
| 200 | 6.4ms | 32µs | 2x ✓ |
| 500 | 16.0ms | 32µs | 5x ✓ |
| 1000 | 32.0ms | 32µs | 10x ✓ |

Non-linear scaling indicates algorithm issues or measurement problems.

### 3. Elimination Testing

Comment out suspected bottleneck to prove causality:

```python
# With _process_body__element:    45.40ms
# converter._process_body__element(...)  # COMMENTED OUT

# Without _process_body__element: 0.03ms
# Conclusion: 100% of cost is in that method (1,500x difference)
```

### 4. Before/After Comparison

After fixing, re-run ALL benchmarks to confirm improvement:

```
BEFORE (v1.4.32)          AFTER (v1.4.34)
──────────────────        ──────────────────
10 nodes:   14.30ms       10 nodes:    5.50ms   (2.6x faster)
100 nodes:  76.10ms       100 nodes:  30.10ms   (2.5x faster)
500 nodes: 374.60ms       500 nodes: 144.30ms   (2.6x faster)
```

---

## Patterns That Emerged

### 1. The 99% Pattern

At each level, one component dominates (often 95-100%). Don't optimize the 1%.

### 2. The Hidden Recursion Pattern

Costs can be hidden inside recursive calls. TEXT processing was invisible at the body level because it happened inside element processing.

### 3. The Decorator Overhead Pattern

Language features like decorators, type checking, and validation add up in tight loops. What's negligible for one call becomes dominant for 1000 calls.

### 4. The Abstraction Tax Pattern

Each abstraction layer adds overhead. Measuring at different layers reveals the tax:

```
High level:  137µs (Html_MGraph__Body)
Mid level:    30µs (MGraph__Edit)
Low level:    10µs (Model__MGraph__Graph)
```

### 5. The State Setup Pattern

Correct state generation is often harder than the measurement itself. Invest time in getting this right.

---

## When to Use This Methodology

### Good Fit

- Performance problems that only appear at scale
- Issues spanning multiple abstraction layers
- Complex systems where guessing is unreliable
- When you need to prove root cause definitively
- When regression prevention is important

### Less Suitable

- Simple, obvious performance issues
- Problems you can fix faster than investigate
- One-off debugging (methodology has upfront cost)

---

## Checklist: Follow the Rabbit Hole

- [ ] Identified outermost layer where problem is observable
- [ ] Created benchmark confirming the issue with numbers
- [ ] Identified dominant cost component (the 99%)
- [ ] Created benchmark for next level down
- [ ] Verified sum of parts ≈ whole
- [ ] Repeated until root cause found
- [ ] Validated with elimination testing
- [ ] Confirmed fix with before/after comparison
- [ ] All level benchmarks remain as regression tests
- [ ] Documented the rabbit hole path

---

## Conclusion

"Follow the Rabbit Hole" transforms performance debugging from guesswork into systematic investigation. By leaving tests at each level, you build understanding incrementally and create a permanent record of why things are the way they are.

The methodology requires investment in tooling and test infrastructure, but that investment compounds - each investigation makes the next one faster. After our perf_1 through perf_9 journey, we not only found the root cause (`@type_safe` overhead) but built a benchmark suite that will catch any future regressions.

Most importantly: **we proved the root cause**. Not guessed, not assumed - proved with data at every level.
