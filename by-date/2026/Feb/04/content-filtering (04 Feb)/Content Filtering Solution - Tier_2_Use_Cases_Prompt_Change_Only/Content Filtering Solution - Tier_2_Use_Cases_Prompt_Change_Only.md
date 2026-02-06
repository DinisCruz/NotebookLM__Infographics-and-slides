# Web Content Filtering Platform
# Tier 2 Use Cases: Prompt Change Only

---

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                                                                               ║
║    ████████╗██╗███████╗██████╗     ██████╗                                    ║
║    ╚══██╔══╝██║██╔════╝██╔══██╗    ╚════██╗                                   ║
║       ██║   ██║█████╗  ██████╔╝     █████╔╝                                   ║
║       ██║   ██║██╔══╝  ██╔══██╗    ██╔═══╝                                    ║
║       ██║   ██║███████╗██║  ██║    ███████╗                                   ║
║       ╚═╝   ╚═╝╚══════╝╚═╝  ╚═╝    ╚══════╝                                   ║
║                                                                               ║
║    SAME CODE · DIFFERENT PROMPT · NEW USE CASE                                ║
║                                                                               ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

---

# Introduction: The Power of Prompt-Based Flexibility

## One Platform, Infinite Lenses

Tier 2 use cases require **zero code changes**. The MVP architecture is already built. The classification system is already running. The caching and serving infrastructure is already deployed.

**The only thing that changes: the prompt.**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│                        SAME PIPELINE, DIFFERENT OUTPUT                      │
│                                                                             │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐                  │
│   │    Fetch     │───▶│   Classify   │───▶│   Generate   │                  │
│   │    Page      │    │   Content    │    │   Variants   │                  │
│   └──────────────┘    └──────────────┘    └──────────────┘                  │
│                              │                                              │
│                              ▼                                              │
│                       ┌──────────────┐                                      │
│                       │              │                                      │
│                       │    PROMPT    │◀─── This is the only variable        │
│                       │              │                                      │
│                       └──────────────┘                                      │
│                                                                             │
│   Tier 1 prompts:  Content quality, Stack filter, Technical/Business...    │
│   Tier 2 prompts:  Mental wellness, Learning focus, Cost focus...          │
│                                                                             │
│   Same infrastructure. New customer segment. New value proposition.         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The 4 Tier 2 Use Cases at a Glance

| ID | Codename | Use Case | Who It's For | Core Value |
|----|----------|----------|--------------|------------|
| **UC-06** | `CALM` | Mental Wellness Filter | Stress-conscious readers | Stay informed, stay sane |
| **UC-07** | `LEARN` | Tutorial & Learning Focus | Developers learning | Turn blogs into courses |
| **UC-08** | `COSTWATCH` | Cost & Pricing Focus | FinOps teams | Never miss a pricing change |
| **UC-09** | `SIGNAL` | Vendor-Neutral Filter | Engineers, consultants | Knowledge without the pitch |

**All 4 work today.** Write the prompt → test → deploy.

---

# Part 1: User Experience & Value Proposition

