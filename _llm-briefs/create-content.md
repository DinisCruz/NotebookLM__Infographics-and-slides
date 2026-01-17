# LLM Brief: Creating CONTENT.md Files

> Instructions for creating CONTENT.md files - Semantic Knowledge Graph (SKG) serializations in markdown format

---

## Purpose

CONTENT.md files are **Semantic Knowledge Graph (SKG) documents serialized in markdown format**. They contain the same structured data you would store in a graph database like Neo4j, but in a human-readable text format.

They serve two audiences:

1. **Humans**: Quick summary, key concepts, tags for finding relevant content
2. **Machines**: Structured SKG data that can be parsed and loaded into graph databases (Neo4j, MGraph-DB, OSBot-Utils Semantic Graphs framework)

---

## What is a Semantic Knowledge Graph (SKG)?

An SKG consists of:

| Component | Purpose | In CONTENT.md | Neo4j Equivalent |
|-----------|---------|---------------|------------------|
| **Nodes** | Entities/concepts | Nodes table | `(:NodeType {id, name})` |
| **Edges** | Relationships | Edges table | `-[:PREDICATE]->` |
| **Ontology** | Valid types & predicates | Node Types + Predicates tables | Schema/constraints |
| **Taxonomy** | Category hierarchy | Tree structure | Hierarchical relationships |
| **Properties** | Metadata | Metadata table | Node properties |

The markdown format is a **serialization format** - like JSON or CSV for databases. The underlying structure IS a semantic knowledge graph.

---

## Design Principles

1. **Human-readable content first** - Summary, concepts, and tags at the top (the "projection")
2. **SKG structure at bottom** - Full graph in collapsible section for programmatic consumption
3. **Searchable** - Tags and search phrases enable discovery
4. **Importable** - Structure can be parsed and loaded into graph databases
5. **Consistent structure** - Same sections in same order across all files

---

## File Structure

```markdown
# Title of Content

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

[2-4 sentences explaining what this content is about and its main thesis]

---

## Key Concepts

- **Concept 1**: Brief explanation
- **Concept 2**: Brief explanation
- **Concept 3**: Brief explanation
[4-6 concepts]

---

## Core Arguments

1. First main argument or point
2. Second main argument or point
3. Third main argument or point
[4-6 numbered points]

---

## Key Quotes

> "Notable quote from the content"

> "Another memorable quote"

[2-4 quotes that capture the essence]

---

## Tags

`tag1` `tag2` `tag3` `tag4` `tag5` [etc.]

[10-15 single-word or hyphenated tags in backticks]

---

## Search Phrases

- "natural language search phrase"
- "another way someone might search"
- "question format works too"
[6-10 phrases]

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | [Methodology / Analysis / Technical Guide / Case Study / etc.] |
| **Domain** | [Software Development / AI Safety / etc.] |
| **Sub-domain** | [Testing Practices / etc.] |
| **Author** | [Name] |
| **Date Created** | [DD Mon YYYY] |
| **Source Format** | [PDF / Markdown / etc.] |
| **Derived Assets** | [Infographic, Slide Deck] |
| **License** | CC BY 4.0 |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `related_to` | [Link or description] |
| `extends` | [Link or description] |
| `contrasts_with` | [Link or description] |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

[Graph structure here - see below]

</details>
```

---

## Section Guidelines

### Summary

- 2-4 sentences maximum
- Answer: "What is this about?" and "Why does it matter?"
- No jargon without explanation
- Written for someone who hasn't read the source
- This is the **projection** of the SKG - human-readable view of the graph data

### Key Concepts

- 4-6 bullet points
- Bold the concept name, then explain in one sentence
- These are the ideas someone should remember
- Use terminology from the source document
- These map to **nodes** in the SKG

### Core Arguments

- 4-6 numbered points
- These are the logical flow of the content
- Each point should be a complete statement
- Order them as they build on each other
- These represent **edges** (logical relationships) in the SKG

### Key Quotes

- 2-4 memorable quotes from the source
- Choose quotes that:
  - Capture the main thesis
  - Are memorable/shareable
  - Contain actionable insight
- Use blockquote format (`>`)
- These are **node properties** in the SKG

### Tags

- 10-15 tags
- Single words or hyphenated phrases only
- Use backticks: `tag-name`
- Include:
  - Main topics
  - Technologies/frameworks mentioned
  - Methodology names
  - Domain terms
- All lowercase
- These enable **search/indexing** of the SKG

### Search Phrases

- 6-10 natural language phrases
- How would someone search for this content?
- Include:
  - Question formats ("how to...")
  - Problem statements ("alternative to...")
  - Specific terms people might use

### Metadata

Required fields:
- Content Type
- Domain
- Author
- Date Created
- License (always CC BY 4.0 for this repo)

Optional fields:
- Sub-domain
- Version
- Repository URL
- Source Format
- Derived Assets

### Related Content

