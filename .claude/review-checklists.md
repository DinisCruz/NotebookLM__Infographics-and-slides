# LLM Brief: Review Checklists

> Consolidated checklists for REVIEWER mode validation

---

## How to Use

1. Identify the content type being reviewed
2. Load the appropriate checklist below
3. Check each item literally (re-read the file, don't assume)
4. Mark: ✅ Pass | ⚠️ Warning | ❌ Fail
5. Document in Review Report (see `execution-modes.md`)

---

## SEMANTIC-GRAPH.md Checklist

| # | Check | Severity |
|---|-------|----------|
| 1 | Navigation links present at top (README, Infographic, Slides, Home) | Required |
| 2 | All navigation links use correct relative paths | Required |
| 3 | URLs are properly encoded (spaces → `%20`, parens → `%28/%29`) | Required |
| 4 | SKG tagline present after navigation | Required |
| 5 | Summary is 2-4 sentences (not more, not less) | Required |
| 6 | Key Concepts has 4-6 bolded terms with explanations | Required |
| 7 | Core Arguments has 4-6 numbered points | Required |
| 8 | Core Arguments flow logically | Warning |
| 9 | Key Quotes has 2-4 quotes from source | Warning |
| 10 | Tags section has 10-15 tags in backticks | Required |
| 11 | Tags are lowercase and hyphenated (no spaces) | Required |
| 12 | Search Phrases has 6-10 natural language phrases | Required |
| 13 | Domain-specific diagram present (flowchart or similar) | Required |
| 14 | Ontology present as classDiagram | Required |
| 15 | Taxonomy present as mindmap | Required |
| 16 | Knowledge graph present as `graph TB` | Required |
| 17 | All Mermaid diagrams render correctly (valid syntax) | Required |
| 18 | Mermaid uses consistent color conventions | Warning |
| 19 | Cypher export section present | Required |
| 20 | Cypher creates all nodes before relationships | Required |
| 21 | Cypher node IDs are readable (not `n1`, `n2`) | Warning |
| 22 | Cypher node IDs match Mermaid node IDs | Warning |
| 23 | Content accurately reflects source document | Required |
| 24 | Navigation link depth matches folder depth (e.g., `../../../../README.md` for 4 levels) | Required |
| 25 | File is in correct location (`linkedin-published/` not `by-topic/`) | Required |

### Quick Validation Commands

```bash
# Check if file exists
ls -la "path/to/SEMANTIC-GRAPH.md"

# Count sections (should see ## Summary, ## Key Concepts, etc.)
grep -c "^## " "path/to/SEMANTIC-GRAPH.md"

# Check for Mermaid blocks
grep -c '```mermaid' "path/to/SEMANTIC-GRAPH.md"

# Check for Cypher block
grep -c '```cypher' "path/to/SEMANTIC-GRAPH.md"
```

---

## README.md Checklist

| # | Check | Severity |
|---|-------|----------|
| 1 | Breadcrumb navigation present at top | Required |
| 2 | Breadcrumb links are correct and working | Required |
| 3 | Current folder name is bolded (not linked) | Required |
| 4 | Title (H1) present after navigation | Required |
| 5 | One-line description under title | Required |
| 6 | Quick links table present (Source, Infographic, Slides) | Required |
| 7 | Quick links use correct relative paths | Required |
| 8 | URLs are properly encoded | Required |
| 9 | NotebookLM attribution line present | Required |
| 10 | Infographic embedded with `![alt](./image)` | Required |
| 11 | Alt text is descriptive | Warning |
| 12 | Slide mosaic section present (if slides exist) | Required |
| 13 | Slide mosaic is clickable (links to PDF) | Required |
| 14 | Slide count mentioned in heading | Warning |
| 15 | Semantic Knowledge Graph section present | Required |
| 16 | SKG section links to SEMANTIC-GRAPH.md | Required |
| 17 | SKG section describes contents | Warning |
| 18 | All images actually exist in folder | Required |
| 19 | No broken links | Required |
| 20 | Download URL uses correct relative/raw GitHub path | Required |
| 21 | LinkedIn post links included (if webloc files exist) | Warning |
| 22 | Infographic variations documented with issue notes (if applicable) | Warning |
| 23 | Navigation link depth matches folder depth | Required |

### Quick Validation Commands

```bash
# Check if file exists
ls -la "path/to/README.md"

# List all files in folder (verify images exist)
ls -la "path/to/folder/"

# Check for embedded images
grep -E "!\[.*\]\(.*\)" "path/to/README.md"

# Check for links
grep -E "\[.*\]\(.*\)" "path/to/README.md"
```

---

## Slide Mosaic Checklist

| # | Check | Severity |
|---|-------|----------|
| 1 | `slides_mosaic.png` exists in folder | Required |
| 2 | Mosaic contains all slides from PDF | Required |
| 3 | Grid dimensions match slide count (4x3 for 12, 4x4 for 16, 3x3 for 9, etc.) | Warning |
| 4 | Individual slide thumbnails deleted (no `slide_thumb-*.png`) | Required |
| 5 | Image is not corrupted (can be viewed) | Required |
| 6 | README.md embeds the mosaic | Required |
| 7 | Mosaic is clickable (links to PDF) | Required |

### Quick Validation Commands

```bash
# Check mosaic exists
ls -la "path/to/slides_mosaic.png"

# Check for leftover thumbnails (should return nothing)
ls "path/to/folder/" | grep "slide_thumb"

# Count pages in source PDF (requires poppler-utils)
pdfinfo "path/to/slides.pdf" | grep Pages

# Check file size (should be reasonable, ~200KB-1MB)
du -h "path/to/slides_mosaic.png"
```

---

## Curated Guide Checklist

| # | Check | Severity |
|---|-------|----------|
| 1 | Folder exists in `curated-guides/` (for new collections only) | Required |
| 2 | Folder name is lowercase with hyphens | Required |
| 3 | README.md exists in guide folder | Required |
| 4 | Breadcrumb navigation correct | Required |
| 5 | Title and description present | Required |
| 6 | Collection Overview table present | Required |
| 7 | All links in table are correct and working | Required |
| 8 | Direct SEMANTIC-GRAPH.md links provided | Required |
| 9 | Links point to `linkedin-published/` (not `by-topic/`) | Required |
| 10 | Use Cases section present | Warning |
| 11 | Statistics section present | Warning |
| 12 | Parent `curated-guides/README.md` updated | Required |
| 13 | URLs properly encoded | Required |
| 14 | At least 3 related pieces included | Warning |

---

## File Movement / Publishing Checklist

Use this checklist when moving content from staging to published location.

| # | Check | Severity |
|---|-------|----------|
| 1 | Destination folder created in `linkedin-published/[Category]/` | Required |
| 2 | All files transferred (README, SEMANTIC-GRAPH, mosaic, images, PDFs, weblocs) | Required |
| 3 | README.md navigation updated for new location depth | Required |
| 4 | SEMANTIC-GRAPH.md navigation updated for new location depth | Required |
| 5 | Parent category README updated with new folder link | Required |
| 6 | Curated guides updated to point to published location (not by-topic) | Required |
| 7 | No orphaned files left in source (or intentional copies documented) | Warning |
| 8 | Download URLs updated to reflect new location | Required |

### Quick Validation Commands

```bash
# List files in destination
ls -la "linkedin-published/[Category]/[Folder]/"

# Check README navigation depth
head -5 "linkedin-published/[Category]/[Folder]/README.md"

# Find any broken links in curated guides
grep -r "by-topic" curated-guides/
```

---

## General Link Validation

For any file with links:

```bash
# Extract all markdown links and check each
grep -oE "\[.*?\]\(.*?\)" "file.md"

# Common encoding issues to check:
# - Spaces should be %20
# - ( should be %28
# - ) should be %29
# - ' should be %27
```

---

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| **Required** | Must pass for completion | Fix before marking done |
| **Warning** | Should pass, minor issue | Note in report, fix if time permits |
