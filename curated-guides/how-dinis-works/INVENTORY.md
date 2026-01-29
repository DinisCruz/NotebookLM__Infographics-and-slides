# Content Inventory for "How Dinis Works" Presentation

*Quick reference showing which materials have full processing (README + SEMANTIC-GRAPH) vs partial*

---

## Fully Processed (README + SEMANTIC-GRAPH + Neo4j Ready)

These are ready for live demos and deep dives:

| # | Content | Topic | Assets |
|---|---------|-------|--------|
| 1 | [LLM-Assisted Development Workflow](../../linkedin-published/GenAI%20development/LLM-Assisted%20Development%20Workflow%20%28Jan%202026%29/) | AI Dev | Infographic, Slides, SKG |
| 2 | [Vibe Coding Workflow](../../linkedin-published/GenAI%20development/Vibe%20Coding%20Workflow%20for%20Rapid%20Product-Market%20Fit%20Discovery/) | AI Dev | Infographic, Slides, SKG |
| 3 | [Scientific American - Semantic Web](../../linkedin-published/3rd%20Party%20Content/%2828%20Jan%29%20-%20Scientific%20American%20-%20The%20Semantic%20Web%20%28May%202001%29/) | SKG Foundation | Infographic, Slides, SKG |
| 4 | [Unbaking the Cake](../../linkedin-published/3rd%20Party%20Content/Unbaking%20the%20Cake%20-%20Capturing%20Data%20Before%20Entropy/) | SKG/Data | Infographic, Slides, SKG |
| 5 | [Data Physics and Semantic Capture](../../linkedin-published/3rd%20Party%20Content/Data%20Physics%20and%20the%20Semantic%20Capture%20of%20Intent/) | SKG/Data | Infographic, Slides, SKG |
| 6 | [Semantic Podcast Recommender](../../linkedin-published/Cyber%20Security%20and%20Business/%2820%20Jan%29%20-%20Semantic%20Graph-Powered%20Podcast%20Recommender/) | SKG Application | Infographic, Slides, SKG |
| 7 | [AWS Well-Architected - MitmProxy](../../linkedin-published/Dev%20Briefs/MitmProxy%20Service/AWS%20Well-Architected%20Framework%20Review%20–%20Web%20Content%20Filtering%20Proxy%20Solution/) | Cloud Security | Infographic, Slides, SKG |
| 8 | [Deployment Modes - Proxy](../../linkedin-published/Dev%20Briefs/MitmProxy%20Service/Deployment%20Modes%20for%20the%20Man-in-the-Middle%20Proxy%20Solution%20%28AWS-Oriented%29/) | Cloud Security | Infographic, Slides, SKG |
| 9 | [Wardley Maps - Software Evolution](../../linkedin-published/Wardley%20Maps/Wardley%20Maps%20as%20an%20Analogy%20for%20Software%20Development%20Evolution/) | Strategy | Infographic, Slides, SKG |
| 10 | [osbot-fast-api v0.34.0](../../by-topic/dev-briefs/osbot-fast-api/v0.34.0__debrief__unified-service-client-architecture/) | Dev Brief | Infographic, Slides, SKG |
| 11 | [Open Source Strategy](../../by-topic/Dinis%20Cruz%20-%20Startups/Open%20Source%20strategy/) | Startups | Infographic, Slides, SKG |
| 12 | [EU-US Tech Decoupling](../../by-topic/3rd%20party%20-%20documents%20or%20text%20/What%20if%20Europe%20Severed%20Tech%20Ties%20with%20the%20U.S._/) | Geopolitics | Infographic, Slides, SKG |
| 13 | [Phoenix Security Graph Technologies](../../by-date/2026/Jan/29/Technical%20Briefing%20on%20Phoenix%20Security%20Graph%20Technologies,%20Data%20Models,%20and%20genAI%20Integration/) | ASPM/Graph | Infographic, Slides, SKG |

---

## Partially Processed (README or Infographic only)

These have visual assets but may need SEMANTIC-GRAPH enhancement:

