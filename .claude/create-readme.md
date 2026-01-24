# LLM Brief: Creating README.md Files

> Instructions for creating README.md files in the NotebookLM Infographics & Slides repository

---

## Purpose

README.md files provide navigation and quick access to content within each folder. They should be scannable, link to all assets, and render well on GitHub.

---

## File Structure

Each content folder README should follow this structure:

```markdown
[🏠 Home](../../../README.md) / [Parent](../) / **Current Folder Name**

---

# Folder Title

One-line description of what this content is about.

| 📄 Source | 🖼️ Infographic | 📊 Slides |
|---|---|---|
| [Source Document](./source-file.pdf) | [View Image](./infographic.png) | [Slide Deck](./slides.pdf) |

> *Generated with [Google NotebookLM](https://notebooklm.google.com/) — Source document → Infographic → Slide deck*

---

## 🖼️ Infographic

![Alt text](./infographic.png)

---

## Key Themes (optional)

- **Theme 1**: Brief description
- **Theme 2**: Brief description
```

---

## Rules

### 1. Breadcrumb Navigation

- Always start with breadcrumb navigation linking back to Home and parent folders
- Use emoji 🏠 for Home link
- Bold the current folder name (no link)
- Calculate relative paths correctly based on folder depth

**Example for a folder 4 levels deep:**
```markdown
[🏠 Home](../../../../README.md) / [For LinkedIn](../../../) / [Published](../../) / [Development & Engineering](../) / **Current Folder**
```

### 2. Quick Links Table

- Place immediately after the title and description
- Include all three document types when present: Source, Infographic, Slides
- Use URL encoding for special characters in filenames (spaces → `%20`, parentheses → `%28` `%29`)
- If a document type is missing, omit that column or show "N/A"

### 3. Embedded Infographic

- Always embed the infographic image so it displays in GitHub preview
- Use descriptive alt text
- If multiple infographics exist, embed all of them

### 4. URL Encoding Reference

| Character | Encoded |
|-----------|---------|
| Space | `%20` |
| `(` | `%28` |
| `)` | `%29` |
| `'` | `%27` |
| `£` | `%C2%A3` |
| `–` (en-dash) | `%E2%80%91` |

### 5. Parent Folder READMEs

For folders that contain subfolders (not content), use this structure:

```markdown
[🏠 Home](../../README.md) / [Parent](../) / **Category Name**

---

# Category Name

Description of what this category contains.

---

## 📂 Contents

| Folder | Description |
|--------|-------------|
| [Subfolder 1](./subfolder-1/) | Brief description |
| [Subfolder 2](./subfolder-2/) | Brief description |

> *Generated with [Google NotebookLM](https://notebooklm.google.com/) — Source document → Infographic → Slide deck*
```

---

## File Detection

When creating a README, first list the folder contents to identify:

1. **Source documents**: Usually PDFs with descriptive names (white papers, briefs, articles)
2. **Infographics**: PNG or JPG files, often with dates in filename
3. **Slide decks**: PDFs with "slide" or presentation-style names
4. **Other markdown**: May contain the source content

Common filename patterns:
- `DD Mon - Title.pdf` → Slide deck
- `DD Mon - Title.png/jpg` → Infographic
- `Title_With_Underscores.pdf` → Source document
- `*.md` → Source document (markdown)

---

## Description Writing

- Keep descriptions to 1-2 sentences
- Focus on what the content teaches or explains
- Use active voice
- Don't repeat the title verbatim

**Good:** "A practical alternative to traditional TDD - flipping the script on test-driven development."

**Bad:** "This document is about Pass-Driven Development which is a methodology."

---

## Example: Complete README

```markdown
[🏠 Home](../../../../README.md) / [For LinkedIn](../../../) / [Published](../../) / [Development & Engineering](../) / **Pass-Driven Development (PDD)**

---

# Pass-Driven Development (PDD)

A practical alternative to traditional TDD - flipping the script on test-driven development.

| 📄 Source | 🖼️ Infographic | 📊 Slides |
|---|---|---|
| [PDD Research](./Pass-Driven%20Development%20%28PDD%29_%20A%20Practical%20Alternative%20to%20Traditional%20TDD.pdf) | [View Image](./16%20Jan%20-%20Pass-Driven%20Development%20-%20Flipping%20the%20Script.jpg) | [Slide Deck](./16%20Jan%20-%20Pass_Driven_Code.pdf) |

> *Generated with [Google NotebookLM](https://notebooklm.google.com/) — Source document → Infographic → Slide deck*

---

## 🖼️ Infographic

![Pass-Driven Development - Flipping the Script](./16%20Jan%20-%20Pass-Driven%20Development%20-%20Flipping%20the%20Script.jpg)
```

---

## Checklist

Before finalizing a README:

- [ ] Breadcrumb navigation is correct and links work
- [ ] All three document types are linked (or noted as missing)
- [ ] URLs are properly encoded
- [ ] Infographic is embedded with alt text
- [ ] Description is concise and informative
- [ ] NotebookLM attribution line is included
