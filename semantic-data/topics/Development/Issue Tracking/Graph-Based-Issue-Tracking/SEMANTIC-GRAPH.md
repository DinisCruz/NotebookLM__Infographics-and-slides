# Graph-Based Issue Tracking

[📖 README](./README.md) · [🖼️ Infographic](../../../../sources/2026/01/31/Graph-Based-Issue-Tracking/31%20Jan%20%20-%20Git-Native%20vs.%20Traditional%20Trackers.jpg) · [📑 Slides](../../../../sources/2026/01/31/Graph-Based-Issue-Tracking/31%20Jan-%20The_Repository_as_AI_Infrastructure.pdf) · [🏠 Home](../../../../../README.md)

> *Semantic Knowledge Graph (SKG) — machine-readable metadata for search, discovery, and graph database integration*

---

## Summary

A graph-based issue tracking system that lives inside your Git repository, eliminating external dependencies, authentication complexity, and vendor lock-in. Issues, tasks, relationships, and activity logs are stored as JSON files alongside your code — committed, versioned, and portable. This is infrastructure designed for AI agent collaboration.

---

## Key Concepts

- **Git-Native**: Issues stored in `.issues/` directory as JSON files, versioned with code
- **Zero External Dependencies**: Works with git credentials alone, no API tokens or OAuth
- **AI Agent Infrastructure**: Designed for Claude Code and similar agents to track work
- **Graph Structure**: Issues connect via typed relationships (blocks, depends-on, relates-to)
- **Portable**: Works across any git host — GitHub, GitLab, Bitbucket, self-hosted
- **Offline-Capable**: No network required to create, update, or query issues

---

## Core Arguments

1. AI agents need task management but traditional trackers introduce authentication complexity
2. Storing issues in the repository eliminates vendor lock-in and external dependencies
3. Git credentials already exist — no new authentication mechanism needed
4. Issue history versioned with code history enables powerful correlation
5. JSON files are simple, portable, and tool-agnostic
6. Graph structure enables rich relationships between issues, tasks, and code

---

## Key Quotes

> "This isn't just another issue tracker. It's infrastructure for AI agent collaboration."

> "When AI agents like Claude Code work on your codebase, they need a way to track tasks, report progress, and coordinate with humans and other agents."

> "Make the repository itself the source of truth."

---

## Tags

`issue-tracking` `git-native` `ai-agents` `claude-code` `task-management` `graph-database` `json` `portable` `offline` `devops` `infrastructure` `collaboration`

---

## Search Phrases

- "git native issue tracking"
- "issue tracker without external dependencies"
- "AI agent task management"
- "store issues in git repository"
- "alternative to github issues"
- "portable issue tracking"
- "issue tracking for AI coding assistants"

---

## Semantic Knowledge Graph

### System Architecture (Visual)

```mermaid
flowchart TB
    subgraph repo ["📁 YOUR REPOSITORY"]
        src["src/"]
        tests["tests/"]
        docs["docs/"]
        issues[".issues/"]
    end

    subgraph issueStructure ["📋 .issues/ STRUCTURE"]
        index["_index.json"]
        config["config/"]
        data["data/"]
    end

    subgraph consumers ["👥 CONSUMERS"]
        cli["CLI Tools"]
        webui["Web UI"]
        claude["Claude Code"]
        cicd["CI/CD"]
    end

    issues --> issueStructure
    consumers --> issues

    style repo fill:#e8f5e9,stroke:#4caf50
    style issueStructure fill:#e3f2fd,stroke:#1976d2
    style consumers fill:#fff3e0,stroke:#ff9800
```

### Ontology

```mermaid
classDiagram
    class Issue {
        <<type>>
        A trackable work item
        id: string
        title: string
        status: Status
    }
    class Task {
        <<type>>
        A subtask of an issue
    }
    class Bug {
        <<type>>
        A defect to fix
    }
    class Feature {
        <<type>>
        A capability to add
    }
    class Relationship {
        <<type>>
        Connection between issues
        blocks, depends_on, relates_to
    }

    Issue <|-- Task
    Issue <|-- Bug
    Issue <|-- Feature
    Issue --> Relationship : has
    Relationship --> Issue : connects
```

### Taxonomy

```mermaid
mindmap
  root((Git-Native Issues))
    Problem Space
      Authentication Complexity
      Vendor Lock-in
      Network Dependencies
      Rate Limiting
    Solution Components
      .issues/ Directory
      JSON Storage
      Graph Relationships
      Activity Logs
    Consumers
      Human Developers
      AI Agents
      CI/CD Pipelines
      Web UI
    Benefits
      No API Tokens
      Works Offline
      Versioned History
      Portable
```

### Knowledge Graph