---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-06  ·  CALM  ·  Mental Wellness Filter                                 ┃
┃                                                                             ┃
┃   "Stay informed. Stay sane."                                               ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    😰  "I want to stay informed, but the news stresses me out."             │
│                                                                             │
│    😰  "Every headline is designed to trigger anxiety."                     │
│                                                                             │
│    😰  "I end up doomscrolling even on professional content sites."         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Research shows negative news exposure correlates with worse mental health outcomes. News sites often lead with alarming content because it drives engagement — but that's not what readers actually want.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  🧘     │   "As someone who wants to STAY INFORMED WITHOUT              │
│   │ Reader  │    THE ANXIETY..."                                            │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   automatically filter out alarming, crisis-focused,    │   │
│   │               and doom-and-gloom content                            │   │
│   │                                                                     │   │
│   │   KEEPING     constructive news, solutions, progress updates,       │   │
│   │               and neutral factual reporting                         │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I can read the news without it ruining my day         │   │
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
│  🧘 Wellness-    │  │  🏥 Healthcare   │  │  👥 HR/People    │
│     Conscious    │  │     Settings     │  │     Teams        │
│     Individuals  │  │                  │  │                  │
│                  │  │  Waiting rooms,  │  │  Employee        │
│  Manage their    │  │  recovery        │  │  wellbeing       │
│  media diet      │  │  spaces          │  │  initiatives     │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 CONSTRUCTIVE                                                           │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Solutions and progress reports                                          │
│   ✓ Neutral factual updates                                                 │
│   ✓ Positive developments and achievements                                  │
│   ✓ How-to and educational content                                          │
│                                                                             │
│   🟡 BALANCED                                                               │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Mixed-tone reporting (acknowledges challenges + solutions)              │
│   ✓ Factual coverage without emotional amplification                        │
│                                                                             │
│   🔴 HIGH-STRESS / FILTER                                                   │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ Alarming headlines designed to trigger anxiety                          │
│   ✗ Crisis framing without actionable information                           │
│   ✗ Doom-focused content ("everything is falling apart")                    │
│   ✗ Conflict amplification                                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: Tech News Site (Typical Day)                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 BREAKING: Major security breach affects millions     ◀── Alarming     │
│   📰 Industry layoffs continue as recession looms         ◀── Doom         │
│   📰 New framework released for modern web development    ◀── Neutral      │
│   📰 "We're all doomed": Expert warns of AI catastrophe   ◀── Doom         │
│   📰 Tutorial: Building resilient microservices           ◀── Constructive │
│   📰 Company stock crashes 40% after earnings miss        ◀── Alarming     │
│   📰 Open source project celebrates 10 years              ◀── Positive     │
│                                                                             │
│   Reader after 5 minutes: 😰 anxious, distracted, unproductive              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With CALM Filter                                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 New framework released for modern web development                      │
│   📰 Tutorial: Building resilient microservices                             │
│   📰 Open source project celebrates 10 years of community growth            │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 3 of 7 · High-stress content filtered · CALM mode        │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   Reader after 5 minutes: 🧘 informed, focused, ready to work               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Your mental health is worth protecting."                              ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Average person: 20+ minutes/day on news                         │     ║
║   │   Reported stress from news: 56% of Americans (APA study)         │     ║
║   │                                                                   │     ║
║   │   CALM filter: Same information, lower cortisol                   │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

## Unique Selling Point: B2C Potential

