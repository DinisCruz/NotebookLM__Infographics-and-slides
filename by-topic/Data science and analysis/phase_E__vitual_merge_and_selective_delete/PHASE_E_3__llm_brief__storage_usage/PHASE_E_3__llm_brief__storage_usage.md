# Phase E_3 Storage Module - LLM Brief

**Purpose:** Guide for using the storage abstraction layer in subsequent phases  
**Target Audience:** LLM sessions working on Phase E_4+ (HTML transformations, etc.)  
**Last Updated:** January 2025

---

## Important Context

### About Phase Experiments

These "phase" experiments are **exploratory development**:

- **Rapid iteration** to discover what works and what doesn't
- **Not production code** - intentionally rough around the edges
- **Learning-focused** - each phase builds knowledge for the next
- Learnings feed into **selective, strategic rewrites** when adding to main codebase
- Expect to find improvements needed - that's the point
- This brief itself may evolve as subsequent phases reveal new requirements

When bringing this code into the main project, we will:
1. Review what patterns worked well
2. Identify what needs refinement
3. Selectively rewrite with production quality
4. Integrate strategically with existing architecture

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Core Concepts](#core-concepts)
3. [Setup Patterns](#setup-patterns)
4. [Context Management](#context-management)
5. [Core Operations](#core-operations)
6. [File Organization Conventions](#file-organization-conventions)
7. [Backend-Agnostic Code](#backend-agnostic-code)
8. [Direct Cache Service Access](#direct-cache-service-access)
9. [Cross-Session/Target Access](#cross-sessiontarget-access)
10. [Testing Patterns](#testing-patterns)
11. [Common Workflows](#common-workflows)

---

## Quick Start

### Minimal Example - Save and Load

```python
from phase_e.storage.backends.Perf__Storage__Memory import Perf__Storage__Memory

# Create storage and set context
storage = Perf__Storage__Memory()
storage.set_context('my_session', 'my_target')

# Save data
storage.save('results/metrics.json', {'latency_ms': 42})
storage.save_string('source/page.html', '<html>...</html>')

# Load data
metrics = storage.load('results/metrics.json')
html = storage.load_string('source/page.html')

# Check existence
if storage.exists('results/metrics.json'):
    print("Data exists!")
```

### With Factory (Recommended)

```python
from phase_e.storage.factory.Perf__Storage__Factory   import Perf__Storage__Factory
from phase_e.storage.schemas.Schema__Perf__Storage__Config import Schema__Perf__Storage__Config
from phase_e.storage.enums.Enum__Storage_Mode         import Enum__Storage_Mode

# Configure for memory backend
config = Schema__Perf__Storage__Config(storage_mode=Enum__Storage_Mode.MEMORY)

# Create storage via factory
factory = Perf__Storage__Factory(config=config)
storage = factory.create()

# Set context and use
storage.set_context('html_transform_batch_001', 'example.com/page1')
storage.save_string('source/original.html', html_content)
```

---

## Core Concepts

### Session and Target

Storage is organized hierarchically:

```
Session: "html_transform_batch_001"     ← One per batch/run/experiment
    │
    ├── Target: "example.com/page1"     ← One per item being processed
    │   ├── source/original.html
    │   ├── graphs/parsed.json
    │   └── output/transformed.html
    │
    ├── Target: "example.com/page2"
    │   └── ...
    │
    └── Target: "other-site.com/home"
        └── ...
```

- **Session**: Groups related processing runs (e.g., a batch job, an experiment)
- **Target**: Individual item being processed (e.g., a URL, a document)

### Three Storage Backends

| Backend | Use Case | Persistence |
|---------|----------|-------------|
| `Perf__Storage__Memory` | Unit tests, temporary processing | None (in-memory dict) |
| `Perf__Storage__Local` | Development, local experiments | Disk files |
| `Perf__Storage__Cache_Service` | Production, shared storage | MGraph-AI Cache Service |

### Backend-Agnostic Interface

All backends implement `Perf__Storage__Base`:

```python
class Perf__Storage__Base:
    def set_context(session_name, target_name) -> None
    def save(key, data: dict) -> bool              # JSON data
    def save_string(key, content: str) -> bool     # String content
    def load(key) -> Optional[dict]                # Returns None if not found
    def load_string(key) -> Optional[str]          # Returns None if not found
    def exists(key) -> bool
    def delete(key) -> bool
    def list_keys(prefix='') -> List[str]
```

---

## Setup Patterns

### Pattern 1: Direct Backend (Simple)

```python
from phase_e.storage.backends.Perf__Storage__Memory import Perf__Storage__Memory

storage = Perf__Storage__Memory()
storage.set_context('session', 'target')
```

### Pattern 2: Factory with Config (Flexible)

```python
from phase_e.storage.factory.Perf__Storage__Factory        import Perf__Storage__Factory
from phase_e.storage.schemas.Schema__Perf__Storage__Config import Schema__Perf__Storage__Config
from phase_e.storage.enums.Enum__Storage_Mode              import Enum__Storage_Mode

# Memory backend
config = Schema__Perf__Storage__Config(storage_mode=Enum__Storage_Mode.MEMORY)

# OR Local backend
config = Schema__Perf__Storage__Config(
    storage_mode       = Enum__Storage_Mode.LOCAL,
    local_storage_path = './my_results'
)

# Create via factory
storage = Perf__Storage__Factory(config=config).create()
storage.set_context('session', 'target')
```

### Pattern 3: Cache Service Backend (Production)

```python
from phase_e.storage.factory.Perf__Storage__Factory        import Perf__Storage__Factory
from phase_e.storage.schemas.Schema__Perf__Storage__Config import Schema__Perf__Storage__Config
from phase_e.storage.enums.Enum__Storage_Mode              import Enum__Storage_Mode
from phase_e.fast_api.Phase_E__Fast_API__Test_Objs         import client_cache_service

# Get cache client (in-memory for testing, or configured for production)
cache_client, cache_service = client_cache_service()

config = Schema__Perf__Storage__Config(
    storage_mode    = Enum__Storage_Mode.CACHE_SERVICE,
    cache_namespace = 'html-transforms'
)

storage = Perf__Storage__Factory(
    config       = config,
    cache_client = cache_client,
    session_name = 'html_transform_batch_001',
    target_name  = 'example.com/page1'
).create()
```

### Pattern 4: Environment-Based Config

```python
from phase_e.storage.config.Perf__Storage__Config import Perf__Storage__Config

# Reads from environment variables:
# - PERF_STORAGE_MODE: local | memory | cache_service
# - PERF_CACHE_NAMESPACE: namespace name
# - PERF_LOCAL_STORAGE_PATH: path for local storage

config_manager = Perf__Storage__Config().setup()
config = config_manager.config
```

---

## Context Management

### Setting Context

```python
# Initial context
storage.set_context('batch_001', 'example.com/page1')

# Process first target
storage.save_string('source/original.html', html1)
storage.save('graphs/parsed.json', graph1)

# Switch to next target (same session)
storage.set_context('batch_001', 'example.com/page2')

# Now saving to different target
storage.save_string('source/original.html', html2)  # Different location!
```

### Context Isolation

Each session/target combination is isolated:

```python
storage.set_context('session_a', 'target_1')
storage.save('data.json', {'value': 'A1'})

storage.set_context('session_a', 'target_2')
storage.save('data.json', {'value': 'A2'})

storage.set_context('session_b', 'target_1')
storage.save('data.json', {'value': 'B1'})

# Each data.json is independent
storage.set_context('session_a', 'target_1')
assert storage.load('data.json') == {'value': 'A1'}
```

### For Cache Service: Context in Constructor

With `Perf__Storage__Cache_Service`, session/target are set at construction:

```python
storage = Perf__Storage__Cache_Service(
    config       = config,
    client       = cache_client_wrapper,
    session_name = 'batch_001',
    target_name  = 'example.com/page1'
)
# No set_context() needed - it's fixed for this instance
```

To process multiple targets, create multiple storage instances or use the factory pattern.

---

## Core Operations

### Saving Data

```python
# JSON data (dicts)
storage.save('results/metrics.json', {
    'latency_ms': 42,
    'nodes_processed': 150,
    'success': True
})

# String content (HTML, Markdown, text)
storage.save_string('source/original.html', '<html>...</html>')
storage.save_string('reports/summary.md', '# Summary\n\nAll tests passed.')
```

### Loading Data

```python
# JSON data - returns dict or None
metrics = storage.load('results/metrics.json')
if metrics:
    print(f"Latency: {metrics['latency_ms']}ms")

# String content - returns str or None
html = storage.load_string('source/original.html')
if html:
    process_html(html)
```

### Checking Existence

```python
if storage.exists('results/metrics.json'):
    # Load and use existing data
    metrics = storage.load('results/metrics.json')
else:
    # Compute and save
    metrics = compute_metrics()
    storage.save('results/metrics.json', metrics)
```

### Deleting Data

```python
if storage.delete('temp/intermediate.json'):
    print("Deleted successfully")
else:
    print("File not found or delete failed")
```

### Listing Keys

```python
# List all keys in current context
all_keys = storage.list_keys()

# List keys with prefix
result_keys = storage.list_keys('results/')
# Returns: ['results/metrics.json', 'results/summary.json', ...]
```

**Note:** `list_keys()` is not yet implemented for Cache Service backend (returns `[]`). This is a known limitation to address in future iterations.

---

## File Organization Conventions

### Recommended Directory Structure

```
{session}/{target}/
├── source/                    # Original input files
│   ├── original.html          # Raw HTML input
│   ├── fetched_at.json        # Fetch metadata (timestamp, headers)
│   └── url_info.json          # URL details
│
├── graphs/                    # Graph representations
│   ├── parsed.json            # Initial graph from HTML
│   ├── cleaned.json           # After cleanup transformations
│   ├── transformed.json       # After main transformations
│   └── final.json             # Ready for rendering
│
├── output/                    # Generated outputs
│   ├── rendered.html          # Final HTML output
│   ├── diff.html              # Visual diff (if applicable)
│   └── screenshots/           # If generating screenshots
│       ├── before.png
│       └── after.png
│
├── results/                   # Metrics and analysis
│   ├── metrics.json           # Performance metrics
│   ├── node_stats.json        # Graph node statistics
│   └── transform_log.json     # Transformation audit trail
│
└── reports/                   # Human-readable reports
    ├── summary.md             # Summary report
    ├── analysis.md            # Detailed analysis
    └── errors.md              # Error log (if any)
```

### Naming Conventions

| Convention | Example | Use For |
|------------|---------|---------|
| `snake_case.ext` | `original_html.json` | General files |
| Descriptive names | `before_transform.json` | State snapshots |
| Timestamps (optional) | `metrics_20250111.json` | Versioned outputs |

### JSON vs String

| Use `save()` / `load()` | Use `save_string()` / `load_string()` |
|-------------------------|---------------------------------------|
| Structured data (metrics, configs) | HTML content |
| Graph representations | Markdown reports |
| Metadata objects | Plain text logs |
| Anything you'll query programmatically | Anything rendered as-is |

---

## Backend-Agnostic Code

### The Goal

Write transformation code that doesn't know or care which storage backend is used:

```python
from phase_e.storage.base.Perf__Storage__Base import Perf__Storage__Base

def transform_html_page(storage: Perf__Storage__Base, html_content: str) -> str:
    """Transform HTML - storage backend is irrelevant here."""
    
    # Save source
    storage.save_string('source/original.html', html_content)
    
    # Create graph
    graph = parse_html_to_graph(html_content)
    storage.save('graphs/parsed.json', graph.json())
    
    # Transform
    transformed = apply_transformations(graph)
    storage.save('graphs/transformed.json', transformed.json())
    
    # Render output
    output_html = render_graph_to_html(transformed)
    storage.save_string('output/rendered.html', output_html)
    
    # Save metrics
    storage.save('results/metrics.json', {
        'input_size': len(html_content),
        'output_size': len(output_html),
        'nodes_processed': len(transformed.nodes)
    })
    
    return output_html
```

### Type Hint for Abstraction

```python
from phase_e.storage.base.Perf__Storage__Base import Perf__Storage__Base

def my_function(storage: Perf__Storage__Base):
    # Works with Memory, Local, or Cache Service
    ...
```

### What NOT to Import in Transformation Code

```python
# ❌ Don't import specific backends
from phase_e.storage.backends.Perf__Storage__Cache_Service import ...
from phase_e.storage.cache_service.Cache_Service__Client import ...

# ❌ Don't import cache service specifics
from mgraph_ai_service_cache_client.client... import ...

# ✅ Only import the base class for type hints
from phase_e.storage.base.Perf__Storage__Base import Perf__Storage__Base
```

### Dependency Injection Pattern

```python
class HtmlTransformer:
    def __init__(self, storage: Perf__Storage__Base):
        self.storage = storage
    
    def process(self, url: str, html: str):
        self.storage.save_string('source/original.html', html)
        # ... transformation logic
```

The caller/orchestrator decides which backend:

```python
# In test
storage = Perf__Storage__Memory()
transformer = HtmlTransformer(storage)

# In production
storage = factory.create()  # Returns Cache Service backend
transformer = HtmlTransformer(storage)
```

---

## Direct Cache Service Access

### When to Bypass the Abstraction

Sometimes you need cache service features not exposed by `Perf__Storage__Base`:

| Scenario | Why Direct Access |
|----------|-------------------|
| Cross-session lookups | Load baseline from previous run |
| Query by hash | Check if content already processed |
| Access metadata | Get cache entry timestamps, refs |
| Bulk operations | Efficient multi-target queries |
| Debug/inspect | Examine cache structure |

### Getting the Cache Client

```python
from phase_e.fast_api.Phase_E__Fast_API__Test_Objs import client_cache_service
from phase_e.storage.cache_service.Cache_Service__Client import Cache_Service__Client
from phase_e.storage.schemas.Schema__Perf__Storage__Config import Schema__Perf__Storage__Config

# In-memory cache service (testing)
cache_client, cache_service = client_cache_service()

# Wrap with our client
config = Schema__Perf__Storage__Config(cache_namespace='my-namespace')
client = Cache_Service__Client(config=config, cache_client=cache_client)
```

### Direct Client Operations

```python
# Find entry by semantic path
cache_id = client.find_entry_by_key(cache_key='sessions/batch_001/targets/example.com')

# Check if hash exists (content-addressable lookup)
result = client.hash_exists(cache_hash='798b21460e17d4a6')
if result['exists']:
    print(f"Content already cached with ID: {result.get('cache_id')}")

# Retrieve entry metadata
entry_data = client.retrieve_entry(cache_id=cache_id)

# Health check
if client.health_check():
    print("Cache service is healthy")
```

### Accessing the Underlying Official Client

For operations not wrapped by `Cache_Service__Client`:

```python
# Access official client directly
official_client = client.cache_client

# Use any official client API
namespaces = official_client.namespaces().list()
stats = official_client.namespace().stats(namespace='my-namespace')

# Admin operations
files = official_client.admin_storage().files__in__path(
    path='my-namespace/data/key-based/sessions',
    recursive=True
)
```

### Direct Cache Service Access (Backend)

If you have a `Perf__Storage__Cache_Service` instance:

```python
# Get the wrapped client
cache_storage: Perf__Storage__Cache_Service = factory.create()

# Access client wrapper
client = cache_storage.client

# Get cache key/hash for current context
cache_key = cache_storage.cache_key()    # 'sessions/batch_001/targets/example.com'
cache_hash = cache_storage.cache_hash()  # '798b21460e17d4a6'

# Ensure entry exists and get cache_id
cache_id = cache_storage.ensure_entry()
```

---

## Cross-Session/Target Access

### Loading from Different Context

```python
# Current context
storage.set_context('batch_002', 'example.com/page1')

# Need to load baseline from previous batch
# Option 1: Temporarily switch context
storage.set_context('batch_001', 'example.com/page1')
baseline = storage.load('results/metrics.json')
storage.set_context('batch_002', 'example.com/page1')  # Switch back

# Option 2: Create separate storage instance
baseline_storage = Perf__Storage__Memory()  # Or from factory
baseline_storage.set_context('batch_001', 'example.com/page1')
baseline = baseline_storage.load('results/metrics.json')
```

### Cross-Session with Cache Service

For production cross-session access, use direct client:

```python
# Find entry from different session
baseline_key = 'sessions/baseline_batch/targets/example.com'
baseline_id = client.find_entry_by_key(cache_key=baseline_key)

if baseline_id:
    # Load specific child data
    baseline_metrics = client.retrieve_child_json(
        cache_id     = baseline_id,
        data_key     = 'results',
        data_file_id = 'metrics.json'
    )
```

### Comparing Across Sessions

```python
def compare_with_baseline(current_storage, baseline_session: str):
    """Compare current results with baseline session."""
    
    # Get current metrics
    current = current_storage.load('results/metrics.json')
    
    # Load baseline via direct client access
    client = current_storage.client
    baseline_key = f'sessions/{baseline_session}/targets/{current_storage.target_name}'
    baseline_id = client.find_entry_by_key(cache_key=baseline_key)
    
    if baseline_id:
        baseline = client.retrieve_child_json(
            cache_id     = baseline_id,
            data_key     = 'results',
            data_file_id = 'metrics.json'
        )
        
        return {
            'latency_diff': current['latency_ms'] - baseline['latency_ms'],
            'improved': current['latency_ms'] < baseline['latency_ms']
        }
    
    return None  # No baseline found
```

---

## Testing Patterns

### Pattern 1: Memory Backend (Fastest)

```python
from unittest import TestCase
from phase_e.storage.backends.Perf__Storage__Memory import Perf__Storage__Memory

class test_HtmlTransformer(TestCase):
    
    def setUp(self):
        self.storage = Perf__Storage__Memory()
        self.storage.set_context('test_session', 'test_target')
    
    def test_transform_saves_output(self):
        transformer = HtmlTransformer(self.storage)
        transformer.process('<html><body>Test</body></html>')
        
        assert self.storage.exists('output/rendered.html') is True
        output = self.storage.load_string('output/rendered.html')
        assert '<body>' in output
```

### Pattern 2: In-Memory Cache Service (Real API)

```python
from unittest import TestCase
from phase_e.fast_api.Phase_E__Fast_API__Test_Objs import client_cache_service
from phase_e.storage.backends.Perf__Storage__Cache_Service import Perf__Storage__Cache_Service
from phase_e.storage.cache_service.Cache_Service__Client import Cache_Service__Client
from phase_e.storage.schemas.Schema__Perf__Storage__Config import Schema__Perf__Storage__Config
from phase_e.storage.enums.Enum__Storage_Mode import Enum__Storage_Mode

class test_HtmlTransformer__CacheService(TestCase):
    
    @classmethod
    def setUpClass(cls):
        # Start in-memory cache service (~100ms)
        cls.cache_client, cls.cache_service = client_cache_service()
        cls.config = Schema__Perf__Storage__Config(
            storage_mode    = Enum__Storage_Mode.CACHE_SERVICE,
            cache_namespace = 'test_transforms'
        )
    
    def setUp(self):
        client_wrapper = Cache_Service__Client(cache_client=self.cache_client)
        self.storage = Perf__Storage__Cache_Service(
            config       = self.config,
            client       = client_wrapper,
            session_name = 'test_session',
            target_name  = 'test_target'
        )
    
    def test_transform_with_real_cache(self):
        transformer = HtmlTransformer(self.storage)
        transformer.process('<html><body>Test</body></html>')
        
        # Verify via storage abstraction
        assert self.storage.exists('output/rendered.html') is True
        
        # Can also verify via direct cache service access
        cache_id = self.storage.ensure_entry()
        entry = self.cache_service.retrieve_by_id(cache_id=cache_id, namespace='test_transforms')
        assert entry is not None
```

### Pattern 3: Shared Test Fixtures

```python
from unittest import TestCase
from phase_e.fast_api.Phase_E__Fast_API__Test_Objs import client_cache_service

class BaseStorageTest(TestCase):
    """Base class for tests needing storage."""
    
    @classmethod
    def setUpClass(cls):
        cls.cache_client, cls.cache_service = client_cache_service()
    
    def create_memory_storage(self, session='test', target='target'):
        storage = Perf__Storage__Memory()
        storage.set_context(session, target)
        return storage
    
    def create_cache_storage(self, session='test', target='target'):
        # ... setup cache storage
        return storage
```

---

## Common Workflows

### Workflow 1: Batch Processing Multiple URLs

```python
def process_url_batch(urls: List[str], session_name: str, factory: Perf__Storage__Factory):
    """Process multiple URLs in a single session."""
    
    results = []
    
    for url in urls:
        # Create storage for this target
        target_name = url.replace('https://', '').replace('/', '_')
        
        storage = factory.create()
        storage.set_context(session_name, target_name)
        
        # Fetch and process
        html = fetch_url(url)
        storage.save_string('source/original.html', html)
        storage.save('source/url_info.json', {'url': url, 'fetched_at': timestamp()})
        
        # Transform
        output = transform_html(storage, html)
        
        # Collect results
        results.append({
            'url': url,
            'target': target_name,
            'success': output is not None
        })
    
    return results
```

### Workflow 2: Resume Interrupted Processing

```python
def process_with_resume(urls: List[str], session_name: str, storage_factory):
    """Resume processing from where we left off."""
    
    for url in urls:
        target_name = url_to_target(url)
        storage = storage_factory.create()
        storage.set_context(session_name, target_name)
        
        # Check if already processed
        if storage.exists('output/rendered.html'):
            print(f"Skipping {url} - already processed")
            continue
        
        # Check if we have source but failed transformation
        if storage.exists('source/original.html'):
            html = storage.load_string('source/original.html')
            print(f"Resuming {url} from saved source")
        else:
            html = fetch_url(url)
            storage.save_string('source/original.html', html)
        
        # Process
        transform_html(storage, html)
```

### Workflow 3: Compare Before/After

```python
def transform_and_compare(storage: Perf__Storage__Base, html: str):
    """Transform HTML and save comparison data."""
    
    # Save original
    storage.save_string('source/original.html', html)
    
    # Parse to graph
    original_graph = parse_html(html)
    storage.save('graphs/original.json', original_graph.json())
    
    # Transform
    transformed_graph = transform(original_graph)
    storage.save('graphs/transformed.json', transformed_graph.json())
    
    # Render
    output_html = render(transformed_graph)
    storage.save_string('output/rendered.html', output_html)
    
    # Save comparison metrics
    storage.save('results/comparison.json', {
        'original_nodes': len(original_graph.nodes),
        'transformed_nodes': len(transformed_graph.nodes),
        'original_size': len(html),
        'output_size': len(output_html),
        'size_reduction_pct': (1 - len(output_html)/len(html)) * 100
    })
```

---

## Known Limitations

| Limitation | Workaround | Future Fix |
|------------|------------|------------|
| `list_keys()` not implemented for Cache Service | Use direct client admin API | Implement via refs API |
| No bulk save/load | Loop over items | Add batch methods |
| No streaming for large files | Load entire content | Add streaming support |
| Session/target fixed at construction for Cache Service | Create new instance per target | Consider context switching |

---

## Summary

### For Backend-Agnostic Code
- Import only `Perf__Storage__Base` for type hints
- Use `save`, `load`, `save_string`, `load_string`, `exists`, `delete`
- Let caller inject storage instance
- Follow file organization conventions

### For Direct Cache Access
- Use `Cache_Service__Client` wrapper for common operations
- Access `client.cache_client` for official client APIs
- Use `find_entry_by_key()` for cross-session lookups
- Use `hash_exists()` for content-addressable queries

### For Testing
- Use `Perf__Storage__Memory` for fast unit tests
- Use `client_cache_service()` for in-memory integration tests
- Create fresh storage per test to ensure isolation

---

## Quick Reference

```python
# Setup
from phase_e.storage.backends.Perf__Storage__Memory import Perf__Storage__Memory
from phase_e.storage.base.Perf__Storage__Base import Perf__Storage__Base

# Create and configure
storage = Perf__Storage__Memory()
storage.set_context('session', 'target')

# Core operations
storage.save('path/file.json', {'key': 'value'})     # Save JSON
storage.save_string('path/file.html', '<html>...')   # Save string
data = storage.load('path/file.json')                # Load JSON (or None)
text = storage.load_string('path/file.html')         # Load string (or None)
storage.exists('path/file.json')                     # Check existence
storage.delete('path/file.json')                     # Delete
storage.list_keys('path/')                           # List keys with prefix

# Type hint for functions
def my_func(storage: Perf__Storage__Base): ...
```