```mermaid
graph TB
    subgraph problems ["❌ TRADITIONAL TRACKERS"]
        P1["Auth Complexity\n(API tokens, OAuth)"]
        P2["Vendor Lock-in\n(proprietary DB)"]
        P3["Network Required\n(online only)"]
        P4["Rate Limiting\n(API quotas)"]
    end

    subgraph solution ["✅ GIT-NATIVE SOLUTION"]
        S1["Graph-Based Issues\n(methodology)"]
        S2[".issues/ Directory\n(storage)"]
        S3["JSON Files\n(format)"]
    end

    subgraph benefits ["🎯 BENEFITS"]
        B1["Uses Git Creds\n(no new auth)"]
        B2["Works Offline\n(local first)"]
        B3["Versioned with Code\n(history)"]
        B4["Portable\n(any git host)"]
    end

    subgraph agents ["🤖 AI AGENTS"]
        A1["Claude Code"]
        A2["GitHub Copilot"]
        A3["Cursor"]
    end

    P1 & P2 & P3 & P4 -.->|addressed_by| S1
    S1 -->|uses| S2
    S2 -->|contains| S3
    S1 -->|produces| B1 & B2 & B3 & B4
    A1 & A2 & A3 -->|uses| S1

    style P1 fill:#ffcdd2,stroke:#c62828
    style P2 fill:#ffcdd2,stroke:#c62828
    style P3 fill:#ffcdd2,stroke:#c62828
    style P4 fill:#ffcdd2,stroke:#c62828
    style S1 fill:#c8e6c9,stroke:#2e7d32
    style S2 fill:#c8e6c9,stroke:#2e7d32
    style S3 fill:#c8e6c9,stroke:#2e7d32
    style B1 fill:#fff3e0,stroke:#f57c00
    style B2 fill:#fff3e0,stroke:#f57c00
    style B3 fill:#fff3e0,stroke:#f57c00
    style B4 fill:#fff3e0,stroke:#f57c00
    style A1 fill:#e1bee7,stroke:#7b1fa2
    style A2 fill:#e1bee7,stroke:#7b1fa2
    style A3 fill:#e1bee7,stroke:#7b1fa2
```

### Cypher Import (Neo4j)

```cypher
// ============================================
// NODES
// ============================================

// The solution
CREATE (git_issues:Methodology {
    id: 'graph_based_issue_tracking',
    name: 'Graph-Based Issue Tracking',
    description: 'Git-native issue tracking for AI agent coordination'
})

// Components
CREATE (issues_dir:Component {id: 'issues_dir', name: '.issues/ Directory'})
CREATE (json_storage:Component {id: 'json_storage', name: 'JSON File Storage'})
CREATE (graph_relationships:Component {id: 'graph_rel', name: 'Graph Relationships'})

// Problems solved
CREATE (auth_complexity:Challenge {id: 'auth', name: 'Authentication Complexity'})
CREATE (vendor_lockin:Challenge {id: 'lockin', name: 'Vendor Lock-in'})
CREATE (network_required:Challenge {id: 'network', name: 'Network Dependencies'})
CREATE (rate_limiting:Challenge {id: 'ratelimit', name: 'Rate Limiting'})

// Benefits
CREATE (git_creds:Benefit {id: 'git_creds', name: 'Uses Git Credentials'})
CREATE (offline:Benefit {id: 'offline', name: 'Works Offline'})
CREATE (versioned:Benefit {id: 'versioned', name: 'Versioned with Code'})
CREATE (portable:Benefit {id: 'portable', name: 'Portable Across Hosts'})

// AI Agents
CREATE (claude_code:Agent {id: 'claude_code', name: 'Claude Code'})
CREATE (copilot:Agent {id: 'copilot', name: 'GitHub Copilot'})
CREATE (cursor:Agent {id: 'cursor', name: 'Cursor'})

// ============================================
// RELATIONSHIPS
// ============================================

CREATE (git_issues)-[:USES]->(issues_dir)
CREATE (git_issues)-[:USES]->(json_storage)
CREATE (git_issues)-[:USES]->(graph_relationships)

CREATE (git_issues)-[:ADDRESSES]->(auth_complexity)
CREATE (git_issues)-[:ADDRESSES]->(vendor_lockin)
CREATE (git_issues)-[:ADDRESSES]->(network_required)
CREATE (git_issues)-[:ADDRESSES]->(rate_limiting)

CREATE (git_issues)-[:PRODUCES]->(git_creds)
CREATE (git_issues)-[:PRODUCES]->(offline)
CREATE (git_issues)-[:PRODUCES]->(versioned)
CREATE (git_issues)-[:PRODUCES]->(portable)

CREATE (claude_code)-[:CAN_USE]->(git_issues)
CREATE (copilot)-[:CAN_USE]->(git_issues)
CREATE (cursor)-[:CAN_USE]->(git_issues)
```
