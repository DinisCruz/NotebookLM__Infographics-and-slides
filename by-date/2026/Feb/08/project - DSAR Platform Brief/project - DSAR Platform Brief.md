# Project Brief: GDPR DSAR Concierge Platform

**Author:** Dinis Cruz
**Date:** February 2026
**Status:** Concept / Pre-MVP

---

## Origin

This project emerged from a real-world friction point: attempting to exercise GDPR data subject access rights (DSAR) against Otter.ai. The process of finding the correct submission channel, composing legally compliant request text, and anticipating identity verification requirements highlighted a gap — there is no consumer-grade tool that makes exercising GDPR rights as simple as it should be.

---

## Vision

A GenAI-agent-powered platform that helps individuals request, track, and (optionally) store their personal data from companies subject to GDPR. The platform acts as a **privacy concierge** — guiding users through the full DSAR lifecycle from discovery to fulfilment to escalation.

**One-liner:** *"Turn GDPR rights into a button."*

---

## Core Design Principles

1. **Guide-first, automate-second.** The default mode helps users submit requests themselves in the right place, with the right text. Full orchestration is opt-in.

2. **Zero-access architecture.** When the platform handles data storage, it **never** has access to the plaintext DSAR results. Client-side encryption (public/private key pair generated in the browser) ensures only the user can decrypt their data.

3. **Identity verification is a first-class workflow.** Companies must authenticate DSAR requesters. The platform anticipates this, coaches users through it, and advocates for minimal disclosure.

