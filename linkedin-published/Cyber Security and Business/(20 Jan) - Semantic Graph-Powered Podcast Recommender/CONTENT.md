# Semantic Graph-Powered Podcast Recommender

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

A semantic knowledge graph-powered podcast recommender system that transforms unstructured podcast content into structured knowledge graphs, enabling highly personalized and explainable recommendations. The system uses LLMs to automatically generate semantic graphs from episode descriptions, represents user preferences as persona graphs using the same vocabulary, and matches users to content via graph traversal rather than opaque vector similarity. Built on a serverless architecture with full provenance tracking, every recommendation is traceable to specific graph connections.

---

## Key Concepts

- **Knowledge Graph vs Vector Search**: Graph-based strategies store knowledge as explicit nodes and relationships aligned with human reasoning, avoiding the "interpretability bottleneck" of vector-based semantic search where recommendations are based on opaque high-dimensional math.

- **Two-Phase Architecture**: Offline ingestion phase (RSS fetch → XML parse → episode extraction → LLM analysis → graph storage) and online recommendation phase (persona graph → graph matching → explainable results).

- **Persona Graphs**: User interests represented as semantic graphs using the same vocabulary as content graphs — nodes for topics, people, entities with weights indicating preference strength.

- **LLM-Powered Graph Construction**: Large Language Models analyze episode descriptions to extract entities (topics, people, organizations) and relationships, outputting structured JSON graphs that are stored with full provenance.

- **Segment-Level Recommendations**: Beyond episode-level matching, the system can identify and recommend specific segments within episodes (e.g., "Episode X – Segment 2 (10:00–20:00): discussion on Knowledge Graphs").

- **Serverless Cache-Centric Architecture**: All data stored as JSON files in cloud storage (S3) with MemoryFS abstraction, enabling versioning, reproducibility, and full provenance tracking for every transformation.

---

## Core Arguments

1. Traditional recommendation methods (collaborative filtering, keyword search) struggle to capture the rich semantic relationships within podcast content — knowledge graphs make these explicit and queryable.

2. Vector-based semantic search introduces an "interpretability bottleneck" that hinders traceability — graph-based recommendations can explain exactly why each suggestion was made via graph path tracing.

3. Spotify reported double-digit improvements in audiobook engagement after deploying graph-powered recommendations, demonstrating that this approach works at scale.

4. LLM-generated knowledge graphs require architectural constraints (defined vocabularies, validation rules, provenance tracking) to mitigate hallucinations and ensure trustworthiness.

5. The serverless, cache-centric architecture enables full provenance and determinism — every recommendation can be traced back through cached files to the original RSS input.

6. Treating content ingestion as graph-building and recommendations as graph queries provides control and visibility crucial for trust-dependent AI applications.

---

## Key Quotes

> "In AI systems that assist with complex tasks – from enterprise Q&A to personalized feeds – properties like provenance, determinism, and explainability are not luxuries but requirements."

> "By representing podcast information as a knowledge graph, we can trace exactly why a recommendation was made and ensure each connection is grounded in the source content."

> "We don't require the initial graph to be perfect. We prefer to get a decent first draft and then improve it over time."

> "The knowledge graph is not just a product of AI, but a verified, evolving dataset that can be trusted for downstream use."

---

## Tags

`knowledge-graph` `podcast-recommendations` `semantic-search` `explainable-ai` `llm-graph-construction` `persona-graphs` `serverless-architecture` `provenance-tracking` `graph-traversal` `myfeeds-ai` `content-personalization` `graph-matching` `rss-processing` `trustworthy-ai`

---

## Search Phrases

- "knowledge graph podcast recommendations"
- "semantic graph-powered recommender system"
- "explainable AI recommendations"
- "LLM knowledge graph construction"
- "persona graph user modeling"
- "graph-based vs vector-based recommendations"
- "serverless knowledge graph architecture"
- "podcast content semantic analysis"
- "provenance tracking AI systems"
- "segment-level podcast recommendations"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | White Paper / Technical Architecture |
| **Domain** | AI/ML / Recommendation Systems |
| **Sub-domain** | Knowledge Graphs / Personalization |
| **Authors** | Dinis Cruz, ChatGPT Deep Research |
| **Date Created** | January 2026 |
| **Source Format** | PDF (12 pages) |
| **Derived Assets** | Infographic, Workflow Diagram |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `inspired_by` | MyFeeds.ai Platform Architecture |
| `references` | Spotify Graph-Based Audiobook Recommendations |
| `contrasts_with` | Vector Database / Embedding-based Search |
| `related_to` | LLM-Generated Knowledge Graphs |
| `related_to` | Trustworthy AI Architecture Patterns |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `system` | The overall recommendation system |
| `phase` | A major processing phase |
| `component` | A system component or service |
| `data_artifact` | A data structure or stored artifact |
| `capability` | A feature or capability |
| `principle` | A design principle |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `contains` | `part_of` | System contains phases/components |
| `produces` | `produced_by` | Component produces data artifact |
| `enables` | `enabled_by` | Component enables capability |
| `follows` | `precedes` | Phase follows another phase |
| `enforces` | `enforced_by` | System enforces principle |

### Taxonomy

```
semantic_podcast_recommender
├── ingestion_phase
│   ├── rss_fetcher
│   ├── xml_parser
│   ├── episode_extractor
│   ├── llm_graph_generator
│   └── graph_indexer
├── recommendation_phase
│   ├── persona_graph_builder
│   ├── graph_matcher
│   └── explanation_generator
├── data_artifacts
│   ├── episode_semantic_graph
│   ├── persona_graph
│   ├── topic_to_episode_index
│   └── recommendation_subgraph
├── architecture
│   ├── serverless_services
│   ├── cache_database
│   └── memoryfs_abstraction
└── principles
    ├── provenance_tracking
    ├── explainability
    └── iterative_refinement
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `podcast_recommender` | `system` | Semantic Podcast Recommender |
| `ingestion_phase` | `phase` | Offline Ingestion Phase |
| `recommendation_phase` | `phase` | Online Recommendation Phase |
| `llm_service` | `component` | LLM Graph Generation Service |
| `cache_service` | `component` | Cache Database Service |
| `episode_graph` | `data_artifact` | Episode Semantic Graph |
| `persona_graph` | `data_artifact` | User Persona Graph |
| `explainability` | `capability` | Explainable Recommendations |
| `segment_recommendations` | `capability` | Segment-Level Recommendations |
| `provenance` | `principle` | Full Provenance Tracking |
| `graph_matching` | `capability` | Graph-Based Matching |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `podcast_recommender` | `contains` | `ingestion_phase` |
| `podcast_recommender` | `contains` | `recommendation_phase` |
| `ingestion_phase` | `precedes` | `recommendation_phase` |
| `llm_service` | `produces` | `episode_graph` |
| `recommendation_phase` | `uses` | `persona_graph` |
| `graph_matching` | `enables` | `explainability` |
| `podcast_recommender` | `enforces` | `provenance` |
| `cache_service` | `enables` | `provenance` |
| `episode_graph` | `enables` | `segment_recommendations` |

</details>
