# Criteria-Filtered Web Browsing MVP
## A Specification for Intelligent Content Filtering

---

# Part 1: The Problem Worth Solving

## You Already Know This Feeling

It's 8:47 AM. You open the AWS News Blog—a source you trust—to catch up on cloud announcements. The page loads with 19 articles. You scan. You scroll. You mentally filter: *"Skip... skip... maybe... skip... seen it... skip..."*

Three minutes later, you've found two articles worth reading. The other 17? Noise. Not bad content—just not *your* content right now.

**This is information overload.** Not "too much internet"—but too much cognitive work extracting signal from noise on pages you actually want to read.

```
┌─────────────────────────────────────────────────────────────────────┐
│                        THE DAILY RITUAL                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   8:47 AM    Open trusted news source                               │
│   8:48 AM    Scan 19 headlines                                      │
│   8:49 AM    Mentally categorize each one                           │
│   8:50 AM    Decide 2 are worth reading                             │
│   8:51 AM    Feel vaguely drained                                   │
│                                                                     │
│   Total time:     4 minutes                                         │
│   Useful time:    Reading 2 articles                                │
│   Wasted time:    Filtering 17 you didn't want                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

Research confirms what you already feel: information overload measurably impacts decision-making, productivity, and wellbeing. The solution isn't "read less"—it's **filter smarter**.

---

## What If the Page Already Knew?

Imagine visiting the same AWS News Blog, but the page *already* shows only what matches your intent:

- **Only technical deep-dives** (hide the marketing announcements)
- **Only positive/engaging content** (hide the dry corporate updates)
- **Only content relevant to your stack** (hide services you don't use)

Same URL. Same visual design. Same trusted source. Just... *filtered for you*.

```
┌─────────────────────────────────────────────────────────────────────┐
│  BEFORE: aws.amazon.com/blogs/aws                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📰 Top announcements of AWS re:Invent 2025                         │
│  📰 AWS IAM Identity Center now supports multi-Region...            │
│  📰 AWS Weekly Roundup: Amazon Bedrock agent workflows...           │
│  📰 AWS Weekly Roundup: Amazon EC2 G7e instances...                 │
│  📰 Announcing Amazon SageMaker Unified Studio...                   │
│  📰 New CloudFormation features for infrastructure...               │
│  📰 AWS Cost Explorer adds commitment tracking...                   │
│  📰 ... (19 total articles)                                         │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│  AFTER: Same URL, filtered view                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📰 Top announcements of AWS re:Invent 2025                         │
│  📰 AWS IAM Identity Center now supports multi-Region...            │
│  📰 Announcing Amazon EC2 G7e instances accelerated...              │
│  📰 Amazon EC2 X8i instances powered by Intel Xeon 6...             │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────┐        │
│  │  Showing 4 of 19 articles • Filtered by: Engaging Content │       │
│  └─────────────────────────────────────────────────────────┘        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**That's what this system does.**

---

# Part 2: User Stories

## Primary User Story

> **As a** time-constrained professional who regularly checks high-volume content sources,  
> **I want to** visit my usual URLs and see a page that looks identical but only contains items matching my chosen criteria,  
> **So that** I can stay informed without mentally processing an exhaustive list every time.

## Detailed User Stories

### Content Consumption Stories

> **As a** cloud architect who monitors AWS announcements,  
> **I want to** see only technical deep-dives and genuine product launches (filtering out marketing summaries and abstract platform descriptions),  
> **So that** I can focus my limited reading time on actionable technical content.

> **As a** someone managing news-related stress,  
> **I want to** automatically filter out promotional and corporate-speak content from my regular reading sources,  
> **So that** I only engage with genuine, substantive content.

> **As a** team lead who shares relevant articles with my team,  
> **I want to** quickly identify engaging, tutorial-style content versus dry announcements,  
> **So that** I can share content my team will actually find valuable.

### Trust and Transparency Stories

> **As a** user who values knowing what I'm *not* seeing,  
> **I want to** see visual indicators of what content was filtered and why,  
> **So that** I can trust the system isn't hiding important information.