4. **Serverless-first.** Built on a serverless stack consistent with the patterns used at [investor.myfeeds.ai](https://investor.myfeeds.ai/) and other Dinis Cruz startups.

---

## Operating Modes

### Mode 1 — Guide-Only (Default)

The platform tells the user:

- **Where** to submit (URL, form, email)
- **What** to select (dropdown options, subject lines)
- **What** to paste (legally correct DSAR text, tailored to scope)
- **How** to handle responses (verification requests, partial fulfilment, refusals)

The platform stores only minimal metadata (company, dates, status). It never touches returned data.

### Mode 2 — Facilitated (Zero-Access)

The platform additionally:

- Generates and submits request packets
- Maintains timelines and sends reminders
- Stores **encrypted** DSAR deliverables (encrypted client-side before upload)
- Tracks fulfilment status

The platform **cannot**: read DSAR results, access private keys, perform server-side decryption, or log plaintext content.

---

## GenAI Agent Architecture

Six narrow, purpose-built agents with strict tool contracts:

### A. Target Discovery Agent
Determines the correct DSAR channel for a given company — web form, email, portal, or postal — along with the controller's legal entity, DPO contact, typical identity verification requirements, and known gotchas (e.g., enterprise accounts where the employer is the data controller).

### B. Request Composer Agent
Generates legally compliant DSAR text (Articles 15, 20, etc.) tailored to jurisdiction (EU vs UK GDPR), request type, and user-specified scope priorities (e.g., "transcripts first"). In facilitated mode, embeds the user's public key with a request to encrypt returned data.

### C. Verification Coach Agent
Handles the "prove you are you" loop. Produces minimal-disclosure verification responses ranked from least to most invasive. Provides redaction guidance if government ID is unavoidable, and escalation language if the company's identity requirements are excessive.

### D. Submission Orchestrator Agent
In guide-only mode: outputs a step-by-step checklist with direct links and copy-paste blocks. In facilitated mode: packages requests, stores encrypted payload metadata, and triggers timeline events.

### E. Follow-up & Deadline Agent
Tracks the 30-day GDPR clock, generates follow-up messages with escalating tone (gentle → firm), and flags extensions or non-responses.

### F. Evidence & Audit Agent
Creates regulator-ready evidence bundles for escalation: timeline of events, copies of correspondence, proof of submission, and a structured "controller failure" narrative with GDPR article citations.

---

## DSAR Case Lifecycle

```
INTAKE
  → TARGET_DISCOVERY
    → REQUEST_DRAFTED
      → USER_APPROVED
        → SUBMITTED / GUIDED_TO_SUBMIT
          → AWAITING_ACK
            → IDENTITY_VERIFICATION_REQUIRED
              → IDENTITY_VERIFICATION_SENT
            → AWAITING_FULFILLMENT
              → FULFILLED_ENCRYPTED / FULFILLED_USER_RECEIVED
              → PARTIALLY_FULFILLED
              → REFUSED
                → ESCALATION_PREPARED
          → CLOSED
```

Identity verification is not an exception — it is an expected branch that the system anticipates and prepares for at every stage.

---

## Zero-Access Crypto Workflow

1. Browser generates a public/private key pair via WebCrypto API
2. Public key (and fingerprint) is embedded in the DSAR request with a note: *"Please encrypt any files containing my personal data to this key before transmission"*
3. If the company returns unencrypted data (common), the user downloads it locally and the browser encrypts it client-side before uploading to platform storage
4. The platform stores only encrypted blobs — it holds the public key but never the private key
5. A **separate, out-of-scope service** handles decryption, parsing, and exploration of retrieved data

**Agent constraints:** Agents must never request the private key, never ask users to paste raw DSAR content into chat, and never process plaintext personal data server-side.

---

## Agent Tool Contracts

These are the only "verbs" the agents can invoke:

| Tool | Purpose |
|------|---------|
| `lookup_company_dsar_info(id)` | Returns DSAR channels, controller identity, verification patterns |
| `generate_keypair_client_side()` | Client-only; private key never leaves browser |
| `compose_dsar_request(params)` | Generates subject, body, checklist for the request |
| `render_user_steps(profile, text)` | Step-by-step instructions tailored to channel type |
| `classify_incoming_message(text)` | Categorises responses: ACK, VERIFY_ID, FULFILLED, REFUSAL, etc. |
| `compose_verification_reply(msg, policy)` | Minimal-disclosure response + alternatives |
| `timeline_recommendations(state)` | Next action, deadline, follow-up templates |
| `store_encrypted_artifact(blob, meta)` | Accepts only encrypted blobs; validates client-side |
| `generate_escalation_pack(history)` | Complaint text + evidence checklist for DPA submission |

---

## Verification Strategy

The Verification Coach ranks identity methods from least to most invasive:

1. Account login confirmation (most privacy-preserving)
2. Email verification link/code to account email
3. Two-factor confirmation within account
4. Partial data matching (last 4 digits, billing month)
5. Government ID (often excessive — push back)
6. Selfie + ID (highest risk — always push back)

Default response template advocates for minimal disclosure. If government ID is unavoidable, the agent provides redaction guidance and recommends watermarking: *"Provided solely for DSAR verification to [Company] on [Date]."*

---

## MVP Scope

The initial release focuses on guide-only mode for the top 200 consumer services:

1. **Company DSAR directory** — correct channels, forms, contacts for ~200 services
2. **Request composer** — Access + Portability requests with jurisdiction-aware legal text
3. **Step-by-step submission guidance** — tailored to each company's channel (web form, email, portal)
4. **Verification coaching** — anticipate and respond to identity challenges
5. **Deadline tracking** — 30-day clock with follow-up templates
6. **Basic escalation** — DPA complaint drafts with evidence timeline

Facilitated mode, encrypted vault storage, and the data parsing service are follow-on phases.

---

## Business Model

**Pay-per-usage with top-up credits.** Users pre-purchase credits and spend them as they use the platform. The platform charges a markup on the underlying cost to fulfil each action (GenAI inference, email delivery, encrypted storage, postal mailing, etc.).

| Action | Approximate Credit Cost | What It Covers |
|--------|------------------------|----------------|
| Company lookup | Low | Discovery agent: channel, DPO, verification intel |
| DSAR composition | Low | Request Composer agent: legal text + scope tailoring |
| Guided submission | Low | Step-by-step instructions + copy-paste blocks |
| Facilitated submission | Medium | Orchestrated send + evidence capture + timeline tracking |
| Verification coaching | Low | Per-response guidance through identity challenges |
| Follow-up generation | Low | Deadline-aware escalating follow-up messages |
| Encrypted vault storage | Low (recurring) | Per-GB/month for encrypted blob storage |
| Escalation pack | Medium | DPA complaint draft + evidence bundle |
| Postal mailing | High | Physical print, post, and tracking for non-digital channels |

**Pricing principles:**

- **No subscriptions.** Credits never expire. Users pay only for what they use.
- **Transparent markup.** The platform's margin sits on top of real fulfilment costs (AI inference, compute, storage, postage). Users can see the cost breakdown.
- **Free tier.** A small initial credit grant lets users try the platform (e.g., one full guided DSAR flow) before topping up.
- **Bulk discounts.** Larger top-ups reduce the per-credit cost.

**Additional revenue streams:** B2B2C partnerships (employers, unions, privacy orgs purchase credit pools for their members) and white-label/API access for organisations integrating DSAR workflows into their own platforms.

---

## Technical Stack

Serverless-first, consistent with existing startup infrastructure:

- **Frontend:** Next.js (static + server components)
- **Auth:** Cognito / Auth0 / Clerk
- **API:** API Gateway + Lambda
- **Workflows:** Step Functions (DSAR lifecycle state machine)
- **Events:** EventBridge (deadline reminders, follow-up triggers)
- **Data:** DynamoDB (case state + audit trail)
- **Storage:** S3 (per-user encrypted prefixes, object lock for immutability)
- **Encryption:** KMS (envelope encryption), WebCrypto (client-side key generation)
- **Search:** OpenSearch Serverless or vector store (vault indexing)
- **GenAI:** Orchestrator Lambda with strict tool contracts and user-approval gates

---

## Out of Scope (Future Services)

- **Data Explorer Service:** A separate platform for loading, parsing, and navigating decrypted DSAR data — structuring transcripts, metadata, and account information into a searchable personal data ledger
- **Browser Extension:** Detects service usage and suggests DSAR opportunities
- **Continuous DSAR:** Periodic automated refresh requests to monitor data accumulation

---

## Go-to-Market

- **Launch narrative:** *"Request your data from 50 companies in 1 hour"*
- **Community partnerships:** Privacy NGOs, digital rights groups, investigative journalists
- **Content engine:** Step-by-step DSAR guides for specific companies (Otter, Meta, Google, etc.)
- **Viral mechanism:** Shareable (redacted) DSAR dashboard showing what companies know about you

---

## Key Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Companies resist non-standard DSAR channels | Guide-only mode lets users submit through official channels directly |
| Identity verification complexity varies wildly | Verification Coach agent with company-specific patterns + escalation templates |
| Users may paste sensitive data into chat | Agent guardrails explicitly prohibit this; UI warnings reinforce |
| Platform itself becomes a privacy liability | Zero-access architecture; encrypted-only storage; practice what we preach |
| Legal exposure from providing DSAR "advice" | Platform generates templates, not legal advice; clear disclaimers |
