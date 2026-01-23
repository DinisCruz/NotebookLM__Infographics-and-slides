# HTML Graph Cache Mode Integration Guide (v0.8.11)

[Home](../../../../../README.md) > [By Topic](../../../../README.md) > [Dev Briefs](../../README.md) > [HTML Graph Service](../README.md) > v0.8.11 Cache Mode Integration

---

## Overview

Technical integration guide for adding `mitm-mode=cache` to the MitmProxy service. The cache mode provides passive HTML caching via HTML Graph without transformation — checking cache on request, storing HTML on response miss, and serving cached content on hits. This enables page caching for offline modification workflows.

## Contents

| File | Description |
|------|-------------|
| `v0.8.11__integration-guide__html-graph-cache-mode.md` | Full integration guide with code samples |
| `21 Jan - Graph_Cache_Mode_Integration.pdf` | Extended PDF documentation |
| `21 Jan - HTML Graph Cache Mode Integration.jpg` | Visual flow diagram |

## Key Topics

- **Cache Mode Flow**: Request phase cache check → upstream fetch on miss → response phase storage
- **Enum Extension**: Adding CACHE value to `Enum__HTML__Transformation_Mode`
- **Handler Implementation**: `HTML_Graph__Cache__Handler` with `check_cache()` and `store_html()` methods
- **Service Modifications**: Changes to `Proxy__Request__Service` and `Proxy__Response__Service`
- **Response Headers**: Cache hit/miss indicators (`x-cache-source`, `x-html-graph-stored`)

## Cache Mode Flow

| Phase | Action |
|-------|--------|
| Request | Parse cookies, detect `mitm-mode=cache` |
| Request | Check cache via `html_graph_handler.check_cache()` |
| Cache HIT | Return cached HTML immediately (short-circuit) |
| Cache MISS | Continue to upstream server |
| Response | Store HTML via `html_graph_handler.store_html()` |
| Response | Add `x-html-graph-*` headers, return original HTML |

## Files to Create/Modify

| File | Purpose |
|------|---------|
| `HTML_Graph__Cache__Handler.py` | Main cache handler (NEW) |
| `Enum__HTML__Transformation_Mode.py` | Add CACHE enum value |
| `Proxy__Request__Service.py` | Add cache check |
| `Proxy__Response__Service.py` | Add cache store |

## Source

- **Version**: v0.8.11
- **Date**: January 2026
- **Service**: MitmProxy Service / HTML Graph Service

---

*Generated for NotebookLM content pipeline*