> **As a** skeptical first-time user,  
> **I want to** toggle between filtered and unfiltered views instantly,  
> **So that** I can verify the filtering is working as expected before trusting it.

> **As a** user who wants progressive disclosure,  
> **I want to** see filtered content redacted (XXX) before it's fully hidden,  
> **So that** I understand the scope of what's being removed before committing to the clean view.

### Customization Stories

> **As a** power user with specific content preferences,  
> **I want to** define my own filtering criteria using natural language (e.g., "show only content about serverless and containers"),  
> **So that** the filter adapts to my needs without requiring code changes.

> **As an** administrator deploying this for my team,  
> **I want to** configure different filtering profiles for different use cases,  
> **So that** team members can switch between "focus mode" and "comprehensive mode" as needed.

---

# Part 3: The Experience

## Six Viewing Modes

The system provides six distinct viewing modes, designed as a **progressive trust ladder**—each step reveals more about how filtering works before you commit to the final clean view.

```
┌────────────────────────────────────────────────────────────────────────────┐
│                         THE PROGRESSIVE TRUST LADDER                       │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│   MAIN ──▶ XXX ──▶ GROUPS ──▶ REDACT ──▶ HIDE ──▶ CLEAN                   │
│     │       │        │          │         │         │                      │
│     │       │        │          │         │         └─ Final clean view    │
│     │       │        │          │         └─ Content removed, space kept   │
│     │       │        │          └─ Filter candidates shown as XXX          │
│     │       │        └─ See classification (green/yellow/red dots)         │
│     │       └─ Full redaction demo (all text → XXX)                        │
│     └─ Original page with interception active                              │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

### Mode 1: MAIN — Original with Interception

The original page, unmodified. The toolbar at top confirms interception is active.

```
┌─ TOOLBAR ───────────────────────────────────────────────────────────────────┐
│ [Main] [XXX] [Groups] [Redact] [Hide] [Clean]     "Original AWS Blog page   │
│                                                    with interception"       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  AWS News Blog                                                              │
│  ─────────────────────────────────────────────────────                      │
│                                                                             │
│  ┌──────────┐  Top announcements of AWS re:Invent 2025                      │
│  │  IMAGE   │  by AWS News Blog Team | 30 NOV 2025                          │
│  │          │  Discover our most impactful innovations across analytics...  │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  AWS IAM Identity Center now supports multi-Region...         │
│  │  IMAGE   │  by Channy Yun | 03 FEB 2026                                  │
│  │          │  AWS IAM Identity Center now supports multi-Region...         │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  AWS Weekly Roundup: Amazon Bedrock agent workflows...        │
│  │  IMAGE   │  by Betty Zheng | 02 FEB 2026                                 │
│  │          │  Over the past week, we passed Laba festival...               │
│  └──────────┘                                                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**User learns:** "The system is intercepting traffic but hasn't changed anything yet."

---

### Mode 2: XXX — Full Redaction Demo

Every text character replaced with X. Demonstrates the system's ability to transform content while preserving structure.