This is the only Tier 2 use case with clear **consumer appeal**. Consider:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   💡 MARKET OPPORTUNITY                                                     │
│                                                                             │
│   B2B Angle:                                                                │
│   • HR teams offering "low-stress news reading" as a benefit                │
│   • Healthcare waiting rooms with filtered content displays                 │
│   • Corporate wellness programs                                             │
│                                                                             │
│   B2C Angle (future):                                                       │
│   • Browser extension / app for individuals                                 │
│   • Subscription service for "calm news"                                    │
│   • Mental health apps integration                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   UC-07  ·  LEARN  ·  Tutorial & Learning Focus                             ┃
┃                                                                             ┃
┃   "Turn any tech blog into a learning resource."                            ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    📚  "I'm learning AWS. The blog has great tutorials... buried in news."  │
│                                                                             │
│    📚  "I want hands-on content, not announcement press releases."          │
│                                                                             │
│    📚  "I'm building skills. Show me what teaches, not what sells."         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Technical blogs mix tutorials with announcements, opinions, and corporate updates. Learners want the hands-on content.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  📚     │   "As a DEVELOPER LEARNING A NEW TECHNOLOGY..."               │
│   │ Learner │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see only tutorials, how-tos, and hands-on guides      │   │
│   │                                                                     │   │
│   │   HIDING      announcements, news updates, opinion pieces, and      │   │
│   │               content without practical learning value              │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I can focus on building skills, not reading news      │   │
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
│  🎓 Bootcamp     │  │  💻 Self-Taught  │  │  🏢 Corporate    │
│     Students     │  │     Developers   │  │     Training     │
│                  │  │                  │  │                  │
│  Need curated    │  │  Learning from   │  │  Curating        │
│  learning paths  │  │  official docs   │  │  resources for   │
│                  │  │  and blogs       │  │  employees       │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 HANDS-ON LEARNING                                                      │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Step-by-step tutorials                                                  │
│   ✓ "How to build X" posts                                                  │
│   ✓ Code walkthroughs and examples                                          │
│   ✓ Practical guides with exercises                                         │
│   ✓ Getting started guides                                                  │
│                                                                             │
│   🟡 CONCEPTUAL                                                             │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Architecture explanations                                               │
│   ✓ Best practices discussions                                              │
│   ✓ Concept deep-dives                                                      │
│                                                                             │
│   🔴 NOT LEARNING CONTENT                                                   │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ Product announcements (no tutorial component)                           │
│   ✗ News and updates                                                        │
│   ✗ Opinion pieces and editorials                                           │
│   ✗ Case studies without technical depth                                    │
│   ✗ Marketing content                                                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: AWS Blog (Learner Perspective)                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 AWS announces new partnership with...              ◀── News           │
│   📰 Tutorial: Building a serverless API with Lambda    ◀── Learning ✓    │
│   📰 Weekly roundup: What's new in January              ◀── News           │
│   📰 How to implement DynamoDB single-table design      ◀── Learning ✓    │
│   📰 Customer success story: Company X saves 40%        ◀── Case study     │
│   📰 Getting started with Step Functions                ◀── Learning ✓    │
│   📰 AWS re:Invent keynote highlights                   ◀── News           │
│                                                                             │
│   Learner: "3 tutorials in 7 posts. Lots of scrolling to find them."        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With LEARN Filter                                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📚 Tutorial: Building a serverless API with Lambda                        │
│   📚 How to implement DynamoDB single-table design                          │
│   📚 Getting started with Step Functions                                    │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 3 tutorials · 4 non-learning items filtered · LEARN mode │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   Learner: "Perfect. These are my study materials for today."               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Turn any tech blog into a focused learning resource."                 ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Developer learning time: Precious                               │     ║
║   │   Tutorial-to-noise ratio on vendor blogs: Low                    │     ║
║   │                                                                   │     ║
║   │   LEARN filter: 100% learning content, 0% distractions            │     ║
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
┃   UC-08  ·  COSTWATCH  ·  Cost & Pricing Focus                              ┃
┃                                                                             ┃
┃   "Never be surprised by a pricing change."                                 ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    💸  "A pricing change was buried in a general feature announcement."     │
│                                                                             │
│    💸  "Our AWS bill spiked and we missed the savings plan announcement."   │
│                                                                             │
│    💸  "I can't read every post. But I MUST know about pricing changes."    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

