# LLM Workflow Brief: Issues UI Frontend Development

**Purpose:** Complete reference for LLM sessions working on the Issues UI frontend
**Version:** 1.0
**Last Updated:** 2026-02-02

---

## Introduction: What This Document Covers

This briefing document provides everything an LLM (Claude Code session) needs to continue frontend development on the **Issues UI** - a graph-based issue tracking system. The Issues UI is a standalone web application that displays and manages issues stored as JSON files in a folder-based graph structure.

### What You Are Working On

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         THE ISSUES UI PROJECT                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  ┌─────────────────────┐         ┌─────────────────────┐                        │
│  │   .issues/          │         │    Backend APIs     │                        │
│  │   (JSON files in    │◄───────►│  (Python/FastAPI)   │                        │
│  │    git repo)        │  CRUD   │                     │                        │
│  └─────────────────────┘         └──────────┬──────────┘                        │
│          │                                   │                                   │
│          │ Git tracks                        │ REST API                          │
│          │ all changes                       │                                   │
│          │                                   ▼                                   │
│          │                        ┌─────────────────────┐                        │
│          └───────────────────────►│    Issues UI        │◄── YOU ARE HERE       │
│                                   │  (Web Components)   │                        │
│                                   │                     │                        │
│                                   │  - Kanban Board     │                        │
│                                   │  - Node List        │                        │
│                                   │  - Node Detail      │                        │
│                                   │  - Graph Viewer     │                        │
│                                   │  - Create Modal     │                        │
│                                   └─────────────────────┘                        │
│                                                                                  │
│  KEY INSIGHT: Issues are stored as JSON files in .issues/ folder                │
│  Git provides: version history, collaboration, branching, offline access        │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Your Role: Frontend-Only Development

You are the **UI/Frontend Claude Code session**. Your scope is:

- **DO:** Modify files in `mgraph_ai_ui_html_transformation_workbench__ui/issues_ui/`
- **DO:** Create/update implementation briefs in `docs/dev-briefs/issues-ui/`
- **DO:** Create/modify issue JSON files in `.issues/data/`
- **DO NOT:** Modify backend Python code in `mgraph_ai_ui_html_transformation_workbench/`
- **DO NOT:** Modify API endpoints or server-side logic

When you need backend changes, you write a **briefing document** for the Backend Claude session.

---

## Project Structure

### Repository Layout

```
MGraph-AI__UI__Html-Transformation-Workbench/
├── .issues/                          # ISSUE TRACKING DATA (Git-tracked)
│   ├── _index.json                   # Global index
│   ├── config/
│   │   ├── node-types.json           # Issue types: bug, task, feature, etc.
│   │   └── link-types.json           # Relationship types: has-task, blocks, etc.
│   └── data/
│       ├── bug/
│       │   ├── _index.json           # Bug index (next_index, count)
│       │   ├── Bug-1/node.json       # Individual bug
│       │   ├── Bug-2/node.json
│       │   └── ...
│       ├── task/
│       │   ├── _index.json
│       │   ├── Task-1/node.json
│       │   └── ...
│       ├── feature/
│       ├── version/
│       ├── user-story/
│       └── person/
│
├── docs/dev-briefs/
│   └── issues-ui/                    # BRIEFING DOCUMENTS
│       ├── v0.1.3__doc-1__planning.md
│       ├── v0.1.4__implementation-brief__graph-visualization.md
│       ├── v0.1.5__implementation-brief__ux-improvements.md
│       ├── v0.2.20-ui-frontend-briefing__on__backend-changes.md
│       └── LLM_WORKFLOW_BRIEF__Issues_UI_Frontend.md  # THIS FILE
│
├── mgraph_ai_ui_html_transformation_workbench__ui/
│   └── issues_ui/                    # UI SOURCE CODE
│       └── v0/v0.1/
│           ├── v0.1.0/               # Base version (complete files)
│           │   ├── index.html
│           │   ├── components/
│           │   │   ├── shell/
│           │   │   ├── node-list/
│           │   │   ├── node-detail/
│           │   │   ├── kanban-board/
│           │   │   └── create-node-modal/
│           │   ├── js/
│           │   │   ├── api-client.js
│           │   │   ├── events.js
│           │   │   └── graph-service.js
│           │   └── css/
│           ├── v0.1.1/               # Surgical override only
│           ├── v0.1.2/               # Surgical override only
│           ├── v0.1.3/               # Surgical override only
│           └── v0.1.4/               # Current version
│               ├── index.html        # Entry point
│               ├── components/
│               │   ├── graph-viewer/
│               │   ├── messages-panel/
│               │   └── ...
│               └── js/
│                   ├── graph-service-override.js
│                   └── messages-service.js
│
└── mgraph_ai_ui_html_transformation_workbench/    # BACKEND (not your scope)
    └── (Python FastAPI code)
```