```
┌─ TOOLBAR ───────────────────────────────────────────────────────────────────┐
│ [Main] [XXX] [Groups] [Redact] [Hide] [Clean]     "Full Redaction: All text │
│                                                    replaced with X"         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  XXX XXXX XXXX                                                              │
│  ─────────────────────────────────────────────────────                      │
│                                                                             │
│  ┌──────────┐  XXX XXXXXXXXXXXX XX XXX XX:XXXXXX 2025                        │
│  │  IMAGE   │  XX XXX XXXX XXXX XXXX | XX 30 XXX 2025                       │
│  │          │  XXXXXXXX XXX XXXX XXXXXXXXX XXXXXXXXXXX XXXXXXXX...          │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  XXX XXX XXXXXXXX XXXXXX XXX XXXXXXXX XXXXX-XXXXXX...         │
│  │  IMAGE   │  XX XXXXXX XXX | XX 03 XXX 2026                               │
│  │          │  XXX XXX XXXXXXXX XXXXXX XXX XXXXXXXXX XXXXX-XXXXXX...        │
│  └──────────┘                                                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**User learns:** "The system can transform any text while keeping layout intact."

---

### Mode 3: GROUPS — See the Classification

Each content block tagged with a colored indicator showing its classification:

- 🟢 **Green (Group A):** Engaging, genuine, helpful content
- 🟡 **Yellow (Group B):** Neutral, informational content  
- 🔴 **Red (Group C):** Filter candidates (promotional, abstract, less relevant)

```
┌─ TOOLBAR ───────────────────────────────────────────────────────────────────┐
│ [Main] [XXX] [Groups] [Redact] [Hide] [Clean]     "Group Classification:    │
│                                                    🟢 engaging 🟡 neutral   │
│                                                    🔴 filter-candidates"    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  AWS News Blog                                                              │
│  ─────────────────────────────────────────────────────                      │
│                                                                             │
│  ┌──────────┐  🟢🟢 Top announcements of AWS re:Invent 2025                 │
│  │  IMAGE   │  by AWS News Blog Team | 30 NOV 2025                          │
│  │          │  Discover our most impactful innovations across analytics...  │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  🟡 AWS IAM Identity Center now supports multi-Region...      │
│  │  IMAGE   │  by Channy Yun | 03 FEB 2026                                  │
│  │          │  AWS IAM Identity Center now supports multi-Region...         │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  🔴 AWS Weekly Roundup: Amazon Bedrock agent workflows...     │
│  │  IMAGE   │  by Betty Zheng | 02 FEB 2026                                 │
│  │          │  Over the past week, we passed Laba festival...               │
│  └──────────┘                                                               │
│                                                                             │
│                                              Stats: 🟢5  🟡6  🔴8           │
└─────────────────────────────────────────────────────────────────────────────┘
```

**User learns:** "I can see exactly how the AI classified each item before anything is filtered."

---

### Mode 4: REDACT — Filter Candidates Visible but Masked

Red-group content replaced with XXX but still visible. The user sees *what* would be filtered and *where* it is.

```
┌─ TOOLBAR ───────────────────────────────────────────────────────────────────┐
│ [Main] [XXX] [Groups] [Redact] [Hide] [Clean]     "Selective Redaction:     │
│                                                    Group C text → XXX"      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  AWS News Blog                                                              │
│  ─────────────────────────────────────────────────────                      │
│                                                                             │
│  ┌──────────┐  Top announcements of AWS re:Invent 2025                      │
│  │  IMAGE   │  by AWS News Blog Team | 30 NOV 2025                          │
│  │          │  Discover our most impactful innovations across analytics...  │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  AWS IAM Identity Center now supports multi-Region...         │
│  │  IMAGE   │  by Channy Yun | 03 FEB 2026                                  │
│  │          │  AWS IAM Identity Center now supports multi-Region...         │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░│
│  │  IMAGE   │  XXX XXXXXX XXXXXXX: XXXXXX XXXXXXX XXXXX XXXXXXXXX...        │
│  │          │  XX XXXXX XXXXX | XX 02 XXX 2026                              │
│  └──────────┘  XXXX XXX XXXX XXXX, XX XXXXXX XXXX XXXXXXXX...               │
│               ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░│
│                                                                             │
│                                              8/19 filtered                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

**User learns:** "I can see exactly which items are filter candidates, with their position preserved."

---

### Mode 5: HIDE — Content Removed, Space Preserved

Filter candidates removed from view, but their *space* remains as a visual ghost. This shows the "weight" of what's being filtered.

