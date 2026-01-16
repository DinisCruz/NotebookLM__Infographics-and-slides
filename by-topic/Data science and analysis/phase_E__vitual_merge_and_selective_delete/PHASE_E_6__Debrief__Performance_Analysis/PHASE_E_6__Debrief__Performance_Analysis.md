# Phase E_6: Performance Analysis - Data Debrief

## Executive Summary

Phase E_6 performance testing revealed that **MGraph construction (L2) is the primary bottleneck** at 83% of pipeline time, while caching provides **48x speedup** on cache hits. The architecture scales linearly with document size, and setup overhead accounts for ~25% of operation time but can be amortized through instance reuse.

## Test Results Summary

| Test | Key Finding |
|------|-------------|
| E_6_1 | L2 (dict→MGraph) = 83% of pipeline |
| E_6_2 | MGraph creation = 66% of Phase E0 transform |
| E_6_3 | Cache hit = 1.7ms vs miss = 82ms (48x faster) |
| E_6_4 | Linear scaling: ~370µs per paragraph |
| E_6_5 | Setup overhead = 25%, L3 load = 77% of load time |

---

## E_6_1: Pipeline Stages Breakdown (100 nodes)

### Results
```
L1 (HTML → Dict):     700µs    ( 1.9%)
L2 (Dict → MGraph):   31.1ms   (83.2%)  ← BOTTLENECK
L3 (MGraph → HTML):    5.6ms   (15.0%)
─────────────────────────────────────
Full Pipeline:        38.9ms   (100%)
Sum of Parts:         37.4ms   (96.1%)  ✓ validates
```

### Analysis
- **L2 dominates** with 83% of total time
- HTML parsing (L1) is essentially free at <1ms
- Reconstruction (L3) is reasonable at 5.6ms
- Sum of parts matches full pipeline (good validation)

### Visualization
```
Pipeline Time Distribution (100 nodes):
L1 ██                                                    1.9%
L2 ██████████████████████████████████████████████████   83.2%
L3 █████████                                            15.0%
```

---

## E_6_2: Phase E0 Transform Breakdown (100 nodes)

### Results
```
MGraph Creation:       29.9ms   (66.0%)  ← BOTTLENECK
Text Extraction:        1.7ms   ( 3.8%)
Virtual Merge:          3.6ms   ( 7.9%)
Classification:         2.2ms   ( 4.9%)
Node Deletion:          4.8ms   (10.6%)
HTML Reconstruction:    3.1ms   ( 6.8%)
─────────────────────────────────────────
Full Transform:        50.3ms   (100%)
Sum of Parts:          45.3ms   (90.1%)
```

### Analysis
- MGraph creation remains the dominant cost (66%)
- All other operations are relatively cheap (<5ms each)
- ~5ms gap suggests additional orchestration overhead
- Transform adds ~11ms over raw pipeline (50.3ms vs 38.9ms)

---

## E_6_3: Cache Hit vs Miss (100 nodes)

### Results
```
CACHE MISS (Save):
├── Create Entry:       9.3ms   (24%)
├── Save L1 (HTML):     4.9ms   (13%)
├── Save L2 (Dict):     3.2ms   ( 8%)
├── Save L3 (MGraph):  22.1ms   (56%)
└── Full Save:         39.1ms

CACHE HIT (Load):
├── Load L1:            2.1ms   ( 5%)
├── Load L2:            3.9ms   ( 9%)
├── Load L3:           38.5ms   (87%)  ← Deserialization bottleneck
└── Full Load:         44.3ms

MANAGER OPERATIONS:
├── Full Cache Miss:   82.0ms   (includes pipeline build)
└── Cache Hit:          1.7ms   ← 48x FASTER
```

### Key Insight: Cache Hit Performance
```
Cache Miss:  82.0ms  ████████████████████████████████████████
Cache Hit:    1.7ms  █

Speedup: 48x
```

### Anomaly: L3 Load > L3 Save
- L3 Save: 22.1ms (serialization)
- L3 Load: 38.5ms (deserialization)
- **Deserialization is 74% more expensive than serialization**
- `Html_MGraph__Document.from_json()` is the bottleneck

---

## E_6_4: Scaling Analysis (1-300 paragraphs)

### Results
```
Paragraphs │ Full Pipeline │ L2 Only   │ L1+L3 Overhead
───────────┼───────────────┼───────────┼───────────────
    1      │     4.2ms     │   4.3ms   │    -0.1ms
    5      │     6.4ms     │   4.4ms   │     2.0ms
   10      │     7.2ms     │   7.0ms   │     0.2ms
   50      │    20.0ms     │  19.9ms   │     0.1ms
  100      │    37.4ms     │  30.6ms   │     6.8ms
  200      │    76.2ms     │  58.0ms   │    18.2ms
  300      │   110.9ms     │  91.0ms   │    19.9ms
```

### Scaling Characteristics
```
Full Pipeline Scaling:
  1 para  ██                               4.2ms
  5 para  ███                              6.4ms
 10 para  ████                             7.2ms
 50 para  ██████████                      20.0ms
100 para  ███████████████████             37.4ms
200 para  ██████████████████████████████████████  76.2ms
300 para  ███████████████████████████████████████████████████████  110.9ms
```

### Linear Regression
- **Slope**: ~370µs per paragraph
- **Intercept**: ~4ms (fixed overhead)
- **Formula**: `time_ms ≈ 4 + 0.37 * paragraphs`

### L2 Dominance Confirmed
- L2 accounts for 82-100% of processing time at all scales
- Overhead (L1+L3) grows slightly with size but remains <20%

---

## E_6_5: Backend Comparison & Cost Breakdown (50 nodes)