---

## Issue Data Format

### Node JSON Structure

Each issue is a `node.json` file in `.issues/data/{type}/{Label}/node.json`:

```json
{
  "node_id": "a19a03b3",       // 8-char alphanumeric ID (REQUIRED FORMAT)
  "node_type": "task",          // bug, task, feature, version, user-story, person
  "node_index": 18,             // Auto-incrementing index per type
  "label": "Task-18",           // {Type}-{Index} format
  "title": "Add create button to node detail",
  "description": "Markdown description with **formatting**",
  "status": "todo",             // Varies by type (see below)
  "created_at": 1769992806312,  // Unix timestamp (ms)
  "updated_at": 1769992806312,
  "created_by": "eeeeffff",     // 8-char alphanumeric ID
  "tags": ["ui", "enhancement"],
  "links": [
    {
      "link_type_id": "344d6fd2",
      "verb": "task-of",
      "target_id": "18dacd07",
      "target_label": "Feature-12",
      "created_at": 1769994000000
    }
  ],
  "properties": {
    "estimate": "M",            // S, M, L, XL
    "component": "node-detail"
  }
}
```

### CRITICAL: Obj_Id Format

When creating new issues, `node_id` and `created_by` MUST be **8 alphanumeric characters**:

```
✅ VALID:   "a19a03b3", "eeeeffff", "12345678", "abcdef12"
❌ INVALID: "ui-claude", "user-1", "abc", "12345678901234"
```

### Status Values by Type

| Type | Statuses |
|------|----------|
| bug | backlog → confirmed → in-progress → testing → resolved → closed |
| task | backlog → todo → in-progress → review → done |
| feature | proposed → approved → in-progress → released |
| version | planned → in-progress → released → deprecated |
| user-story | draft → ready → in-progress → implemented → validated |

---

## IFD Methodology (Iterative Flow Development)

### Core Concept: Surgical Overrides

The UI uses **version layering**. Each minor version contains **only the changes** for that version, not complete copies of files.

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        IFD VERSION LAYERING                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  v0.1.0/index.html loads:                                                       │
│  ├── v0.1.0/js/api-client.js          (FULL: 200 lines)                         │
│  ├── v0.1.0/js/graph-service.js       (FULL: 150 lines)                         │
│  └── v0.1.0/components/shell.js       (FULL: 400 lines)                         │
│                                                                                  │
│  v0.1.4/index.html loads:                                                       │
│  ├── ../v0.1.0/js/api-client.js       ◄── Link back to base                     │
│  ├── ../v0.1.0/js/graph-service.js    ◄── Link back to base                     │
│  ├── js/graph-service-override.js     ◄── SURGICAL: adds 1 method (30 lines)    │
│  ├── ../v0.1.0/components/shell.js    ◄── Link back to base                     │
│  └── components/shell/issues-shell.js ◄── SURGICAL: override 2 methods          │
│                                                                                  │
│  RESULT: Later definitions override earlier ones via prototype patching         │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Surgical Override Pattern