FinOps teams need to track pricing and cost-related content, but it's often buried in general announcements.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  💰     │   "As a FINOPS ENGINEER or CLOUD COST MANAGER..."             │
│   │ FinOps  │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see only cost-related content                         │   │
│   │               (pricing changes, savings plans, cost optimization,   │   │
│   │               billing updates, instance pricing)                    │   │
│   │                                                                     │   │
│   │   HIDING      general features with no cost implications            │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I never miss a pricing change that affects our        │   │
│   │               cloud budget                                          │   │
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
│  📊 FinOps       │  │  💼 CFOs of      │  │  🔧 Cloud Cost   │
│     Teams        │  │     Cloud-Heavy  │  │     Consultants  │
│                  │  │     Companies    │  │                  │
│  Dedicated cost  │  │                  │  │  MSPs managing   │
│  optimization    │  │  Need strategic  │  │  client          │
│  function        │  │  cost visibility │  │  cloud spend     │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 COST CRITICAL                                                          │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Pricing changes and updates                                             │
│   ✓ New savings plans and reserved instances                                │
│   ✓ Cost optimization features (Cost Explorer, Budgets)                     │
│   ✓ Billing and payment updates                                             │
│   ✓ Free tier changes                                                       │
│   ✓ Data transfer pricing                                                   │
│                                                                             │
│   🟡 COST ADJACENT                                                          │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ New instance types (pricing implications)                               │
│   ✓ New regions (affects pricing and data transfer)                         │
│   ✓ Service retirements (migration cost implications)                       │
│                                                                             │
│   🔴 NOT COST RELEVANT                                                      │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ Feature announcements (no pricing mentioned)                            │
│   ✗ Integrations and partnerships                                           │
│   ✗ Tutorials and how-tos                                                   │
│   ✗ Security features (unless pricing-related)                              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: AWS Blog (FinOps Perspective)                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 New Lambda memory options up to 10GB                                   │
│   📰 AWS Cost Explorer adds commitment tracking        ◀── Cost relevant   │
│   📰 Amazon Bedrock foundation model updates                                │
│   📰 Savings Plans now cover additional services       ◀── Cost relevant   │
│   📰 Step Functions adds new workflow features                              │
│   📰 EC2 Spot instance pricing changes                 ◀── Cost relevant   │
│   📰 CloudWatch adds custom dashboards                                      │
│                                                                             │
│   FinOps team: "Which 3 of these 7 affect our budget?"                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With COSTWATCH Filter                                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   💰 AWS Cost Explorer adds commitment tracking and recommendations         │
│   💰 Savings Plans now cover Lambda, Fargate, and SageMaker                 │
│   💰 EC2 Spot instance pricing changes for M7 family                        │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 3 cost-relevant items · COSTWATCH mode                   │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   FinOps team: "These 3 need to go into our monthly cost review."           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Never be surprised by a pricing change again."                        ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   One missed savings plan announcement = $10,000s in lost savings │     ║
║   │   One missed pricing increase = Unexpected budget overrun         │     ║
║   │                                                                   │     ║
║   │   COSTWATCH: Every cost-relevant update, zero noise               │     ║
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
┃   UC-09  ·  SIGNAL  ·  Vendor-Neutral Filter                                ┃
┃                                                                             ┃
┃   "Get the knowledge. Skip the pitch."                                      ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    🎯  "AWS blogs have great technical content... wrapped in sales pitch."  │
│                                                                             │
│    🎯  "I want to learn from vendor expertise, not be marketed to."         │
│                                                                             │
│    🎯  "Every post ends with 'start using X today' — I just want the info." │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Vendor blogs mix genuine technical knowledge with product marketing. Engineers want the former, not the latter.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  🔍     │   "As a TECHNICAL PROFESSIONAL reading vendor content..."     │
│   │Engineer │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see technical substance                               │   │
│   │               (how things work, architecture, code, implementation) │   │
│   │                                                                     │   │
│   │   HIDING      sales and marketing messages                          │   │
│   │               ("why you should buy," success stories, competitive   │   │
│   │               positioning, calls-to-action)                         │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I can learn from vendor expertise without being       │   │
│   │               sold to                                               │   │
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
│  👩‍💻 Engineers   │  │  🏢 Consultants  │  │  ✍️ Technical    │
│     Who Read     │  │                  │  │     Writers      │
│     Vendor Blogs │  │  Need vendor-    │  │                  │
│                  │  │  neutral info    │  │  Extracting      │
│  Want knowledge, │  │  for clients     │  │  facts from      │
│  not marketing   │  │                  │  │  marketing       │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Classification System

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   🟢 TECHNICAL SUBSTANCE                                                    │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ How things work (architecture, internals)                               │
│   ✓ Code samples and implementations                                        │
│   ✓ Technical specifications and details                                    │
│   ✓ Performance characteristics and benchmarks                              │
│   ✓ Integration patterns and best practices                                 │
│                                                                             │
│   🟡 MIXED                                                                  │
│   ─────────────────────────────────────────────────────────────────         │
│   ✓ Technical content with moderate product positioning                     │
│   ✓ Feature announcements with real technical depth                         │
│                                                                             │
│   🔴 SALES/MARKETING                                                        │
│   ─────────────────────────────────────────────────────────────────         │
│   ✗ "Why you should choose [product]"                                       │
│   ✗ Competitive positioning and comparisons                                 │
│   ✗ Customer success stories focused on vendor benefits                     │
│   ✗ ROI calculators and business justification                              │
│   ✗ "Get started today" / "Sign up now" content                             │
│   ✗ Awards and recognition announcements                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: Vendor Blog (Engineer Perspective)                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   📰 Why enterprises are choosing [Vendor] for AI     ◀── Sales pitch      │
│   📰 Deep dive: How our distributed storage works     ◀── Technical ✓      │
│   📰 Customer spotlight: Company X's 10x improvement  ◀── Success story    │
│   📰 Implementing event-driven architectures          ◀── Technical ✓      │
│   📰 [Vendor] named leader in Magic Quadrant          ◀── Marketing        │
│   📰 Under the hood: Our consensus algorithm          ◀── Technical ✓      │
│   📰 Get started with [Product] in 5 minutes          ◀── CTA              │
│                                                                             │
│   Engineer: "3 useful technical posts, 4 marketing posts."                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With SIGNAL Filter                                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   🔧 Deep dive: How our distributed storage works                           │
│   🔧 Implementing event-driven architectures                                │
│   🔧 Under the hood: Our consensus algorithm                                │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  Showing 3 technical items · 4 marketing items filtered · SIGNAL  │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   Engineer: "Now I can learn from their expertise without the sales."       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Get the knowledge. Skip the pitch."                                   ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Vendor blogs: Great engineers writing content                   │     ║
║   │   Problem: Wrapped in marketing and CTAs                          │     ║
║   │                                                                   │     ║
║   │   SIGNAL: Extracts the engineering, filters the sales             │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

