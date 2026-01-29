# LLM Brief: HTML Transformation Workbench UI

## What Is This?

A web-based workbench for loading, transforming, and saving HTML pages. Users can apply various transformations (content filtering, style modifications, LLM-based analysis) and see before/after comparisons in real-time.

**Key idea:** Load HTML from cache → Apply transformations → Preview results → Save transformed version

---

## Why Build This?

1. **Content Moderation** - Remove/blur negative content, filter by topic relevance
2. **Style Manipulation** - Modify CSS, restructure layouts
3. **LLM Processing** - Use AI to analyze/transform content
4. **Visual Debugging** - See exactly what transformations do before saving
5. **Workflow Automation** - Chain multiple transformations together

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        HTML Transformation Workbench                         │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      Global Navigation Bar                          │    │
│  │  [Workbench] [API Explorer] [Settings] [Mini App 1] [Mini App 2]   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      Active Mini App Container                       │    │
│  │                                                                      │    │
│  │   ┌──────────────────────────────────────────────────────────────┐  │    │
│  │   │                    Mini App Content                          │  │    │
│  │   │         (loaded dynamically based on navigation)             │  │    │
│  │   └──────────────────────────────────────────────────────────────┘  │    │
│  │                                                                      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      Global Services Layer                          │    │
│  │  - API Client (html-graph, text-transform, llms)                   │    │
│  │  - Configuration Manager (local storage)                            │    │
│  │  - Event Bus (cross-component communication)                        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Services Integration

### API Endpoints

| Service | Base URL | Purpose |
|---------|----------|---------|
| HTML Graph | `https://html-graph.dev.mgraph.ai` | Store/retrieve HTML |
| Text Transform | `https://text-transform.dev.mgraph.ai` | Text transformations |
| LLMs | `https://llms.dev.mgraph.ai` | AI-powered analysis |

### Authentication

Each service requires a header-based API key:

```javascript
// Stored in localStorage
{
    "html-graph": {
        "headerName": "X-API-Key",
        "headerValue": "your-api-key-here"
    },
    "text-transform": {
        "headerName": "X-API-Key",
        "headerValue": "your-api-key-here"
    },
    "llms": {
        "headerName": "X-API-Key",
        "headerValue": "your-api-key-here"
    }
}
```

---

## Core Mini Apps

### 1. HTML Workbench (Primary)

The main transformation workspace:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          HTML Workbench                                  │
├─────────────────────────────────────────────────────────────────────────┤
│  URL/Cache Key: [example.com/about____________] [Load] [Load from URL]  │
├───────────────────────────────────┬─────────────────────────────────────┤
│         BEFORE (Original)         │         AFTER (Transformed)         │
│  ┌─────────────────────────────┐  │  ┌─────────────────────────────┐   │
│  │                             │  │  │                             │   │
│  │   [Raw HTML] [Preview]      │  │  │   [Raw HTML] [Preview]      │   │
│  │                             │  │  │                             │   │
│  │   <html>                    │  │  │   <html>                    │   │
│  │     <body>                  │  │  │     <body>                  │   │
│  │       <h1>Hello</h1>        │  │  │       <h1>Hello</h1>        │   │
│  │       <p>Content...</p>     │  │  │       <!-- removed -->      │   │
│  │     </body>                 │  │  │     </body>                 │   │
│  │   </html>                   │  │  │   </html>                   │   │
│  │                             │  │  │                             │   │
│  └─────────────────────────────┘  │  └─────────────────────────────┘   │
├───────────────────────────────────┴─────────────────────────────────────┤
│                        TRANSFORMATIONS                                   │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ [x] Remove Negative Content    [x] Blur Sensitive Images        │    │
│  │ [ ] Filter by Topic            [ ] Simplify Styles              │    │
│  │ [ ] LLM Summarization          [ ] Remove Ads                   │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  [Apply Transformations]                    [Save to Cache] [Export]    │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2. API Explorer (Postman-style)

