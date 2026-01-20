# IFD Snapshot Generator - LLM Implementation Brief

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

The IFD Snapshot Generator is a Python service that programmatically extracts all dependencies from IFD (Incremental Feature Development) minor version HTML entry points and creates self-contained snapshots. It leverages Html_MGraph's multi-graph architecture (scripts_graph, styles_graph, attrs_graph) for clean dependency extraction, automatically classifying files as "base" or "surgical" based on load order. The service outputs manifest.json (complete dependency mapping), content.txt (formatted dump with tree structure), and optional zip/folder extraction — enabling the consolidation workflow from scattered minor versions to clean major versions.

---

## Key Concepts

- **IFD Surgical Override Pattern**: Development approach where files are scattered across version folders — base files in earlier versions, surgical overrides (small modifications) in later versions, all referenced by a single HTML entry point.

- **Html_MGraph Multi-Graph Leverage**: Instead of manually parsing HTML for `<script>` and `<link>` tags, the service uses Html_MGraph's dedicated `scripts_graph` and `styles_graph` for clean, type-safe dependency extraction.

- **Load Chain Grouping**: Dependencies with the same "logical name" (e.g., `js/services/api-client`) are grouped into ordered chains — base file first, surgical overrides in version order.

- **Base vs Surgical Classification**: First occurrence of a logical_name is classified as "base", all subsequent occurrences are "surgical" — enabling provenance tracking for consolidation.

- **Type_Safe Schema Pattern**: All data structures use OSBot-Utils Type_Safe framework with custom Safe types (Safe_Str__IFD_Version, Safe_UInt__IFD_Load_Order) for runtime validation.

- **Snapshot Output Formats**: Four output options — manifest.json (JSON dependency map), content.txt (human-readable dump with file tree), snapshot.zip (in-memory archive), and extracted folder (physical file structure).

---

## Core Arguments

1. The IFD surgical override pattern creates scattered file dependencies that are difficult to track manually — programmatic extraction ensures complete and accurate dependency mapping.

2. Leveraging Html_MGraph's existing multi-graph architecture (scripts_graph, styles_graph) provides cleaner extraction than manual HTML parsing with regex or DOM traversal.

3. Grouping dependencies into load chains by logical name preserves the critical load order that makes surgical overrides work correctly.

4. The base/surgical classification enables provenance tracking — developers know exactly which version introduced each piece of functionality.

5. Multiple output formats serve different use cases: manifest.json for programmatic access, content.txt for LLM consumption, zip for distribution, folder for direct inspection.

6. Type_Safe schemas with custom Safe types catch data validation errors at runtime rather than silently propagating invalid values.

---

## Key Quotes

> "The key insight is that Html_MGraph already separates scripts and styles into dedicated graphs — this is far cleaner than manually parsing HTML for `<script>` and `<link>` tags."

> "The first occurrence of a logical_name is classified as base, all subsequent occurrences are surgical."

> "This enables the consolidation workflow: v0.2.x (scattered) → Snapshot Generator → v0.2.10__snapshot (clean) → LLM merge → v0.3.0"

> "Schemas are PURE DATA — no methods allowed. One schema per file."

---

## Tags

`ifd-snapshot` `dependency-extraction` `html-mgraph` `surgical-override` `load-chains` `type-safe-python` `manifest-generation` `version-consolidation` `multi-graph-architecture` `osbot-utils` `snapshot-builder` `provenance-tracking`

---

## Search Phrases

- "IFD snapshot generator implementation"
- "Html_MGraph dependency extraction"
- "surgical override consolidation"
- "load chain grouping algorithm"
- "Type_Safe schema definitions Python"
- "base vs surgical file classification"
- "HTML entry point dependency mapping"
- "version consolidation workflow"
- "manifest.json content.txt generation"
- "scripts_graph styles_graph usage"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | LLM Implementation Brief |
| **Domain** | HTML Graph Processing / IFD Methodology |
| **Sub-domain** | Dependency Extraction / Version Consolidation |
| **Version** | v1.4.55 |
| **Package** | `mgraph_ai_service_html_graph.service.ifd_snapshot` |
| **Dependencies** | Html_MGraph Service, OSBot-Utils v3.63+ |
| **Date** | January 2026 |
| **Target Audience** | LLM sessions and developers |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `depends_on` | Html_MGraph Multi-Graph Architecture (v1.4.54) |
| `uses` | OSBot-Utils Type_Safe Framework |
| `enables` | IFD Version Consolidation Workflow |
| `related_to` | FLeT REST API Implementation |
| `part_of` | MGraph-AI Service HTML Graph |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `service` | A Python service class |
| `schema` | A Type_Safe data schema |
| `output` | A generated output format |
| `method` | A service method |
| `pattern` | A design or development pattern |
| `concept` | A domain concept |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `contains` | `part_of` | Service contains methods/schemas |
| `produces` | `produced_by` | Service produces outputs |
| `uses` | `used_by` | Service uses another component |
| `enables` | `enabled_by` | Capability enables workflow |
| `classifies` | `classified_by` | Service classifies files |

### Taxonomy

```
ifd_snapshot_generator
├── services
│   ├── IFD_Snapshot (facade)
│   ├── IFD_Dependency_Extractor
│   └── IFD_Snapshot_Builder
├── schemas
│   ├── Schema__IFD_Dependency
│   ├── Schema__IFD_Load_Chain
│   ├── Schema__IFD_Html_Entry
│   ├── Schema__IFD_Manifest
│   ├── Schema__IFD_Snapshot_Config
│   ├── Schema__IFD_Snapshot_Result
│   └── Safe_Types__IFD
├── outputs
│   ├── manifest_json
│   ├── content_txt
│   ├── snapshot_zip
│   └── extracted_folder
├── concepts
│   ├── surgical_override
│   ├── load_chain
│   ├── base_file
│   ├── surgical_file
│   └── logical_name
└── patterns
    ├── type_safe_schema
    ├── multi_graph_leverage
    └── consolidation_workflow
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `ifd_snapshot` | `service` | IFD_Snapshot (Facade) |
| `dependency_extractor` | `service` | IFD_Dependency_Extractor |
| `snapshot_builder` | `service` | IFD_Snapshot_Builder |
| `html_mgraph` | `service` | Html_MGraph |
| `manifest_json` | `output` | manifest.json |
| `content_txt` | `output` | content.txt |
| `snapshot_zip` | `output` | snapshot.zip |
| `load_chain` | `concept` | Load Chain |
| `surgical_override` | `pattern` | Surgical Override Pattern |
| `schema_dependency` | `schema` | Schema__IFD_Dependency |
| `schema_manifest` | `schema` | Schema__IFD_Manifest |
| `consolidation` | `pattern` | Version Consolidation Workflow |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `ifd_snapshot` | `contains` | `dependency_extractor` |
| `ifd_snapshot` | `contains` | `snapshot_builder` |
| `dependency_extractor` | `uses` | `html_mgraph` |
| `dependency_extractor` | `produces` | `schema_dependency` |
| `snapshot_builder` | `produces` | `manifest_json` |
| `snapshot_builder` | `produces` | `content_txt` |
| `snapshot_builder` | `produces` | `snapshot_zip` |
| `schema_dependency` | `part_of` | `load_chain` |
| `ifd_snapshot` | `enables` | `consolidation` |
| `surgical_override` | `enabled_by` | `load_chain` |
| `schema_manifest` | `contains` | `load_chain` |

</details>
