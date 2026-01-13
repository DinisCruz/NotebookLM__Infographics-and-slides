# Business Debrief: HTML Content Processing Pipeline

**Date**: January 12, 2026  
**Audience**: Business Partners  
**Topic**: Web Content Capture, Storage, and Transformation System

---

## What We've Built

We have created an **end-to-end pipeline** that captures web pages, stores them in a structured cache, and applies intelligent transformations to extract and filter content. This forms the foundation for automated content processing at scale.

---

## The Pipeline at a Glance

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│              │      │              │      │              │      │              │
│  Live Web    │ ───► │   Capture    │ ───► │    Cache     │ ───► │  Transform   │
│   Pages      │      │   & Parse    │      │   Storage    │      │   & Filter   │
│              │      │              │      │              │      │              │
└──────────────┘      └──────────────┘      └──────────────┘      └──────────────┘
                                                   │
                                                   ▼
                                          ┌──────────────┐
                                          │              │
                                          │ Cache Browser│
                                          │   Console    │
                                          │              │
                                          └──────────────┘
                                    cache.dev.mgraph.ai/console
```

---

## How It Works

### Step 1: Web Page Capture

We fetch HTML from any public URL:
- **NPR** (text.npr.org)
- **Paul Graham's Essays** (paulgraham.com)
- **The Intercept** (theintercept.com)
- **Joel on Software** (joelonsoftware.com)
- **BBC Sport** (bbc.com/sport)
- And any other web page

The system captures the complete HTML including metadata, headers, and timing information.

### Step 2: Structured Storage (The LETS Pipeline)

Each captured page is processed through multiple layers and stored in our cache service:

| Layer | What's Stored | Purpose |
|-------|---------------|---------|
| **L0** | URL metadata, fetch timing | Track what was captured and when |
| **L1** | Raw HTML | Original page exactly as received |
| **L2** | Parsed HTML structure | Structured representation with element IDs |
| **L3** | Graph document (MGraph) | Relationships between all page elements |
| **L4** | Reconstructed HTML | Verify round-trip accuracy |
| **L5** | Transformed versions | Filtered and processed content |

### Step 3: Content Transformations

Once stored, we can apply various transformations without re-fetching:

| Transformation | Output | Use Case |
|----------------|--------|----------|
| **Original Reconstructed** | Full page from graph | Baseline verification |
| **Body Only** | Just main content | Remove navigation/chrome |
| **Phase E_0 Filtered** | Intelligently filtered content | Content extraction |
| **Text Content** | All text blocks | Text analysis, summarization |

---

## The Cache Browser Console

All stored data is accessible through our web-based **Cache Browser** at:

**https://cache.dev.mgraph.ai/console/v0/v0.1/v0.1.3/index.html**

### What You Can Do:

1. **Browse** - Navigate the folder structure of cached sites
2. **Preview** - See rendered HTML directly in the browser
3. **Inspect** - View raw JSON, formatted data, or source code
4. **Compare** - Switch between L1 (original) and L5 (transformed) versions

### Screenshot: NPR with Phase E_0 Filtering

The attached screenshot shows `text.npr.org` after Phase E_0 filtering:

- **Left panel**: File browser showing the layer structure (L0 → L5)
- **Center panel**: The filtered HTML source with preserved CSS styling
- **Right panel**: Live preview of the filtered content

The filtered version shows NPR headlines:
- "These are the 2026 Golden Globe winners"
- "Nationwide anti-ICE protests call for accountability..."
- "Iran warns US troops and Israel will be targets..."
- And more news items with navigation links

---

## What is Phase E_0 Filtering?

Phase E_0 is our **content filtering engine** that:

1. **Extracts** all text content from a page
2. **Groups** text by its parent element (paragraphs, headings, links)
3. **Scores** each text block for relevance
4. **Removes** low-scoring content
5. **Preserves** page structure and styling

### The Result

- Original page structure maintained
- CSS styling preserved
- Irrelevant content removed
- Clean, focused output

This is the foundation for:
- **LLM context preparation** - Feed clean content to AI models
- **Content summarization** - Extract key information
- **Data extraction** - Pull structured data from web pages
- **Archival** - Store cleaned versions of pages

---

## Current Test Sites

| Site | Content Type | Status |
|------|--------------|--------|
| text.npr.org | News headlines | ✅ Captured & Transformed |
| paulgraham.com | Essays/Blog | ✅ Captured & Transformed |
| theintercept.com | Investigative journalism | ✅ Captured & Transformed |
| joelonsoftware.com | Tech blog | ✅ Captured & Transformed |
| bbc.com/sport | Sports news | ✅ Captured & Transformed |
| docs.diniscruz.ai | Documentation | ✅ Captured & Transformed |

---

## Business Value

### Today
- **Automated capture** of web content
- **Structured storage** enabling multiple views
- **Content filtering** for cleaner data
- **Visual inspection** through Cache Browser

### Next Steps
- **ML-based filtering** - Train models on content relevance
- **LLM integration** - Use AI for intelligent content decisions
- **Scheduled capture** - Automatic periodic snapshots
- **API access** - Programmatic access to cached/transformed content

---

## Technical Infrastructure

| Component | Location | Purpose |
|-----------|----------|---------|
| Cache Service | cache.dev.mgraph.ai | Storage backend |
| Cache Browser | /console/v0/v0.1/v0.1.3/ | Visual inspection UI |
| LETS Pipeline | Python scripts | Capture & processing |
| Phase E_0 Engine | Python module | Content filtering |

---

## Summary

We have built a **complete pipeline** for capturing, storing, and transforming web content:

1. ✅ **Capture** any public web page
2. ✅ **Store** in structured, queryable cache
3. ✅ **Transform** with multiple processing options
4. ✅ **Filter** content intelligently with Phase E_0
5. ✅ **Visualize** through web-based Cache Browser

This infrastructure provides the foundation for automated content processing, AI-ready data preparation, and scalable web archival.

---

*For technical details, see the engineering debriefs in the project repository.*