Debug and test API calls:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          API Explorer                                    │
├─────────────────────────────────────────────────────────────────────────┤
│  Service: [HTML Graph ▼]                                                │
│  Method:  [POST ▼]                                                      │
│  Endpoint: [/flet-html-domain/html/load/{namespace}/key/{cache_key}]   │
│                                                                          │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │ Path Parameters         │  │ Headers                             │   │
│  │ namespace: [html-cache] │  │ X-API-Key: [••••••••••]            │   │
│  │ cache_key: [example.com]│  │ Content-Type: application/json      │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                          │
│  Request Body:                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ {}                                                               │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  [Send Request]                                                          │
│                                                                          │
│  Response (200 OK - 45ms):                                              │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ {                                                                │    │
│  │   "success": true,                                               │    │
│  │   "found": true,                                                 │    │
│  │   "html": "<html>...</html>",                                   │    │
│  │   "cache_id": "aa27fb2a-..."                                    │    │
│  │ }                                                                │    │
│  └─────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
```

### 3. Settings

Configuration panel:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            Settings                                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  API Configuration                                                       │
│  ────────────────                                                        │
│                                                                          │
│  HTML Graph API                                                          │
│  Base URL:    [https://html-graph.dev.mgraph.ai    ]                    │
│  Header Name: [X-API-Key                           ]                    │
│  Header Value:[••••••••••••••••••••••              ] [Show]             │
│  Status: ✓ Connected                                [Test Connection]   │
│                                                                          │
│  Text Transform API                                                      │
│  Base URL:    [https://text-transform.dev.mgraph.ai]                    │
│  Header Name: [X-API-Key                           ]                    │
│  Header Value:[••••••••••••••••••••••              ] [Show]             │
│  Status: ✓ Connected                                [Test Connection]   │
│                                                                          │
│  LLMs API                                                                │
│  Base URL:    [https://llms.dev.mgraph.ai          ]                    │
│  Header Name: [X-API-Key                           ]                    │
│  Header Value:[••••••••••••••••••••••              ] [Show]             │
│  Status: ✓ Connected                                [Test Connection]   │
│                                                                          │
│  ────────────────                                                        │
│                                                                          │
│  Default Namespace: [html-cache                    ]                    │
│                                                                          │
│  [Save Settings]                              [Reset to Defaults]       │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Web Components Structure

```
workbench-app/
├── workbench-shell.js           # Main shell, navigation, routing
├── components/
│   ├── global/
│   │   ├── nav-bar.js           # Global navigation
│   │   ├── api-client.js        # Shared API client (not a visual component)
│   │   ├── config-manager.js    # Local storage config management
│   │   ├── profile-service.js   # Profile load/save operations
│   │   └── event-bus.js         # Cross-component events
│   │
│   ├── settings/
│   │   ├── settings-panel.js    # Main settings view
│   │   └── api-config-card.js   # Individual API config card
│   │
│   ├── api-explorer/
│   │   ├── api-explorer.js      # Main explorer view
│   │   ├── request-builder.js   # Build requests
│   │   └── response-viewer.js   # View responses
│   │
│   ├── html-workbench/
│   │   ├── html-workbench.js    # Main workbench view
│   │   ├── html-loader.js       # Load HTML from cache/URL
│   │   ├── html-editor.js       # View/edit raw HTML
│   │   ├── html-preview.js      # Render HTML preview
│   │   ├── transform-panel.js   # Select transformations
│   │   └── diff-viewer.js       # Show before/after diff
│   │
│   ├── profiles/
│   │   ├── profile-manager.js   # Main profile management view
│   │   ├── profile-editor.js    # Create/edit profile
│   │   ├── profile-list.js      # List available profiles
│   │   └── transform-config.js  # Configure individual transform
│   │
│   ├── site-browser/
│   │   ├── site-browser.js      # Main site browser view
│   │   ├── domain-input.js      # Domain search input
│   │   ├── page-list.js         # List pages in domain
│   │   └── batch-actions.js     # Batch operation controls
│   │
│   └── analysis/
│       ├── page-analysis.js     # Page analysis view
│       ├── site-analysis.js     # Site analysis view
│       ├── structure-panel.js   # DOM structure display
│       ├── content-panel.js     # Content analysis display
│       ├── links-panel.js       # Link analysis display
│       ├── dom-tree-graph.js    # DOM tree visualization
│       └── link-graph.js        # Site link graph visualization
```

---

## Event-Driven Architecture

### Global Events

```javascript
// HTML loaded from cache
document.dispatchEvent(new CustomEvent('html-loaded', {
    detail: { cacheKey: 'example.com/about', html: '<html>...', cacheId: '...' }
}));

// Transformation applied
document.dispatchEvent(new CustomEvent('transform-applied', {
    detail: { type: 'remove-negative', before: '...', after: '...' }
}));

// HTML saved to cache
document.dispatchEvent(new CustomEvent('html-saved', {
    detail: { cacheKey: 'example.com/about', cacheId: '...' }
}));

// API config changed
document.dispatchEvent(new CustomEvent('config-changed', {
    detail: { service: 'html-graph', config: {...} }
}));

// Navigation
document.dispatchEvent(new CustomEvent('navigate', {
    detail: { target: 'html-workbench' }
}));