| # | Content | Topic | Missing |
|---|---------|-------|---------|
| 1 | [Next-Level Vibe Coding (Multi-Agent)](../../linkedin-published/Development%20and%20Enginering/%2818%20Jan%29%20-%20Next-Level%20Vibe%20Coding_%20Multi-Agent%20%26%20Multi-Role%20Workflow/) | AI Dev | SEMANTIC-GRAPH |
| 2 | [Pass-Driven Development](../../linkedin-published/Development%20and%20Enginering/%2816%20Jan%29%20-%20Pass-Driven%20Development%20%28PDD%29%20-%20A%20Practical%20Alternative%20to%20Traditional%20TDD/) | Dev Methodology | SEMANTIC-GRAPH |
| 3 | [Human Checkpoints in AI Workflows](../../linkedin-published/Development%20and%20Enginering/%2816%20Jan%29%20-%20The%20Importance%20of%20Human%20Checkpoints%20in%20AI%20Agent%20Workflows/) | AI Dev | SEMANTIC-GRAPH |
| 4 | [Claude Flow - CRM Series](../../linkedin-published/GenAI%20development/Claude%20Flow/Claude%20Flow%20-%20CRM%20Hive-Mind%20Series/) | Multi-Agent | SEMANTIC-GRAPH (series) |
| 5 | [GenCloudTwin](../../linkedin-published/Cyber%20Security%20and%20Business/%282%20Jan%29%20-%20%20GenCloudTwin%20-%20Creating%20a%20Cloud%20Environment%20Digital%20Twin%20with%20Semantic%20Knowledge%20Graphs/) | Cloud Security | SEMANTIC-GRAPH |
| 6 | [GenCloudBCP](../../linkedin-published/Cyber%20Security%20and%20Business/%282%20Jan%29%20-%20Ephemeral%20Cloud%20Environments%20and%20GenCloudBCP_%20A%20New%20Paradigm%20for%20Resilience%20and%20Disaster%20Recovery/) | Cloud Security | SEMANTIC-GRAPH |
| 7 | [Empowering GenAI Entrepreneur](../../linkedin-published/Development%20and%20Enginering/%2818%20Jan%29%20-%20Empowering%20the%20GenAI%20Entrepreneur_%20From%20Idea%20to%20Impact/) | Startups | SEMANTIC-GRAPH |
| 8 | [Runtime Type Safety + PDD](../../linkedin-published/Development%20and%20Enginering/%2816%20Jan%29%20-%20Combining%20Pass%E2%80%91Driven%20Development%20and%20Runtime%20Type%20Safety%20for%20Resilient%20Software/) | Dev Methodology | SEMANTIC-GRAPH |

---

## Recommended Presentation Flow

For a ~30 minute presentation:

### Opening (5 min)
- **This repository** — Quick GitHub tour showing structure
- **The workflow diagram** — Source → NotebookLM → Claude Cowork → SKG

### Core Demo (15 min)
1. **Semantic Web article** (#3) — Show the foundational vision
2. **LLM-Assisted Development** (#1) — Actual workflow in action
3. **Unbaking the Cake** (#4) — Why native capture beats RAG
4. **AWS Well-Architected - MitmProxy** (#7) — Security-focused content

### Live Demo (5-10 min)
- Show Claude Cowork processing a staged file
- Open a SEMANTIC-GRAPH.md in GitHub (Mermaid renders)
- Import Cypher into Neo4j sandbox (if time)

### Q&A / Discussion
- How this applies to different domains
- Multi-agent orchestration patterns

---

## Concept Mapping for Different Audiences

These concepts from the workflow map to various domains:

| Concept | Application |
|---------|-------------|
| Semantic Knowledge Graphs | Contextualizing complex findings |
| Ontologies + Taxonomies | Standardizing classifications |
| Neo4j export | Graph-based reasoning |
| Multi-agent orchestration | Automated triage workflows |
| Human checkpoints | Analyst approval gates |
| Native semantic capture | Capturing context at source |

---

## Materials to Potentially Add

To enhance the collection:

1. **Add SEMANTIC-GRAPH to GenCloudTwin** — Relevant to cloud infrastructure
2. **Add SEMANTIC-GRAPH to Human Checkpoints** — Directly applies to approval workflows
3. **Create domain-specific sections** with relevant grouped content

---

*Inventory generated: January 2026*
