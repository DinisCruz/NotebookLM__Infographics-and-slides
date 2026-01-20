# IFD Snapshot Generator - LLM Implementation Brief (v1.4.55)

[Home](../../../../README.md) > [By Topic](../../../README.md) > [Dev Briefs](../../README.md) > [HTML Graph Service](../README.md) > IFD Snapshot Generator Implementation Brief

---

## Overview

Implementation guide for the IFD Snapshot Generator service, a Python tool that programmatically extracts all dependencies from IFD minor version HTML entry points and creates self-contained snapshots. The service leverages Html_MGraph's multi-graph architecture for clean dependency extraction, enabling consolidation from scattered minor versions to major versions.

## Contents

| File | Description |
|------|-------------|
| `v1.4.55__ifd-snapshot-generator__llm-implementation-brief.md` | Full implementation guide with code examples |
| `CONTENT.md` | Semantic Knowledge Graph metadata for search and discovery |

## Problem Solved

The IFD surgical override pattern scatters files across version folders:

```
v0.2/
├── v0.2.0/js/api-client.js     (base - 500 lines)
├── v0.2.3/js/api-client.js     (surgical - 15 lines)
├── v0.2.5/js/api-client.js     (surgical - 20 lines)
└── v0.2.10/playground.html      (entry point)
```

The Snapshot Generator extracts and consolidates all dependencies into a clean package.

## Architecture

| Component | Role |
|-----------|------|
| **IFD_Snapshot** | Main facade with `generate()`, `generate_with_zip()`, `generate_to_folder()` |
| **IFD_Dependency_Extractor** | Uses Html_MGraph to extract CSS/JS dependencies from HTML |
| **IFD_Snapshot_Builder** | Builds manifest, content.txt, zip, and folder outputs |

## Key Outputs

| Output | Description |
|--------|-------------|
| `manifest.json` | Complete dependency mapping with load order and provenance |
| `content.txt` | Formatted text dump with file tree and all file contents |
| `snapshot.zip` | In-memory zip with all files (optional) |
| Extracted folder | Physical folder with snapshot contents (optional) |

## Schema Files

| Schema | Purpose |
|--------|---------|
| `Schema__IFD_Dependency` | Single file dependency extracted from HTML |
| `Schema__IFD_Load_Chain` | Ordered sequence forming a logical component |
| `Schema__IFD_Html_Entry` | HTML entry point with its dependencies |
| `Schema__IFD_Manifest` | Complete snapshot manifest |
| `Schema__IFD_Snapshot_Config` | Configuration for snapshot generation |
| `Schema__IFD_Snapshot_Result` | Result of snapshot generation |
| `Safe_Types__IFD` | Custom Safe types for IFD domain |

## Consolidation Workflow

```
v0.2.x (scattered) → Snapshot Generator → v0.2.10__snapshot (clean) → LLM merge → v0.3.0
```

## Source

- **Version**: v1.4.55
- **Package**: `mgraph_ai_service_html_graph.service.ifd_snapshot`
- **Dependencies**: Html_MGraph Service (same repo), OSBot-Utils v3.63+
- **Target Audience**: LLM sessions and developers implementing IFD consolidation

---

*Generated for NotebookLM content pipeline*