// Profile events
document.dispatchEvent(new CustomEvent('profile-loaded', {
    detail: { profileId: 'clean-news', profile: {...} }
}));

document.dispatchEvent(new CustomEvent('profile-saved', {
    detail: { profileId: 'clean-news' }
}));

document.dispatchEvent(new CustomEvent('profile-applied', {
    detail: { profileId: 'clean-news', cacheId: '...', resultCacheId: '...' }
}));

// Site browser events
document.dispatchEvent(new CustomEvent('site-loaded', {
    detail: { domain: 'example.com', pages: [...] }
}));

document.dispatchEvent(new CustomEvent('pages-selected', {
    detail: { domain: 'example.com', cacheKeys: ['example.com/about', 'example.com/contact'] }
}));

document.dispatchEvent(new CustomEvent('batch-transform-complete', {
    detail: { profileId: 'clean-news', domain: 'example.com', results: [...] }
}));

// Analysis events
document.dispatchEvent(new CustomEvent('analysis-complete', {
    detail: { type: 'structure', cacheId: '...', analysis: {...} }
}));

document.dispatchEvent(new CustomEvent('site-analysis-complete', {
    detail: { type: 'sitemap', domain: 'example.com', analysis: {...} }
}));

document.dispatchEvent(new CustomEvent('graph-generated', {
    detail: { type: 'dom-tree', cacheId: '...', graph: {...} }
}));
```

---

## HTML Graph API Integration

### Load HTML by URL/Key

```javascript
async function loadHtml(cacheKey) {
    const config = getConfig('html-graph');
    const response = await fetch(
        `${config.baseUrl}/flet-html-domain/html/load/${namespace}/key/${cacheKey}`,
        {
            method: 'POST',
            headers: {
                [config.headerName]: config.headerValue,
                'Content-Type': 'application/json'
            }
        }
    );
    return response.json();
}
```

### Save HTML

```javascript
async function saveHtml(cacheKey, html) {
    const config = getConfig('html-graph');
    const response = await fetch(
        `${config.baseUrl}/flet-html-domain/html/store/${namespace}/key/${cacheKey}`,
        {
            method: 'POST',
            headers: {
                [config.headerName]: config.headerValue,
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ html })
        }
    );
    return response.json();
}
```

### URL to Cache Key

```javascript
function urlToCacheKey(url) {
    const parsed = new URL(url);
    const path = parsed.pathname === '/' ? '/root' : parsed.pathname;
    return `${parsed.hostname}${path}`;
}