```
┌─ TOOLBAR ───────────────────────────────────────────────────────────────────┐
│ [Main] [XXX] [Groups] [Redact] [Hide] [Clean]     "Hide Mode: Group C       │
│                                                    hidden, spaces remain"   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  AWS News Blog                                                              │
│  ─────────────────────────────────────────────────────                      │
│                                                                             │
│  ┌──────────┐  Top announcements of AWS re:Invent 2025                      │
│  │  IMAGE   │  by AWS News Blog Team | 30 NOV 2025                          │
│  │          │  Discover our most impactful innovations across analytics...  │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  AWS IAM Identity Center now supports multi-Region...         │
│  │  IMAGE   │  by Channy Yun | 03 FEB 2026                                  │
│  │          │  AWS IAM Identity Center now supports multi-Region...         │
│  └──────────┘                                                               │
│                                                                             │
│  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐ │
│                                                                             │
│  │                     (filtered content was here)                        │ │
│                                                                             │
│  └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘ │
│                                                                             │
│                                              8/19 filtered                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

**User learns:** "I can see how much content is being removed by the empty spaces left behind."

---

### Mode 6: CLEAN — Final Filtered View

The destination state: a clean page with only the content that passes the filter. Original design intact, page reflows naturally.

```
┌─ TOOLBAR ───────────────────────────────────────────────────────────────────┐
│ [Main] [XXX] [Groups] [Redact] [Hide] [Clean]     "Clean Mode: Group C      │
│                                                    removed, page reflowed"  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  AWS News Blog                                                              │
│  ─────────────────────────────────────────────────────                      │
│                                                                             │
│  ┌──────────┐  Top announcements of AWS re:Invent 2025                      │
│  │  IMAGE   │  by AWS News Blog Team | 30 NOV 2025                          │
│  │          │  Discover our most impactful innovations across analytics...  │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  AWS IAM Identity Center now supports multi-Region...         │
│  │  IMAGE   │  by Channy Yun | 03 FEB 2026                                  │
│  │          │  AWS IAM Identity Center now supports multi-Region...         │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  Announcing Amazon EC2 G7e instances accelerated by NVIDIA... │
│  │  IMAGE   │  by Channy Yun | 20 JAN 2026                                  │
│  │          │  AWS introduces Amazon EC2 G7e instances accelerated...       │
│  └──────────┘                                                               │
│                                                                             │
│  ┌──────────┐  Amazon EC2 X8i instances powered by Intel Xeon 6 processors..│
│  │  IMAGE   │  by Channy Yun | 15 JAN 2026                                  │
│  │          │  Amazon EC2 X8i instances are generally available...          │
│  └──────────┘                                                               │
│                                                                             │
│                                              8/19 filtered                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

**User learns:** "This is the final experience—same page, same design, just the content I want."

---

## Why the Trust Ladder Matters

Traditional filters are binary: content is either shown or hidden. Users must trust the algorithm blindly.

The progressive trust ladder inverts this:

| Step | User Question | System Answer |
|------|---------------|---------------|
| MAIN | "Is it working?" | "Yes, interception active" |
| XXX | "Can it modify content?" | "Yes, see full redaction" |
| GROUPS | "How does it classify?" | "See the colored indicators" |
| REDACT | "What would be filtered?" | "These items, shown as XXX" |
| HIDE | "How much am I missing?" | "This much (empty spaces)" |
| CLEAN | "Show me the final result" | "Here—clean, focused, yours" |

By the time users reach CLEAN mode, they've verified every step. Trust is *earned*, not assumed.

---

# Part 4: The Classification System

## Not Sentiment—Content Quality

A critical distinction: this system does **not** use simple positive/negative sentiment analysis. Instead, it classifies content by **engagement quality and relevance**.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     CLASSIFICATION MODEL                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  GROUP A (Green) — Engaging/Genuine — ~30-40% of content            │    │
│  │  ───────────────────────────────────────────────────────────────    │    │
│  │  • Personal stories, announcements, celebrations                    │    │
│  │  • Helpful tutorials, tips, practical advice                        │    │
│  │  • Interesting technical deep-dives                                 │    │
│  │  • Content that feels genuine and engaging                          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  GROUP B (Yellow) — Neutral/Informational — ~30-40% of content      │    │
│  │  ───────────────────────────────────────────────────────────────    │    │
│  │  • News updates, factual information                                │    │
│  │  • Product updates, releases                                        │    │
│  │  • General informational content                                    │    │
│  │  • Neither particularly engaging nor promotional                    │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  GROUP C (Red) — Filter Candidates — ~20-30% of content             │    │
│  │  ───────────────────────────────────────────────────────────────    │    │
│  │  • Marketing speak, promotional content                             │    │
│  │  • Abstract platform/tool descriptions                              │    │
│  │  • Vague or buzzword-heavy content                                  │    │
│  │  • Content that feels less authentic or more corporate              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Why Not Simple Sentiment?

