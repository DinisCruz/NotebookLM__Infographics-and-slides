# Semantic Graph-Powered Podcast Recommender

[Home](../../../README.md) > [By Topic](../../README.md) > [Cyber Security, Gen AI Projects and Business Topics](../README.md) > Semantic Graph-Powered Podcast Recommender

---

## Overview

This folder contains materials for a white paper on semantic knowledge graph-powered podcast recommendation systems. The system transforms unstructured podcast content into structured knowledge graphs, enabling highly personalized and explainable recommendations through graph traversal rather than opaque vector similarity.

## Contents

| File | Description |
|------|-------------|
| `Semantic Graph-Powered Podcast Recommender.pdf` | Full white paper (12 pages) covering architecture, implementation, and use cases |
| `20 Jan - Semantic Podcast Recommender Workflow.jpg` | Workflow diagram showing the system architecture |
| `20 Jan - From_Black_Box_to_Glass_Box.pdf` | Infographic on explainable AI recommendations |
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

## Use Cases

- Fine-grained episode recommendations based on actual content (not just genres)
- Segment-level recommendations (specific timestamps within episodes)
- Explainable recommendations ("Recommended because...")
- Cross-episode knowledge exploration

## Source

- **Authors**: Dinis Cruz and ChatGPT Deep Research
- **Date**: January 2026
- **Platform**: MyFeeds.ai architecture

---

*Generated for NotebookLM content pipeline*