// Examples:
// https://example.com        → example.com/root
// https://example.com/about  → example.com/about
```

---

## Transformation Types

### Client-Side (JavaScript)

| Transform | Description |
|-----------|-------------|
| Remove Elements | Remove by CSS selector |
| Blur Images | Apply CSS blur to images |
| Simplify Styles | Strip inline styles, simplify CSS |
| Remove Ads | Remove common ad selectors |
| Extract Content | Keep only main content |

### Server-Side (LLM)

| Transform | Description | Service |
|-----------|-------------|---------|
| Sentiment Filter | Remove negative content | `llms.dev.mgraph.ai` |
| Topic Filter | Keep only relevant topics | `llms.dev.mgraph.ai` |
| Summarize | Create summary version | `llms.dev.mgraph.ai` |
| Translate | Translate content | `text-transform.dev.mgraph.ai` |

---

## Local Storage Schema

```javascript
// Key: 'workbench-config'
{
    "version": 1,
    "services": {
        "html-graph": {
            "baseUrl": "https://html-graph.dev.mgraph.ai",
            "headerName": "X-API-Key",
            "headerValue": "encrypted-or-plain-key"
        },
        "text-transform": {
            "baseUrl": "https://text-transform.dev.mgraph.ai",
            "headerName": "X-API-Key",
            "headerValue": "encrypted-or-plain-key"
        },
        "llms": {
            "baseUrl": "https://llms.dev.mgraph.ai",
            "headerName": "X-API-Key",
            "headerValue": "encrypted-or-plain-key"
        }
    },
    "defaults": {
        "namespace": "html-cache"
    },
    "recentCacheKeys": [
        "example.com/about",
        "example.com/contact"
    ]
}
```

---

## Phase 1: Foundation

**Goal:** Basic framework + Settings + Simple load/save test

### Deliverables

1. **Shell Component** (`workbench-shell.js`)
   - Global navigation bar
   - Mini app container with routing
   - Load settings from local storage

2. **Settings Panel** (`settings-panel.js`)
   - API configuration for all 3 services
   - Test connection buttons
   - Save to local storage

3. **Simple HTML Test** (`html-workbench.js` - minimal)
   - Input for cache key
   - Load button → fetch from HTML Graph
   - Display raw HTML
   - Save button → store to HTML Graph

### Success Criteria

- [ ] Can configure API keys in Settings
- [ ] Settings persist in local storage
- [ ] Can load HTML by cache key
- [ ] Can save HTML to cache key
- [ ] Navigation between Settings and Workbench works

---

## Phase 2: Site Browser & Profiles

**Goal:** Browse sites, manage transformation profiles

### Deliverables

1. **Site Browser** (`site-browser.js`)
   - Enter domain, list all cached pages
   - Select multiple pages
   - View page metadata

2. **Profile Manager** (`profile-manager.js`)
   - Create/edit transformation profiles
   - List available profiles
   - Tag and search profiles

3. **Profile API** (server-side)
   - `GET/POST /flet-html-domain/profiles/{namespace}/{profile_id}`
   - Store profiles as data files

### Success Criteria

- [ ] Can list all pages for a domain
- [ ] Can create and save transformation profiles
- [ ] Profiles persist in cache
- [ ] MitmProxy can lookup profile by ID

---

## Phase 3: HTML Workbench + Transforms

**Goal:** Full before/after comparison, apply profiles

### Deliverables

1. Split-pane before/after view
2. Raw HTML / Preview toggle
3. Apply profile to single page
4. Batch apply profile to site
5. Basic client-side transforms

### Success Criteria

- [ ] Can view before/after HTML
- [ ] Can apply profile to page
- [ ] Can batch apply to multiple pages
- [ ] Transform results saved to cache

---

## Phase 4: API Explorer

**Goal:** Postman-style debugging UI

### Deliverables

1. Service/endpoint selection
2. Request builder with parameters
3. Response viewer with timing
4. Request history

---

## Phase 5: Page Analysis

**Goal:** Analyze individual page structure and content

### Deliverables

1. **Structure Analysis** - DOM tree, element counts
2. **Content Analysis** - Topics, sentiment, readability (LLM)
3. **Link Analysis** - Internal/external links
4. **Graph Visualization** - DOM tree graph

### Success Criteria

- [ ] Can analyze page structure
- [ ] Analysis saved to entity data files
- [ ] Can visualize DOM tree

---

## Phase 6: Site Analysis

**Goal:** Site-wide analysis and graphs

### Deliverables

1. **Site Map Generation** - Page hierarchy
2. **Content Overview** - Aggregated analysis
3. **Link Graph** - Internal link visualization
4. **Site Comparison** - Changes over time

### Success Criteria

- [ ] Can generate site map
- [ ] Can visualize site link graph
- [ ] Analysis saved to `_site-analysis` entity

---

## Phase 7: LLM Integration

**Goal:** Server-side transformations via LLM

### Deliverables

1. Sentiment analysis transform
2. Topic filtering transform
3. Content summarization
4. Integration with `llms.dev.mgraph.ai`

---

## IFD (Iterative Flow Development)

Each phase follows this cycle:

```
1. Define → What does this component do?
2. Design → What does it look like? (ASCII/wireframe)
3. Build  → Implement as web component
4. Test   → Does it work with the APIs?
5. Refine → Iterate based on usage
```

Each mini app gets its own IFD version tracking:

```
workbench-shell    v1.0.0
├── settings       v1.0.0
├── api-explorer   v0.1.0 (in progress)
└── html-workbench v1.2.0
```

---

## Summary

| Phase | Focus | Key Components |
|-------|-------|----------------|
| 1 | Foundation | Shell, Settings, Basic load/save |
| 2 | Sites & Profiles | Site Browser, Profile Manager |
| 3 | Workbench | Before/after, apply profiles, batch ops |
| 4 | API Explorer | Request/response debugging |
| 5 | Page Analysis | Structure, content, links, DOM graph |
| 6 | Site Analysis | Site map, content overview, link graph |
| 7 | LLM | Server-side transforms |

**Start with Phase 1** - Get the framework running, settings working, and a simple HTML load/save to prove the API integration works.

---

## New API Endpoints Summary

### Profile Management
| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/flet-html-domain/profiles/{ns}` | List all profiles |
| GET | `/flet-html-domain/profiles/{ns}/{profile_id}` | Get profile |
| POST | `/flet-html-domain/profiles/{ns}/{profile_id}` | Create/update profile |
| DELETE | `/flet-html-domain/profiles/{ns}/{profile_id}` | Delete profile |
| POST | `/flet-html-domain/profiles/{ns}/{profile_id}/apply` | Apply to page |
| POST | `/flet-html-domain/profiles/{ns}/{profile_id}/apply/site/{domain}` | Apply to site |

