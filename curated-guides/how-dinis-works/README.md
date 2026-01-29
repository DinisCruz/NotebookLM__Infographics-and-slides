[🏠 Home](../../README.md) / [Curated Guides](../README.md) / **How Dinis Works**

---

# How Dinis Works: AI-Assisted Development & Semantic Knowledge Workflows

*A curated guide for senior developers and architects — showcasing the integration of GenAI tools, semantic knowledge graphs, and modern development practices.*

---

## Quick Summary

This guide demonstrates a practical, production-ready workflow that combines:

1. **AI-Assisted Development** — Using Claude, multi-agent orchestration, and "vibe coding" to build software faster
2. **Semantic Knowledge Graphs** — Capturing intent and context in machine-readable form
3. **NotebookLM Pipeline** — Transforming documents into infographics, slides, and exportable knowledge graphs
4. **Claude Cowork** — Real-time AI collaboration for content processing, code generation, and workflow automation

The result: solo developers or small teams can operate at the velocity of much larger organizations while maintaining quality through structured human checkpoints.

---

## The Workflow Stack (How This Repository is Built)

*This is the exact workflow used to create and maintain the content in this repository — you're seeing it in action right now with Claude Cowork.*

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                    CONTENT CREATION WORKFLOW (THIS REPO)                         │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│  ┌─────────────┐    ┌─────────────────┐    ┌──────────────────┐                 │
│  │  SOURCE     │    │  NOTEBOOKLM     │    │  CLAUDE COWORK   │                 │
│  │  DOCUMENTS  │───▶│  PROCESSING     │───▶│  ENHANCEMENT     │                 │
│  │             │    │                 │    │  (You are here)  │                 │
│  │ • Debriefs  │    │ • Infographics  │    │                  │                 │
│  │ • Analysis  │    │ • Slide decks   │    │ • README.md      │                 │
│  │ • Research  │    │ • Summaries     │    │ • SEMANTIC-GRAPH │                 │
│  │ • PDFs      │    │                 │    │ • Neo4j Cypher   │                 │
│  └─────────────┘    └─────────────────┘    └──────────────────┘                 │
│         │                                           │                            │
│         │                                           ▼                            │
│         │                              ┌──────────────────────┐                  │
│         │                              │  GITHUB REPOSITORY   │                  │
│         │                              │  NotebookLM__        │                  │
│         │                              │  Infographics-and-   │                  │
│         │                              │  slides              │                  │
│         │                              └──────────────────────┘                  │
│         │                                           │                            │
│         ▼                                           ▼                            │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                    SEMANTIC KNOWLEDGE LAYER                              │    │
│  │  • Machine-readable metadata  • Graph database integration               │    │
│  │  • LLM context for agents     • Cross-reference discovery                │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Curated Materials by Theme

### 1. AI-Assisted Development Workflow

*How to leverage LLMs and multi-agent systems for software development*

| Resource | Focus | Has SEMANTIC-GRAPH? | Link |
|----------|-------|---------------------|------|
| **LLM-Assisted Development Workflow** | Context engineering, human-in-the-loop | ✅ | [View →](../../linkedin-published/GenAI%20development/LLM-Assisted%20Development%20Workflow%20%28Jan%202026%29/) |
| **Vibe Coding Workflow** | 8-step rapid prototyping | ✅ | [View →](../../linkedin-published/GenAI%20development/Vibe%20Coding%20Workflow%20for%20Rapid%20Product-Market%20Fit%20Discovery/) |
| **Next-Level Vibe Coding: Multi-Agent** | Multi-agent orchestration | README | [View →](../../linkedin-published/Development%20and%20Enginering/%2818%20Jan%29%20-%20Next-Level%20Vibe%20Coding_%20Multi-Agent%20%26%20Multi-Role%20Workflow/) |
| **Pass-Driven Development (PDD)** | Iterative dev methodology | README | [View →](../../linkedin-published/Development%20and%20Enginering/%2816%20Jan%29%20-%20Pass-Driven%20Development%20%28PDD%29%20-%20A%20Practical%20Alternative%20to%20Traditional%20TDD/) |
| **Human Checkpoints in AI Workflows** | Quality gates for AI-assisted work | README | [View →](../../linkedin-published/Development%20and%20Enginering/%2816%20Jan%29%20-%20The%20Importance%20of%20Human%20Checkpoints%20in%20AI%20Agent%20Workflows/) |
| **Claude Flow - CRM Hive-Mind Series** | Real multi-agent dev example | README | [View →](../../linkedin-published/GenAI%20development/Claude%20Flow/Claude%20Flow%20-%20CRM%20Hive-Mind%20Series/) |