```javascript
// v0.1.0/components/node-detail/node-detail.js (FULL - 300 lines)
class NodeDetail extends HTMLElement {
    constructor() { /* ... */ }
    render() { /* original */ }
    handleDelete() { /* original uses confirm() */ }
    // ... many more methods
}
customElements.define('node-detail', NodeDetail);

// v0.1.5/components/node-detail/node-detail.js (SURGICAL - 20 lines)
// Override ONLY handleDelete to use messages panel instead of confirm()
(function() {
    const _originalRender = NodeDetail.prototype.render;

    NodeDetail.prototype.handleDelete = async function() {
        const confirmed = await window.issuesApp.messages.confirm(
            `Delete ${this.state.node.label}?`,
            { confirmLabel: 'Delete', confirmStyle: 'danger' }
        );
        if (confirmed) {
            await this._doDelete();
        }
    };
})();
```

### Version Rules

| Version Type | Contains | Rule |
|--------------|----------|------|
| v0.1.0 | Complete base files | Self-contained |
| v0.1.1-v0.1.N | Surgical overrides only | Links back to v0.1.0 |
| v0.2.0 | Merged consolidation | Self-contained (merges all v0.1.x) |

---

## Development Workflow

### The Issue Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        ISSUE STATUS WORKFLOW                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│    ┌──────────┐    ┌──────────┐    ┌─────────────┐    ┌──────────┐              │
│    │ BACKLOG  │───►│   TODO   │───►│ IN-PROGRESS │───►│  REVIEW  │───► DONE     │
│    └──────────┘    └──────────┘    └─────────────┘    └──────────┘              │
│         │               │                │                  │                    │
│         │               │                │                  │                    │
│    Issues exist   You pick them    You implement      Push for              │
│    in backlog     for a version    the changes        user review               │
│                   and plan                                                       │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Version Planning Workflow

When starting work on a new version:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                    VERSION PLANNING WORKFLOW                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  STEP 1: Review Backlog                                                          │
│  ─────────────────────                                                           │
│  Read all issues with status "backlog" in .issues/data/                          │
│  Understand scope, dependencies, estimates                                       │
│                                                                                  │
│  STEP 2: Select Issues for Version                                               │
│  ─────────────────────────────────                                               │
│  Pick 3-8 related issues for the version                                         │
│  Consider: dependencies, scope, theme (e.g., "UX improvements")                  │
│                                                                                  │
│  STEP 3: Create Implementation Brief                                             │
│  ───────────────────────────────────                                             │
│  Create: docs/dev-briefs/issues-ui/vX.Y.Z__implementation-brief__theme.md        │
│  Include:                                                                        │
│  - Issue-by-issue analysis                                                       │
│  - ASCII diagrams for UI changes                                                 │
│  - Code snippets for approach                                                    │
│  - File structure                                                                │
│  - Testing checklist                                                             │
│                                                                                  │
│  STEP 4: Update Issue Statuses                                                   │
│  ────────────────────────────────                                                │
│  Change selected issues from "backlog" → "todo"                                  │
│                                                                                  │
│  STEP 5: Commit & Push Plan                                                      │
│  ─────────────────────────                                                       │
│  Commit the brief + status changes                                               │
│  Push to branch for human review                                                 │
│                                                                                  │
│  STEP 6: Wait for Approval                                                       │
│  ─────────────────────────                                                       │
│  Human reviews the plan                                                          │
│  May request changes or approve                                                  │
│                                                                                  │
│  STEP 7: Implement (after approval)                                              │
│  ────────────────────────────────                                                │
│  Change issues: "todo" → "in-progress"                                           │
│  Create version folder: v0.1.5/                                                  │
│  Implement each issue                                                            │
│  Change issues: "in-progress" → "review"                                         │
│  Commit & push                                                                   │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Cross-Team Communication: Briefing Documents

### When You Need Backend Changes

If you discover you need a new API endpoint or backend fix, you create a **briefing document**:

```
docs/dev-briefs/issues-ui/vX.Y.Z__backend-request__description.md
```

**Example: Requesting a new API**