### Site Browsing
| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/cache-entity/{ns}/entities/site/{domain}` | List pages for domain |

### Page Analysis
| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/flet-html-domain/html/analyze/{ns}/structure` | Analyze DOM structure |
| POST | `/flet-html-domain/html/analyze/{ns}/content` | Analyze content (LLM) |
| POST | `/flet-html-domain/html/analyze/{ns}/links` | Analyze links |
| POST | `/flet-html-domain/html/graph/{ns}/dom-tree` | Generate DOM graph |

### Site Analysis
| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/flet-html-domain/site/analyze/{ns}/sitemap/{domain}` | Generate site map |
| POST | `/flet-html-domain/site/analyze/{ns}/content/{domain}` | Site content overview |
| POST | `/flet-html-domain/site/graph/{ns}/links/{domain}` | Site link graph |

---

## Potential New FLeTs

To support server-side transformations, we could add these FLeTs to the HTML Graph API:

---

## Transformation Profiles (New Concept)

### What Is a Transformation Profile?

A **Transformation Profile** is a saved, reusable configuration that defines:
- Which transformations to apply
- With what parameters
- Identified by a `profile_id` (maps to `file_id` in cache)

**Key use case:** MitmProxy sends a cookie value (e.g., `transform=clean-news`) → Workbench looks up profile `clean-news` → Applies those transformations automatically.

### Profile Schema

```json
{
    "profile_id": "clean-news",
    "name": "Clean News Articles",
    "description": "Remove ads, negative comments, simplify layout",
    "created_at": 1768970000000,
    "transforms": [
        {
            "type": "remove-elements",
            "params": {
                "selectors": [".ad", ".advertisement", ".sponsored"]
            }
        },
        {
            "type": "sentiment-filter",
            "params": {
                "threshold": 0.4,
                "target_elements": [".comment", ".reply"]
            }
        },
        {
            "type": "simplify",
            "params": {
                "remove_styles": true,
                "remove_scripts": true
            }
        }
    ],
    "tags": ["news", "readability", "clean"]
}
```

### Profile Storage

Profiles are stored as data files attached to a special entity:

```
Namespace: html-cache
Cache Key: _profiles/transforms
Data Files:
  - profiles/clean-news.json
  - profiles/minimal-view.json
  - profiles/focus-mode.json
```

### MitmProxy Integration

```
┌─────────────────┐                      ┌──────────────────────────────────┐
│   Browser       │                      │     MitmProxy                     │
│                 │  Cookie:             │                                   │
│  GET /article   │  transform=clean-news│  1. Intercept request             │
│  ─────────────► │  ──────────────────► │  2. Extract transform cookie      │
│                 │                      │  3. Fetch HTML from origin        │
│                 │                      │  4. Store raw HTML                │
│                 │                      │  5. Lookup profile "clean-news"   │
│                 │                      │  6. Apply transformations         │
│                 │                      │  7. Return transformed HTML       │
│  ◄───────────── │  ◄────────────────── │                                   │
│  Transformed    │                      │                                   │
└─────────────────┘                      └──────────────────────────────────┘
```

### Profile API Endpoints

```
# List all profiles
GET /flet-html-domain/profiles/{namespace}

# Get profile by ID
GET /flet-html-domain/profiles/{namespace}/{profile_id}

# Create/update profile
POST /flet-html-domain/profiles/{namespace}/{profile_id}
{
    "name": "Clean News",
    "transforms": [...],
    "tags": ["news", "clean"]
}

# Delete profile
DELETE /flet-html-domain/profiles/{namespace}/{profile_id}

# Apply profile to HTML (by cache_id)
POST /flet-html-domain/profiles/{namespace}/{profile_id}/apply
{
    "cache_id": "aa27fb2a-..."
}

# Apply profile to HTML (by cache_key)
POST /flet-html-domain/profiles/{namespace}/{profile_id}/apply/key/{cache_key}
```

---

## Site Browsing & Batch Operations

### Browse Pages by Site

Since cache keys are structured as `{domain}/{path}`, we can list all pages for a site:

```
# List all pages for a domain
GET /cache-entity/{namespace}/entities/site/{domain}

Example:
GET /cache-entity/html-cache/entities/site/example.com

