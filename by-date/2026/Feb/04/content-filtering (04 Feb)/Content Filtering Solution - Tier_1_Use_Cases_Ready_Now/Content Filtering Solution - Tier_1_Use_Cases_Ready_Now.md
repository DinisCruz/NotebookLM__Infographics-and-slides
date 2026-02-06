# Web Content Filtering Platform
# Tier 1 Use Cases: Ready Now

---

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                                                                               ║
║    ████████╗██╗███████╗██████╗      ██╗                                       ║
║    ╚══██╔══╝██║██╔════╝██╔══██╗    ███║                                       ║
║       ██║   ██║█████╗  ██████╔╝    ╚██║                                       ║
║       ██║   ██║██╔══╝  ██╔══██╗     ██║                                       ║
║       ██║   ██║███████╗██║  ██║     ██║                                       ║
║       ╚═╝   ╚═╝╚══════╝╚═╝  ╚═╝     ╚═╝                                       ║
║                                                                               ║
║    READY NOW · DEMO TODAY · SELL TOMORROW                                     ║
║                                                                               ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

---

# Introduction: What's Possible Today

## The Platform in One Sentence

> **Visit any website through our proxy and see only the content that matches your intent — same URL, same design, intelligently filtered.**

## What the MVP Delivers Right Now

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   ┌─────────────┐      ┌─────────────┐      ┌─────────────┐                 │
│   │             │      │             │      │             │                 │
│   │  Original   │ ───▶ │   Classify  │ ───▶ │  Filtered   │                 │
│   │   Website   │      │   Content   │      │    View     │                 │
│   │             │      │             │      │             │                 │
│   └─────────────┘      └─────────────┘      └─────────────┘                 │
│                                                                             │
│   You visit the         AI categorizes       You see only                   │
│   same URL as           each content         what matches                   │
│   always                block                your criteria                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**No app to install.** Configure your browser once, and every qualifying page is filtered automatically.

**No new UI to learn.** The original website's design stays intact — we just remove the noise.

**No code changes per use case.** Want different filtering criteria? Change the prompt. That's it.

---

## The 5 Tier 1 Use Cases at a Glance

| ID | Codename | Use Case | Who It's For | Core Value |
|----|----------|----------|--------------|------------|
| **UC-01** | `CLARITY` | Content Quality Filter | Knowledge workers | See substance, hide fluff |
| **UC-02** | `STACKLENS` | Technology Stack Filter | Engineering teams | See your stack, hide the rest |
| **UC-03** | `ALTITUDE` | Technical vs Business | Mixed audiences | Right depth for your role |
| **UC-04** | `SENTINEL` | Security & Compliance | Security teams | Never miss what matters |
| **UC-05** | `ORIGIN` | New Features Only | Frequent readers | Skip the roundups |

**All 5 use cases work with the current MVP.** The only difference between them is the classification prompt.

---

# Part 1: User Experience & Value Proposition