Consider these two AWS blog headlines:

1. *"AWS Lambda now supports 10GB memory configurations"*
2. *"Unlock the power of cloud-native transformation with AWS"*

Both are "positive" in sentiment. But #1 is a concrete technical announcement (useful), while #2 is marketing speak (filterable). Simple sentiment analysis can't distinguish them—**content quality classification can**.

---

## The Power of Prompt-Based Classification

Here's the key insight: **the classification criteria are defined by a prompt, not hard-coded logic**.

This means:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    PROMPT-BASED FLEXIBILITY                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Current prompt (content quality):                                          │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  "Classify as engaging (tutorials, deep-dives) vs promotional       │    │
│  │   (marketing speak, buzzwords) vs neutral (factual updates)"        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Alternative prompt (technology focus):                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  "Classify as relevant (serverless, containers, AI/ML) vs           │    │
│  │   irrelevant (database, networking) vs mixed"                       │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Alternative prompt (audience targeting):                                   │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  "Classify as developer-focused (code, APIs) vs executive-focused   │    │
│  │   (business, ROI) vs operations-focused (monitoring, costs)"        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│                              ▼                                              │
│                                                                             │
│  SAME SYSTEM, DIFFERENT FILTER — just change the prompt                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**This dramatically expands the platform's use cases without code changes:**

| Use Case | Prompt Focus |
|----------|--------------|
| Mental wellness | Filter promotional/corporate content |
| Technical focus | Filter non-technical announcements |
| Security team | Show only security-related updates |
| Cost management | Show only pricing/cost-related content |
| Executive briefing | Filter technical details, keep strategic |

---

# Part 5: How It Works

## Architecture Overview

The system operates as a transparent proxy layer between the user's browser and the web:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         HIGH-LEVEL ARCHITECTURE                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│                                                                             │
│    ┌──────────┐         ┌──────────────┐         ┌───────────────┐          │
│    │  User's  │ ──────▶ │   MitM       │ ──────▶ │  Origin Site  │          │
│    │  Browser │         │   Proxy      │         │  (AWS Blog)   │          │
│    │          │ ◀────── │   (EC2)      │ ◀────── │               │          │
│    └──────────┘         └──────────────┘         └───────────────┘          │
│         │                      │                                            │
│         │                      │                                            │
│         │                      ▼                                            │
│         │               ┌──────────────┐                                    │
│         │               │     S3       │                                    │
│         │               │   (Cache)    │◀───────────┐                       │
│         │               └──────────────┘            │                       │
│         │                      ▲                    │                       │
│         │                      │                    │                       │
│         │               ┌──────┴───────┐            │                       │
│         │               │              │            │                       │
│         │               ▼              ▼            │                       │
│         │        ┌───────────┐  ┌───────────┐      │                       │
│         │        │  Lambda   │  │  Lambda   │      │                       │
│         │        │  (API)    │  │ (HTML     │──────┘                       │
│         │        └───────────┘  │  Graph)   │                               │
│         │               │       └───────────┘                               │
│         │               ▼              ▲                                    │
│         │        ┌───────────┐         │                                    │
│         │        │   LLM     │─────────┘                                    │
│         │        │(Classify) │  Out-of-band                                 │
│         │        └───────────┘  classification                              │
│         │                                                                   │
│         └──────────────────────────────────────────────────────────────────▶│
│                     User receives filtered HTML from cache                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Out-of-Band Processing Model