### Full Pipeline Results
```
Backend          │ Save      │ Load
─────────────────┼───────────┼───────────
In-memory        │  39.4ms   │  27.9ms
```
*(Local and Live servers disabled in final run)*

### Cost Breakdown Model
```
Component                    │ Time     │ % of Save
─────────────────────────────┼──────────┼──────────
B_01 JSON Serialization      │   3.5ms  │    8.9%
B_02 Request Overhead        │   1.0ms  │    2.5%
B_03 Storage + Entry         │  10.3ms  │   26.1%
B_04 Layer Creation          │   6.0µs  │    0.0%  ← FREE
B_05 Full Setup              │   9.0ms  │   22.8%
B_06 Pure L1 Save            │   6.1ms  │   15.5%
B_07 Pure L2 Save            │   3.6ms  │    9.1%
B_08 Pure L3 Save            │  13.2ms  │   33.5%
─────────────────────────────┼──────────┼──────────
B_09 Pure L1 Load            │   2.1ms  │    7.5%
B_10 Pure L2 Load            │   3.1ms  │   11.1%
B_11 Pure L3 Load            │  21.5ms  │   77.1%  ← BOTTLENECK
```

### Validated Cost Model

**Save Pipeline (39.4ms):**
```
Predicted: B_05 + B_06 + B_07 + B_08 = 9.0 + 6.1 + 3.6 + 13.2 = 31.9ms
Actual:    39.4ms
Gap:       7.5ms (metadata saves, additional requests)
```

**Load Pipeline (27.9ms):**
```
Predicted: B_09 + B_10 + B_11 = 2.1 + 3.1 + 21.5 = 26.7ms
Actual:    27.9ms
Match:     ✓ (~1ms variance)
```

### Visual Cost Distribution

**Save (39.4ms):**
```
Setup    ██████████████████████████                    25%
L1 Save  ██████████████████                            15%
L2 Save  ██████████                                     9%
L3 Save  ██████████████████████████████████            34%
Other    █████████████████                             17%
```

**Load (27.9ms):**
```
L1 Load  ████                                           8%
L2 Load  ██████                                        11%
L3 Load  ████████████████████████████████████████████  77%
```

---

## Key Findings

### 1. MGraph Construction is the Bottleneck
- L2 (Dict → MGraph) = **83%** of pipeline time
- This is the primary target for optimization

### 2. Deserialization > Serialization
- L3 Load (21.5ms) is **63% more expensive** than L3 Save (13.2ms)
- `Html_MGraph__Document.from_json()` needs optimization

### 3. Setup Overhead is Significant but Amortizable
- ~25% of operation time is setup (storage + entry creation)
- Layer creation is essentially free (6µs)
- Reusing storage/layer instances eliminates this cost

### 4. Caching Provides 48x Speedup
- Cache miss: 82ms (full build)
- Cache hit: 1.7ms (manager retrieval)
- ROI is immediate for any repeated access

### 5. Linear Scaling Confirmed
- ~370µs per paragraph
- No exponential blowup at scale
- Predictable performance budgeting possible

---

## Optimization Recommendations

### High Impact
1. **Profile `Html_MGraph__Document.from_json()`** — 77% of load time
2. **Profile `Html__To__Html_MGraph__Document__Node_Id_Reuse`** — 83% of build time
3. **Implement lazy L3 loading** — Only deserialize when MGraph is actually needed

### Medium Impact
4. **Batch cache operations** — Reduce request count from ~8 to ~2
5. **Connection pooling** — Reduce per-request overhead
6. **Instance reuse** — Keep storage/layers alive across operations

### Low Impact (Already Optimized)
- Layer creation (6µs) — no action needed
- L1/L2 operations — already fast
- HTML parsing — negligible cost

---

## Performance Budget

For a typical 100-paragraph document:

| Operation | Time | Budget |
|-----------|------|--------|
| First request (cache miss) | ~82ms | Acceptable for initial fetch |
| Subsequent requests (cache hit) | ~1.7ms | Excellent for repeated access |
| Full pipeline build | ~39ms | Target: <50ms |
| L3 deserialization | ~38ms | Target: <20ms (needs optimization) |

---

## Appendix: Raw Data

### E_6_1 Results
| Benchmark | Time | % |
|-----------|------|---|
| A_01__L1__html_to_dict | 700µs | 1.9% |
| A_02__L2__dict_to_mgraph | 31.1ms | 83.2% |
| A_03__L3__mgraph_to_html | 5.6ms | 15.0% |
| B_01__full_pipeline | 38.9ms | 100% |

### E_6_3 Results
| Benchmark | Time |
|-----------|------|
| A_01__create_entry | 9.3ms |
| A_02__save_L1 | 4.9ms |
| A_03__save_L2 | 3.2ms |
| A_04__save_L3 | 22.1ms |
| A_05__full_save | 39.1ms |
| B_01__load_L1 | 2.1ms |
| B_02__load_L2 | 3.9ms |
| B_03__load_L3 | 38.5ms |
| B_04__full_load | 44.3ms |
| C_01__manager_miss | 82.0ms |
| C_02__manager_hit | 1.7ms |

### E_6_5 Results
| Benchmark | Time |
|-----------|------|
| A_01__save_pipeline | 39.4ms |
| A_02__load_pipeline | 27.9ms |
| B_01__raw_json_serialize | 3.5ms |
| B_02__request_overhead | 1.0ms |
| B_03__create_storage_entry | 10.3ms |
| B_04__create_layers | 6.0µs |
| B_05__full_setup | 9.0ms |
| B_06__pure_L1_save | 6.1ms |
| B_07__pure_L2_save | 3.6ms |
| B_08__pure_L3_save | 13.2ms |
| B_09__pure_L1_load | 2.1ms |
| B_10__pure_L2_load | 3.1ms |
| B_11__pure_L3_load | 21.5ms |