---

### 2. Semantic Knowledge Graphs

*Machine-readable context for AI agents and automation — enabling contextualization and reasoning*

| Resource | Focus | Has SEMANTIC-GRAPH? | Link |
|----------|-------|---------------------|------|
| **The Semantic Web (Scientific American 2001)** | Foundational vision by Tim Berners-Lee | ✅ | [View →](../../linkedin-published/3rd%20Party%20Content/%2828%20Jan%29%20-%20Scientific%20American%20-%20The%20Semantic%20Web%20%28May%202001%29/) |
| **Unbaking the Cake** | Native semantic capture vs RAG | ✅ | [View →](../../linkedin-published/3rd%20Party%20Content/Unbaking%20the%20Cake%20-%20Capturing%20Data%20Before%20Entropy/) |
| **Data Physics and Semantic Capture of Intent** | Capturing intent, not just data | ✅ | [View →](../../linkedin-published/3rd%20Party%20Content/Data%20Physics%20and%20the%20Semantic%20Capture%20of%20Intent/) |
| **Semantic Graph-Powered Podcast Recommender** | Practical SKG application | ✅ | [View →](../../linkedin-published/Cyber%20Security%20and%20Business/%2820%20Jan%29%20-%20Semantic%20Graph-Powered%20Podcast%20Recommender/) |
| **Wardley Maps as Software Evolution** | Strategic evolution mapping | ✅ | [View →](../../linkedin-published/Wardley%20Maps/Wardley%20Maps%20as%20an%20Analogy%20for%20Software%20Development%20Evolution/) |

---

### 3. Cloud Security & Infrastructure

*Content filtering, cloud architectures, and security patterns*

| Resource | Focus | Has SEMANTIC-GRAPH? | Link |
|----------|-------|---------------------|------|
| **Phoenix Security Graph Technologies** | Graph-based ASPM architecture, AI agents, MCP integration | ✅ | [View →](../../by-date/2026/Jan/29/Technical%20Briefing%20on%20Phoenix%20Security%20Graph%20Technologies,%20Data%20Models,%20and%20genAI%20Integration/) |
| **AWS Well-Architected Review - MitmProxy** | Six pillars security assessment | ✅ | [View →](../../linkedin-published/Dev%20Briefs/MitmProxy%20Service/AWS%20Well-Architected%20Framework%20Review%20–%20Web%20Content%20Filtering%20Proxy%20Solution/) |
| **Deployment Modes for Proxy Solution** | Six deployment patterns | ✅ | [View →](../../linkedin-published/Dev%20Briefs/MitmProxy%20Service/Deployment%20Modes%20for%20the%20Man-in-the-Middle%20Proxy%20Solution%20%28AWS-Oriented%29/) |
| **GenCloudTwin - Cloud Digital Twin** | Semantic KG for cloud environments | README | [View →](../../linkedin-published/Cyber%20Security%20and%20Business/%282%20Jan%29%20-%20%20GenCloudTwin%20-%20Creating%20a%20Cloud%20Environment%20Digital%20Twin%20with%20Semantic%20Knowledge%20Graphs/) |
| **GenCloudBCP - Ephemeral Cloud Environments** | Resilience and disaster recovery | README | [View →](../../linkedin-published/Cyber%20Security%20and%20Business/%282%20Jan%29%20-%20Ephemeral%20Cloud%20Environments%20and%20GenCloudBCP_%20A%20New%20Paradigm%20for%20Resilience%20and%20Disaster%20Recovery/) |
| **EU-US Tech Decoupling Scenario** | Geopolitical tech risk analysis | ✅ | [View →](../../by-topic/3rd%20party%20-%20documents%20or%20text%20/What%20if%20Europe%20Severed%20Tech%20Ties%20with%20the%20U.S._/) |

