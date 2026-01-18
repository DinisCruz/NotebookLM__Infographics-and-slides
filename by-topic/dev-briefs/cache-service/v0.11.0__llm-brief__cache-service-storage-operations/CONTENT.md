# Cache Service Storage Operations

## Summary

Technical brief for the MGraph Cache Service's two-tier storage architecture. The system separates entity management (Tier 1) from data storage (Tier 2), enabling efficient deduplication through hash-based lookups, fast retrieval via dual reference indexes, and flexible organization of related content (HTML, graphs, metadata) under a single cache entry.

---

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Two-Tier Storage** | Separates entry anchors (Tier 1) from actual data content (Tier 2) |
| **cache_key** | Semantic identifier, typically a URL or path |
| **cache_hash** | Hash of cache_key for deduplication checks |
| **cache_id** | Unique GUID for each cache entry |
| **Dual Reference System** | Both `by-hash` and `by-id` indexes for O(1) lookups |
| **content_hash** | Hash of actual stored content for change detection |

---

## Two-Tier Model

### Tier 1: Entry Storage
- Creates the entity "anchor" with identity
- Stores metadata and configuration files
- Maintains reference files for indexing
- Files: `{file_id}.json`, `.config`, `.metadata`

### Tier 2: Data Storage
- Stores actual content within entry's data folder
- Supports multiple data types per entry
- Organized by `data_key` and `data_file_id`
- Examples: raw HTML, cleaned HTML, graph data

---

## File Structure

```
{namespace}/
├── data/key-based/{cache_key}/
│   ├── {file_id}.json           ← Entry content
│   ├── {file_id}.json.config    ← Entry config
│   ├── {file_id}.json.metadata  ← Entry metadata
│   └── {file_id}/data/          ← Tier 2 data folder
│       └── {data_key}/{data_file_id}.*
└── refs/
    ├── by-hash/{aa}/{bb}/{cache_hash}.json
    └── by-id/{xx}/{yy}/{cache_id}.json
```

---

## Storage Sequence

1. **Initialize Context**: Set defaults, generate cache_id
2. **Calculate Hash**: Derive cache_hash from cache_key
3. **Store Entry**: Write entry file + config + metadata
4. **Update Hash Reference**: Create/update by-hash index
5. **Create ID Reference**: Create by-id index
6. **Build Response**: Return cache_id + status

---

## Tags

`cache-service` `mgraph` `two-tier-storage` `deduplication` `hash-index` `data-architecture` `storage-pattern`

---

<details>
<summary>📊 Semantic Knowledge Graph</summary>

```
NODES:
  cache_service:
    type: system
    label: "MGraph Cache Service"
    description: "Two-tier caching system for web content"

  tier_1_entry:
    type: component
    label: "Tier 1: Entry Storage"
    description: "Entity anchor with identity and metadata"

  tier_2_data:
    type: component
    label: "Tier 2: Data Storage"
    description: "Actual content within entry's data folder"

  cache_key:
    type: identifier
    label: "cache_key"
    description: "Semantic identifier, typically URL"

  cache_hash:
    type: identifier
    label: "cache_hash"
    description: "Hash of cache_key for deduplication"

  cache_id:
    type: identifier
    label: "cache_id"
    description: "Unique GUID for entry"

  by_hash_index:
    type: index
    label: "by-hash Index"
    description: "Reference file for hash-based lookups"

  by_id_index:
    type: index
    label: "by-id Index"
    description: "Reference file for ID-based lookups"

  metadata_file:
    type: file
    label: "Metadata File"
    description: "Timestamps, content hashes, versions"

  strategy_pattern:
    type: pattern
    label: "Strategy Pattern"
    description: "Pluggable storage strategies"

EDGES:
  cache_service -> tier_1_entry:
    relation: contains
    label: "manages"

  cache_service -> tier_2_data:
    relation: contains
    label: "manages"

  tier_1_entry -> tier_2_data:
    relation: references
    label: "points to data folder"

  cache_key -> cache_hash:
    relation: derives
    label: "hashed to"

  cache_hash -> by_hash_index:
    relation: stored_in
    label: "indexed by"

  cache_id -> by_id_index:
    relation: stored_in
    label: "indexed by"

  tier_1_entry -> metadata_file:
    relation: contains
    label: "includes"

  cache_service -> strategy_pattern:
    relation: uses
    label: "storage via"

  by_hash_index -> cache_service:
    relation: enables
    label: "deduplication for"

  by_id_index -> cache_service:
    relation: enables
    label: "fast retrieval for"
```

</details>
