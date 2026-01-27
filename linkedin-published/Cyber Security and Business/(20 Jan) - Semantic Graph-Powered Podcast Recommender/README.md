# Semantic Graph-Powered Podcast Recommender

[🏠 Home](../../../../README.md) / [LinkedIn Published](../../README.md) / [Cyber Security and Business](../README.md) / **Semantic Graph-Powered Podcast Recommender**

> A white paper on semantic knowledge graph-powered podcast recommendation systems that transform unstructured podcast content into structured knowledge graphs, enabling highly personalized and explainable recommendations through graph traversal rather than opaque vector similarity.

| 📄 Source | 🖼️ Infographic | 📑 Slides |
|-----------|----------------|-----------|
| [White Paper](./Semantic%20Graph-Powered%20Podcast%20Recommender.pdf) | [Workflow Diagram](./20%20Jan%20-%20Semantic%20Podcast%20Recommender%20Workflow.jpg) | [From Black Box to Glass Box (PDF)](./20%20Jan%20-%20From_Black_Box_to_Glass_Box.pdf) |

> Generated with [Google NotebookLM](https://notebooklm.google.com/) — Source document → Infographic → Slide deck

---

## 🖼️ Infographic

> **Semantic Podcast Recommender Workflow** — Visual architecture showing the two-phase system design

![Semantic Podcast Recommender Workflow](./20%20Jan%20-%20Semantic%20Podcast%20Recommender%20Workflow.jpg)

---

## 📑 Slide Deck

> **From Black Box to Glass Box** — NotebookLM-generated presentation on explainable AI recommendations

[![All Slides](./slides_mosaic.png)](./20%20Jan%20-%20From_Black_Box_to_Glass_Box.pdf)

*Click image to open the slide deck*

---

## 🧠 Semantic Knowledge Graph

> **Machine-readable metadata** — Structured content for search, discovery, and graph database integration

This document includes a comprehensive [SEMANTIC-GRAPH.md](./SEMANTIC-GRAPH.md) file containing:

| Section | Description |
|---------|-------------|
| **Summary** | Knowledge graph-powered recommendation concept |
| **Key Concepts** | 6 core concepts: knowledge graphs, persona graphs, LLM construction |
| **Core Arguments** | 6 strategic arguments for graph-based recommendations |
| **Key Quotes** | 4 memorable quotes from the white paper |
| **Mermaid Diagrams** | Two-phase architecture, knowledge graph vs vector, persona matching |
| **Ontology** | Node types and predicates tables |
| **Taxonomy** | Mindmap and ASCII tree of system structure |
| **Knowledge Graph** | Visual mermaid + Nodes table + Edges table |
| **Cypher Import** | Ready-to-import Neo4j graph database queries |

[📖 View SEMANTIC-GRAPH.md](./SEMANTIC-GRAPH.md)

---

## Source Information

| Field | Value |
|-------|-------|
| **Authors** | Dinis Cruz, ChatGPT Deep Research |
| **Date** | January 2026 |
| **Platform** | MyFeeds.ai Architecture |
| **Content Type** | White Paper / Technical Architecture |
| **Domain** | AI/ML / Recommendation Systems |
| **Source Format** | PDF (12 pages) |
| **License** | CC BY 4.0 |

---

## LinkedIn Posts

| Post | URL |
|------|-----|
| Infographic Post | [How I would create a podcast recommender](https://www.linkedin.com/posts/diniscruz_here-is-how-i-would-create-a-podcast-recommender-activity-7419405617867415552-Ls6n/) |
| Slide Deck Post | [Semantic Podcasts: From Black Box to Glass Box](https://www.linkedin.com/posts/diniscruz_semantic-podcasts-from-black-box-toglass-activity-7419407375717048320-moZY/) |

---

## Key Topics

- **Knowledge Graph vs Vector Search**: Graph-based strategies store knowledge as explicit nodes and relationships aligned with human reasoning
- **Two-Phase Architecture**: Offline ingestion phase and online recommendation phase
- **Persona Graphs**: User interests represented as semantic graphs using the same vocabulary as content
- **LLM-Powered Graph Construction**: Large Language Models analyze episode descriptions to extract entities and relationships
- **Segment-Level Recommendations**: Identify and recommend specific segments within episodes
- **Serverless Cache-Centric Architecture**: JSON files in cloud storage with full provenance tracking

---

## Architecture Overview

The system operates in two phases:

| Phase | Components | Output |
|-------|------------|--------|
| **Offline Ingestion** | RSS Fetch → XML Parse → Episode Extract → LLM Analysis → Graph Storage | Episode Semantic Graphs |
| **Online Recommendation** | Persona Graph → Graph Matching → Explanation Generation | Ranked Results with Explanations |

---

## Contents

| File | Description |
|------|-------------|
| [Semantic Graph-Powered Podcast Recommender.pdf](./Semantic%20Graph-Powered%20Podcast%20Recommender.pdf) | Full white paper (12 pages) |
| [20 Jan - Semantic Podcast Recommender Workflow.jpg](./20%20Jan%20-%20Semantic%20Podcast%20Recommender%20Workflow.jpg) | Workflow diagram / infographic |
| [20 Jan - From_Black_Box_to_Glass_Box.pdf](./20%20Jan%20-%20From_Black_Box_to_Glass_Box.pdf) | Slide deck on explainable AI |
| [slides_mosaic.png](./slides_mosaic.png) | Slide deck mosaic preview |
| [SEMANTIC-GRAPH.md](./SEMANTIC-GRAPH.md) | Semantic Knowledge Graph metadata |
| [CONTENT.md](./CONTENT.md) | Original content extraction |

---

| ← Previous | Home | Next → |
|------------|------|--------|
| [Back to Cyber Security & Business](../README.md) | [🏠 Home](../../../../README.md) | - |

---

*Generated for NotebookLM content pipeline*
