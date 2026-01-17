# Using Databases as Data Projections, Not Primary Data Stores

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This document presents a paradigm shift in data architecture: instead of treating databases as the single source of truth, use them as disposable, purpose-built query layers (projections) on top of durable storage. Raw data lives in scalable storage (data lakes, event logs), and specialized databases are populated from that source for specific query needs - enabling cost efficiency, polyglot persistence, and true reproducibility.

---

## Key Concepts

- **Databases as Projections**: Databases are cached views derived from a single source of truth, not the master store
- **Single Source of Truth (SSOT)**: Raw data stored in scalable storage (S3, event logs, data lakes) - the authoritative source
- **Polyglot Persistence**: Multiple specialized databases (SQL, graph, search) each optimized for specific access patterns
- **Ephemeral Databases**: Spin up database instances when needed, tear down after use - no always-on servers required
- **Database Mesh**: Domain teams owning their own optimized database projections from common raw data

---

## Core Arguments

1. Traditional databases become monolithic, stateful repositories that are costly to scale and maintain
2. Much database cost is incurred when idle - an always-on server running 24/7 even when not needed
3. If the database is lost, it can be recreated from the source - the database becomes a cache, not the source
4. Netflix's Unified Data Architecture embodies "model once, represent everywhere" philosophy
5. Event sourcing uses polyglot read models: the event log is truth, multiple databases are projections
6. Companies that successfully leverage this paradigm can model data once and use it everywhere

---

## Key Quotes

> "Pay for processing only when it runs. No costs incurred when the database is idle (since it isn't running at all)"

> "If a read model is lost or corrupted, simply replay the source data or events to rebuild it - no special backup restore needed"

> "Treat your data as a first-class asset independent of any particular database technology"

---

## Tags

`data-projections` `database-architecture` `event-sourcing` `cqrs` `polyglot-persistence` `data-lake` `ephemeral-database` `database-mesh` `serverless-analytics` `single-source-of-truth`

---

## Search Phrases

- "databases as data projections"
- "ephemeral database instances"
- "event sourcing polyglot"
- "database as cache"
- "single source of truth architecture"
- "serverless database pattern"
- "model once represent everywhere"
- "disposable database infrastructure"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Architecture / Pattern |
| **Domain** | Software Development, Data Architecture |
| **Sub-domain** | Database Design, Event Sourcing |
| **Author** | Dinis Cruz, ChatGPT Deep Research |
| **Date Created** | 2 Jan 2026 |
| **Source Format** | PDF Research Paper |
| **Derived Assets** | Infographic, Slide Deck |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `related_to` | Event Sourcing Pattern (AWS Prescriptive Guidance) |
| `related_to` | Netflix Unified Data Architecture |
| `uses` | Ephemeral Neo4j Instances |
| `supports` | [Ephemeral Cloud Environments](../../Cyber%20Security%20and%20Business/(2%20Jan)%20-%20Ephemeral%20Cloud%20Environments%20and%20GenCloudBCP_%20A%20New%20Paradigm%20for%20Resilience%20and%20Disaster%20Recovery/) |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `paradigm` | Architectural approach |
| `pattern` | Design pattern |
| `benefit` | Positive outcome |
| `technology` | Implementation technology |
| `challenge` | Consideration or difficulty |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `enables` | `enabled_by` | Paradigm makes benefit possible |
| `implements` | `implemented_by` | Technology realizes pattern |
| `addresses` | `addressed_by` | Paradigm handles challenge |
| `uses` | `used_by` | Pattern leverages technology |

### Taxonomy

```
data_projection_architecture (root)
├── paradigm
│   ├── databases_as_projections
│   └── single_source_of_truth
├── pattern
│   ├── event_sourcing
│   ├── cqrs
│   ├── polyglot_persistence
│   └── database_mesh
├── benefit
│   ├── cost_efficiency
│   ├── scalability
│   ├── resilience
│   └── domain_autonomy
├── technology
│   ├── data_lake
│   ├── serverless_analytics
│   └── ephemeral_databases
└── challenge
    ├── eventual_consistency
    ├── pipeline_complexity
    └── data_duplication
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `db_as_projection` | `paradigm` | Databases as Projections |
| `ssot` | `paradigm` | Single Source of Truth |
| `event_sourcing` | `pattern` | Event Sourcing |
| `cqrs` | `pattern` | CQRS |
| `polyglot` | `pattern` | Polyglot Persistence |
| `cost_efficiency` | `benefit` | Cost Efficiency |
| `scalability` | `benefit` | Scalability & Elasticity |
| `resilience` | `benefit` | Resilience & Reproducibility |
| `data_lake` | `technology` | Data Lake Storage |
| `ephemeral_db` | `technology` | Ephemeral Databases |
| `eventual_consistency` | `challenge` | Eventual Consistency |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `db_as_projection` | `uses` | `ssot` |
| `db_as_projection` | `enables` | `cost_efficiency` |
| `db_as_projection` | `enables` | `scalability` |
| `db_as_projection` | `enables` | `resilience` |
| `event_sourcing` | `implements` | `db_as_projection` |
| `cqrs` | `implements` | `db_as_projection` |
| `polyglot` | `implements` | `db_as_projection` |
| `data_lake` | `supports` | `ssot` |
| `ephemeral_db` | `supports` | `db_as_projection` |
| `db_as_projection` | `addresses` | `eventual_consistency` |

</details>
