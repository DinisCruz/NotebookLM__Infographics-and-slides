# LLM Brief: Creating Slide Mosaics

> Instructions for creating `slides_mosaic.png` files — 4x4 grid previews of slide decks

---

## Purpose

Slide mosaics provide a visual preview of an entire slide deck in a single image. They:

1. **Enable quick scanning** — See all slides at a glance
2. **Work as clickable thumbnails** — Link to the full PDF in README
3. **Improve discoverability** — Visual content is more engaging than text links

---

## Prerequisites

These tools must be available:

| Tool | Package | Purpose |
|------|---------|---------|
| `pdftoppm` | `poppler-utils` | Extract PDF pages as images |
| `montage` | `imagemagick` | Combine images into a grid |

Install if needed:
```bash
# Ubuntu/Debian
apt-get install poppler-utils imagemagick

# macOS
brew install poppler imagemagick
```

---

## Basic Workflow

### Step 1: Extract Slides as Images

```bash
pdftoppm -png -r 100 "slides.pdf" slide_thumb
```

| Flag | Purpose |
|------|---------|
| `-png` | Output PNG format |
| `-r 100` | Resolution: 100 DPI (good for thumbnails) |
| `"slides.pdf"` | Input PDF file |
| `slide_thumb` | Output prefix (creates slide_thumb-01.png, etc.) |

This creates: `slide_thumb-01.png`, `slide_thumb-02.png`, ... `slide_thumb-NN.png`

### Step 2: Create Mosaic Grid

```bash
montage slide_thumb-*.png -tile 4x4 -geometry 300x+5+5 -background white slides_mosaic.png
```

| Flag | Purpose |
|------|---------|
| `slide_thumb-*.png` | Input files (wildcard) |
| `-tile 4x4` | Grid layout: 4 columns × 4 rows |
| `-geometry 300x+5+5` | Each thumbnail 300px wide, 5px padding |
| `-background white` | White background between tiles |
| `slides_mosaic.png` | Output filename |

### Step 3: Cleanup Temporary Files

```bash
rm slide_thumb-*.png
```

---

## Complete Script

```bash
# Navigate to content folder
cd "/path/to/content/folder"

# Extract slides (adjust filename as needed)
pdftoppm -png -r 100 "23 Jan - Slide_Deck_Name.pdf" slide_thumb

# Create mosaic
montage slide_thumb-*.png -tile 4x4 -geometry 300x+5+5 -background white slides_mosaic.png

# Cleanup
rm slide_thumb-*.png

# Verify
ls -la slides_mosaic.png
```

---

## Handling Different Slide Counts

| Slides | Tile Setting | Result |
|--------|--------------|--------|
| 1-4 | `-tile 2x2` | 2×2 grid |
| 5-9 | `-tile 3x3` | 3×3 grid |
| 10-16 | `-tile 4x4` | 4×4 grid (default) |
| 17-25 | `-tile 5x5` | 5×5 grid |
| 26+ | `-tile 4x` | 4 columns, auto rows |

**Recommended:** Use `-tile 4x4` for most decks. Extra slides will wrap to additional rows automatically.

---

## Counting Slides

To check how many slides a PDF has:

```bash
# Method 1: Count extracted files
pdftoppm -png -r 50 "slides.pdf" /tmp/count && ls /tmp/count-*.png | wc -l && rm /tmp/count-*.png

# Method 2: Use pdfinfo (if available)
pdfinfo "slides.pdf" | grep Pages
```

---

## Using in README.md

Add the mosaic as a clickable thumbnail:

```markdown
## 📑 Slide Deck (15 slides)

[![All Slides](./slides_mosaic.png)](./23%20Jan%20-%20Slide_Deck.pdf)

*Click image to open the slide deck* · [⬇️ Download PDF](https://github.com/DinisCruz/NotebookLM__Infographics-and-slides/raw/refs/heads/main/path/to/slides.pdf)
```

**Key points:**
- `[![alt](image)](link)` makes image clickable
- Link to the local PDF file
- Include slide count in the heading
- Add raw GitHub URL for direct download

---

## Troubleshooting

### "montage: command not found"

```bash
# Install ImageMagick
apt-get install imagemagick  # or brew install imagemagick
```

### "pdftoppm: command not found"

```bash
# Install poppler-utils
apt-get install poppler-utils  # or brew install poppler
```

### Mosaic looks too small/large

Adjust `-geometry` value:
- `200x+5+5` — Smaller thumbnails
- `300x+5+5` — Default (recommended)
- `400x+5+5` — Larger thumbnails

### Files have spaces in names

Always quote filenames:
```bash
pdftoppm -png -r 100 "23 Jan - My Slides.pdf" slide_thumb
```

### Too many slides for 4x4 grid

Use auto-rows:
```bash
montage slide_thumb-*.png -tile 4x -geometry 300x+5+5 -background white slides_mosaic.png
```

---

## Quality Settings

| Use Case | Resolution | Geometry | File Size |
|----------|------------|----------|-----------|
| Quick preview | `-r 72` | `200x` | ~200KB |
| Standard (recommended) | `-r 100` | `300x` | ~500KB |
| High quality | `-r 150` | `400x` | ~1MB |

---

## Checklist

Before completing a slide mosaic:

- [ ] PDF file exists and is readable
- [ ] `pdftoppm` extracted all slides
- [ ] Mosaic created with appropriate tile setting
- [ ] Temporary `slide_thumb-*.png` files deleted
- [ ] Final `slides_mosaic.png` is in content folder
- [ ] README.md updated with clickable mosaic
- [ ] Slide count mentioned in README heading
