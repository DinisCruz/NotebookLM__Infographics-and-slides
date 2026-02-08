# Model Abstraction Layer (MAL)

## The Core Problem

Our data stores, environments, and authorization systems **were never designed for agentic workflows**. They were built for human-operated access patterns with implicit trust boundaries that agents don't respect — not because they're malicious, but because they're proactive, creative, and relentless in pursuing their goals. This mismatch is the root cause of the security horror stories emerging from early agentic deployments.

The fundamental issue: when we grant an agent access to a resource (an inbox, a filesystem, a calendar), we grant access to the *entire* resource with all its capabilities, when the actual task requires only a tiny, well-defined slice.

## The Concept

A **Model Abstraction Layer** sits between LLM agents and the underlying resources they interact with. It acts as a controlled, policy-driven proxy that reduces:

- **Attack surface** — fewer capabilities exposed means fewer vectors for exploitation
- **Data exposure** — only task-relevant data reaches the model
- **Blast radius** — if something goes wrong, damage is contained to the scoped surface

Architecturally, it slots in as an intermediary, potentially fitting into the MCP ecosystem:

```
Agent ←→ [MCP] ←→ Model Abstraction Layer ←→ [MCP] ←→ Resource
```

The MAL sits in front of the asset. It should look and feel like the real resource to the agent — a **transparent proxy** — but with strictly scoped data and capabilities.

## The Inbox Example

An email inbox is a deceptively dangerous resource. Granting an agent access to an inbox doesn't just expose emails — it exposes:

- Password reset links → account takeover
- 2FA tokens → bypass second-factor auth
- Contacts → social engineering surface
- Confidential data across the entire history
- Send capability → impersonation, phishing, social engineering
- Delete capability → destruction of evidence/records

**What the user actually wants:** "Check my latest emails and give me an overview."

**What the agent gets without MAL:** Full read/write/delete access to every email, contact, and credential ever received.

**What the agent should get with MAL:** A filtered, read-only view of the last week's emails, with password resets, 2FA tokens, and confidential content stripped out — served as clean structured JSON.

## Two Protection Dimensions

The abstraction layer protects along two axes:

### 1. Data Protection (Reads)
Control *what information* is consumed by the agent:

- Temporal filtering (last day, last week, last month)
- Content filtering (strip sensitive patterns: credentials, tokens, confidential markers)
- Structural filtering (only certain fields, certain senders, certain categories)
- Format normalization (raw email → clean JSON with consistent schema)

### 2. Capability Protection (Writes / Actions)
Control *what operations* the agent can perform:

- Restrict to read-only when write isn't needed
- Allow only specific action types (e.g., reply but not forward, create but not delete)
- Scope actions to specific targets (e.g., can only send to pre-approved contacts)
- Rate-limit or require confirmation for high-impact operations

This effectively adds a **fine-grained authorization layer** on top of existing systems whose native permissions lack the granularity needed for agent-level least-privilege.

## Operating Modes

### Transparent Mode
The agent interacts with what appears to be the real resource. The MAL is invisible — it intercepts and filters without the agent needing awareness. The interface contract matches the original resource.

### Normalized / Clean Mode
The MAL transforms the data into a cleaner, more structured format before the agent sees it. Instead of raw email with headers, MIME parts, and formatting artifacts, the agent receives a well-structured JSON document. This reduces token waste, prompt injection surface, and parsing errors.

### Progressive Trust Mode
Start narrow, expand over time:

1. Day 1: Last 24 hours of emails, read-only
2. Week 1: Last 7 days, read-only
3. Month 1: Last 30 days, read + draft replies
4. Established: Broader access with demonstrated safety

The trust boundary evolves based on observed behavior and accumulated confidence.

## Self-Describing Abstraction Layers

A powerful property: the MAL can **describe its own capabilities and constraints** to the agent. This enables:

- The agent can assess: "Is this surface sufficient for my task?"
- Negotiation: the agent requests additional scope if needed, with human approval
- Transparency: clear contract of what's available vs. what's restricted
- Auditability: the policy is explicit and inspectable

## Composability

Abstraction layers can be **chained** — MAL of MAL — each adding a level of scoping:

```
Agent → MAL (task-specific) → MAL (department-level) → Resource
```

This mirrors how schema-driven development controls data flow through schema mappings — here, we control it through permission models and abstraction policies.

## Applicability Across Resource Types

The pattern is universal across any resource an agent might touch:

| Resource | Without MAL | With MAL |
|---|---|---|
| **Email Inbox** | Full history, all capabilities | Last N days, filtered content, read-only |
| **Calendar** | All events, full CRUD | Today + next week, read + create only |
| **File System** | Entire tree | Scoped directory, specific file types |
| **Database** | All tables, all rows | Specific views, row-level filtering |
| **APIs** | All endpoints | Subset of operations, rate-limited |
| **Code Repos** | Full repo access | Specific branches/paths, no force-push |

## Policy-Driven and Programmable

A critical property: MAL policies can be **created and validated programmatically**. This means:

- Agents can help *author* abstraction layer policies (meta-level)
- Policies can be version-controlled, reviewed, and tested
- Automated validation: does this policy satisfy least-privilege for task X?
- Scales across an organization without manual per-resource configuration

## Security and Resilience Benefits

- **Prompt injection mitigation**: Less data flowing through the model means fewer opportunities for injected instructions to reach the agent
- **Least privilege enforcement**: The agent gets exactly what it needs, nothing more
- **Defense in depth**: Even if the agent is compromised, the MAL limits what's reachable
- **Resilience**: Scoped access means scoped failure — a misbehaving agent can't cascade across the full resource
- **Auditability**: All access flows through a chokepoint with explicit policy

## Relationship to Schema-Driven Development

Analogous to how schema-driven development controls data flow through schema mappings, the MAL controls agent-resource interaction through permission models and abstraction policies. The schema defines the shape of what's allowed; the MAL enforces it at runtime.

---

## Implementation Plan

### Architecture: Agent-Built by Agents

The implementation is designed to be executed by a fleet of bot agents (mix of OpenAI Codex and Claude Code), with the following roles:

| Role | Responsibility |
|---|---|
| **Architect / Orchestrator** | System design, component boundaries, interface contracts |
| **Conductor** | Workflow coordination, task sequencing, dependency management |
| **Developer** | Implementation of MAL core, adapters, policy engine |
| **QA** | Test creation, validation, security testing |
| **Librarian** | Knowledge mapping, connecting dots across components, documentation coherence |

### Task Management

All issues, tasks, and development tracking managed via the **Issues FS Pipe** project (`i-project`), providing filesystem-based issue tracking integrated into the agent workflow.

### Deliverable: Public Website

A public-facing website that serves as both documentation and community resource:

- Explanation of the MAL concept, principles, and security model
- Reading-page format — clean, presentable, shareable
- Implementation examples and proof-of-concept demos
- Different visual treatments for different concepts
- Fully test-backed implementations
- Open for community contribution and feedback

### Proof-of-Concept Implementations

Target a few concrete MAL implementations to demonstrate the pattern:

1. **Email MAL** — Scoped inbox access with temporal and content filtering
2. **Filesystem MAL** — Directory-scoped, file-type-filtered access
3. **Calendar MAL** — Time-windowed, read-biased calendar access

Each implementation should demonstrate both transparent and normalized modes, progressive trust, and self-description capabilities.
