# LLM Briefing: Git-Native Issue Tracking System — Backend Development

**Version:** v0.2.19
**Last Updated:** 2026-02-01
**Purpose:** Enable a new Claude session to continue backend development with full context
**Scope:** Backend/API development, coordination with UI Claude Code sessions

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [The Big Picture](#the-big-picture)
3. [System Architecture](#system-architecture)
4. [The Development Workflow](#the-development-workflow)
5. [Project Structure](#project-structure)
6. [Current State](#current-state)
7. [Key Patterns & Conventions](#key-patterns--conventions)
8. [Coordinating with UI Claude Code](#coordinating-with-ui-claude-code)
9. [Common Tasks](#common-tasks)
10. [Known Issues & TODOs](#known-issues--todos)

---

## Executive Summary

### What This Is

A **git-native, graph-based issue tracking system** where:
- Issues are JSON files in a `.issues/` folder within the repository
- Issues link to each other forming a graph (Version → Feature → Task → Bug)
- Everything is versioned with Git alongside the code
- Claude Code can read issues directly from the filesystem
- A visual Kanban UI allows humans to manage issues

### Why This Matters

Traditional issue trackers (Jira, GitHub Issues) live outside the codebase. When you tell Claude Code to "implement the features in the backlog," it can't — it has no access to your issue tracker.

**Our solution:** Issues ARE the codebase. Claude Code checks out the repo and can read `.issues/data/task/Task-14/node.json` directly.

### What This Briefing Covers

This document enables you (a new Claude session) to:
1. Understand the system architecture
2. Continue backend/API development
3. Coordinate with UI Claude Code sessions via briefing documents
4. Create new issues as JSON files
5. Fix bugs and implement features
6. Maintain consistency with existing patterns

### What This Briefing Does NOT Cover

- Frontend/UI implementation details (that's a separate Claude Code session)
- Deployment or infrastructure
- User authentication (not implemented yet)

---

## The Big Picture

### The Meta-Loop

We are **using this issue tracking system to develop itself**. The workflow:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              THE DEVELOPMENT LOOP                                │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│    ┌─────────┐                                                                   │
│    │  HUMAN  │                                                                   │
│    └────┬────┘                                                                   │
│         │                                                                        │
│         │ 1. Records voice memo / writes brief                                   │
│         ▼                                                                        │
│    ┌─────────────┐                                                               │
│    │ CLAUDE CHAT │  (You are here - Backend focus)                               │
│    │  (Backend)  │                                                               │
│    └──────┬──────┘                                                               │
│           │                                                                      │
│           │ 2. Creates issues as JSON, implements backend changes                │
│           │ 3. Writes briefing docs for UI team                                  │
│           ▼                                                                      │
│    ┌─────────────┐                                                               │
│    │  GIT REPO   │  (.issues/ folder + code)                                     │
│    └──────┬──────┘                                                               │
│           │                                                                      │
│           │ 4. Human commits, briefs Claude Code                                 │
│           ▼                                                                      │
│    ┌─────────────┐                                                               │
│    │ CLAUDE CODE │  (Separate session - UI focus)                                │
│    │    (UI)     │                                                               │
│    └──────┬──────┘                                                               │
│           │                                                                      │
│           │ 5. Reads issues, implements UI, writes planning docs                 │
│           ▼                                                                      │
│    ┌─────────────┐                                                               │
│    │  GIT REPO   │  (Updated code)                                               │
│    └──────┬──────┘                                                               │
│           │                                                                      │
│           │ 6. Human reviews in Kanban UI, drags to 'done'                       │
│           ▼                                                                      │
│    ┌─────────┐                                                                   │
│    │  HUMAN  │  ──── (repeat) ────▶                                              │
│    └─────────┘                                                                   │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### The Multi-Claude Pattern

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│     ┌─────────┐         voice memo / brief          ┌─────────────────┐     │
│     │  HUMAN  │ ──────────────────────────────────▶ │  CLAUDE CHAT    │     │
│     └─────────┘                                     │  (Backend Dev)  │     │
│          ▲                                          └────────┬────────┘     │
│          │                                                   │              │
│          │                                    JSON issues +  │              │
│          │                                    API code +     │              │
│          │                                    briefing docs  │              │
│          │                                                   ▼              │
│          │                                          ┌────────────────┐      │
│          │                                          │                │      │
│          │         reviews in UI                    │   GIT REPO     │      │
│          │         drags cards                      │   (.issues/)   │      │
│          │         commits                          │                │      │
│          │                                          └───────┬────────┘      │
│          │                                                  │               │
│          │                                    checkout +    │               │
│          │                                    read issues   │               │
│          │                                                  ▼               │
│          │                                          ┌─────────────────┐     │
│          └───────────────────────────────────────── │  CLAUDE CODE    │     │
│                        code changes                 │  (UI Dev)       │     │
│                                                     └─────────────────┘     │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## System Architecture

### High-Level Components

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              ISSUES UI APPLICATION                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                         FRONTEND (UI)                                │   │
│   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌───────────┐  │   │
│   │   │   Kanban    │  │   Issues    │  │   Node      │  │   Graph   │  │   │
│   │   │   Board     │  │   List      │  │   Detail    │  │   Viewer  │  │   │
│   │   └─────────────┘  └─────────────┘  └─────────────┘  └───────────┘  │   │
│   │                              │                                       │   │
│   │                              ▼                                       │   │
│   │                      ┌─────────────────┐                             │   │
│   │                      │  graph-service  │  (JS API client)            │   │
│   │                      └────────┬────────┘                             │   │
│   └───────────────────────────────┼──────────────────────────────────────┘   │
│                                   │                                          │
│                                   │ HTTP REST                                │
│                                   ▼                                          │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                         BACKEND (API)                                │   │
│   │                                                                      │   │
│   │   ┌─────────────────────────────────────────────────────────────┐   │   │
│   │   │                    FastAPI Routes                            │   │   │
│   │   │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐   │   │   │
│   │   │  │Routes__Nodes │  │Routes__Links │  │Routes__Comments  │   │   │   │
│   │   │  └──────────────┘  └──────────────┘  └──────────────────┘   │   │   │
│   │   └─────────────────────────────┬───────────────────────────────┘   │   │
│   │                                 │                                    │   │
│   │   ┌─────────────────────────────▼───────────────────────────────┐   │   │
│   │   │                      Services                                │   │   │
│   │   │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐   │   │   │
│   │   │  │Node__Service │  │Type__Service │  │Comments__Service │   │   │   │
│   │   │  └──────────────┘  └──────────────┘  └──────────────────┘   │   │   │
│   │   └─────────────────────────────┬───────────────────────────────┘   │   │
│   │                                 │                                    │   │
│   │   ┌─────────────────────────────▼───────────────────────────────┐   │   │
│   │   │                  Graph__Repository                           │   │   │
│   │   │              (Storage abstraction layer)                     │   │   │
│   │   └─────────────────────────────┬───────────────────────────────┘   │   │
│   │                                 │                                    │   │
│   └─────────────────────────────────┼────────────────────────────────────┘   │
│                                     │                                        │
│                                     ▼                                        │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                        STORAGE LAYER                                 │   │
│   │                                                                      │   │
│   │      Storage_FS__Memory          OR         Storage_FS__Local_Disk   │   │
│   │        (In-memory)                            (File system)          │   │
│   │                                                                      │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                     │                                        │
│                                     ▼                                        │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                         .issues/ FOLDER                              │   │
│   │                                                                      │   │
│   │    .issues/                                                          │   │
│   │    ├── config/                                                       │   │
│   │    │   ├── node-types.json      # bug, task, feature, version...    │   │
│   │    │   └── link-types.json      # has-task, blocks, depends-on...   │   │
│   │    └── data/                                                         │   │
│   │        ├── bug/                                                      │   │
│   │        │   ├── Bug-1/node.json                                       │   │
│   │        │   └── _index.json                                           │   │
│   │        ├── task/                                                     │   │
│   │        │   ├── Task-1/node.json                                      │   │
│   │        │   ├── Task-2/node.json                                      │   │
│   │        │   └── _index.json                                           │   │
│   │        ├── feature/                                                  │   │
│   │        └── version/                                                  │   │
│   │                                                                      │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Data Model

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              GRAPH DATA MODEL                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   NODE TYPES                           LINK TYPES                            │
│   ──────────                           ──────────                            │
│   • bug      (red)                     • has-feature / feature-of            │
│   • task     (blue)                    • has-task / task-of                  │
│   • feature  (green)                   • has-bug / bug-of                    │
│   • version  (amber)                   • has-story / story-of                │
│   • user-story (cyan)                  • blocks / blocked-by                 │
│   • person   (purple)                  • depends-on / dependency-of          │
│                                        • assigned-to / assignee-of           │
│                                        • relates-to (symmetric)              │
│                                                                              │
│   EXAMPLE GRAPH                                                              │
│   ─────────────                                                              │
│                                                                              │
│              ┌─────────────────┐                                             │
│              │   Version-1     │                                             │
│              │   v0.1.3        │                                             │
│              └────────┬────────┘                                             │
│                       │ has-feature                                          │
│          ┌────────────┼────────────┐                                         │
│          ▼            ▼            ▼                                         │
│   ┌────────────┐ ┌────────────┐ ┌────────────┐                               │
│   │ Feature-11 │ │ Feature-12 │ │ Feature-13 │                               │
│   │ Editor     │ │ Graph View │ │ Schema Ext │                               │
│   └─────┬──────┘ └─────┬──────┘ └────────────┘                               │
│         │ has-task     │ has-task                                            │
│    ┌────┼────┐         │                                                     │
│    ▼    ▼    ▼         ▼                                                     │
│  ┌────┐┌────┐┌────┐  ┌────┐                                                  │
│  │T-2 ││T-3 ││T-4 │  │T-6 │                                                  │
│  └────┘└────┘└────┘  └────┘                                                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Node JSON Structure

```json
{
  "node_id": "f030a011",
  "node_type": "feature",
  "node_index": 11,
  "label": "Feature-11",
  "title": "Issue Editor",
  "description": "Enable direct editing of issues in the UI.\n\n## Goals\n- Edit title, description, status",
  "status": "in-progress",
  "created_at": 1769965000000,
  "updated_at": 1769972000000,
  "created_by": "dc000001",
  "tags": ["ui", "editor", "v0.1.3"],
  "links": [
    { "link_type_id": "344d6fd2", "verb": "has-task", "target_id": "", "target_label": "Task-2", "created_at": 1769965000000 }
  ],
  "properties": {
    "priority": "high",
    "estimate": "L",
    "comments": [
      { "id": "abc123", "author": "human", "text": "Looks good!", "created_at": 1769970000000 }
    ]
  }
}
```

---

## The Development Workflow

### Your Role (Backend Claude Chat)

You are the **Backend Development Claude**. Your responsibilities:

1. **Implement backend features** — Routes, Services, Schemas
2. **Fix backend bugs** — API issues, data corruption, validation
3. **Create issues as JSON** — When planning new work
4. **Write briefing docs** — For UI Claude Code sessions
5. **Review UI team docs** — Respond to their questions/proposals

### Typical Session Flow

```
1. Human provides context (voice memo transcript, screenshot, or description)
         │
         ▼
2. You analyze and understand the request
         │
         ▼
3. If needed, read transcript file: /mnt/transcripts/...
         │
         ▼
4. Implement backend changes:
   • Create/modify Schema files
   • Create/modify Service files
   • Create/modify Route files
   • Write tests
         │
         ▼
5. If UI is affected, write briefing doc for UI Claude Code
         │
         ▼
6. Create task JSON files for backlog if needed
         │
         ▼
7. Present files to human for review/commit
```

### File Output Locations

```
/mnt/user-data/outputs/
├── patched/                    # Modified Python files
│   ├── Node__Service.py
│   ├── Routes__Comments.py
│   └── ...
├── docs/                       # Briefing documents
│   ├── v0.2.18-ui-frontend-briefing.md
│   └── v0.2.19-backend-briefing.md
├── issues/                     # New task JSON files
│   ├── Task-14/node.json
│   └── Task-15/node.json
└── config/                     # Config file updates
    ├── node-types.json
    └── link-types.json
```

---

## Project Structure

### Backend Code Organization

```
mgraph_ai_ui_html_transformation_workbench/
├── fast_api/
│   ├── Html_Transformation_Workbench__Fast_API.py    # Main FastAPI app
│   └── routes/
│       ├── Routes__Nodes.py                          # Node CRUD endpoints
│       ├── Routes__Links.py                          # Link endpoints  
│       ├── Routes__Comments.py                       # Comment endpoints (NEW)
│       └── Routes__Server.py                         # Health/status
│
├── service/
│   └── issues/
│       └── graph_services/
│           ├── Node__Service.py                      # Node business logic
│           ├── Type__Service.py                      # Node type management
│           ├── Link__Service.py                      # Link business logic
│           ├── Comments__Service.py                  # Comment CRUD (NEW)
│           └── Index__Status__Service.py             # Index management
│
├── schemas/
│   └── graph/
│       ├── Schema__Node.py                           # Node data structure
│       ├── Schema__Node__Link.py                     # Link within node
│       ├── Schema__Comment.py                        # Comment structure (NEW)
│       ├── Schema__Graph__Response.py                # Graph API response (NEW)
│       └── Safe_Str__Graph_Types.py                  # Type-safe string types
│
└── repository/
    └── Graph__Repository.py                          # Storage abstraction
```

### Key Files to Know

| File | Purpose |
|------|---------|
| `Html_Transformation_Workbench__Fast_API.py` | Main app, wires everything together |
| `Node__Service.py` | Most business logic lives here |
| `Graph__Repository.py` | All file I/O goes through here |
| `Schema__Node.py` | The core data structure |
| `Safe_Str__Graph_Types.py` | Custom type-safe string types |

---

## Current State

### Backend Version: v0.2.19

### Recently Implemented Features

| Feature | Status | Description |
|---------|--------|-------------|
| Graph Traversal API | ✅ Done | `GET /nodes/api/nodes/{type}/{label}/graph?depth=N` |
| Properties Deep Merge | ✅ Done | PATCH preserves existing properties |
| Comments API | ✅ Done | Dedicated CRUD endpoints for comments |
| Create Node Safety | ✅ Done | Prevents overwriting existing nodes |
| Multiline Descriptions | ✅ Done | Newlines preserved (not converted to underscores) |

### API Endpoints

```
NODES
  GET    /nodes/api/nodes                              List all nodes
  POST   /nodes/api/nodes                              Create node
  GET    /nodes/api/nodes/{type}/{label}               Get node
  PATCH  /nodes/api/nodes/{type}/{label}               Update node
  DELETE /nodes/api/nodes/{type}/{label}               Delete node
  GET    /nodes/api/nodes/{type}/{label}/graph         Get node graph (NEW)

COMMENTS
  GET    /comments/api/{type}/{label}                  List comments
  POST   /comments/api/{type}/{label}                  Create comment
  GET    /comments/api/{type}/{label}/{id}             Get comment
  PATCH  /comments/api/{type}/{label}/{id}             Update comment
  DELETE /comments/api/{type}/{label}/{id}             Delete comment

LINKS
  GET    /links/api/links                              List all links
  POST   /links/api/links                              Create link
  DELETE /links/api/links/{id}                         Delete link

CONFIG
  GET    /nodes/api/types                              List node types
  GET    /nodes/api/link-types                         List link types

SERVER
  GET    /server/status                                Health check
```

### Node Types Configured

| Type | Color | Statuses |
|------|-------|----------|
| bug | #ef4444 (red) | backlog, confirmed, in-progress, testing, resolved, closed |
| task | #3b82f6 (blue) | backlog, todo, in-progress, review, done |
| feature | #22c55e (green) | proposed, approved, in-progress, released |
| version | #f59e0b (amber) | planned, in-progress, released, deprecated |
| user-story | #06b6d4 (cyan) | draft, ready, in-progress, implemented, validated |
| person | #8b5cf6 (purple) | active, inactive |

---

## Key Patterns & Conventions

### Type_Safe Framework

All schemas inherit from `Type_Safe`. This provides runtime type validation.

```python
from osbot_utils.type_safe.Type_Safe import Type_Safe

class Schema__Node(Type_Safe):
    node_id     : Node_Id
    title       : Safe_Str__Text
    description : Safe_Str__Issue__Node__Description    # Preserves newlines
    status      : Safe_Str__Status
```

**Key behaviors:**
- `Safe_Str__*` types auto-initialize to `''` (empty string), not `None`
- Use truthiness checks: `if request.title:` not `if request.title is not None:`
- Custom regex validation via `Safe_Str` subclasses

### Code Style Conventions

```python
# Comments aligned to column 70-80
def some_method(self                              ,                          # Comment here
                param1 : str                      ,
                param2 : int
           ) -> ReturnType:

# No docstrings (use aligned comments instead)
# Explicit `is False` checks
if self.repository.node_save(node) is False:
    return error_response

# Type hints on everything
def method(self, param: Safe_Str__Text) -> Schema__Response:
```

### Service Pattern

```python
class Some__Service(Type_Safe):
    repository : object = None                                               # Injected dependency

    def do_thing(self, request: Schema__Request) -> Schema__Response:
        # 1. Validate
        if not request.required_field:
            return Schema__Response(success=False, message='Field required')

        # 2. Load data
        entity = self.repository.load(...)
        if entity is None:
            return Schema__Response(success=False, message='Not found')

        # 3. Business logic
        entity.field = request.new_value
        entity.updated_at = Timestamp_Now()

        # 4. Save
        if self.repository.save(entity) is False:
            return Schema__Response(success=False, message='Save failed')

        # 5. Return success
        return Schema__Response(success=True, entity=entity)
```

### Creating New Issues (JSON)

When you need to create tasks for the backlog:

```python
{
  "node_id": "xxxxxxxx",           # 8 hex chars, generate new
  "node_type": "task",
  "node_index": 19,                # Next available number
  "label": "Task-19",              # {Type}-{Index}
  "title": "Short descriptive title",
  "description": "Detailed description with markdown.\n\n## Requirements\n- Item 1\n- Item 2",
  "status": "backlog",
  "created_at": 1769987000000,     # Epoch milliseconds
  "updated_at": 1769987000000,
  "created_by": "dc000001",        # Use this placeholder
  "tags": ["relevant", "tags"],
  "links": [
    { "link_type_id": "344d6fd2", "verb": "task-of", "target_id": "", "target_label": "Feature-11", "created_at": 1769987000000 }
  ],
  "properties": {
    "estimate": "S",               # S, M, L
    "component": "node-service"
  }
}
```

---

## Coordinating with UI Claude Code

### When to Write a UI Briefing

Write a briefing doc when:
- New API endpoints are available
- API behavior has changed
- Bug fixes affect UI assumptions
- New capabilities are unlocked

### Briefing Document Template

```markdown
# UI Frontend Briefing: [Feature Name]

**From:** Backend Team
**To:** UI Claude Code Session
**Date:** YYYY-MM-DD

## Summary
What changed and why it matters to UI.

## New Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /path | What it does |

## Example Usage
\`\`\`javascript
const response = await fetch('/endpoint');
\`\`\`

## Response Format
\`\`\`json
{ "example": "response" }
\`\`\`

## Tasks Unblocked
Which UI tasks can now proceed.

## Questions?
Contact info / how to coordinate.
```

### When You Receive a UI Doc

The UI Claude Code may create planning docs. Review them for:
1. **API assumptions** — Are they using endpoints correctly?
2. **Schema mismatches** — Are field names right?
3. **Missing features** — Do they need backend work?

Respond with corrections or confirmations.

---

## Common Tasks

### Adding a New API Endpoint

1. Create/update Schema in `schemas/graph/`
2. Add method to Service in `service/issues/graph_services/`
3. Add route in `fast_api/routes/`
4. Wire up in `Html_Transformation_Workbench__Fast_API.py`
5. Write tests
6. Update UI briefing doc

### Fixing a Bug

1. Understand the issue (ask for screenshots/logs if needed)
2. Identify the file(s) involved
3. Implement fix
4. Write test case that would have caught it
5. Update relevant docs if behavior changed

### Creating Tasks for Backlog

1. Determine next available task number
2. Create `Task-N/node.json` file
3. Link to relevant feature
4. Update `_index.json` if needed

---

## Known Issues & TODOs

### Pending Work

| Task | Description | Priority |
|------|-------------|----------|
| Task-3 | Comments UI (needs UI work now that API is ready) | High |
| Task-6 | Graph visualization component | Medium |
| Task-8 | Graph API is done, UI can integrate | Medium |

### Technical Debt

- `Schema__Node.properties` uses `Dict[str, Any]` — should be typed
- `Schema__Node.tags` uses `List[Safe_Str__Text]` — should be `List[Safe_Str__Tag]`
- Some services have TODOs for refactoring to use "Issue" naming

### Known Quirks

- **Type_Safe auto-init:** Safe_Str types initialize to `''` not `None`
- **Index sync:** `_index.json` can get out of sync; safety check prevents overwrites
- **Link verb field:** Use `link.verb` not `link.link_type`

---

## Quick Reference

### Environment Variables

```bash
ISSUES__IN_MEMORY=false    # Use disk storage (true = in-memory)
ISSUES__PATH=.issues       # Path to issues folder
```

### Running the Server

```bash
./run-locally.sh
# Or
uvicorn mgraph_ai_ui_html_transformation_workbench.fast_api.Html_Transformation_Workbench__Fast_API:app --port 10041
```

### Testing

```bash
pytest tests/unit/fast_api/routes/local_data/test_Routes__Nodes.py -v
pytest tests/unit/test_comments_api.py -v
```

### Key Imports

```python
from osbot_utils.type_safe.Type_Safe import Type_Safe
from osbot_utils.type_safe.primitives.domains.identifiers.Obj_Id import Obj_Id
from osbot_utils.type_safe.primitives.domains.identifiers.safe_int.Timestamp_Now import Timestamp_Now
from mgraph_ai_ui_html_transformation_workbench.schemas.graph.Safe_Str__Graph_Types import Safe_Str__Node_Type, Safe_Str__Node_Label, Safe_Str__Status
```

---

## Starting a New Session

When starting fresh with this briefing:

1. **Read this document** to understand context
2. **Ask human** what they want to work on
3. **Check transcript** at `/mnt/transcripts/` if context is compacted
4. **Review recent issues** in `.issues/data/task/` to see current state
5. **Continue the workflow** — implement, brief, coordinate

Welcome to the team! 🚀

---

*Briefing created: 2026-02-01*
*Backend version: v0.2.19*
*For: New Claude Chat sessions focused on backend development*