---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-01  ·  CLARITY  ·  Content Quality Filter                              ┃
┃                                                                             ┃
┃   "See substance. Hide fluff."                                              ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    😫  "I check this blog daily. 70% is marketing fluff."                   │
│                                                                             │
│    😫  "I spend more time filtering than reading."                          │
│                                                                             │
│    😫  "The good stuff is buried under corporate speak."                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Content-heavy sites mix genuinely valuable material with promotional noise. Every visit requires mental triage.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  👤     │   "As a KNOWLEDGE WORKER who reads industry blogs..."         │
│   │ Reader  │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   automatically see only engaging, substantive content  │   │
│   │               (tutorials, deep-dives, real announcements)           │   │
│   │                                                                     │   │
│   │   HIDING      marketing speak, promotional material, and            │   │
│   │               buzzword-heavy corporate fluff                        │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I spend my reading time on content that's actually    │   │
│   │               valuable — not mentally filtering every visit         │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯
```

---

## Target Buyers

```
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│                  │  │                  │  │                  │
│  📚 Knowledge    │  │  📣 Content      │  │  🔬 Research     │
│     Workers      │  │     Teams        │  │     Analysts     │
│                  │  │                  │  │                  │
│  Read 5+ sources │  │  DevRel, Mktg,   │  │  Scan hundreds   │
│  daily           │  │  Comms teams     │  │  of sources      │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: The Daily Grind                                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   aws.amazon.com/blogs/aws                                                  │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │  📰 Top announcements of AWS re:Invent 2025          ◀── Worth it   │   │
│   │  📰 AWS Weekly Roundup: Bedrock, SageMaker...        ◀── Skip       │   │
│   │  📰 Unlock cloud-native transformation...            ◀── Marketing  │   │
│   │  📰 AWS IAM Identity Center multi-Region...          ◀── Worth it   │   │
│   │  📰 Accelerate your journey to the cloud...          ◀── Marketing  │   │
│   │  📰 New EC2 G7e instances with NVIDIA...             ◀── Worth it   │   │
│   │  📰 How customers are leveraging AWS...              ◀── Case study │   │
│   │  📰 Best practices for enterprise adoption...        ◀── Marketing  │   │
│   │  📰 Amazon EC2 X8i instances generally available...  ◀── Worth it   │   │
│   │  📰 Transform your business with generative AI...    ◀── Marketing  │   │
│   │                                                                     │   │
│   │                         ... 19 articles total ...                   │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   Time spent: 4 minutes                                                     │
│   Articles worth reading: 4                                                 │
│   Mental energy wasted on filtering: 😩😩😩                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With CLARITY Filter                                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   aws.amazon.com/blogs/aws   [CLARITY filter active]                        │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │  📰 Top announcements of AWS re:Invent 2025                         │   │
│   │  📰 AWS IAM Identity Center multi-Region replication...             │   │
│   │  📰 New EC2 G7e instances with NVIDIA RTX PRO 6000...               │   │
│   │  📰 Amazon EC2 X8i instances generally available...                 │   │
│   │                                                                     │   │
│   │  ┌───────────────────────────────────────────────────────────────┐  │   │
│   │  │  Showing 4 of 19 articles · 15 filtered as promotional/fluff  │  │   │
│   │  └───────────────────────────────────────────────────────────────┘  │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   Time spent: 30 seconds                                                    │
│   Articles shown: Only the ones worth reading                               │
│   Mental energy: Preserved 🧠✨                                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 GROUP A: ENGAGING                        ~30-40% of content            │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Tutorials and how-to guides                                             │
│   ✓ Technical deep-dives                                                    │
│   ✓ Genuine product announcements                                           │
│   ✓ Personal stories and real experiences                                   │
│                                                                             │
│   🟡 GROUP B: NEUTRAL                         ~30-40% of content            │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Factual news updates                                                    │
│   ✓ Product releases (straightforward)                                      │
│   ✓ Documentation updates                                                   │
│                                                                             │
│   🔴 GROUP C: FILTER CANDIDATES               ~20-30% of content            │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ Marketing speak and buzzwords                                           │
│   ✗ Promotional content                                                     │
│   ✗ Abstract platform descriptions                                          │
│   ✗ "Unlock/Transform/Accelerate your..." language                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "How much is 10 minutes of your attention worth, every day?"           ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   10 min/day  ×  250 work days  =  41 hours/year                  │     ║
║   │                                                                   │     ║
║   │   At $75/hr knowledge worker rate  =  $3,000+/year in attention   │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CLARITY User Journey                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────┐    ┌─────┐    ┌─────┐    ┌─────┐    ┌─────┐    ┌─────┐           │
│   │  1  │───▶│  2  │───▶│  3  │───▶│  4  │───▶│  5  │───▶│  6  │           │
│   └─────┘    └─────┘    └─────┘    └─────┘    └─────┘    └─────┘           │
│                                                                             │
│   Open       Navigate    See        Check      Verify     Read              │
│   browser    to blog     GROUPS     REDACT     with       in CLEAN          │
│   (proxy     as usual    mode       mode       HIDE       mode              │
│   config'd)              (dots)     (XXX)      mode       (focused)         │
│                                                                             │
│   ─────────────────────────────────────────────────────────────────────     │
│                                                                             │
│   "Am I       "Same      "What did  "What      "How       "This is          │
│   connected?" URL"       it find?"  exactly?"  much?"     what I want"      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-02  ·  STACKLENS  ·  Technology Stack Filter                           ┃
┃                                                                             ┃
┃   "See your stack. Hide the rest."                                          ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    😫  "AWS announced 50 things at re:Invent. 5 affect our stack."          │
│                                                                             │
│    😫  "My team shouldn't have to read about Amazon Braket."                │
│                                                                             │
│    😫  "We're a Lambda/DynamoDB shop. Show me Lambda/DynamoDB."             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

AWS (and other cloud providers) announce dozens of services. Most are irrelevant to any specific team.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  👤     │   "As an ENGINEERING LEAD with a defined tech stack..."       │
│   │  Lead   │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see announcements filtered to services we actually    │   │
│   │               use (Lambda, ECS, DynamoDB, API Gateway, etc.)        │   │
│   │                                                                     │   │
│   │   HIDING      services we don't use and won't adopt                 │   │
│   │               (GameLift, Braket, Ground Station, etc.)              │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     my team stays informed about relevant updates         │   │
│   │               without drowning in noise                             │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯
```

---

## Target Buyers

```
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│                  │  │                  │  │                  │
│  🏗️ Engineering  │  │  ⚙️ Platform     │  │  🚀 DevOps       │
│     Managers     │  │     Teams        │  │     Leads        │
│                  │  │                  │  │                  │
│  Keep team       │  │  Define what     │  │  Track infra     │
│  focused         │  │  tech is used    │  │  changes         │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Stack Profile Concept

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        EXAMPLE: "Serverless Shop" Profile                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  OUR STACK (always show)                                          │     │
│   │  ─────────────────────────────────────────────────────────────    │     │
│   │  Lambda · API Gateway · DynamoDB · S3 · CloudWatch · Step         │     │
│   │  Functions · EventBridge · SQS · SNS · Cognito                    │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  ADJACENT (show if relevant)                                      │     │
│   │  ─────────────────────────────────────────────────────────────    │     │
│   │  General security · IAM · Networking · Monitoring · Cost mgmt     │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  OUT OF SCOPE (hide)                                              │     │
│   │  ─────────────────────────────────────────────────────────────    │     │
│   │  EC2 · ECS · EKS · RDS · GameLift · Braket · Ground Station ·     │     │
│   │  Media services · IoT · Robotics · Blockchain · Mainframe         │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: AWS re:Invent Recap (everything)                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 Amazon Bedrock introduces new foundation models                        │
│   📰 AWS Lambda now supports 10GB memory                        ◀── Ours    │
│   📰 Amazon GameLift adds container support                                 │
│   📰 New EC2 instances with custom silicon                                  │
│   📰 DynamoDB global tables enhanced                            ◀── Ours    │
│   📰 Amazon Braket quantum computing updates                                │
│   📰 API Gateway WebSocket improvements                         ◀── Ours    │
│   📰 AWS Ground Station new locations                                       │
│   📰 Step Functions adds JSONata support                        ◀── Ours    │
│   📰 Amazon EKS anywhere enhancements                                       │
│                        ... 40 more announcements ...                        │
│                                                                             │
│   Your team: "Which of these 50 things matter to us?"                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With STACKLENS (Serverless Profile)                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 AWS Lambda now supports 10GB memory configurations                     │
│   📰 DynamoDB global tables enhanced with new consistency options           │
│   📰 API Gateway WebSocket connection improvements                          │
│   📰 Step Functions adds JSONata support for data transformation            │
│   📰 EventBridge Scheduler adds new recurrence patterns                     │
│   📰 CloudWatch adds custom metrics aggregation                             │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 6 of 50 · Profile: "Serverless Shop" · 44 hidden         │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   Your team: "These are the 6 things we need to evaluate."                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Your team shouldn't read about Amazon Braket when they're             ║
║       building serverless microservices."                                   ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   5 engineers  ×  30 min each reviewing announcements  =  2.5 hrs │     ║
║   │                                                                   │     ║
║   │   With STACKLENS: 5 engineers × 5 min each = 25 min               │     ║
║   │                                                                   │     ║
║   │   Savings per major announcement cycle: 2+ hours of eng time      │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-03  ·  ALTITUDE  ·  Technical vs Business Filter                       ┃
┃                                                                             ┃
┃   "Right depth for your role."                                              ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    👩‍💻 Engineer:  "I don't care about ROI. Show me the API."                │
│                                                                             │
│    👔 Executive: "I don't need the code. What's the business impact?"       │
│                                                                             │
│    😫 Both:      "This article wastes my time with stuff I don't need."     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Many sites mix deeply technical content with executive summaries. Different roles need different views.

---

## The User Stories (Two Personas)

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  👩‍💻    │   "As a SENIOR ENGINEER..."                                   │
│   │  Dev    │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see only technically detailed content                 │   │
│   │               (code examples, architecture, implementation)         │   │
│   │                                                                     │   │
│   │   HIDING      business summaries, ROI discussions, and              │   │
│   │               "what it means for your organization" sections        │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │   SO THAT     I can evaluate technical fit without wading through   │   │
│   │               business justification I don't need                   │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯

╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  👔     │   "As a TECHNICAL EXECUTIVE..."                               │
│   │  CTO    │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see strategic summaries and business impact           │   │
│   │               (capabilities, positioning, adoption considerations)  │   │
│   │                                                                     │   │
│   │   HIDING      implementation details, code samples, and             │   │
│   │               low-level technical specifications                    │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │   SO THAT     I can stay informed at the right altitude without     │   │
│   │               getting lost in technical weeds                       │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯
```

---

## Target Buyers

```
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│                  │  │                  │  │                  │
│  🏢 Mixed Orgs   │  │  📊 Consultants  │  │  📚 Training     │
│                  │  │                  │  │     Teams        │
│  Different roles │  │  Prepare         │  │                  │
│  need different  │  │  materials for   │  │  Curate content  │
│  views           │  │  different       │  │  by audience     │
│                  │  │  audiences       │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## Two Profiles, Same Source

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  TECHNICAL VIEW (Code Depth)                                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   🟢 SHOW: API reference, code samples, architecture diagrams,              │
│            implementation guides, SDK documentation                         │
│                                                                             │
│   🔴 HIDE: Executive summaries, ROI calculations, market positioning,       │
│            "why your business needs this"                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│  EXECUTIVE VIEW (Strategic Altitude)                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   🟢 SHOW: Capability overviews, business benefits, competitive             │
│            positioning, adoption considerations, pricing implications       │
│                                                                             │
│   🔴 HIDE: Code samples, API details, implementation specifics,             │
│            configuration parameters                                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Same source. Different lens. Everyone gets what they need."           ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   CTO reads the same AWS blog as the senior engineer              │     ║
║   │   Each sees content at their appropriate altitude                 │     ║
║   │   No one wastes time on irrelevant depth                          │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-04  ·  SENTINEL  ·  Security & Compliance Focus                        ┃
┃                                                                             ┃
┃   "Never miss what matters."                                                ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    😫  "A security update was buried in a general announcement."            │
│                                                                             │
│    😫  "I can't read every blog post. But I can't miss security news."      │
│                                                                             │
│    😫  "Compliance changes hide in feature release posts."                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Security teams have a mandate to never miss relevant updates — but most content is noise for their role.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  🔒     │   "As a SECURITY ENGINEER or COMPLIANCE OFFICER..."           │
│   │ SecOps  │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see only security-relevant content                    │   │
│   │               (vulnerabilities, compliance, IAM, encryption,        │   │
│   │               security features, audit capabilities)                │   │
│   │                                                                     │   │
│   │   HIDING      general feature launches, pricing updates, and        │   │
│   │               content with no security implications                 │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I can efficiently monitor my threat landscape         │   │
│   │               without reading everything                            │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯
```

---

## Target Buyers

```
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│                  │  │                  │  │                  │
│  🔒 Security     │  │  📋 Compliance   │  │  🛡️ MSSPs        │
│     Teams        │  │     Officers     │  │                  │
│                  │  │                  │  │                  │
│  Track CVEs,     │  │  Monitor         │  │  Watch multiple  │
│  security        │  │  regulatory      │  │  clients'        │
│  features        │  │  changes         │  │  landscapes      │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 SECURITY CRITICAL                                                      │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ CVEs and vulnerability disclosures                                      │
│   ✓ Security feature releases                                               │
│   ✓ IAM and access control changes                                          │
│   ✓ Encryption and key management updates                                   │
│   ✓ Compliance certifications (SOC2, HIPAA, FedRAMP)                        │
│   ✓ Audit and logging enhancements                                          │
│                                                                             │
│   🟡 SECURITY ADJACENT                                                      │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Networking changes (may have security implications)                     │
│   ✓ Monitoring and observability (detection relevant)                       │
│   ✓ General infrastructure (context useful)                                 │
│                                                                             │
│   🔴 NOT SECURITY RELEVANT                                                  │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ General feature launches                                                │
│   ✗ Pricing and billing                                                     │
│   ✗ Media services, gaming, IoT                                             │
│   ✗ Marketing announcements                                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: General AWS Blog (Security team perspective)                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 New EC2 instances available                                            │
│   📰 Lambda memory increased to 10GB                                        │
│   📰 IAM Identity Center multi-region support          ◀── SECURITY        │
│   📰 Amazon GameLift container support                                      │
│   📰 KMS key rotation improvements                     ◀── SECURITY        │
│   📰 DynamoDB global tables enhanced                                        │
│   📰 Security Hub new compliance standards             ◀── SECURITY        │
│   📰 MediaConvert new formats                                               │
│   📰 GuardDuty threat detection expanded               ◀── SECURITY        │
│                                                                             │
│   Security team: *scans everything, afraid to miss something*               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With SENTINEL Filter                                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   🔒 IAM Identity Center now supports multi-region replication              │
│   🔒 AWS KMS introduces automatic key rotation improvements                 │
│   🔒 Security Hub adds CIS AWS Foundations Benchmark v3.0                   │
│   🔒 Amazon GuardDuty expands threat detection for containers               │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 4 security-relevant items of 15 total                    │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   Security team: "These 4 items are our action list."                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Never miss a security announcement. Never waste time on               ║
║       irrelevant ones."                                                     ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Risk of missing critical security update:        HIGH           │     ║
║   │   Time wasted reading irrelevant content:          HIGH           │     ║
║   │   Solution: Automated, reliable security filter    ✓              │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-05  ·  ORIGIN  ·  New Features Only Filter                             ┃
┃                                                                             ┃
┃   "Skip the roundups. Read it once."                                        ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    😫  "I saw this announcement Monday. Now it's in the weekly roundup."    │
│                                                                             │
│    😫  "Is this new, or have I read this before in different packaging?"    │
│                                                                             │
│    😫  "The recap posts just rehash what I already know."                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Blogs republish content in multiple formats: original → weekly roundup → monthly recap → year-end summary. Frequent readers see the same news multiple times.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  👤     │   "As a FREQUENT READER who checks sources often..."          │
│   │ Reader  │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see only original announcements and new content       │   │
│   │                                                                     │   │
│   │   HIDING      weekly roundups, monthly recaps, "ICYMI" posts,       │   │
│   │               and year-end summaries that re-list old items         │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I don't re-read the same information in different     │   │
│   │               packaging                                             │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯
```

---

## Target Buyers

```
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│                  │  │                  │  │                  │
│  📖 Frequent     │  │  📰 News Junkies │  │  🔄 "Already     │
│     Readers      │  │                  │  │     Read It"     │
│                  │  │  Check multiple  │  │     People       │
│  Check daily     │  │  sources daily   │  │                  │
│  or multiple     │  │                  │  │  Déjà vu         │
│  times/week      │  │                  │  │  fatigue         │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Content Lifecycle Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    THE SAME NEWS, 4 DIFFERENT TIMES                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   DAY 1 (Monday)                                                            │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  📰 "AWS Lambda now supports 10GB memory configurations"            │   │
│   │     [ORIGINAL ANNOUNCEMENT]                        ◀── Worth reading │  │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   DAY 5 (Friday)                                                            │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  📰 "AWS Weekly Roundup: Lambda 10GB, Bedrock updates, and more"    │   │
│   │     [WEEKLY ROUNDUP]                               ◀── Skip, saw it  │  │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   END OF MONTH                                                              │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  📰 "January AWS Updates: Everything you might have missed"         │   │
│   │     [MONTHLY RECAP]                                ◀── Skip, saw it  │  │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   END OF YEAR                                                               │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  📰 "2025 Year in Review: Top AWS announcements"                    │   │
│   │     [YEAR-END SUMMARY]                             ◀── Skip, saw it  │  │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ORIGIN filter shows: 1 item (the original)                                │
│   ORIGIN filter hides: 3 items (the repackaging)                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 ORIGINAL CONTENT                                                       │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ First announcement of something new                                     │
│   ✓ Original deep-dive or tutorial                                          │
│   ✓ New analysis or unique perspective                                      │
│                                                                             │
│   🟡 ADDS VALUE                                                             │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Roundups that add context beyond original                               │
│   ✓ Analysis pieces that synthesize multiple announcements                  │
│   ✓ Tutorials based on recently announced features                          │
│                                                                             │
│   🔴 REDUNDANT                                                              │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ Weekly roundups that just list items                                    │
│   ✗ Monthly recaps                                                          │
│   ✗ "ICYMI" (In Case You Missed It) posts                                   │
│   ✗ Year-end summaries                                                      │
│   ✗ "Top announcements from [event]" (after initial coverage)               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: AWS Blog (Frequent Reader)                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 AWS Weekly Roundup: Bedrock, SageMaker, and more   ◀── Roundup        │
│   📰 New EC2 G7e instances announced                    ◀── Original       │
│   📰 January 2026 AWS updates recap                     ◀── Recap          │
│   📰 DynamoDB introduces new consistency options        ◀── Original       │
│   📰 ICYMI: Top announcements from re:Invent 2025       ◀── ICYMI          │
│   📰 Lambda adds ARM64 support for containers           ◀── Original       │
│   📰 AWS Weekly Roundup: Lambda, DynamoDB, and more     ◀── Roundup        │
│                                                                             │
│   "Wait, have I read about Lambda already? Is this new?"                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With ORIGIN Filter                                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 New EC2 G7e instances announced                                        │
│   📰 DynamoDB introduces new consistency options                            │
│   📰 Lambda adds ARM64 support for containers                               │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 3 original items · 4 roundups/recaps hidden              │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   "These 3 are new. I haven't seen them before."                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Read it once. Skip the repackaging."                                  ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Average blog: 30% roundups/recaps                               │     ║
║   │   For frequent readers: That's 30% wasted time                    │     ║
║   │                                                                   │     ║
║   │   ORIGIN gives back that 30%                                      │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

# Part 2: Technical Implementation

---

## Architecture Overview (All Use Cases)

All Tier 1 use cases share the same architecture. The only difference is the classification prompt.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        SHARED ARCHITECTURE                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                      OUT-OF-BAND PROCESSING                         │   │
│   │   (runs daily or on schedule — not at request time)                 │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│        ┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐      │
│        │ Schedule │────▶│  Fetch   │────▶│ Classify │────▶│ Generate │      │
│        │ Trigger  │     │   HTML   │     │  Blocks  │     │ Variants │      │
│        └──────────┘     └──────────┘     └──────────┘     └──────────┘      │
│                                                │                │           │
│                                                │                ▼           │
│                                         ┌──────┴───────┐  ┌──────────┐      │
│                                         │   Claude     │  │    S3    │      │
│                                         │   Sonnet     │  │  Cache   │      │
│                                         │  (via API)   │  │          │      │
│                                         └──────────────┘  └──────────┘      │
│                                                                │           │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                       REQUEST-TIME SERVING                          │   │
│   │   (fast — just serves pre-generated HTML from cache)                │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                │           │
│        ┌──────────┐     ┌──────────┐     ┌──────────┐          │           │
│        │  User's  │────▶│   Proxy  │────▶│  Serve   │◀─────────┘           │
│        │ Browser  │     │ (mitmprx)│     │  Cached  │                      │
│        └──────────┘     └──────────┘     └──────────┘                      │
│                                                │                           │
│                                                ▼                           │
│                                         Response: ~70ms                    │
│                                         (no LLM call)                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Why This Architecture Works

| Concern | Solution |
|---------|----------|
| **Latency** | LLM runs out-of-band; request-time is just S3 fetch (~70ms) |
| **Cost** | LLM costs amortized over daily/scheduled runs, not per-request |
| **Reliability** | If LLM fails, serve last-known-good cached version |
| **Consistency** | Same classification shown to all users until next refresh |

---

## The Six View Variants

For each target page, the system generates 6 HTML variants stored in S3:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        CACHED VARIANTS PER PAGE                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   /cache/aws-blog/                                                          │
│   │                                                                         │
│   ├── main.html      ─── Original page, interception confirmed              │
│   ├── xxx.html       ─── Full redaction (all text → X)                      │
│   ├── groups.html    ─── Classification dots visible (🟢🟡🔴)               │
│   ├── redact.html    ─── Group C shown as XXX                               │
│   ├── hide.html      ─── Group C removed, whitespace preserved              │
│   └── clean.html     ─── Group C removed, page reflowed                     │
│                                                                             │
│   User switches modes via toolbar → proxy serves corresponding variant      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Classification Prompts by Use Case

### UC-01: CLARITY (Content Quality)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: CLARITY                                                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier. Split content blocks into 3 groups.          │
│                                                                             │
│  GROUP A (Green) - ENGAGING - ~30-40% of content:                           │
│  - Personal stories, announcements, celebrations                            │
│  - Helpful tutorials, tips, practical advice                                │
│  - Interesting technical deep-dives                                         │
│  - Content that feels genuine and engaging                                  │
│                                                                             │
│  GROUP B (Yellow) - NEUTRAL - ~30-40% of content:                           │
│  - News updates, factual information                                        │
│  - Product updates, releases (straightforward)                              │
│  - General informational content                                            │
│                                                                             │
│  GROUP C (Red) - FILTER CANDIDATES - ~20-30% of content:                    │
│  - Marketing speak, promotional content                                     │
│  - Abstract platform/tool descriptions                                      │
│  - Vague or buzzword-heavy content                                          │
│  - "Unlock/Transform/Accelerate your..." language                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Status:** ✅ Implemented and tested

---

### UC-02: STACKLENS (Technology Stack)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: STACKLENS (Example: Serverless Profile)                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier for a serverless development team.            │
│  Split content blocks into 3 groups based on technology relevance.          │
│                                                                             │
│  GROUP A (Green) - OUR STACK - show prominently:                            │
│  - Lambda, API Gateway, DynamoDB, S3, CloudWatch                            │
│  - Step Functions, EventBridge, SQS, SNS, Cognito                           │
│  - Serverless patterns, event-driven architecture                           │
│                                                                             │
│  GROUP B (Yellow) - ADJACENT - show if relevant:                            │
│  - General security, IAM, networking fundamentals                           │
│  - Monitoring, observability, cost management                               │
│  - Cross-cutting concerns that affect our stack                             │
│                                                                             │
│  GROUP C (Red) - OUT OF SCOPE - filter out:                                 │
│  - EC2, ECS, EKS, RDS (we don't use these)                                  │
│  - GameLift, Braket, Ground Station, IoT, Robotics                          │
│  - Media services, blockchain, mainframe                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Status:** 📝 Template ready, customize per customer stack

---

### UC-03: ALTITUDE (Technical vs Business)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: ALTITUDE - TECHNICAL VIEW                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier for technical engineers.                      │
│  Split content based on technical depth.                                    │
│                                                                             │
│  GROUP A (Green) - TECHNICAL DEPTH - show:                                  │
│  - Code samples, API references, SDK documentation                          │
│  - Architecture diagrams, implementation guides                             │
│  - Configuration details, parameters, technical specs                       │
│                                                                             │
│  GROUP B (Yellow) - MIXED - show:                                           │
│  - Technical overview with some business context                            │
│  - Feature announcements with technical details                             │
│                                                                             │
│  GROUP C (Red) - BUSINESS ONLY - filter out:                                │
│  - ROI calculations, business justification                                 │
│  - Executive summaries, market positioning                                  │
│  - "Why your organization needs..." content                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: ALTITUDE - EXECUTIVE VIEW                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier for technical executives.                     │
│  Split content based on strategic relevance.                                │
│                                                                             │
│  GROUP A (Green) - STRATEGIC - show:                                        │
│  - Capability overviews, business benefits                                  │
│  - Competitive positioning, market implications                             │
│  - Pricing changes, adoption considerations                                 │
│                                                                             │
│  GROUP B (Yellow) - MIXED - show:                                           │
│  - Feature announcements with business context                              │
│  - High-level technical changes with strategic impact                       │
│                                                                             │
│  GROUP C (Red) - TECHNICAL WEEDS - filter out:                              │
│  - Code samples, API details                                                │
│  - Configuration parameters, implementation specifics                       │
│  - Low-level technical documentation                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Status:** 📝 Two templates, deploy as separate profiles

---

### UC-04: SENTINEL (Security Focus)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: SENTINEL                                                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier for a security team.                          │
│  Split content based on security relevance.                                 │
│                                                                             │
│  GROUP A (Green) - SECURITY CRITICAL - show prominently:                    │
│  - CVEs, vulnerability disclosures, security patches                        │
│  - IAM changes, access control updates                                      │
│  - Encryption, KMS, key management                                          │
│  - Compliance certifications (SOC2, HIPAA, FedRAMP, etc.)                   │
│  - Security Hub, GuardDuty, Inspector updates                               │
│  - Audit logging, CloudTrail enhancements                                   │
│                                                                             │
│  GROUP B (Yellow) - SECURITY ADJACENT - show:                               │
│  - Networking changes (VPC, security groups)                                │
│  - Monitoring and observability (detection-relevant)                        │
│  - Infrastructure changes with security implications                        │
│                                                                             │
│  GROUP C (Red) - NOT SECURITY RELEVANT - filter out:                        │
│  - General feature launches (no security angle)                             │
│  - Pricing, billing, cost management                                        │
│  - Media services, gaming, IoT (unless security-specific)                   │
│  - Marketing announcements                                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Status:** 📝 Template ready

---

### UC-05: ORIGIN (New Features Only)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: ORIGIN                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier identifying original vs redundant content.    │
│  Split content based on novelty and uniqueness.                             │
│                                                                             │
│  GROUP A (Green) - ORIGINAL CONTENT - show:                                 │
│  - First announcement of something new                                      │
│  - Original deep-dives, tutorials, or analysis                              │
│  - Unique perspectives not covered elsewhere                                │
│                                                                             │
│  GROUP B (Yellow) - ADDS VALUE - show:                                      │
│  - Roundups that add significant context beyond original                    │
│  - Analysis pieces synthesizing multiple announcements                      │
│  - Tutorials building on recently announced features                        │
│                                                                             │
│  GROUP C (Red) - REDUNDANT - filter out:                                    │
│  - Weekly roundups that just list items without new insight                 │
│  - Monthly recaps rehashing known announcements                             │
│  - "ICYMI" posts repeating old content                                      │
│  - Year-end summaries of previously covered items                           │
│  - "Top N announcements from [event]" after initial coverage                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Status:** 📝 Template ready

---

## S3 Cache Structure

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           S3 BUCKET LAYOUT                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   s3://mgraph-ai-cache/                                              │
│   │                                                                         │
│   ├── profiles/                                                             │
│   │   ├── clarity/              ◀── UC-01 Content Quality                   │
│   │   ├── stacklens-serverless/ ◀── UC-02 (customizable per customer)       │
│   │   ├── altitude-technical/   ◀── UC-03 Technical view                    │
│   │   ├── altitude-executive/   ◀── UC-03 Executive view                    │
│   │   ├── sentinel/             ◀── UC-04 Security focus                    │
│   │   └── origin/               ◀── UC-05 New features only                 │
│   │                                                                         │
│   └── pages/                                                                │
│       └── aws.amazon.com/                                                   │
│           └── blogs/                                                        │
│               └── aws/                                                      │
│                   ├── clarity/                                              │
│                   │   ├── main.html                                         │
│                   │   ├── xxx.html                                          │
│                   │   ├── groups.html                                       │
│                   │   ├── redact.html                                       │
│                   │   ├── hide.html                                         │
│                   │   ├── clean.html                                        │
│                   │   └── metadata.json                                     │
│                   ├── sentinel/                                             │
│                   │   └── ... (same 6 variants)                             │
│                   └── origin/                                               │
│                       └── ... (same 6 variants)                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Implementation Checklist

| Use Case | Codename | Prompt | Testing | Demo Ready |
|----------|----------|--------|---------|------------|
| Content Quality | CLARITY | ✅ Done | ✅ Done | ✅ Yes |
| Tech Stack | STACKLENS | 📝 Template | ⬜ Needed | ⬜ After test |
| Technical/Business | ALTITUDE | 📝 2 Templates | ⬜ Needed | ⬜ After test |
| Security Focus | SENTINEL | 📝 Template | ⬜ Needed | ⬜ After test |
| New Features | ORIGIN | 📝 Template | ⬜ Needed | ⬜ After test |

**To make any Tier 1 use case demo-ready:**
1. Finalize prompt wording
2. Run classification on target page
3. Review Group A/B/C distribution
4. Adjust prompt if needed
5. Generate 6 variants
6. Test mode switching

---

## Quick Reference: AWS Infrastructure

| Component | Endpoint / ARN |
|-----------|----------------|
| Proxy | `mitmproxy.dev.ai` |
| API | `mitmproxy-api.dev.ai` |
| Cache | `s3://mgraph-ai-cache` |
| Region | `eu-west-2` (London) |

---

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                                                                               ║
║   END OF TIER 1 USE CASES DOCUMENT                                            ║
║                                                                               ║
║   5 use cases · Same architecture · Different prompts                         ║
║                                                                               ║
║   "Same platform. Different lens. You decide what to see."                    ║
║                                                                               ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```