Response:
{
    "success": true,
    "namespace": "html-cache",
    "domain": "example.com",
    "count": 15,
    "pages": [
        {
            "cache_id": "aa27fb2a-...",
            "cache_key": "example.com/root",
            "path": "/",
            "stored_at": 1768955158133
        },
        {
            "cache_id": "bb38gc3b-...",
            "cache_key": "example.com/about",
            "path": "/about",
            "stored_at": 1768955200000
        },
        {
            "cache_id": "cc49hd4c-...",
            "cache_key": "example.com/blog/post-1",
            "path": "/blog/post-1",
            "stored_at": 1768955300000
        }
    ]
}
```

### Batch Apply Transformations

Apply a profile to all pages of a site:

```
POST /flet-html-domain/profiles/{namespace}/{profile_id}/apply/site/{domain}

Example:
POST /flet-html-domain/profiles/html-cache/clean-news/apply/site/news.example.com

Response:
{
    "success": true,
    "profile_id": "clean-news",
    "domain": "news.example.com",
    "pages_processed": 15,
    "pages_transformed": 14,
    "pages_failed": 1,
    "results": [
        {
            "cache_key": "news.example.com/article-1",
            "original_cache_id": "aa27fb2a-...",
            "transformed_cache_id": "xx11yy22-...",
            "status": "success"
        },
        ...
    ]
}
```

### Site Browser UI

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          Site Browser                                    │
├─────────────────────────────────────────────────────────────────────────┤
│  Domain: [example.com____________] [Browse]                             │
│                                                                          │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ Found 15 pages                          [Select All] [Clear All]  │  │
│  ├───────────────────────────────────────────────────────────────────┤  │
│  │ [x] /                    Jan 20, 2026    1.2 KB                   │  │
│  │ [x] /about               Jan 20, 2026    2.4 KB                   │  │
│  │ [x] /blog/post-1         Jan 20, 2026    5.1 KB                   │  │
│  │ [x] /blog/post-2         Jan 20, 2026    4.8 KB                   │  │
│  │ [ ] /contact             Jan 19, 2026    1.1 KB                   │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                                                          │
│  Apply Profile: [clean-news ▼]                                          │
│                                                                          │
│  [Apply to Selected (4)]          [View Selected] [Delete Selected]     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Page & Site Analysis (New Workflow)

### Analysis Types

| Analysis | Scope | Output |
|----------|-------|--------|
| Structure Analysis | Single page | DOM tree, element counts, hierarchy |
| Content Analysis | Single page | Word count, topics, sentiment, readability |
| Link Analysis | Single page | Internal/external links, broken links |
| Site Map | Entire site | Page hierarchy, navigation structure |
| Site Content | Entire site | Topic distribution, content patterns |
| Site Comparison | Multiple versions | Changes over time, diff |

### Analysis Storage

Analysis results are stored as data files on the entity:

```
Entity: example.com/about
Cache Key: example.com/about
Data Files:
  - html/raw.txt                           # Original HTML
  - analysis/structure.json                # DOM structure analysis
  - analysis/content.json                  # Content analysis
  - analysis/links.json                    # Link analysis
  - graphs/dom-tree.json                   # Graph data (for visualization)
  - graphs/link-graph.json                 # Link graph data
```

Site-level analysis stored on a special entity:

```
Entity: example.com/_site-analysis
Cache Key: example.com/_site-analysis
Data Files:
  - analysis/sitemap.json                  # Site structure
  - analysis/content-overview.json         # Aggregated content analysis
  - graphs/site-structure.json             # Site hierarchy graph
  - graphs/internal-links.json             # Internal link graph
```

### Analysis API Endpoints

#### Page Analysis

```
# Analyze single page structure
POST /flet-html-domain/html/analyze/{namespace}/structure
{
    "cache_id": "aa27fb2a-..."
}

Response:
{
    "success": true,
    "cache_id": "aa27fb2a-...",
    "analysis": {
        "element_count": 245,
        "max_depth": 12,
        "elements": {
            "div": 45,
            "p": 23,
            "span": 67,
            "a": 34,
            "img": 12
        },
        "structure": {
            "has_header": true,
            "has_footer": true,
            "has_nav": true,
            "main_content_selector": "article.content"
        }
    },
    "saved_to": "analysis/structure.json"
}

# Analyze page content (uses LLM)
POST /flet-html-domain/html/analyze/{namespace}/content
{
    "cache_id": "aa27fb2a-..."
}

Response:
{
    "success": true,
    "cache_id": "aa27fb2a-...",
    "analysis": {
        "word_count": 1234,
        "reading_time_minutes": 5,
        "language": "en",
        "topics": ["technology", "AI", "machine learning"],
        "sentiment": {
            "overall": 0.7,
            "distribution": {"positive": 0.6, "neutral": 0.3, "negative": 0.1}
        },
        "readability": {
            "grade_level": 12,
            "flesch_score": 45
        },
        "entities": ["OpenAI", "GPT-4", "Claude"]
    },
    "saved_to": "analysis/content.json"
}