# Part 2: Technical Implementation

---

## Shared Architecture (Same as Tier 1)

Tier 2 uses the identical infrastructure as Tier 1:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   TIER 1  ══════════════════════════════════════════════════════════════    │
│   │                                                                         │
│   │   • CLARITY (Content Quality)                                           │
│   │   • STACKLENS (Technology Stack)                                        │
│   │   • ALTITUDE (Technical vs Business)                                    │
│   │   • SENTINEL (Security Focus)                                           │
│   │   • ORIGIN (New Features Only)                                          │
│   │                                                                         │
│   │              │                                                          │
│   │              │  SAME                                                    │
│   │              │  ARCHITECTURE                                            │
│   │              │                                                          │
│   │              ▼                                                          │
│   │                                                                         │
│   TIER 2  ══════════════════════════════════════════════════════════════    │
│   │                                                                         │
│   │   • CALM (Mental Wellness)                                              │
│   │   • LEARN (Tutorial Focus)                                              │
│   │   • COSTWATCH (Pricing Focus)                                           │
│   │   • SIGNAL (Vendor-Neutral)                                             │
│   │                                                                         │
│   └─────────────────────────────────────────────────────────────────────    │
│                                                                             │
│   The only difference: the classification PROMPT                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Classification Prompts

### UC-06: CALM (Mental Wellness)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: CALM                                                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier focused on mental wellness.                   │
│  Split content based on emotional tone and stress impact.                   │
│                                                                             │
│  GROUP A (Green) - CONSTRUCTIVE - ~30-40% of content:                       │
│  - Solutions, progress reports, positive developments                       │
│  - Neutral factual updates without emotional amplification                  │
│  - How-to guides and educational content                                    │
│  - Achievements and constructive news                                       │
│                                                                             │
│  GROUP B (Yellow) - BALANCED - ~30-40% of content:                          │
│  - Mixed-tone reporting (challenges + solutions)                            │
│  - Factual coverage of complex issues                                       │
│  - Nuanced discussions                                                      │
│                                                                             │
│  GROUP C (Red) - HIGH-STRESS - ~20-30% of content:                          │
│  - Alarming headlines designed to trigger anxiety                           │
│  - Crisis framing without actionable information                            │
│  - Doom-focused content ("everything is falling apart")                     │
│  - Fear-based or outrage-inducing language                                  │
│  - Conflict amplification                                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### UC-07: LEARN (Tutorial Focus)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: LEARN                                                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier for developers learning new technologies.     │
│  Split content based on learning and educational value.                     │
│                                                                             │
│  GROUP A (Green) - HANDS-ON LEARNING - ~30-40% of content:                  │
│  - Step-by-step tutorials and how-to guides                                 │
│  - Code walkthroughs and examples                                           │
│  - "How to build X" posts with practical exercises                          │
│  - Getting started guides                                                   │
│  - Workshops and labs                                                       │
│                                                                             │
│  GROUP B (Yellow) - CONCEPTUAL - ~30-40% of content:                        │
│  - Architecture explanations and diagrams                                   │
│  - Best practices discussions                                               │
│  - Deep-dive explanations of concepts                                       │
│                                                                             │
│  GROUP C (Red) - NOT LEARNING CONTENT - ~20-30% of content:                 │
│  - Product announcements without tutorial component                         │
│  - News and updates                                                         │
│  - Opinion pieces and editorials                                            │
│  - Customer case studies without technical depth                            │
│  - Marketing and promotional content                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### UC-08: COSTWATCH (Pricing Focus)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: COSTWATCH                                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier for FinOps and cloud cost management teams.   │
│  Split content based on cost and pricing relevance.                         │
│                                                                             │
│  GROUP A (Green) - COST CRITICAL - show prominently:                        │
│  - Pricing changes and updates                                              │
│  - Savings plans, reserved instances, spot pricing                          │
│  - Cost optimization features (Cost Explorer, Budgets, etc.)                │
│  - Billing and payment updates                                              │
│  - Free tier changes                                                        │
│  - Data transfer pricing                                                    │
│                                                                             │
│  GROUP B (Yellow) - COST ADJACENT - show:                                   │
│  - New instance types (pricing implications)                                │
│  - New regions (affects pricing and data transfer)                          │
│  - Service retirements (migration cost implications)                        │
│                                                                             │
│  GROUP C (Red) - NOT COST RELEVANT - filter:                                │
│  - Feature announcements with no pricing mentioned                          │
│  - Integrations and partnerships                                            │
│  - Tutorials and how-tos (unless cost-focused)                              │
│  - Security features (unless pricing-related)                               │
│  - General marketing content                                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### UC-09: SIGNAL (Vendor-Neutral)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PROMPT: SIGNAL                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  You are a content classifier extracting technical substance from vendor    │
│  blogs while filtering marketing and sales content.                         │
│                                                                             │
│  GROUP A (Green) - TECHNICAL SUBSTANCE - show:                              │
│  - How things work (architecture, internals, design decisions)              │
│  - Code samples and implementations                                         │
│  - Technical specifications and documentation                               │
│  - Performance characteristics and benchmarks                               │
│  - Integration patterns and best practices                                  │
│                                                                             │
│  GROUP B (Yellow) - MIXED - show:                                           │
│  - Technical content with moderate product positioning                      │
│  - Feature announcements with real technical depth                          │
│                                                                             │
│  GROUP C (Red) - SALES/MARKETING - filter:                                  │
│  - "Why you should choose [product]" content                                │
│  - Competitive positioning and comparisons                                  │
│  - Customer success stories focused on vendor benefits                      │
│  - ROI calculators and business justification                               │
│  - "Get started today" / "Sign up now" calls-to-action                      │
│  - Awards, recognition, and industry accolades                              │
│  - Partnership announcements                                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Implementation Status