A key architectural decision: **classification happens offline, not at request time**.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      OUT-OF-BAND PROCESSING FLOW                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STEP 1: Scheduled Fetch (e.g., daily via EventBridge/GitHub Actions)│   │
│  │  ────────────────────────────────────────────────────────────────── │    │
│  │                                                                     │    │
│  │      Scheduler ──▶ Fetch AWS Blog ──▶ Store raw HTML in S3          │    │
│  │                                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                 │                                           │
│                                 ▼                                           │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STEP 2: Classification                                             │    │
│  │  ────────────────────────────────────────────────────────────────── │    │
│  │                                                                     │    │
│  │      Extract content blocks ──▶ Send to LLM ──▶ Receive groupings   │    │
│  │                                                                     │    │
│  │      Input:  [{id: "post-1", text: "Top announcements..."},         │    │
│  │               {id: "post-2", text: "AWS Weekly Roundup..."}]        │    │
│  │                                                                     │    │
│  │      Output: {group_a: ["post-1"], group_b: [...], group_c: [...]}  │    │
│  │                                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                 │                                           │
│                                 ▼                                           │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STEP 3: Generate View Variants                                     │    │
│  │  ────────────────────────────────────────────────────────────────── │    │
│  │                                                                     │    │
│  │      For each mode (GROUPS, REDACT, HIDE, CLEAN):                   │    │
│  │         Transform HTML ──▶ Store variant in S3                      │    │
│  │                                                                     │    │
│  │      Result: 6 cached HTML files ready to serve                     │    │
│  │                                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                 │                                           │
│                                 ▼                                           │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STEP 4: User Request (instant response from cache)                 │    │
│  │  ────────────────────────────────────────────────────────────────── │    │
│  │                                                                     │    │
│  │      Browser ──▶ Proxy ──▶ Serve cached variant from S3             │    │
│  │                                                                     │    │
│  │      Latency: ~70-80ms (no LLM call at request time)                │    │
│  │                                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Why Out-of-Band?

| Concern | Real-Time Processing | Out-of-Band Processing |
|---------|---------------------|------------------------|
| Latency | +2-5 seconds (LLM call) | ~70ms (cached) |
| Cost | Per-request LLM costs | Amortized daily cost |
| Reliability | Depends on LLM availability | Graceful degradation to last-known-good |
| Complexity | Request-path failure handling | Simple cache-and-serve |

For content that updates on a known cadence (daily blog posts), out-of-band processing is strictly superior.

---

## Deployed AWS Infrastructure

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    AWS DEPLOYMENT (eu-west-2)                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  DNS & Edge                                                                 │
│  ───────────────────────────────────────────────────────────────────────    │
│  • mitmproxy.dev.ai      → NLB → EC2 Proxy                           │
│  • mitmproxy-api.dev.ai  → CloudFront → Lambda API                   │
│  • cache.dev.ai          → CloudFront → Lambda Cache                 │
│                                                                             │
│  Compute                                                                    │
│  ───────────────────────────────────────────────────────────────────────    │
│  • EC2 (t2.micro)              Mitmproxy instance                          │
│  • Auto Scaling Group          Optional scaling path                        │
│  • Network Load Balancer       TCP:8080 ingress                            │
│                                                                             │
│  Serverless                                                                 │
│  ───────────────────────────────────────────────────────────────────────    │
│  • Lambda: mitmproxy__api      Request handling, classification            │
│  • Lambda: html_graph__dev     HTML transformation, variant generation     │
│  • Lambda: cache__dev          Cache management                            │
│  • Lambda: aws_comprehend__dev Sentiment analysis (alternative mode)       │
│                                                                             │
│  Storage                                                                    │
│  ───────────────────────────────────────────────────────────────────────    │
│  • S3: mgraph-ai-cache  Cached HTML, view variants                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Inline Mode (Alternative Architecture)

For real-time filtering needs, the system also supports an inline mode using AWS Comprehend for sentiment analysis:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    INLINE MODE (Real-Time Sentiment)                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    Browser ──▶ Proxy ──▶ Lambda API ──▶ AWS Comprehend                     │
│                              │                                              │
│                              ▼                                              │
│                    ┌─────────────────┐                                      │
│                    │ Sentiment Score │                                      │
│                    │ ≥ threshold?    │                                      │
│                    └────────┬────────┘                                      │
│                             │                                               │
│                    ┌────────┴────────┐                                      │
│                    ▼                 ▼                                      │
│              Replace with       Show original                               │
│                  XXX              content                                   │
│                                                                             │
│  Use case: Real-time negativity filtering where freshness > latency         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

