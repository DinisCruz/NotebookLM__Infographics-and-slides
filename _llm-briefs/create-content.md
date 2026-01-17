# LLM Brief: Creating CONTENT.md Files

> Instructions for creating CONTENT.md semantic index files in the NotebookLM Infographics & Slides repository

---

## Purpose

CONTENT.md files provide a searchable, semantic index of each content piece. They serve two audiences:

1. **Humans**: Quick summary, key concepts, tags for finding relevant content
2. **Machines**: Structured metadata and optional semantic graph for knowledge integration

---

## Design Principles

1. **Human-readable content first** - Summary, concepts, and tags at the top
2. **Semantic graph optional** - Hidden in collapsible section at bottom
3. **Searchable** - Tags and search phrases enable discovery
4. **Consistent structure** - Same sections in same order across all files

---

## File Structure

```markdown
# Title of Content

> *Semantic content index for search, discovery, and knowledge graph integration*

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

## Semantic Graph

<details>
<summary>Click to expand graph structure (for programmatic consumption)</summary>

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

### Key Concepts

- 4-6 bullet points
- Bold the concept name, then explain in one sentence
- These are the ideas someone should remember
- Use terminology from the source document

### Core Arguments

- 4-6 numbered points
- These are the logical flow of the content
- Each point should be a complete statement
- Order them as they build on each other

### Key Quotes

- 2-4 memorable quotes from the source
- Choose quotes that:
  - Capture the main thesis
  - Are memorable/shareable
  - Contain actionable insight
- Use blockquote format (`>`)

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

Use these relationship types:
- `related_to` - General relationship
- `extends` - Builds upon another piece
- `contrasts_with` - Offers alternative view
- `part_of` - Belongs to larger work
- `uses` - Depends on framework/tool
- `applied_in` - Real-world application
- `references` - Cites external source

---

## Semantic Graph Section

The graph section is **optional but encouraged** for complex content. It should be wrapped in a collapsible `<details>` tag.

### Structure

```markdown
<details>
<summary>Click to expand graph structure (for programmatic consumption)</summary>

### Ontology

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

```
root_category
├── subcategory1
│   ├── item1
│   └── item2
└── subcategory2
    └── item3
```

### Graph

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

### Graph Guidelines

1. **Use readable IDs** - `pdd`, `green_bar`, not `n1`, `n2`
2. **Keep it focused** - 8-15 nodes maximum
3. **Show key relationships** - Don't try to capture everything
4. **Match content structure** - Nodes should map to Key Concepts

---

## Content Type Templates

### For Methodologies (PDD, TDD, etc.)

Emphasize:
- Core Arguments (the reasoning)
- Key Concepts (the practices)
- Tags: include methodology name, domain, alternatives

### For Technical Guides

Emphasize:
- Key Concepts (the capabilities)
- Code/implementation aspects in summary
- Tags: include framework names, languages

### For Analysis/Opinion Pieces

Emphasize:
- Core Arguments (the thesis)
- Key Quotes (memorable phrases)
- Tags: include the problem domain

### For Case Studies

Emphasize:
- Summary (what happened, outcome)
- Key Quotes (lessons learned)
- Related Content (other cases)

---

## Reading Source Documents

To create a CONTENT.md, you need to read the source. Priority order:

1. **PDF source document** - The original research/article
2. **Markdown source** - If provided instead of PDF
3. **Slide deck** - Can supplement if source is thin
4. **Infographic** - Usually summarizes key points visually

Extract:
- Main thesis/argument
- Key terminology and concepts
- Memorable quotes
- Logical flow of arguments

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
- [ ] Semantic Graph (if included) uses readable IDs
- [ ] Human-readable sections come before graph section

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
