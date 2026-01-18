[🏠 Home](../../../../README.md) / [By Topic](../../../) / [Dev Briefs](../../) / [Cache Service](../) / **v0.11.0 Storage Operations**

---

# Cache Service Storage Operations - LLM Technical Brief

Comprehensive technical reference for the MGraph Cache Service's two-tier storage model, covering entry management, data storage, hash-based deduplication, and dual reference indexing.

| 📄 Source | 🖼️ Infographic | 📊 Slides |
|---|---|---|
| [Source Doc](./v0.11.0__llm-brief__cache-service-storage-operations.md) | [View Image](./18%20Jan%20-%20MGraph%20Two-Tier%20Caching%20Explained.jpg) | [Slide Deck](./18%20Jan%20-%20MGraph_Cache_Storage_Architecture.pdf) |

> *Generated with [Google NotebookLM](https://notebooklm.google.com/) — Source document → Infographic → Slide deck*

---

## 🖼️ Infographic

![MGraph Two-Tier Caching Explained](./18%20Jan%20-%20MGraph%20Two-Tier%20Caching%20Explained.jpg)

---

## Key Themes

- **Two-Tier Storage Model**: Tier 1 for entity anchors, Tier 2 for actual data content
- **Hash-based Deduplication**: Uses `cache_hash` derived from `cache_key` to prevent duplicates
- **Dual Reference System**: Both `by-hash` and `by-id` indexes for O(1) lookups
- **Metadata System**: Timestamps, content hashes, and version tracking
- **Strategy Pattern**: Pluggable storage strategies for flexibility
