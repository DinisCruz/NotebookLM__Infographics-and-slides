# Football Pilates Scheduler — Project Brief

**Version:** 1.0 DRAFT  
**Date:** February 2026  
**Stack:** M-Graph (FastAPI/Lambda) · AWS S3/DynamoDB · Issues FS · Claude Teams Multi-Agent  

---

## 1. Executive Summary

This project delivers a lightweight, privacy-first scheduling web application (codename **"Football Pilates Scheduler"**) that enables an organiser to coordinate group Pilates class times with friends across multiple football teams. The tool draws inspiration from Doodle but is purpose-built with end-to-end encryption — only the organiser (holding the admin private key) can decrypt and read submitted votes.

The application will be built on AWS serverless infrastructure using the **M-Graph framework** (FastAPI on Lambda). All project management, task tracking, and sign-off will be orchestrated through **Issues FS**. Development will be executed by a **multi-agent Claude Teams** setup with clearly defined roles: Architect, Developer, QA, and Conductor.

---

## 2. Background & Motivation

The organiser plays football regularly with multiple local teams, involving a wide circle of friends (ages ~35–60). Having personally benefited from Pilates — improved core strength, fewer injuries, better cardio, and weight management — the organiser routinely encourages teammates to try it.

A local Pilates studio in **Chiswick** has offered to run a dedicated "footballers' Pilates" class. The challenge is finding the date and time that works for the most people across several WhatsApp groups. This tool solves that coordination problem while respecting participant privacy.

---

## 3. User Stories

### 3.1 Organiser (Admin)

| ID | Story | Priority | Points |
|----|-------|----------|--------|
| ORG-1 | As the organiser, I want to create a new poll with a set of proposed dates and time slots so that my friends can vote on availability. | Must | 5 |
| ORG-2 | As the organiser, I want to share a link via WhatsApp to multiple football groups with a friendly message explaining the offer. | Must | 2 |
| ORG-3 | As the organiser, I want to view decrypted vote results (names, times, football group) using my admin private key. | Must | 8 |
| ORG-4 | As the organiser, I want to start and stop the voting period so I can control when the poll is active. | Must | 3 |
| ORG-5 | As the organiser, I want to see a visual breakdown of votes per time slot to quickly identify the best option. | Should | 5 |
| ORG-6 | As the organiser, I want to pick a shortlist of top time slots and optionally run a second round of voting. | Could | 5 |
| ORG-7 | As the organiser, I want an admin dashboard that shows poll status, total votes, and lets me manage the lifecycle. | Must | 5 |

### 3.2 Voter (Friend / Visitor)

