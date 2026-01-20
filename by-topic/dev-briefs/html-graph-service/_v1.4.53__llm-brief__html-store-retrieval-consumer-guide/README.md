# HTML Store & Retrieval - Consumer Integration Guide (v1.4.53)

[Home](../../../../README.md) > [By Topic](../../../README.md) > [Dev Briefs](../../README.md) > [HTML Graph Service](../README.md) > HTML Store & Retrieval Consumer Guide

---

## Overview

Consumer integration guide for the HTML Store & Retrieval system, providing unified, observable, versioned HTML caching. This guide covers both REST API integration and internal service usage patterns, including a complete MVP use case for MitmProxy passive mode caching.

## Contents

| File | Description |
|------|-------------|
| `v1.4.53__llm-brief__html-store-retrieval-consumer-guide.md` | Full consumer integration guide |
| `20 Jan - Unified Versioned HTML Caching Architecture.jpg` | Architecture infographic |
| `20 Jan - HTML_Store_Retrieval_System_Architecture.pdf` | PDF slide deck |
| `CONTENT.md` | Semantic Knowledge Graph metadata for search and discovery |

## System Capabilities

| Capability | Description |
|------------|-------------|
| **Multi-mode Storage** | Store HTML from raw content, URLs, or explicit cache keys |
| **Flexible Retrieval** | Load HTML by cache_id, cache_hash, cache_key, or URL |
| **Automatic Versioning** | Same cache_key can have multiple versions (different cache_ids) |
| **Built-in Observability** | Every operation records flow data, logs, tasks, and durations |
| **Entity-Centric Model** | Each "page" is an entity that can hold multiple data files |

## Integration Patterns

### REST API (External Clients)
```
Base URL: https://html-graph.dev.mgraph.ai
Docs: https://html-graph.dev.mgraph.ai/docs
```

### Internal Service (Python)
```python
from mgraph_ai_service_html_graph.service.flet_pipeline.flet.FLeT__Html__Domain__Service import FLeT__Html__Domain__Service
```

## Key Operations

| Operation | REST API | Internal Service |
|-----------|----------|------------------|
| Store raw HTML | `POST /html/store/{ns}/raw` | `domain_service.store_raw()` |
| Store from URL | `POST /html/store/{ns}/url` | `domain_service.store_from_url()` |
| Load by ID | `POST /html/load/{ns}/id` | `domain_service.load_by_id()` |
| Load by URL | `POST /html/load/{ns}/url` | `domain_service.load_by_url()` |

## MVP Use Case: MitmProxy Passive Mode

The guide includes a complete pattern for transparent caching:
1. First request → Fetch from origin, save to cache, return original
2. Subsequent requests → Load from cache, optionally transform

## Source

- **Version**: v1.4.53
- **Package**: `mgraph_ai_service_html_graph`
- **Date**: January 20, 2026
- **Target Audience**: LLM sessions and developers

---

*Generated for NotebookLM content pipeline*