Use these relationship types (these are **predicates** in SKG terms):
- `related_to` - General relationship
- `extends` - Builds upon another piece
- `contrasts_with` - Offers alternative view
- `part_of` - Belongs to larger work
- `uses` - Depends on framework/tool
- `applied_in` - Real-world application
- `references` - Cites external source

---

## Semantic Knowledge Graph Section

The SKG section contains the full graph structure for programmatic consumption. It should be wrapped in a collapsible `<details>` tag.

### Structure

```markdown
<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

The ontology defines the vocabulary - what node types and predicates are valid in this graph.

#### Node Types

| Ref | Description |
|-----|-------------|
| `type1` | What this type represents |
| `type2` | What this type represents |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `predicate1` | `inverse1` | What this relationship means |

### Taxonomy

The taxonomy defines the category hierarchy for node types.

```
root_category
├── subcategory1
│   ├── item1
│   └── item2
└── subcategory2
    └── item3
```

### Graph

The graph contains the actual nodes and edges (instance data).

#### Nodes

| ID | Type | Name |
|----|------|------|
| `readable_id` | `type1` | Human-Readable Name |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `node1` | `predicate` | `node2` |

</details>
```

### SKG Guidelines

1. **Use readable IDs** - `pdd`, `green_bar`, not `n1`, `n2` (these become node IDs in the graph DB)
2. **Keep it focused** - 8-15 nodes maximum per content piece
3. **Show key relationships** - Don't try to capture everything
4. **Match content structure** - Nodes should map to Key Concepts
5. **Predicates need inverses** - Every relationship should be traversable both ways
6. **Ontology constrains the graph** - Only use node types and predicates defined in the ontology

### Mapping to Graph Databases

| CONTENT.md Section | Neo4j | OSBot-Utils Semantic Graphs |
|--------------------|-------|----------------------------|
| Node Types | Labels | `Schema__Ontology__Node_Type` |
| Predicates | Relationship types | `Schema__Ontology__Predicate` |
| Nodes table | `CREATE (:Type {id, name})` | `Semantic_Graph__Builder.add_node()` |
| Edges table | `CREATE (a)-[:PRED]->(b)` | `Semantic_Graph__Builder.add_edge()` |
| Taxonomy | Hierarchical rels | `Schema__Taxonomy` |
| Metadata | Node properties | `Dict__Node_Properties` |

---

## Content Type Templates

### For Methodologies (PDD, TDD, etc.)

Emphasize:
- Core Arguments (the reasoning)
- Key Concepts (the practices)
- Tags: include methodology name, domain, alternatives
- SKG: methodology → includes → practices, methodology → addresses → challenges

### For Technical Guides

Emphasize:
- Key Concepts (the capabilities)
- Code/implementation aspects in summary
- Tags: include framework names, languages
- SKG: framework → provides → capabilities, class → implements → pattern

### For Analysis/Opinion Pieces

Emphasize:
- Core Arguments (the thesis)
- Key Quotes (memorable phrases)
- Tags: include the problem domain
- SKG: problem → causes → effect, solution → addresses → problem

### For Case Studies

Emphasize:
- Summary (what happened, outcome)
- Key Quotes (lessons learned)
- Related Content (other cases)
- SKG: incident → demonstrates → risk, practice → mitigates → risk

---

## Reading Source Documents

To create a CONTENT.md, you need to read the source. Priority order:

1. **PDF source document** - The original research/article
2. **Markdown source** - If provided instead of PDF
3. **Slide deck** - Can supplement if source is thin
4. **Infographic** - Usually summarizes key points visually

Extract:
- Main thesis/argument → Summary
- Key terminology and concepts → Key Concepts, Nodes
- Memorable quotes → Key Quotes
- Logical flow of arguments → Core Arguments, Edges
- Relationships between concepts → SKG structure

---

## Checklist

Before finalizing a CONTENT.md:

- [ ] Summary is 2-4 sentences and captures the essence
- [ ] Key Concepts has 4-6 bolded terms with explanations
- [ ] Core Arguments flows logically (4-6 points)
- [ ] Key Quotes are actual quotes from the source (2-4)
- [ ] Tags are lowercase, hyphenated, 10-15 total
- [ ] Search Phrases are natural language (6-10)
- [ ] Metadata has all required fields
- [ ] Related Content links are valid
- [ ] SKG section uses readable IDs
- [ ] SKG ontology defines all node types and predicates used
- [ ] SKG edges only use predicates defined in ontology
- [ ] Human-readable sections come before SKG section

---

## Example: Quick Reference

**Good Summary:**
> Pass-Driven Development (PDD) is a practical alternative to Test-Driven Development that starts with passing tests rather than failing ones. This "green bar first" approach reduces psychological friction and accelerates adoption while maintaining test-first discipline.

**Good Tags:**
> `testing` `tdd` `pdd` `test-driven-development` `software-engineering` `methodology` `green-bar`

**Good Search Phrases:**
> - "alternative to TDD"
> - "test-driven development problems"
> - "starting with passing tests"

**Good Node ID:**
> `green_bar_psychology` not `n6`

**Good Edge:**
> `pdd` → `addresses` → `tdd_adoption_difficulty`