---

### 4. Development Practices & Dev Briefs

*Real-world architecture decisions and refactoring case studies*

| Resource | Focus | Has SEMANTIC-GRAPH? | Link |
|----------|-------|---------------------|------|
| **osbot-fast-api v0.34.0** | Unified Service Client Architecture | ✅ | [View →](../../by-topic/dev-briefs/osbot-fast-api/v0.34.0__debrief__unified-service-client-architecture/) |
| **osbot-fast-api v0.33.1** | Service registry pattern | ✅ | [View →](../../linkedin-published/Dev%20Briefs/OSBot%20Fast%20API%20Serverless/) |
| **Runtime Type Checking + PDD** | Type safety for resilient software | README | [View →](../../linkedin-published/Development%20and%20Enginering/%2816%20Jan%29%20-%20Combining%20Pass%E2%80%91Driven%20Development%20and%20Runtime%20Type%20Safety%20for%20Resilient%20Software/) |
| **Databases as Data Projections** | Architecture pattern | README | [View →](../../linkedin-published/Development%20and%20Enginering/%282%20Jan%29%20-%20Using%20Databases%20as%20Data%20Projections%2C%20Not%20Primary%20Data%20Stores/) |

---

### 5. Open Source Strategy & Startups

*Multi-venture approach and open-source business models*

| Resource | Focus | Has SEMANTIC-GRAPH? | Link |
|----------|-------|---------------------|------|
| **Open Source Strategy - Four Ventures** | Symbiotic startup development | ✅ | [View →](../../by-topic/Dinis%20Cruz%20-%20Startups/Open%20Source%20strategy/) |
| **MyFeeds.ai** | Personalized semantic news feeds | Partial | [View →](../../by-topic/Dinis%20Cruz%20-%20Startups/MyFeeds-AI/) |
| **The Cyber Boardroom** | CISO-Board AI communication | Partial | [View →](../../by-topic/Dinis%20Cruz%20-%20Startups/) |
| **Empowering the GenAI Entrepreneur** | From idea to impact | README | [View →](../../linkedin-published/Development%20and%20Enginering/%2818%20Jan%29%20-%20Empowering%20the%20GenAI%20Entrepreneur_%20From%20Idea%20to%20Impact/) |

---

## Why Semantic Knowledge Graphs?

The semantic knowledge graph approach shown here enables:

| Challenge | How SKG Helps |
|-----------|---------------|
| **Contextualizing findings** | Graphs capture relationships between entities |
| **Prioritizing information** | Knowledge graphs enable reasoning about impact chains |
| **Connecting disparate sources** | Graph-based architectures naturally connect data sources |
| **Actionable intelligence** | Neo4j-exportable graphs enable programmatic querying |
| **Reducing noise** | Ontologies + taxonomies filter signal from noise |

---

## Repository Statistics

| Metric | Count |
|--------|-------|
| README.md files | 70+ |
| SEMANTIC-GRAPH.md files | 28+ |
| Infographics (PNG/JPG) | 242+ |
| Slide decks (PDF) | 100+ |
| LinkedIn-published content | 40+ folders |
| Neo4j-ready knowledge graphs | 20+ |

---

## Live Demo Possibilities

1. **Show this repository in GitHub** — Demonstrate the folder structure and rendered Mermaid diagrams
2. **Claude Cowork processing** — Process a staged file live to show the README + SEMANTIC-GRAPH workflow
3. **Neo4j import** — Copy a Cypher script and show the graph visualization
4. **NotebookLM pipeline** — Show source document → infographic → slides transformation

---

## See Also

- [From Idea to Startup](../from-idea-to-startup/) — Complementary guide on the startup journey
- [Semantic Knowledge Graphs Collection](../semantic-knowledge-graphs/) — First batch of enhanced SEMANTIC-GRAPH files
- [CONTENT.md](./CONTENT.md) — Consolidated knowledge graph for this guide (coming soon)

---

*Guide prepared: January 2026*