# Analyze page links
POST /flet-html-domain/html/analyze/{namespace}/links
{
    "cache_id": "aa27fb2a-..."
}

Response:
{
    "success": true,
    "cache_id": "aa27fb2a-...",
    "analysis": {
        "total_links": 34,
        "internal_links": 20,
        "external_links": 14,
        "internal": [
            {"href": "/about", "text": "About Us"},
            {"href": "/contact", "text": "Contact"}
        ],
        "external": [
            {"href": "https://twitter.com/...", "text": "Twitter", "domain": "twitter.com"}
        ]
    },
    "saved_to": "analysis/links.json"
}
```

#### Site Analysis

```
# Generate site map from cached pages
POST /flet-html-domain/site/analyze/{namespace}/sitemap/{domain}

Response:
{
    "success": true,
    "domain": "example.com",
    "analysis": {
        "total_pages": 15,
        "hierarchy": {
            "/": {
                "children": ["/about", "/contact", "/blog"],
                "depth": 0
            },
            "/blog": {
                "children": ["/blog/post-1", "/blog/post-2"],
                "depth": 1
            }
        },
        "orphan_pages": ["/old-page"],
        "max_depth": 3
    },
    "saved_to": "example.com/_site-analysis/analysis/sitemap.json"
}

# Analyze site content overview
POST /flet-html-domain/site/analyze/{namespace}/content/{domain}

Response:
{
    "success": true,
    "domain": "example.com",
    "analysis": {
        "total_pages": 15,
        "total_words": 45000,
        "avg_words_per_page": 3000,
        "topic_distribution": {
            "technology": 0.4,
            "business": 0.3,
            "news": 0.2,
            "other": 0.1
        },
        "content_freshness": {
            "last_7_days": 5,
            "last_30_days": 10,
            "older": 5
        }
    },
    "saved_to": "example.com/_site-analysis/analysis/content-overview.json"
}
```

### Graph Generation

```
# Generate DOM tree graph for visualization
POST /flet-html-domain/html/graph/{namespace}/dom-tree
{
    "cache_id": "aa27fb2a-...",
    "max_depth": 5,
    "format": "d3"           # or "mermaid", "graphviz"
}

Response:
{
    "success": true,
    "cache_id": "aa27fb2a-...",
    "graph": {
        "format": "d3",
        "nodes": [...],
        "edges": [...]
    },
    "saved_to": "graphs/dom-tree.json"
}

# Generate site link graph
POST /flet-html-domain/site/graph/{namespace}/links/{domain}

