# Html_MGraph Multi-Graph Architecture - LLM Usage Brief (v1.4.54)

[Home](../../../../README.md) > [By Topic](../../../README.md) > [Dev Briefs](../../README.md) > [HTML Graph Service](../README.md) > Html_MGraph Multi-Graph Architecture

---

## Overview

Comprehensive guide for LLMs and developers on using the Html_MGraph multi-graph system for HTML manipulation. Instead of representing HTML as a single cluttered graph, Html_MGraph separates concerns into 5 purpose-specific graphs that share node identities, enabling powerful manipulation, analysis, and perfect round-trip reconstruction.

## Contents

| File | Description |
|------|-------------|
| `v1.4.54__html-mgraph-multi-graph__llm-brief.md` | Full technical usage guide |
| `CONTENT.md` | Semantic Knowledge Graph metadata for search and discovery |

## The 5 Graph System

| Graph | Purpose | Contains |
|-------|---------|----------|
| **Html_MGraph__Document** | Orchestrator | References to all other graphs, `<html>` element |
| **Html_MGraph__Head** | Document metadata | `<head>` structure + text (title content, etc.) |
| **Html_MGraph__Body** | Visible content | `<body>` structure + text nodes |
| **Html_MGraph__Attributes** | Element properties | Tag names + attributes for ALL elements |
| **Html_MGraph__Scripts** | JavaScript | `<script>` elements and their content |
| **Html_MGraph__Styles** | CSS | `<style>` elements and `<link>` references |

## Key Design Principles

1. **Separation of Concerns**: Each graph has a focused responsibility
2. **Shared Identity**: Same `Node_Id` used across graphs enables instant cross-graph lookups
3. **Perfect Round-Trip**: HTML → Graphs → HTML preserves everything
4. **Query What You Need**: Work with exactly the aspects needed
5. **Type-Safe Foundation**: Built on MGraph-DB with full runtime type checking

## Quick Start

```python
from mgraph_ai_service_html_graph.service.html_mgraph.Html_MGraph import Html_MGraph

# Parse HTML into multi-graph structure
html_mgraph = Html_MGraph.from_html(html)

# Query elements
tag = html_mgraph.get_tag(node_id)
attrs = html_mgraph.get_attributes(node_id)

# Export back to HTML (perfect round-trip)
html_output = html_mgraph.to_html()
```

## Source

- **Version**: v1.4.54
- **Package**: `mgraph_ai_service_html_graph.service.html_mgraph`
- **Repo**: https://github.com/the-cyber-boardroom/MGraph-AI__Service__Html__Graph
- **Depends On**: MGraph-DB (owasp-sbot/MGraph-DB)

---

*Generated for NotebookLM content pipeline*
