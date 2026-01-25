# (20 Jan) - Semantic Graph-Powered Podcast Recommender

[Home](../../../../README.md) > [LinkedIn Published](../../../README.md) > [LinkedIn Published](../../README.md) > [Cyber Security and Business](../README.md) > Semantic Graph-Powered Podcast Recommender

---

## Overview

White paper and supporting materials on semantic knowledge graph-powered podcast recommendation systems. The system transforms unstructured podcast content into structured knowledge graphs, enabling highly personalized and explainable recommendations through graph traversal rather than opaque vector similarity.

## LinkedIn Posts

| Post | URL |
|------|-----|
| Infographic post | [How I would create a podcast recommender](https://www.linkedin.com/posts/diniscruz_here-is-how-i-would-create-a-podcast-recommender-activity-7419405617867415552-Ls6n/) |
| Slide deck post | [Semantic Podcasts: From Black Box to Glass Box](https://www.linkedin.com/posts/diniscruz_semantic-podcasts-from-black-box-toglass-activity-7419407375717048320-moZY/) |

## Contents

| File | Description |
|------|-------------|
| `Semantic Graph-Powered Podcast Recommender.pdf` | Full white paper (12 pages) covering architecture, implementation, and use cases |
| `20 Jan - Semantic Podcast Recommender Workflow.jpg` | Workflow diagram showing the system architecture |
| `20 Jan - From_Black_Box_to_Glass_Box.pdf` | Infographic on explainable AI recommendations |
| `linkedIn post to infograph.webloc` | Bookmark to LinkedIn infographic post |
| `linkedin post to slide deck.webloc` | Bookmark to LinkedIn slide deck post |
| `CONTENT.md` | Semantic Knowledge Graph metadata for search and discovery |

## Key Topics

- **Knowledge Graph Construction**: LLM-powered extraction of entities and relationships from podcast descriptions
- **Persona Graphs**: User interest representation using the same semantic vocabulary as content
- **Graph-Based Recommendations**: Matching via graph traversal instead of vector similarity
- **Explainable AI**: Every recommendation traceable to specific graph connections
- **Serverless Architecture**: Cloud storage (S3) as backbone with full provenance tracking

## Architecture Highlights

The system operates in two phases:

1. **Offline Ingestion**: RSS feed → Parse XML → Extract episodes → LLM analysis → Semantic graph storage
2. **Online Recommendation**: User persona graph → Graph matching → Ranked results with explanations

## Source

- **Authors**: Dinis Cruz and ChatGPT Deep Research
- **Date**: January 2026
- **Platform**: MyFeeds.ai architecture
- **Published**: 20 January 2026

---

*Published to LinkedIn*