This mode trades latency (~70ms → ~500ms) for real-time filtering without pre-processing.

---

# Part 6: Setup and Constraints

## First-Time Setup Requirements

The MVP requires browser configuration to route traffic through the proxy:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        SETUP REQUIREMENTS                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. PROXY CONFIGURATION                                                     │
│     ─────────────────────────────────────────────────────────────────       │
│     Configure browser to use:  mitmproxy.dev.ai:8080                  │
│                                                                             │
│  2. CERTIFICATE INSTALLATION                                                │
│     ─────────────────────────────────────────────────────────────────       │
│     Install the proxy's CA certificate to enable HTTPS interception.        │
│     (Required for TLS traffic to be decrypted and re-encrypted)            │
│                                                                             │
│     mitmproxy provides a "magic domain" workflow:                          │
│     Visit http://mitm.it after proxy is configured to download cert.       │
│                                                                             │
│  ⚠️  This is the highest-friction step and primary abandonment risk.       │
│      Onboarding UX must treat this as critical path, not afterthought.     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Security Implications

Installing a CA certificate expands the device's trust boundary. This is:

- **Standard practice** for enterprise web gateways and security tools
- **Documented by NIST** as a legitimate enterprise need for traffic visibility
- **A trust decision** that must be clearly communicated to users

For enterprise deployments, this can be managed via MDM/GPO. For individual users, clear onboarding and trust messaging is essential.

---

## MVP Constraints (Accepted)

| Constraint | Status | Implication |
|------------|--------|-------------|
| Single target page | MVP scope | Initially limited to AWS News Blog |
| Configured browser required | MVP scope | User must set proxy + install cert |
| Daily refresh cadence | Sufficient for use case | Not real-time, "fresh enough" |
| Single-tenant deployment | Current architecture | Customer data isolated in their AWS account |

---

# Part 7: Acceptance Criteria

## Functional Tests

| Test | Expected Behavior |
|------|-------------------|
| Visit AWS Blog through proxy, MAIN mode | Original page renders, toolbar shows interception active |
| Switch to GROUPS mode | Each content block shows colored dot (🟢/🟡/🔴) |
| Switch to REDACT mode | Group C content replaced with XXX, position preserved |
| Switch to HIDE mode | Group C content removed, whitespace preserved |
| Switch to CLEAN mode | Group C content removed, page reflows naturally |
| Mode indicator in toolbar | Current mode highlighted, stats displayed |
| Metrics display | Timestamp, bytes, latency, filter count visible |

## Quality Tests

| Test | Expected Behavior |
|------|-------------------|
| Classification distribution | ~30-40% Group A, ~30-40% Group B, ~20-30% Group C |
| Group A content | Tutorials, deep-dives, genuine announcements |
| Group C content | Marketing speak, promotional, abstract descriptions |
| No false positives in Group C | Technical announcements not classified as promotional |

## Non-Functional Tests

| Test | Expected Behavior |
|------|-------------------|
| Request latency (cached) | < 100ms response time |
| Cache freshness | Within configured refresh window (e.g., 24h) |
| Graceful degradation | If classification fails, serve last-known-good |
| TLS handling | No certificate warnings with properly installed CA |

---

# Part 8: Success Metrics

## User Experience Metrics

| Metric | Measurement | Target |
|--------|-------------|--------|
| Time to relevant content | Seconds from page load to clicking useful article | < 30s (vs 3+ min baseline) |
| Articles scanned before click | Count of headlines read before selection | < 5 (vs 10+ baseline) |
| Perceived relevance | User survey: "Did filtering match your intent?" | > 80% positive |
| Trust in system | User survey: "Do you understand what was filtered?" | > 90% positive |

## Technical Metrics

