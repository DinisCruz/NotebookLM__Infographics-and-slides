# GitHub Repository Brief — The Grafter (Customer Perspective)

**Project:** The Grafter — Open Source Customer Engagement  
**Organisation:** The Cyber Boardroom  
**Date:** February 2026  
**Licence:** Creative Commons (documentation & materials) + appropriate OSS licence (code/bots)

---

## Purpose

This repository serves as the working space for The Cyber Boardroom's engagement with The Grafter platform — approached from the perspective of an active customer who wants to consume, contribute to, and help improve the platform.

The repo has three functions:

1. **Documentation hub** — publish all strategic briefs, research, and materials being produced during this engagement.
2. **Agent/bot workspace** — house the first versions of the agentic setup (architect, journalist, cartographer, etc.) being used to drive the project.
3. **Demonstration environment** — provide a tangible, working example that shows the project lead and stakeholders how the agentic workflow, open-source model, and tooling fit together.

---

## Repository Structure

```
grafter-customer/
├── README.md                          # Project overview, context, and how to navigate
├── LICENSE                            # Creative Commons for docs, OSS for code
├── docs/
│   ├── strategic-briefing.md          # Platform strategic vision
│   ├── technical-discovery-brief.md   # Interview brief for current Technical Lead
│   ├── architecture/                  # Architecture decisions and proposals
│   └── research/                      # Public information research on Grafter
├── agents/
│   ├── README.md                      # Agent setup overview and roles
│   ├── architect/                     # Architect agent config and prompts
│   ├── journalist/                    # Journalist agent config and prompts
│   ├── cartographer/                  # Cartographer agent (Wardley Maps)
│   ├── librarian/                     # Knowledge management agent
│   ├── historian/                     # Decision logging agent
│   └── conductor/                     # Orchestration agent
├── research/
│   ├── public-info/                   # Collected public information about Grafter
│   └── platform-analysis/            # Analysis of current platform (public-facing)
└── materials/
    └── voice-memos/                   # Transcripts and synthesised outputs
```

---

## What Gets Published

Everything in this repo is shared openly as a demonstration of the open-source, customer-driven approach being advocated for the platform itself. Specifically:

- **Strategic documents** — all briefs, proposals, and architecture recommendations (Creative Commons licensed).
- **Agent configurations** — the bot/agent setup including prompts, role definitions, and orchestration patterns.
- **Research outputs** — publicly sourced information about the Grafter platform and ecosystem.
- **Voice memo syntheses** — processed and structured outputs from voice memo captures (not raw audio).

---

## Narrative & Positioning

The story this repo tells:

> "We are a customer of The Grafter. We want to help. We want to start consuming the platform and contributing back. So we're sharing all of our ideas, research, and tooling openly — under Creative Commons and open-source licences — as a practical demonstration of what an open-source, community-driven approach to the platform could look like."

This is not a fork or a competing project. It's a customer showing up with tools, ideas, and willingness to contribute — and publishing everything transparently so others can do the same.

---

## Initial Setup Tasks

1. **Create the repo** with the directory structure above.
2. **Add existing documents** — migrate the strategic briefing and technical discovery brief into `docs/`.
3. **Set up the agent framework** — initialise the conductor and first agent roles in `agents/`.
4. **Run public research** — use available tools (web search, AI research) to gather and document publicly available information about The Grafter platform, team, and ecosystem into `research/`.
5. **Configure CI/CD** — basic linting and doc validation (lightweight, expand later).
6. **Write the README** — clear narrative on who this is for, what it contains, and how to engage.

---

## Licensing

| Content Type | Licence |
|-------------|---------|
| Documentation, briefs, research | Creative Commons (CC BY 4.0 recommended) |
| Code, agent configs, tooling | MIT or Apache 2.0 (TBD based on dependencies) |

---

*This brief is part of The Grafter platform strategic planning initiative, driven from a customer perspective by The Cyber Boardroom.*