Response:
{
    "success": true,
    "domain": "example.com",
    "graph": {
        "format": "d3",
        "nodes": [
            {"id": "/", "label": "Home", "type": "page"},
            {"id": "/about", "label": "About", "type": "page"},
            {"id": "twitter.com", "label": "Twitter", "type": "external"}
        ],
        "edges": [
            {"source": "/", "target": "/about", "type": "internal"},
            {"source": "/", "target": "twitter.com", "type": "external"}
        ]
    },
    "saved_to": "example.com/_site-analysis/graphs/internal-links.json"
}
```

### Analysis UI

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          Page Analysis                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  Page: example.com/about                           [Refresh Analysis]   │
│                                                                          │
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐            │
│  │ Structure       │ │ Content         │ │ Links           │            │
│  └─────────────────┘ └─────────────────┘ └─────────────────┘            │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │                     DOM Tree Visualization                       │    │
│  │                                                                  │    │
│  │              ┌──────┐                                           │    │
│  │              │ html │                                           │    │
│  │              └──┬───┘                                           │    │
│  │         ┌──────┴──────┐                                         │    │
│  │      ┌──┴──┐       ┌──┴──┐                                      │    │
│  │      │head │       │body │                                      │    │
│  │      └─────┘       └──┬──┘                                      │    │
│  │                 ┌─────┼─────┐                                   │    │
│  │              ┌──┴──┐ ┌┴─┐ ┌─┴──┐                                │    │
│  │              │header│ │...│ │footer│                            │    │
│  │              └─────┘ └──┘ └────┘                                │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ Content Summary                                                  │    │
│  │ Words: 1,234 | Reading Time: 5 min | Grade Level: 12            │    │
│  │ Topics: technology (40%), AI (35%), business (25%)               │    │
│  │ Sentiment: Positive (0.7)                                        │    │
│  └─────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          Site Analysis                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  Domain: example.com                               [Refresh Analysis]   │
│                                                                          │
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐            │
│  │ Site Map        │ │ Content         │ │ Link Graph      │            │
│  └─────────────────┘ └─────────────────┘ └─────────────────┘            │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │                   Site Link Graph                                │    │
│  │                                                                  │    │
│  │        ┌─────────────────────────────────────┐                  │    │
│  │        │                                     │                  │    │
│  │        ▼                                     │                  │    │
│  │   ┌────────┐      ┌─────────┐      ┌────────┴─┐                 │    │
│  │   │  Home  │─────►│  About  │─────►│ Contact  │                 │    │
│  │   └───┬────┘      └─────────┘      └──────────┘                 │    │
│  │       │                                                          │    │
│  │       ▼                                                          │    │
│  │   ┌────────┐      ┌─────────┐                                   │    │
│  │   │  Blog  │─────►│ Post 1  │                                   │    │
│  │   └───┬────┘      └─────────┘                                   │    │
│  │       │                                                          │    │
│  │       ▼                                                          │    │
│  │   ┌─────────┐                                                   │    │
│  │   │ Post 2  │                                                   │    │
│  │   └─────────┘                                                   │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  Stats: 15 pages | 45,000 words | Max depth: 3                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Updated Mini Apps List

| Mini App | Purpose |
|----------|---------|
| **Settings** | API configuration, connection testing |
| **API Explorer** | Postman-style request debugging |
| **HTML Workbench** | Before/after transformation view |
| **Profile Manager** | Create/edit/delete transformation profiles |
| **Site Browser** | Browse pages by domain, batch operations |
| **Page Analysis** | Structure, content, links analysis + graphs |
| **Site Analysis** | Site-wide analysis, sitemap, link graphs |

---

## Updated Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        HTML Transformation Workbench                         │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │ [Settings] [API Explorer] [Workbench] [Profiles] [Sites] [Analysis] │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      Active Mini App Container                       │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      Global Services Layer                          │    │
│  │  - API Client (html-graph, text-transform, llms)                   │    │
│  │  - Config Manager (local storage)                                   │    │
│  │  - Profile Manager (load/save profiles)                             │    │
│  │  - Event Bus                                                        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## FLeT: html-sentiment-filter

Filter content by sentiment (remove negative paragraphs, comments, etc.)

```
POST /flet-html-domain/html/transform/{namespace}/sentiment
{
    "cache_id": "aa27fb2a-...",
    "threshold": 0.3,              // Remove content with negativity > 0.3
    "target_elements": ["p", "div", "span"]
}

Response:
{
    "success": true,
    "original_cache_id": "aa27fb2a-...",
    "transformed_cache_id": "bb38gc3b-...",
    "removed_count": 5,
    "flow_saved": true
}
```

### FLeT: html-topic-filter

Keep only content related to specified topics:

```
POST /flet-html-domain/html/transform/{namespace}/topic
{
    "cache_id": "aa27fb2a-...",
    "topics": ["technology", "science"],
    "mode": "keep"                 // or "remove"
}

Response:
{
    "success": true,
    "original_cache_id": "aa27fb2a-...",
    "transformed_cache_id": "cc49hd4c-...",
    "matched_sections": 12,
    "flow_saved": true
}
```

### FLeT: html-simplify

Simplify HTML structure and styles:

```
POST /flet-html-domain/html/transform/{namespace}/simplify
{
    "cache_id": "aa27fb2a-...",
    "remove_styles": true,
    "remove_scripts": true,
    "remove_comments": true,
    "flatten_divs": true
}

Response:
{
    "success": true,
    "original_cache_id": "aa27fb2a-...",
    "transformed_cache_id": "dd50ie5d-...",
    "original_size": 45000,
    "simplified_size": 12000,
    "flow_saved": true
}
```

### FLeT: html-extract-content

Extract main content, remove boilerplate:

```
POST /flet-html-domain/html/transform/{namespace}/extract
{
    "cache_id": "aa27fb2a-...",
    "selector": "article",         // Optional CSS selector
    "auto_detect": true            // Use readability algorithm
}

Response:
{
    "success": true,
    "original_cache_id": "aa27fb2a-...",
    "transformed_cache_id": "ee61jf6e-...",
    "extracted_title": "Article Title",
    "word_count": 1234,
    "flow_saved": true
}
```

These FLeTs would:
1. Load original HTML from cache
2. Apply transformation (via LLM or algorithm)
3. Save transformed HTML as new version
4. Record flow data for debugging/inspection
5. Return both cache IDs for before/after comparison