| Metric | Measurement | Target |
|--------|-------------|--------|
| Classification accuracy | % of items user agrees with classification | > 85% |
| Cache hit rate | % of requests served from cache | > 95% |
| System availability | Uptime of proxy + cache | > 99% |
| Processing cost | LLM cost per classification run | < $0.05/day |

---

# Part 9: Market Position and Differentiation

## Adjacent Markets

| Market | Similarity | Differentiation |
|--------|------------|-----------------|
| Secure Web Gateways (SWG) | Proxy architecture, policy enforcement | SWG blocks malware; we filter content *quality* |
| Content Moderation | AI classification | Moderation is platform-side; we're user-side |
| RSS Readers | Content curation | RSS changes the UI; we preserve the original page |
| Browser Extensions | Attention tools | Extensions are DOM-based; we're proxy-based for arbitrary sites |

## Key Differentiators

1. **Preserves original experience**: Same URL, same design, same trust—just filtered
2. **Progressive trust ladder**: Users verify filtering before committing
3. **Prompt-based flexibility**: Change the filter criteria without code changes
4. **Single-tenant deployment**: Customer data stays in customer's cloud
5. **Works on any site**: Proxy architecture isn't limited to sites with APIs

---

# Part 10: Risks and Mitigations

## Over-Personalization / Filter Bubble Risk

**Concern:** Algorithmic filtering could reduce exposure to diverse viewpoints.

**Mitigations:**
- Reversibility: Toggle to unfiltered view anytime
- Transparency: GROUPS mode shows exactly what's classified
- Progressive disclosure: Users see what's filtered before it's hidden
- Optional diversity guardrails: Could require minimum variety in shown content

## TLS Interception Security Risk

**Concern:** CA certificate installation expands device trust boundary.

**Mitigations:**
- Clear onboarding messaging about trust implications
- Enterprise deployment via MDM/GPO with IT oversight
- Certificate pinning and proper validation in proxy
- Compliance with NIST guidance on TLS visibility

## Third-Party Terms of Service Risk

**Concern:** Rewriting and re-serving third-party content may implicate ToS.

**Mitigations:**
- Position as personal-use "lens" at request time
- No public redistribution of cached content
- Per-site terms review for enterprise deployments
- User acknowledgment of responsibility for target site ToS

---

# Appendix A: Endpoint Reference

| Endpoint | Purpose |
|----------|---------|
| `mitmproxy.dev.ai` | Proxy endpoint (browser configuration) |
| `mitmproxy-api.dev.ai` | API endpoint (Lambda) |
| `cache.dev.ai` | Cache service (admin) |
| `13.40.60.229:8080` | Direct EC2 (QA/test bypass) |

---

# Appendix B: Repository Reference

| Repository | Purpose |
|------------|---------|
| `MGraph-AI__Service__mitmproxy` | Mitmproxy + API |
| `MGraph-AI__Service__Cache` | Cache service |
| `MGraph-AI__Service__Html__Graph` | HTML transformation |
| `MGraph-AI__Service__AWS__Comprehend` | Sentiment analysis |

---

# Appendix C: Classification Prompt Reference

Current classification prompt (content quality focus):

```
You are a content classifier for a demonstration system.
Your task is to split content blocks into exactly 3 groups.

GROUP A (Positive/Engaging) - approximately 30-40% of content:
- Personal stories, announcements, celebrations
- Helpful tutorials, tips, advice
- Interesting technical deep-dives
- Content that feels genuine and engaging

GROUP B (Neutral/Informational) - approximately 30-40% of content:
- News updates, factual information
- Product updates, releases
- General informational content
- Neither particularly engaging nor promotional

GROUP C (Promotional/Abstract/Filter-candidates) - approximately 20-30% of content:
- Marketing speak, promotional content
- Abstract platform/tool descriptions
- Vague or buzzword-heavy content
- Content that feels less authentic or more corporate

IMPORTANT:
- Distribute content across ALL THREE groups
- Each group should have at least 1 item (if enough content exists)
- Group C should have at least 20% of items for demonstration purposes
```

**To change filtering behavior:** Modify this prompt. No code changes required.

---

*Document Version: 2.0*  
*Last Updated: February 2026*