```markdown
# Backend Request: Graph Traversal API

**From:** UI/Frontend Claude Code Session
**To:** Backend Team
**Date:** 2026-02-01
**Re:** API needed for graph visualization feature

## Request

I need an endpoint to fetch a node with all its connected nodes for graph visualization.

## Proposed Endpoint

```
GET /nodes/api/nodes/{node_type}/{label}/graph?depth=N
```

## Expected Response

```json
{
  "success": true,
  "root": "Feature-11",
  "depth": 2,
  "nodes": [...],
  "links": [...]
}
```

## Why This Is Needed

Task-15 requires showing a force-directed graph of connected issues...
```

### When You Receive Backend Briefings

The Backend team will create briefing documents for you when they ship changes:

```
docs/dev-briefs/issues-ui/vX.Y.Z-ui-frontend-briefing__on__backend-changes.md
```

**How to read them:**

1. Pull latest from dev branch
2. Read the briefing document
3. Note new endpoints, fixes, and code examples provided
4. Update your understanding of available APIs
5. Proceed with implementation that was blocked

---

## Creating New Issues

When you identify bugs or needed features during development, create them:

### File Location

```
.issues/data/{type}/{Label}/node.json
```

### Template

```json
{
  "node_id": "GENERATE_8_ALPHANUMERIC",
  "node_type": "task",
  "node_index": NEXT_INDEX,
  "label": "Task-{NEXT_INDEX}",
  "title": "Short descriptive title",
  "description": "Detailed description with markdown.\n\n## Details\n...",
  "status": "backlog",
  "created_at": TIMESTAMP_MS,
  "updated_at": TIMESTAMP_MS,
  "created_by": "eeeeffff",
  "tags": ["ui", "relevant", "tags"],
  "links": [],
  "properties": {
    "estimate": "S",
    "component": "affected-component"
  }
}
```

### Finding Next Index

Check `.issues/data/{type}/_index.json`:

```json
{
  "type": "task",
  "next_index": 26,
  "count": 25
}
```

Use `next_index` for your new issue's index, then update the _index.json.

---

## API Reference

### Backend Endpoints (Your UI Can Use)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/nodes/api/nodes/{type}` | GET | List all nodes of type |
| `/nodes/api/nodes/{type}/{label}` | GET | Get single node |
| `/nodes/api/nodes/{type}/{label}` | PATCH | Update node |
| `/nodes/api/nodes/{type}` | POST | Create node |
| `/nodes/api/nodes/{type}/{label}` | DELETE | Delete node |
| `/nodes/api/nodes/{type}/{label}/graph?depth=N` | GET | Get node with connections |

### Graph API Response Format

```javascript
{
  success: true,
  root: "Feature-11",
  depth: 2,
  nodes: [
    { label: "Feature-11", title: "...", node_type: "feature", status: "..." },
    { label: "Task-14", title: "...", node_type: "task", status: "..." }
  ],
  links: [
    { source: "Feature-11", target: "Task-14", link_type: "has-task" },
    { source: "Feature-11", target: "Task-15", link_type: "" }  // May be empty!
  ]
}
```

**Note:** `link_type` can be empty string - handle this in UI code.

---

## Component Architecture

### Shell + Mini-Apps Pattern

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                          ISSUES SHELL                                            │
├─────────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  HEADER                                                                  │    │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐                    │    │
│  │  │ Issues   │ │ Kanban   │ │ Versions │ │ Settings │                    │    │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘                    │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  CONTENT AREA (Shows active mini-app)                                   │    │
│  │                                                                          │    │
│  │  ┌─────────────────────────────────────────────────────────────────┐    │    │
│  │  │  NODE-LIST / KANBAN-BOARD / NODE-DETAIL / GRAPH-VIEWER          │    │    │
│  │  │                                                                  │    │    │
│  │  │  (Mini-apps are Web Components registered with the shell)        │    │    │
│  │  │                                                                  │    │    │
│  │  └─────────────────────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  MESSAGES PANEL (Collapsible sidebar)                                   │    │
│  │  - Error messages                                                        │    │
│  │  - Success notifications                                                 │    │
│  │  - Confirmation dialogs                                                  │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Communication: Event-Driven