| ID | Story | Priority | Points |
|----|-------|----------|--------|
| VOT-1 | As a voter, I want to land on a clear, friendly page that explains what this is about (studio offer, it's free, etc.) so I immediately understand the context. | Must | 3 |
| VOT-2 | As a voter, I want to enter my name, select which football group I usually play with, and pick the time slots that work for me. | Must | 5 |
| VOT-3 | As a voter, I want to see the current aggregated vote counts per slot (without seeing other people's names) so I can factor popularity into my choice. | Should | 3 |
| VOT-4 | As a voter, I want my submission stored locally (browser localStorage) so I can see/edit my vote if I return. | Should | 3 |
| VOT-5 | As a voter, I want a confirmation screen after voting so I know my response was recorded. | Must | 2 |
| VOT-6 | As a voter, I want the experience to work smoothly on mobile (WhatsApp in-app browser). | Must | 3 |

### 3.3 Security & Privacy

| ID | Story | Priority | Points |
|----|-------|----------|--------|
| SEC-1 | All vote payloads (name, group, selections) must be encrypted client-side with the organiser's public key before transmission. The server must never see plaintext PII. | Must | 8 |
| SEC-2 | Only the admin session (holding the private key) can decrypt vote data. | Must | 5 |
| SEC-3 | Aggregated anonymous counts (votes per slot) should be computable without decryption (stored separately or via a counter mechanism). | Should | 5 |
| SEC-4 | Data at rest in S3 must be encrypted (SSE-S3 or SSE-KMS in addition to the application-level encryption). | Must | 2 |

---

## 4. Architecture

### 4.1 High-Level Overview

```
┌─────────────────────────────────────────────────────────┐
│                     FRONTEND (Static)                    │
│  Voter Page  ·  Admin Dashboard  ·  Client-side Crypto   │
│         Hosted: S3 + CloudFront (or Lambda@Edge)         │
└────────────────────┬────────────────────────────────────┘
                     │ HTTPS
┌────────────────────▼────────────────────────────────────┐
│               API GATEWAY + LAMBDA (M-Graph)             │
│                    FastAPI on Lambda                      │
│                                                          │
│  POST /polls          – create poll (admin)              │
│  GET  /polls/{id}     – get poll metadata + counts       │
│  POST /polls/{id}/vote – submit encrypted vote           │
│  PATCH /polls/{id}    – start/stop poll (admin)          │
│  GET  /polls/{id}/results – get encrypted votes (admin)  │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                      AWS STORAGE                         │
│                                                          │
│  S3 Bucket: polls/{poll_id}/                             │
│    ├── meta.json        (poll config, time slots, status)│
│    ├── counts.json      (anonymous aggregated counts)    │
│    └── votes/           (encrypted individual votes)     │
│         ├── {vote_id}.enc.json                           │
│         └── ...                                          │
│                                                          │
│  (Alternative/hybrid: DynamoDB for meta + counts,        │
│   S3 for encrypted vote blobs)                           │
└─────────────────────────────────────────────────────────┘
```

### 4.2 M-Graph Framework

The backend is deployed using M-Graph — the organiser's custom serverless framework that simplifies deploying FastAPI apps to AWS Lambda. Key characteristics:

- FastAPI application with standard route definitions
- Automatic packaging and deployment to Lambda behind API Gateway
- Infrastructure-as-code configuration for easy environment promotion
- Three deployment environments: **dev → qa → prod**

### 4.3 Encryption Model

The encryption scheme ensures **zero-knowledge on the server side**:

1. **Poll creation:** Organiser generates an asymmetric keypair (e.g. RSA-OAEP or X25519 + AES-GCM). The **public key** is embedded in the poll config / shared URL. The **private key** stays with the organiser only.
2. **Voting:** The voter's browser encrypts the vote payload (name, group, selected slots) using the public key before POSTing. The server stores the ciphertext blob as-is.
3. **Counting:** Anonymous slot counts are maintained separately (a simple atomic counter per slot) — no PII involved, so no decryption needed.
4. **Results:** The admin dashboard fetches encrypted blobs and decrypts them client-side using the private key (entered or loaded from a local file).

### 4.4 Data Storage Recommendation

**Primary recommendation: S3** (aligns with organiser preference of "S3 as my database").

- `meta.json` — poll config, slot definitions, status, public key
- `counts.json` — atomic counters per slot (updated via Lambda read-modify-write with conditional puts or a small DynamoDB counter table for atomicity)
- `votes/{vote_id}.enc.json` — encrypted vote blobs

**Hybrid option:** Use DynamoDB for `meta` and `counts` (atomic increments via `UpdateItem`) and S3 for encrypted vote blob storage. This avoids race conditions on the counters while keeping the bulk data in S3.

### 4.5 Environments

| Environment | Purpose | URL Pattern |
|-------------|---------|-------------|
| **dev** | Active development, unstable | `dev-pilates.{domain}` |
| **qa** | Integration testing, pre-release validation | `qa-pilates.{domain}` |
| **prod** | Live, shared with friends | `pilates.{domain}` |

All three environments are independently deployable via M-Graph config.

---

## 5. Multi-Agent Setup (Claude Teams)

### 5.1 Agent Roles

| Agent | Responsibilities | Key Artefacts |
|-------|-----------------|---------------|
| **Conductor** | Orchestrates the overall workflow. Breaks the brief into Issues FS issues, assigns work to agents, monitors progress, resolves blockers, performs final sign-off. | Issues FS issue tree, status reports, go/no-go decisions |
| **Architect** | Defines system design, API contracts, data models, encryption scheme, infrastructure config. Reviews PRs for architectural compliance. | Architecture decision records, OpenAPI spec, data model schemas, M-Graph config |
| **Developer** | Implements backend (FastAPI/Lambda), frontend (static HTML/JS), encryption logic, deployment scripts. Writes code against the issues assigned by Conductor. | Source code, deployment scripts, M-Graph app config |
| **QA** | Writes and executes test plans, integration tests, E2E tests. Validates security requirements (encryption, no plaintext PII on server). Validates across all three environments. | Test plans, test scripts, bug reports (as Issues FS issues), test results |

### 5.2 Orchestration Flow

```
Conductor
  │
  ├─► Creates Issues FS epic + stories from this brief
  ├─► Assigns architecture tasks → Architect
  │     └─► Architect produces: API spec, data model, crypto scheme
  │         └─► Conductor reviews & approves
  ├─► Assigns implementation tasks → Developer
  │     └─► Developer implements against spec
  │         └─► Architect reviews for compliance
  ├─► Assigns test tasks → QA
  │     └─► QA writes test plans from stories
  │     └─► QA executes against dev → qa → prod
  │         └─► Files bugs as Issues FS issues → Developer fixes
  └─► Final sign-off checklist via Issues FS
```

### 5.3 Coordination via Issues FS

All task definition, tracking, and sign-off uses **Issues FS** (the organiser's file-system-based issue tracker with CLI). The entire project should be representable as an Issues FS issue tree.

#### Issue Type Mapping

| Issue Type | Purpose | Example |
|------------|---------|---------|
| **Epic** | Top-level project container | `EPIC: Football Pilates Scheduler` |
| **Story** | User story from Section 3 | `STORY: ORG-3 — Admin views decrypted results` |
| **Task** | Technical implementation unit | `TASK: Implement POST /polls/{id}/vote endpoint` |
| **Bug** | Defect found during QA | `BUG: Vote counter race condition under concurrent submissions` |
| **Spike** | Research / proof-of-concept | `SPIKE: Evaluate RSA-OAEP vs X25519+AES-GCM for client-side crypto` |
| **Test** | Test plan / test execution record | `TEST: E2E — voter flow on mobile Safari` |
| **Deploy** | Deployment verification | `DEPLOY: Promote qa → prod` |

#### Issue Links

- `blocks / blocked-by` — dependency tracking between tasks
- `parent / child` — story → task decomposition
- `tests / tested-by` — linking test issues to stories
- `relates-to` — cross-cutting concerns (e.g. SEC-1 relates to multiple implementation tasks)

#### Lifecycle States

`backlog → in-progress → in-review → done → verified`

Each agent transitions issues they own. The Conductor is responsible for verifying `done → verified` transitions and performing final sign-off on the epic.

---

## 6. Technical Specifications

### 6.1 API Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `POST` | `/polls` | Admin key | Create a new poll with time slots and public key |
| `GET` | `/polls/{poll_id}` | None | Get poll metadata, slot labels, anonymous counts, status |
| `POST` | `/polls/{poll_id}/vote` | None | Submit an encrypted vote payload |
| `PATCH` | `/polls/{poll_id}` | Admin key | Update poll status (open/closed), edit slots |
| `GET` | `/polls/{poll_id}/results` | Admin key | Retrieve all encrypted vote blobs for client-side decryption |

### 6.2 Data Models

**Poll Metadata (`meta.json`)**
```json
{
  "poll_id": "uuid",
  "title": "Footballers' Pilates — Chiswick Studio",
  "description": "Free class offered by the studio. Pick your preferred times!",
  "slots": [
    { "id": "slot-1", "date": "2026-03-15", "time": "10:00", "label": "Sat 15 Mar, 10am" },
    { "id": "slot-2", "date": "2026-03-15", "time": "14:00", "label": "Sat 15 Mar, 2pm" }
  ],
  "status": "open",
  "public_key": "<base64-encoded public key>",
  "created_at": "ISO8601",
  "football_groups": ["Sunday League", "Tuesday 5-a-side", "Thursday Veterans"]
}
```

**Encrypted Vote (`votes/{vote_id}.enc.json`)**
```json
{
  "vote_id": "uuid",
  "poll_id": "uuid",
  "timestamp": "ISO8601",
  "encrypted_payload": "<base64 ciphertext>",
  "selected_slot_ids": ["slot-1", "slot-3"]
}
```

> Note: `selected_slot_ids` is stored in plaintext to enable anonymous counting. The `encrypted_payload` contains PII (name, football group, optional message).

**Counts (`counts.json`)**
```json
{
  "total_votes": 12,
  "slot_counts": { "slot-1": 8, "slot-2": 5, "slot-3": 10 }
}
```

### 6.3 Frontend Pages

| Page | Route | Description |
|------|-------|-------------|
| **Voter Landing** | `/{poll_id}` | Hero section explaining the offer, shows slots with current vote counts, vote form |
| **Vote Confirmation** | `/{poll_id}/thanks` | Confirmation + summary of what they submitted |
| **Admin Dashboard** | `/admin/{poll_id}` | Requires private key. Shows decrypted results, vote visualisation, poll controls |
| **Admin: Create Poll** | `/admin/new` | Form to configure a new poll (generates keypair) |

### 6.4 Client-Side Encryption (Recommended Approach)

Use the **Web Crypto API** (built into all modern browsers):

1. **Key generation** (admin, on poll creation): Generate an RSA-OAEP 2048-bit keypair. Store private key as downloadable JSON/PEM. Embed public key in poll config.
2. **Encryption** (voter browser): `crypto.subtle.encrypt("RSA-OAEP", publicKey, payload)` on the JSON-serialised vote data.
3. **Decryption** (admin browser): `crypto.subtle.decrypt("RSA-OAEP", privateKey, ciphertext)` after loading private key file.

Alternative: X25519 (ECDH) + AES-256-GCM for larger payloads (RSA-OAEP has a size limit based on key size). Worth a spike.

---

## 7. UI/UX Wireframe Descriptions

### 7.1 Voter Page

```
┌──────────────────────────────────────────┐
│  🏈 Footballers' Pilates — Free Class!   │
│                                          │
│  [Studio name] in Chiswick is offering   │
│  a free Pilates class for us.            │
│  Pick the times that work for you!       │
│                                          │
│  ┌──────────────────────────────────┐    │
│  │ Sat 15 Mar, 10am     ██████ 8   │    │
│  │ Sat 15 Mar, 2pm      ████   5   │    │
│  │ Wed 19 Mar, 7pm      ████████ 10│    │
│  └──────────────────────────────────┘    │
│                                          │
│  Your name: [____________]               │
│  Football group: [dropdown ▾]            │
│  Select your slots: ☑ ☐ ☑               │
│                                          │
│  [ Submit Vote ]                         │
└──────────────────────────────────────────┘
```

### 7.2 Admin Dashboard

```
┌──────────────────────────────────────────┐
│  Admin: Footballers' Pilates             │
│  Status: ● OPEN        [Close Voting]    │
│                                          │
│  Total votes: 12                         │
│                                          │
│  🔑 [Load Private Key]                   │
│                                          │
│  ┌──────────────────────────────────┐    │
│  │ Slot Breakdown (bar chart)       │    │
│  │ ████████████████  10 — Wed 7pm   │    │
│  │ ████████████       8 — Sat 10am  │    │
│  │ ██████████         5 — Sat 2pm   │    │
│  └──────────────────────────────────┘    │
│                                          │
│  Decrypted Votes:                        │
│  ┌────────┬───────────────┬──────────┐   │
│  │ Name   │ Group         │ Slots    │   │
│  ├────────┼───────────────┼──────────┤   │
│  │ Dave   │ Sunday League │ 1, 3     │   │
│  │ Mark   │ Tue 5-a-side  │ 1        │   │
│  └────────┴───────────────┴──────────┘   │
│                                          │
│  [Shortlist Top 2]  [Export CSV]         │
└──────────────────────────────────────────┘
```

---

## 8. Deployment & Environments

### 8.1 Pipeline

```
Feature branch → dev deploy → dev tests (QA agent)
       │
       ▼
  Merge to main → qa deploy → integration + E2E tests (QA agent)
       │
       ▼
  Tag release → prod deploy → smoke tests → Conductor sign-off
```

### 8.2 Environment Configuration (M-Graph)

Each environment gets its own M-Graph config with isolated:
- Lambda function + API Gateway stage
- S3 bucket (or prefix)
- CloudFront distribution (if applicable)

### 8.3 Deployment Verification Checklist

For each environment promotion, the QA agent must verify:

1. API health check returns 200
2. Poll creation + vote submission + result retrieval E2E flow passes
3. Encryption/decryption roundtrip works (vote submitted → admin can decrypt)
4. Poll open/close lifecycle works
5. Mobile browser rendering (simulated viewport)
6. No plaintext PII visible in S3 objects or CloudWatch logs
7. CORS configured correctly for frontend origin

---

## 9. Success Criteria & Acceptance

| # | Criterion | Verified By |
|---|-----------|-------------|
| 1 | Organiser can create a poll, get a shareable link, and share it via WhatsApp | QA — E2E test |
| 2 | Voter can land on the page, understand the context, vote, and see confirmation | QA — E2E test |
| 3 | Vote data is encrypted client-side; server/S3 never contains plaintext PII | QA — security audit |
| 4 | Organiser can decrypt and view all votes using private key | QA — E2E test |
| 5 | Anonymous vote counts are visible to voters without decryption | QA — functional test |
| 6 | Poll open/close lifecycle works correctly | QA — functional test |
| 7 | Works on mobile (WhatsApp in-app browser, Safari, Chrome) | QA — device test |
| 8 | All three environments (dev, qa, prod) deploy and function independently | QA — deploy verification |
| 9 | All Issues FS issues are in `verified` state | Conductor — sign-off |
| 10 | User-facing documentation / help text is present on voter page | QA — content review |

---

## 10. Issues FS — Full Project Structure

Below is the recommended issue tree for the Conductor to instantiate in Issues FS at project kick-off:

```
EPIC: Football Pilates Scheduler
│
├── SPIKE: Evaluate crypto approach (RSA-OAEP vs X25519+AES-GCM)
│
├── STORY: ORG-1 — Create poll with time slots
│   ├── TASK: Design poll data model (meta.json schema)
│   ├── TASK: Implement POST /polls endpoint
│   ├── TASK: Build admin "Create Poll" UI page
│   ├── TASK: Implement keypair generation in browser
│   └── TEST: E2E — create poll flow
│
├── STORY: ORG-2 — Share link via WhatsApp
│   ├── TASK: Generate shareable URL with poll ID
│   ├── TASK: Add Open Graph meta tags for WhatsApp preview
│   └── TEST: Verify WhatsApp link preview rendering
│
├── STORY: ORG-3 — View decrypted vote results
│   ├── TASK: Implement GET /polls/{id}/results endpoint
│   ├── TASK: Build admin results table with client-side decryption
│   ├── TASK: Add CSV export functionality
│   └── TEST: E2E — admin decrypt flow
│
├── STORY: ORG-4 — Start/stop voting
│   ├── TASK: Implement PATCH /polls/{id} (status toggle)
│   ├── TASK: Enforce voting closed on POST /vote
│   └── TEST: Functional — voting blocked when closed
│
├── STORY: ORG-5 — Visual vote breakdown
│   ├── TASK: Build bar chart component for admin dashboard
│   └── TEST: Visual regression — chart renders correctly
│
├── STORY: ORG-7 — Admin dashboard
│   ├── TASK: Build dashboard layout (status, counts, controls)
│   ├── TASK: Implement private key loading UI
│   └── TEST: E2E — full admin dashboard flow
│
├── STORY: VOT-1 — Voter landing page
│   ├── TASK: Design and build voter landing page
│   ├── TASK: Write copy explaining the studio offer
│   └── TEST: Content review + mobile rendering
│
├── STORY: VOT-2 — Submit vote
│   ├── TASK: Build vote form (name, group, slot checkboxes)
│   ├── TASK: Implement client-side encryption of vote payload
│   ├── TASK: Implement POST /polls/{id}/vote endpoint
│   ├── TASK: Implement anonymous counter update logic
│   └── TEST: E2E — voter submission flow
│
├── STORY: VOT-3 — See aggregated counts
│   ├── TASK: Build vote count display (progress bars)
│   ├── TASK: Implement GET /polls/{id} count response
│   └── TEST: Functional — counts update after vote
│
├── STORY: VOT-4 — LocalStorage vote memory
│   ├── TASK: Store vote in localStorage after submission
│   ├── TASK: Show "You voted" state on return visit
│   └── TEST: Functional — return visit shows prior vote
│
├── STORY: VOT-5 — Vote confirmation
│   ├── TASK: Build confirmation page
│   └── TEST: Functional — confirmation displays after submit
│
├── STORY: VOT-6 — Mobile compatibility
│   ├── TASK: Responsive CSS for all pages
│   └── TEST: Device testing — WhatsApp browser, Safari, Chrome
│
├── STORY: SEC-1 — Client-side encryption
│   └── (covered by VOT-2 tasks + crypto spike)
│
├── STORY: SEC-2 — Admin-only decryption
│   └── (covered by ORG-3 tasks)
│
├── STORY: SEC-3 — Anonymous counting
│   └── (covered by VOT-2 counter task)
│
├── STORY: SEC-4 — S3 encryption at rest
│   ├── TASK: Enable SSE-S3 on poll bucket
│   └── TEST: Verify encryption headers on S3 objects
│
├── DEPLOY: Dev environment setup
│   ├── TASK: M-Graph config for dev
│   └── TEST: Deploy verification checklist — dev
│
├── DEPLOY: QA environment setup
│   ├── TASK: M-Graph config for qa
│   └── TEST: Deploy verification checklist — qa
│
└── DEPLOY: Prod release
    ├── TASK: M-Graph config for prod
    ├── TEST: Deploy verification checklist — prod
    └── TEST: Smoke tests — prod
```

---

## 11. Open Questions & Decisions

| # | Question | Options | Decision |
|---|----------|---------|----------|
| 1 | Crypto scheme | RSA-OAEP (simpler, size-limited) vs X25519+AES-GCM (flexible) | Spike to decide |
| 2 | Counter atomicity | S3 conditional put vs DynamoDB atomic increment | Architect to recommend |
| 3 | Frontend framework | Vanilla JS (minimal deps) vs lightweight framework (Preact, Alpine) | Architect to recommend |
| 4 | Auth for admin endpoints | API key in header vs signed JWT vs URL token | Architect to recommend |
| 5 | Second-round voting (ORG-6) | Include in v1 or defer to v2 | Defer to v2 (marked as "Could") |
| 6 | Custom domain | Use existing domain or new subdomain | Organiser to decide |

---

## 12. Out of Scope (v1)

- User accounts / authentication for voters
- Email notifications
- Integration with calendar apps
- Multiple organisers / shared admin
- Recurring polls
- Second-round / runoff voting (deferred to v2)

---

## 13. Next Steps

1. **Conductor** instantiates the Issues FS tree from Section 10
2. **Architect** picks up the crypto spike and architecture tasks
3. **Developer** sets up M-Graph project scaffold and dev environment
4. **QA** drafts test plans from the user stories
5. All agents coordinate via Issues FS issue transitions and Claude Teams orchestration

---

*This brief should be loaded into the Claude Teams conductor agent as the source-of-truth project definition. All work items derive from this document and are tracked via Issues FS.*
