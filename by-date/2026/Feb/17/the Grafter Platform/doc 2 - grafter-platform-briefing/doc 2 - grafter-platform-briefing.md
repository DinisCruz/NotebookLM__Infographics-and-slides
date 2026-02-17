# The Grafter Platform — Strategic Briefing

**Project:** The Grafter — Next-Generation Platform  
**Lead:** Rachel  
**Date:** February 2026  
**Status:** Discovery & Architecture Phase

---

## Executive Summary

The Grafter is a community-driven programme helping startups grow, mature, and exit. The team has built an initial platform, but adoption remains limited due to a UX-heavy, compliance-style approach that places too much burden on the user. This briefing outlines a strategic vision for rethinking the platform around graph-based data, open-source principles, and an agentic delivery model.

---

## Current State & Challenges

The existing platform digitised a 200-page document and 400-slide deck into an online form-based experience. While a reasonable first step, it suffers from several structural issues:

- **High user friction** — Users are asked extensive open-ended questions ("What's your go-to-market strategy?") that feel like compliance exercises rather than value-adding interactions.
- **Decontextualised guidance** — Recommendations are generic and disconnected from the user's actual stage, data, and circumstances.
- **Opaque data layer** — Built on something like Firebase with no easy way to export, query, or programmatically access the underlying data. Export is HTML-only.
- **Shallow directory** — The community directory requires manual profile entry (10+ minutes) when most of this information is publicly available (e.g. LinkedIn).
- **No graph connectivity** — Users exist in isolation. There's no mechanism to surface meaningful connections between community members.

---

## Strategic Vision

### 1. Invert the User Experience

**Core principle: minimise user effort, maximise value.**

Instead of asking open-ended questions, the platform should:

- Accept lightweight inputs — a URL, a voice memo, a brain dump — and extract structured data from them.
- Present the user with confirmation prompts ("Is this correct?") rather than blank forms. Confirming facts is dramatically easier than generating answers from scratch.
- Use generative AI to pre-populate profiles, assessments, and recommendations from publicly available data and user-provided context.

### 2. Build on a Graph Data Model

Everything in the platform is fundamentally a graph — companies, people, capabilities, recommendations, data points, and the relationships between them.

- **Capture facts as discrete data points** that feed into analysis and recommendations.
- **Hyperlink every recommendation back to its source data points.** If a recommendation says "you need a go-to-market strategy," the user should see exactly which data points drove that conclusion.
- **Make the graph reactive** — when underlying facts change, conclusions and recommendations update accordingly. This transforms the platform from a static checklist into a living advisory system.
- **Use Wardley Maps as a foundational lens.** Guidance must be stage-dependent. A pre-revenue startup and a scaling company need fundamentally different advice, and the mapping should reflect this.

### 3. Reimagine the Community Directory

Move from a passive directory to an active connection engine:

- **Auto-populate profiles** from LinkedIn and other public sources. Users should only need to provide a URL.
- **Surface graph-based connections** — show users how their graph intersects with others. Who could help them? Who could they help?
- **Adopt a mutual-opt-in model** (inspired by matching platforms): suggest potential connections to both parties independently, and only facilitate introductions when both signal interest. This eliminates the awkwardness of cold outreach within the community.
- **Leverage existing community strength** — the Grafter already has an active WhatsApp group where members know each other. The platform should amplify these existing relationships, not replace them.

### 4. Open Platform Architecture

- **Database = file system.** No opaque datastores. Data should be stored in simple, portable formats (JSON) that are easy to export, share, and version.
- **Every page should expose its data.** If I can see it in the UI, I should be able to get the underlying JSON.
- **Full data export** as a first-class feature — users own their data.
- **Public by default** for non-sensitive data. Sensitive/strategic data is where the premium tier lives.

### 5. Open Source Strategy

The entire platform base should be open source:

- Companies can deploy it internally, learn from it, and contribute back.
- External agents and bots can connect to it programmatically — if it's open source, anyone can integrate with the "mothership" on their own terms.
- The open-source model drives adoption, trust, and ecosystem growth far beyond what a closed platform can achieve.

### 6. Business Model

An on-demand, value-based model rather than gating basic functionality:

| Tier | What's Included |
|------|-----------------|
| **Free / Open Source** | Core platform, public data, community features, data export |
| **Subscription** | Community membership (as exists today), premium features |
| **Premium / On-Demand** | Protected/private strategy data, security features, advanced advisory services |

The monetisation layer sits on top of protection, privacy, and premium services — not on locking down the base platform.

---

## Agentic Delivery Model

The platform build will follow an established multi-agent methodology. Given that this phase is architecture and strategy (no code or deployment yet), the active agent roles are:

| Agent Role | Responsibility |
|------------|----------------|
| **Architect** | Platform architecture, data model design, integration patterns |
| **Conductor** | Orchestration, sequencing, dependency management |
| **Cartographer** | Wardley Mapping integration, stage-based guidance logic |
| **Librarian** | Knowledge management, content structuring, taxonomy |
| **Journalist** | Documentation, briefings, stakeholder communications |
| **Historian** | Decision logging, context preservation, rationale tracking |

**Not needed at this stage:** Developer (no code yet), DevOps (no shipping yet), QA (nothing to test yet).

---

## Recommended Next Steps

1. **Map the full graph schema** — define entities, relationships, and data points that form the platform's core data model.
2. **Design the open-source architecture** — file-system-based storage, JSON-driven pages, API-first design.
3. **Prototype the "low-friction intake" flow** — URL-to-profile, voice-memo-to-assessment, confirm-don't-create UX patterns.
4. **Define the Wardley Map integration** — how stage assessment drives recommendation context.
5. **Scope the connection engine** — mutual-opt-in matching, graph intersection surfacing.
6. **Produce a detailed project plan** with agent assignments and delivery phases.

---

*This briefing was synthesised from a voice memo capture and is intended as a strategic starting point for detailed planning.*
