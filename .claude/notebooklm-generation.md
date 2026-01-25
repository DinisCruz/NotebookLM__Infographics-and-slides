# Creating Infographics and Slide Decks from Source Documents in NotebookLM

> LLM Brief documenting the step-by-step process for creating professional infographics and slide decks from a source markdown document using NotebookLM's AI-powered generation features.

---

## Process Steps

### Step 1: Navigate to the Target Notebook

1. Access the NotebookLM dashboard
2. Locate and click on the desired notebook where you want to create infographics and slides
3. The notebook opens, displaying the Sources panel, Chat area, and Studio panel

### Step 2: Upload the Source Document

1. Click the **Add sources** button in the Sources panel
2. Select **Upload files** option from the dialog
3. Choose and upload your markdown file (or other supported document format)
4. Wait for the file to be processed and added to the sources list

### Step 3: Manage Source Selection ⚠️ Critical Step

This is the most important part of the process to ensure quality output.

#### 3a. Deselect All Sources

1. Locate "Select all sources" option in the Sources panel
2. Click the checkbox next to "Select all sources" to deselect all currently selected sources
3. Verify that **ALL** sources are now unchecked (checkmarks should be gone)
4. The sources count at the bottom should show "0 sources"

> **Note:** NotebookLM may auto-reselect sources, so watch carefully to confirm they remain deselected.

#### 3b. Select Only the Target Source

1. Click the checkbox next to **ONLY** the source file you want to use for generation
2. Verify that only one source has a checkmark
3. The sources count at the bottom should now show "1 source"

This ensures the AI generates content based solely on your chosen document, not all sources.

### Step 4: Generate the Infographic

1. In the Studio panel on the right, locate the **Infographic** button (marked with BETA tag)
2. Click the **Infographic** button
3. NotebookLM will begin generating the infographic based on the selected source
4. You'll see "Generating infographic..." message with a loading spinner
5. The infographic generation typically takes **1-3 minutes** depending on document size
6. Once complete, the infographic will appear in the Studio panel as a new item

### Step 5: Generate the Slide Deck

1. After confirming the infographic generation has started, click the **Slide deck** button in the Studio panel (marked with BETA tag)
2. This initiates the slide deck generation process
3. You'll see "Generating slide deck..." message with a loading spinner
4. The slide deck generation typically takes **3-5 minutes** or longer
5. Both assets can generate simultaneously in the background
6. Once complete, the slide deck will appear in the Studio panel as a separate item

### Step 6: Access and Customize Generated Assets

Both generated assets (infographic and slide deck) will appear in the Studio panel. Each asset shows:

- A descriptive title (auto-generated from content)
- Source count (should be "1 source" for single-source generation)
- Creation timestamp
- Three-dot menu for additional options

Click on any asset to view and customize:

- **Infographics:** Edit layout, colors, text, add/remove elements
- **Slide Decks:** Edit slides, add/remove pages, modify content

Use the pencil icon (Customize button) next to each asset for advanced editing.

---

## Key Best Practices

| Practice | Description |
|----------|-------------|
| **Source Isolation** | Always deselect all sources first, then select only the one you need. This prevents AI from mixing information from multiple documents. |
| **Single Source Focus** | Generating from a single source ensures coherent, focused infographics and slides without confusion from multiple perspectives. |
| **Waiting Time** | Allow adequate time for generation. Infographics typically complete faster than slide decks. Don't interrupt the process. |
| **Iteration** | You can regenerate assets multiple times if needed. Each generation uses the selected sources at that moment. |

### Content Quality Factors

The quality of generated assets depends on:

- Clarity and structure of your source document
- Presence of clear headings, bullet points, and organized sections
- Well-written, concise content

---

## Output Artifacts

| Artifact | Description |
|----------|-------------|
| **Infographic** | A visual representation of key concepts and data from your source document, ideal for presentations and quick understanding |
| **Slide Deck** | A structured presentation with multiple slides, ready for sharing or further editing in Google Slides or other tools |

---

## Technical Notes

- Both generation processes run in parallel and don't interfere with each other
- Notifications appear in the Studio panel showing generation status
- Generated assets are stored within the NotebookLM notebook for easy access
- You can customize, download, or share generated assets directly from NotebookLM

---

## Success Indicators

- [ ] Source properly isolated (only 1 selected)
- [ ] Infographic appeared in Studio panel within 3 minutes
- [ ] Slide deck appeared in Studio panel within 5 minutes
- [ ] Both assets have correct source attribution
- [ ] Content accurately reflects the source document

---

## Troubleshooting

If generation fails or seems stuck:

1. Check that exactly 1 source is selected
2. Refresh the page and try again
3. Ensure your source document is in a supported format
4. Check that the document contains sufficient content for AI to analyze
5. Verify internet connection is stable during generation