Components communicate via CustomEvents, not direct method calls:

```javascript
// Component A emits
this.dispatchEvent(new CustomEvent('node-selected', {
    detail: { label: 'Task-18' },
    bubbles: true
}));

// Shell listens and routes
document.addEventListener('node-selected', (e) => {
    this.showApp('node-detail', { label: e.detail.label });
});
```

### Global Services

```javascript
window.issuesApp = {
    events: EventBus,           // Global event emitter
    graphService: GraphService, // API client for nodes
    messages: MessagesService,  // Toast/confirm notifications
    router: Router              // Hash-based navigation
};
```

---

## Implementation Checklist for New Versions

### Before Starting

- [ ] Pull latest from dev branch
- [ ] Read any new backend briefings
- [ ] Review backlog issues
- [ ] Create implementation brief
- [ ] Get human approval

### During Implementation

- [ ] Create version folder (e.g., `v0.1.5/`)
- [ ] Create `index.html` that loads dependencies in order
- [ ] Use surgical overrides (don't copy entire files)
- [ ] Use IIFE pattern for safe prototype patching
- [ ] Handle edge cases (empty data, API errors)
- [ ] Test with real API (no mocks!)

### After Implementation

- [ ] Update issue statuses to "review"
- [ ] Commit with descriptive message
- [ ] Push to feature branch
- [ ] Document any new issues discovered

---

## Common Patterns

### IIFE for Safe Overrides

```javascript
(function() {
    // Save original if needed
    const _originalMethod = Component.prototype.methodName;

    // Override
    Component.prototype.methodName = function() {
        // New implementation
        // Can call _originalMethod.call(this) if needed
    };
})();
```

### Adding New Method to Existing Service

```javascript
// v0.1.4/js/graph-service-override.js
(function() {
    const graphService = window.issuesApp?.graphService;
    if (!graphService) return;

    // Add new method
    graphService.getNodeGraph = async function(nodeType, label, depth = 1) {
        const url = `/nodes/api/nodes/${nodeType}/${label}/graph?depth=${depth}`;
        const response = await fetch(url, { credentials: 'include' });
        return response.json();
    };
})();
```

### CSS Cascading Override

```css
/* v0.1.4/css/graph-viewer.css */
/* Only include rules that CHANGE from base */
.graph-viewer .node.feature {
    fill: #22c55e;  /* Override base color */
}
```

---

## Troubleshooting

### "Method not found" after override

Check script loading order in index.html. Base must load before override.

### Issue data not saving

Check `node_id` and `created_by` are 8 alphanumeric characters.

### Graph shows empty link_type labels

Filter links before rendering:
```javascript
const linksWithLabels = links.filter(l => l.link_type && l.link_type.trim());
```

### Navigation "Back" goes to wrong view

Track `previousAppId` in shell state, use it in back button handler.

---

## Quick Start for New Session

1. **Read this document**
2. **Check current branch:** `git branch` (should be `claude/implement-workbench-*`)
3. **Pull latest:** `git pull origin dev`
4. **Read recent briefs:** `ls docs/dev-briefs/issues-ui/`
5. **Check backlog:** Read `.issues/data/*/` for status="backlog" issues
6. **Ask human:** "What should I work on?" or continue from previous session

---

## Version History

| UI Version | Key Features |
|------------|--------------|
| v0.1.0 | Base: node list, detail, create modal, kanban |
| v0.1.1 | Shell improvements, API logger |
| v0.1.2 | Automation runner, kanban scroll fix |
| v0.1.3 | Markdown preview, comments, drag-drop kanban |
| v0.1.4 | Graph viewer (D3.js), messages panel, getNodeGraph API |
| v0.1.5 | (Planned) UX improvements, visualization libraries |

---

*This briefing enables any Claude Code session to continue Issues UI development with full context of the workflow, architecture, and conventions.*