| Use Case | Codename | Prompt Status | To Demo-Ready |
|----------|----------|---------------|---------------|
| Mental Wellness | CALM | 📝 Ready | Test on news site |
| Tutorial Focus | LEARN | 📝 Ready | Test on tech blog |
| Cost Focus | COSTWATCH | 📝 Ready | Test on AWS blog |
| Vendor-Neutral | SIGNAL | 📝 Ready | Test on vendor blog |

**Steps to make demo-ready (per use case):**
1. Select target site for demo
2. Run prompt against sample content
3. Review classification quality
4. Adjust prompt if needed
5. Generate 6 view variants
6. Record demo video

---

## Estimated Effort

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   Per Use Case:                                                             │
│   ─────────────────────────────────────────────────────────────────         │
│   • Finalize prompt wording:           30 minutes                           │
│   • Test on sample content:            1 hour                               │
│   • Adjust and retest:                 1-2 hours                            │
│   • Generate variants:                 Automated                            │
│   • Create demo recording:             30 minutes                           │
│   ─────────────────────────────────────────────────────────────────         │
│   Total per use case:                  3-4 hours                            │
│                                                                             │
│   All 4 Tier 2 use cases:              1-2 days                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                                                                               ║
║   END OF TIER 2 USE CASES DOCUMENT                                            ║
║                                                                               ║
║   4 use cases · Zero code changes · Just write the prompt                     ║
║                                                                               ║
║   "Same pipeline. New prompt. New market."                                    ║
║                                                                               ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```
