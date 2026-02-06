# Web Content Filtering Platform
# Tier 3 & 4 Use Cases: Future Development

---

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                                                                               ║
║    ████████╗██╗███████╗██████╗     ██████╗        ██╗  ██╗                    ║
║    ╚══██╔══╝██║██╔════╝██╔══██╗    ╚════██╗       ██║  ██║                    ║
║       ██║   ██║█████╗  ██████╔╝     █████╔╝    ████████████╗                  ║
║       ██║   ██║██╔══╝  ██╔══██╗     ╚═══██╗    ╚═══██╔═██╔═╝                  ║
║       ██║   ██║███████╗██║  ██║    ██████╔╝        ██║ ██║                    ║
║       ╚═╝   ╚═╝╚══════╝╚═╝  ╚═╝    ╚═════╝         ╚═╝ ╚═╝                    ║
║                                                                               ║
║    DEVELOPMENT REQUIRED · WEEKS TO MONTHS · ROADMAP ITEMS                     ║
║                                                                               ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

---

# Introduction: Beyond Prompt Changes

## What Requires Development

Tier 1 and Tier 2 use cases work with the existing MVP — only the classification prompt changes.

Tier 3 and Tier 4 require **new capabilities**:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   TIER 1 + 2                          TIER 3 + 4                            │
│   ───────────────────                 ───────────────────                   │
│                                                                             │
│   ✅ LLM classification               ✅ LLM classification                 │
│   ✅ HTML caching                     ✅ HTML caching                       │
│   ✅ 6 view variants                  ✅ 6 view variants                    │
│   ✅ Mode toolbar                     ✅ Mode toolbar                       │
│                                                                             │
│   ─── Prompt change only ───          ─── NEW CAPABILITIES ───              │
│                                                                             │
│                                       ⬜ Deterministic rules engine          │
│                                       ⬜ CSS selector configuration          │
│                                       ⬜ Hybrid rules + LLM                  │
│                                       ⬜ Layout transformation               │
│                                       ⬜ Template system                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The 3 Future Use Cases at a Glance

| Tier | ID | Codename | Use Case | Development | Timeline |
|------|-----|----------|----------|-------------|----------|
| **3** | UC-10 | `CLEAN` | Nuisance Removal | Rules engine | 2-4 weeks |
| **3** | UC-11 | `PRECISION` | Mechanical Rules | Config UI | 3-6 weeks |
| **4** | UC-12 | `READER` | Flat UI Layout | Template system | 2-3 months |

---

# Part 1: User Experience & Value Proposition

---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   TIER 3                                                                    ┃
┃                                                                             ┃
┃   UC-10  ·  CLEAN  ·  Nuisance Removal                                      ┃
┃                                                                             ┃
┃   "Read without interruption."                                              ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    🚫  "I can't read this article — there's a popup blocking it."           │
│                                                                             │
│    🚫  "Newsletter signup overlays every 30 seconds."                       │
│                                                                             │
│    🚫  "Sticky banners, floating CTAs, autoplay videos... I just want       │
│         to READ."                                                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Many content sites are cluttered with popups, overlays, and intrusive elements that interrupt reading. This is the #1 reason people install ad blockers.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  📖     │   "As a READER of content websites..."                        │
│   │ Reader  │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   automatically remove popups, overlays, sticky         │   │
│   │               banners, newsletter modals, and intrusive CTAs        │   │
│   │                                                                     │   │
│   │   KEEPING     the actual content I came to read                     │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     I can consume content without interruptions           │   │
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
│  👥 Everyone     │  │  🏢 Enterprise   │  │  ♿ Accessibility │
│                  │  │     Teams        │  │     Advocates    │
│  Universal pain  │  │                  │  │                  │
│  point           │  │  Productivity    │  │  Overlays break  │
│                  │  │  mandate         │  │  screen readers  │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## The Nuisance Categories

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        NUISANCE PATTERNS TO REMOVE                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  POPUPS & MODALS                                                    │   │
│   │  ───────────────────────────────────────────────────────────────    │   │
│   │  • Newsletter signup overlays                                       │   │
│   │  • "Subscribe to continue reading" gates                            │   │
│   │  • Exit-intent popups                                               │   │
│   │  • Cookie consent banners (after initial acceptance)                │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  STICKY ELEMENTS                                                    │   │
│   │  ───────────────────────────────────────────────────────────────    │   │
│   │  • Floating CTAs                                                    │   │
│   │  • Sticky header ads                                                │   │
│   │  • Bottom banners that follow scroll                                │   │
│   │  • Chat widgets                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  INTERSTITIALS                                                      │   │
│   │  ───────────────────────────────────────────────────────────────    │   │
│   │  • "Please disable your ad blocker" notices                         │   │
│   │  • Countdown timers before content                                  │   │
│   │  • "Continue to site" intermediate pages                            │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  MEDIA INTERRUPTIONS                                                │   │
│   │  ───────────────────────────────────────────────────────────────    │   │
│   │  • Autoplay videos                                                  │   │
│   │  • Video ads in content                                             │   │
│   │  • Audio that plays automatically                                   │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: Typical Content Site                                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ╔═══════════════════════════════════════════════════════════════════╗     │
│   ║          🔔 SUBSCRIBE TO OUR NEWSLETTER! 🔔                       ║     │
│   ║   [Email address...          ] [SUBSCRIBE]      ✕ close           ║     │
│   ╚═══════════════════════════════════════════════════════════════════╝     │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  ARTICLE TITLE                                    [AD] [AD] [AD]  │     │
│   │  ─────────────────────────────────────────────────────────────    │     │
│   │                                                                   │     │
│   │  First paragraph of content that you're trying to read but        │     │
│   │  can barely see because of all the overlays...                    │     │
│   │                                                                   │     │
│   │  ┌───────────────────┐                                            │     │
│   │  │ 🎬 AUTOPLAY VIDEO │                                            │     │
│   │  │ [Playing ad...]   │                                            │     │
│   │  └───────────────────┘                                            │     │
│   │                                                  ┌──────────────┐ │     │
│   │  More content here that's hard to focus on       │ 💬 CHAT NOW! │ │     │
│   │  because of the floating chat widget...          └──────────────┘ │     │
│   │                                                                   │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│   ╔═══════════════════════════════════════════════════════════════════╗     │
│   ║  🍪 We use cookies. Accept | Decline | Manage                     ║     │
│   ╚═══════════════════════════════════════════════════════════════════╝     │
│                                                                             │
│   Reader: "I can't even see the article."                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With CLEAN Filter                                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  ARTICLE TITLE                                                    │     │
│   │  ─────────────────────────────────────────────────────────────    │     │
│   │                                                                   │     │
│   │  First paragraph of content that you're trying to read. Now       │     │
│   │  you can actually see it clearly.                                 │     │
│   │                                                                   │     │
│   │  More content here, flowing naturally without any interruptions.  │     │
│   │  Just the article, as it should be.                               │     │
│   │                                                                   │     │
│   │  Continue reading without distraction...                          │     │
│   │                                                                   │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │  7 nuisances removed · CLEAN mode                                 │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
│   Reader: "Finally. Just the content."                                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Read without interruption."                                           ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Coalition for Better Ads research: Popups are the #1 most       │     ║
║   │   annoying ad format, driving ad blocker adoption                 │     ║
║   │                                                                   │     ║
║   │   CLEAN: Removes the friction, preserves the content              │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

## Why This Requires Development

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   CURRENT MVP                         CLEAN USE CASE                        │
│   ────────────────────────────        ────────────────────────────          │
│                                                                             │
│   LLM classifies content blocks       Need deterministic rules for:         │
│   into 3 groups                       • CSS selector patterns               │
│                                       • Element type detection              │
│   Works well for:                     • z-index / overlay detection         │
│   • Article classification            • Script neutralization               │
│   • Content categorization                                                  │
│                                       LLM is overkill + unreliable          │
│   Doesn't help with:                  for structural removal                │
│   • Popup detection                                                         │
│   • Overlay removal                                                         │
│   • Script neutralization                                                   │
│                                                                             │
│   ───────────────────────────────────────────────────────────────────────   │
│                                                                             │
│   DEVELOPMENT NEEDED:                                                       │
│   • Rules engine for common patterns                                        │
│   • CSS selector matching                                                   │
│   • Element removal pipeline                                                │
│   • Optional: LLM for ambiguous cases                                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   TIER 3                                                                    ┃
┃                                                                             ┃
┃   UC-11  ·  PRECISION  ·  Mechanical Rules                                  ┃
┃                                                                             ┃
┃   "Predictable. Every time."                                                ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    🎯  "I know EXACTLY what I want removed. Don't guess — just do it."      │
│                                                                             │
│    🎯  "Every time I visit, remove the right sidebar. No exceptions."       │
│                                                                             │
│    🎯  "I want to define rules, not hope an AI gets it right."              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Power users of specific sites know exactly what to hide. They want mechanical consistency, not AI interpretation.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  ⚙️     │   "As a POWER USER of a specific website..."                  │
│   │ Power   │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   define mechanical rules for what to hide and keep     │   │
│   │               (e.g., "always remove .sidebar", "always keep code    │   │
│   │               blocks", "collapse .promo-banner")                    │   │
│   │                                                                     │   │
│   │   WITHOUT     relying on AI interpretation that might vary          │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     every visit returns the exact same "signal-only"      │   │
│   │               view I defined                                        │   │
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
│  ⚙️ Power Users  │  │  🏢 Teams with   │  │  🔧 Organizations│
│                  │  │     Shared Sites │  │     Standardizing│
│  Know exactly    │  │                  │  │     Browsing     │
│  what they want  │  │  Shared configs  │  │                  │
│                  │  │  across team     │  │  Approved site   │
│                  │  │                  │  │  configurations  │
│                  │  │                  │  │                  │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## Example Rule Sets

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        EXAMPLE: AWS BLOG RULES                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ALWAYS REMOVE:                                                            │
│   ────────────────────────────────────────────────────────────────────      │
│   • .aws-nav-promo-banner                                                   │
│   • #aws-cookie-consent                                                     │
│   • .feedback-widget                                                        │
│   • [data-component="recommended-posts"]                                    │
│                                                                             │
│   ALWAYS KEEP:                                                              │
│   ────────────────────────────────────────────────────────────────────      │
│   • article                                                                 │
│   • pre, code                                                               │
│   • .blog-post-content                                                      │
│   • nav.primary-navigation                                                  │
│                                                                             │
│   COLLAPSE (show toggle):                                                   │
│   ────────────────────────────────────────────────────────────────────      │
│   • .author-bio                                                             │
│   • .related-posts                                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                        EXAMPLE: TECH NEWS RULES                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ALWAYS REMOVE:                                                            │
│   ────────────────────────────────────────────────────────────────────      │
│   • .ad-container                                                           │
│   • .sponsored-content                                                      │
│   • .newsletter-signup                                                      │
│   • #comments-section                                                       │
│                                                                             │
│   ALWAYS KEEP:                                                              │
│   ────────────────────────────────────────────────────────────────────      │
│   • .article-body                                                           │
│   • .code-block                                                             │
│   • figure, img                                                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Why PRECISION Builds Trust

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   LLM-BASED FILTERING              MECHANICAL RULES                         │
│   ─────────────────────────        ─────────────────────────                │
│                                                                             │
│   ✓ Flexible                       ✓ Predictable                            │
│   ✓ Handles ambiguity              ✓ 100% consistent                        │
│   ✗ May vary between runs          ✓ User defines exactly                   │
│   ✗ Can miscategorize              ✓ No surprises                           │
│                                    ✓ Higher trust                           │
│                                    ✓ Lower cost (no LLM)                    │
│                                    ✓ Faster (no API call)                   │
│                                                                             │
│   ───────────────────────────────────────────────────────────────────────   │
│                                                                             │
│   BEST OF BOTH:                                                             │
│   Rules for known patterns + LLM for fuzzy zones                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "You know what you want. Tell us once. We do it forever."              ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Define rules → Save profile → Every visit is identical          │     ║
║   │                                                                   │     ║
║   │   No AI guessing. No variation. Pure mechanical consistency.      │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

## Why This Requires Development

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   DEVELOPMENT NEEDED:                                                       │
│   ────────────────────────────────────────────────────────────────────      │
│                                                                             │
│   1. CONFIGURATION UI                                                       │
│      • Interface for defining CSS selectors                                 │
│      • Rule builder (remove / keep / collapse)                              │
│      • Test rules against live page                                         │
│                                                                             │
│   2. RULE ENGINE                                                            │
│      • CSS selector matching                                                │
│      • Rule priority handling                                               │
│      • Conflict resolution                                                  │
│                                                                             │
│   3. PROFILE MANAGEMENT                                                     │
│      • Save/load rule profiles per site                                     │
│      • Share profiles across team                                           │
│      • Version control for rules                                            │
│                                                                             │
│   4. HYBRID MODE (optional)                                                 │
│      • Rules first, then LLM for undefined areas                            │
│      • Override LLM decisions with rules                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---
---

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                                                                             ┃
┃   TIER 4                                                                    ┃
┃                                                                             ┃
┃   UC-12  ·  READER  ·  Flat UI Layout                                       ┃
┃                                                                             ┃
┃   "The content, optimized for reading."                                     ┃
┃                                                                             ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

## The Problem

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│    📖  "This site has great content, but the layout is cluttered."          │
│                                                                             │
│    📖  "Three columns, sidebar, footer links... I just want to READ."       │
│                                                                             │
│    📖  "I wish every site had a 'reader mode' like Safari."                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

Some sites have excellent content but cluttered layouts that make reading, copying, and processing difficult.

---

## The User Story

```
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                             │
│   ┌─────────┐                                                               │
│   │  📖     │   "As a USER of visually cluttered websites..."               │
│   │ Reader  │                                                               │
│   └─────────┘                                                               │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   I WANT TO   see a simplified, reader-optimized layout             │   │
│   │               (centered content, no sidebars, clean typography)     │   │
│   │                                                                     │   │
│   │   KEEPING     the core content and essential navigation             │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│        │                                                                    │
│        ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │   SO THAT     it's easier to read, copy, and process the content    │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
╰─────────────────────────────────────────────────────────────────────────────╯
```

---

## The Experience: Before & After

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  BEFORE: Cluttered Layout                                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  LOGO    NAV NAV NAV NAV    SEARCH    LOGIN                         │   │
│   ├─────────┬─────────────────────────────────────────────────┬─────────┤   │
│   │         │                                                 │         │   │
│   │  LEFT   │  ARTICLE CONTENT                                │  RIGHT  │   │
│   │  SIDE   │  ─────────────────────────                      │  SIDE   │   │
│   │  BAR    │                                                 │  BAR    │   │
│   │         │  The actual content you want to read is here    │         │   │
│   │  • Nav  │  but it's competing for attention with two      │  • Ads  │   │
│   │  • Nav  │  sidebars, multiple navigation areas, and       │  • Ads  │   │
│   │  • Nav  │  various widgets...                             │  • Rec  │   │
│   │         │                                                 │  • Rec  │   │
│   │         │                                                 │         │   │
│   ├─────────┴─────────────────────────────────────────────────┴─────────┤   │
│   │  FOOTER FOOTER FOOTER FOOTER FOOTER FOOTER FOOTER FOOTER FOOTER     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                                    │
                                    ▼

┌─────────────────────────────────────────────────────────────────────────────┐
│  AFTER: With READER Mode                                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                          LOGO    NAV                                │   │
│   │                                                                     │   │
│   │                                                                     │   │
│   │                    ARTICLE TITLE                                    │   │
│   │                    ─────────────                                    │   │
│   │                                                                     │   │
│   │          The actual content you want to read, now in a              │   │
│   │          comfortable single-column layout. No competing             │   │
│   │          sidebars. No distractions. Just the content.               │   │
│   │                                                                     │   │
│   │          Optimal line length for reading. Clean typography.         │   │
│   │          Easy to select and copy.                                   │   │
│   │                                                                     │   │
│   │                                                                     │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Value Proposition

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                                                                             ║
║   💰 "Every site, reader-optimized."                                        ║
║                                                                             ║
║   ┌───────────────────────────────────────────────────────────────────┐     ║
║   │                                                                   │     ║
║   │   Browser reader modes work on ~30% of sites                      │     ║
║   │   READER works on any site, preserving more structure             │     ║
║   │                                                                   │     ║
║   │   Plus: Works server-side, so cached and fast                     │     ║
║   │                                                                   │     ║
║   └───────────────────────────────────────────────────────────────────┘     ║
║                                                                             ║
╚═════════════════════════════════════════════════════════════════════════════╝
```

---

## Why This is Tier 4 (Most Complex)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   TIER 1-3: FILTER WHAT CONTENT SHOWS                                       │
│   ────────────────────────────────────────────────────────────────────      │
│   • Keep original page structure                                            │
│   • Remove or hide elements                                                 │
│   • Preserve site's visual design                                           │
│                                                                             │
│   TIER 4: CHANGE THE LAYOUT ITSELF                                          │
│   ────────────────────────────────────────────────────────────────────      │
│   • Restructure the HTML                                                    │
│   • New layout template                                                     │
│   • Typography and spacing changes                                          │
│   • Must preserve semantic meaning                                          │
│                                                                             │
│   ───────────────────────────────────────────────────────────────────────   │
│                                                                             │
│   DEVELOPMENT NEEDED:                                                       │
│                                                                             │
│   1. CONTENT EXTRACTION                                                     │
│      • Identify main content vs. chrome                                     │
│      • Preserve headings, lists, code blocks, images                        │
│      • Handle edge cases (tables, embedded media)                           │
│                                                                             │
│   2. TEMPLATE SYSTEM                                                        │
│      • Reader-optimized layout templates                                    │
│      • Typography presets                                                   │
│      • Responsive design                                                    │
│                                                                             │
│   3. STYLE GENERATION                                                       │
│      • Option to preserve site colors/fonts                                 │
│      • Option for standardized "reader" style                               │
│                                                                             │
│   4. NAVIGATION PRESERVATION                                                │
│      • Keep essential nav working                                           │
│      • Toggle back to original layout                                       │
│                                                                             │
│   TIMELINE: 2-3 months                                                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

# Part 2: Technical Implementation Roadmap

---

## Development Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   TIER 3: UC-10 CLEAN (Nuisance Removal)                                    │
│   ─────────────────────────────────────────────────────────────────         │
│   Timeline: 2-4 weeks                                                       │
│                                                                             │
│   Week 1-2:                                                                 │
│   • Define common nuisance patterns (selectors, element types)              │
│   • Build deterministic removal pipeline                                    │
│   • Test on 10 popular content sites                                        │
│                                                                             │
│   Week 3-4:                                                                 │
│   • Handle edge cases (sites that break without certain scripts)            │
│   • Optional: Add LLM fallback for ambiguous elements                       │
│   • Integration with existing 6-mode system                                 │
│                                                                             │
│   Deliverable: CLEAN mode that removes popups, overlays, sticky banners     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   TIER 3: UC-11 PRECISION (Mechanical Rules)                                │
│   ─────────────────────────────────────────────────────────────────         │
│   Timeline: 3-6 weeks                                                       │
│                                                                             │
│   Week 1-2:                                                                 │
│   • Design rule schema (selectors, actions, priorities)                     │
│   • Build rule engine                                                       │
│   • Basic CLI for rule definition                                           │
│                                                                             │
│   Week 3-4:                                                                 │
│   • Profile management (save/load per site)                                 │
│   • Rule testing interface (preview before apply)                           │
│   • Integration with existing pipeline                                      │
│                                                                             │
│   Week 5-6 (optional):                                                      │
│   • Hybrid mode (rules + LLM)                                               │
│   • Simple web UI for rule configuration                                    │
│   • Team sharing of profiles                                                │
│                                                                             │
│   Deliverable: User-defined mechanical rules per site                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   TIER 4: UC-12 READER (Flat UI)                                            │
│   ─────────────────────────────────────────────────────────────────         │
│   Timeline: 2-3 months                                                      │
│                                                                             │
│   Month 1:                                                                  │
│   • Content extraction R&D (what heuristics work best)                      │
│   • Basic template system                                                   │
│   • Prototype on 5 diverse sites                                            │
│                                                                             │
│   Month 2:                                                                  │
│   • Handle complex content (tables, code, media)                            │
│   • Style system (site colors vs. reader theme)                             │
│   • Navigation preservation                                                 │
│                                                                             │
│   Month 3:                                                                  │
│   • Edge cases and polish                                                   │
│   • Performance optimization                                                │
│   • Integration with proxy system                                           │
│                                                                             │
│   Deliverable: Reader-optimized layouts for arbitrary sites                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Priority Recommendation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   RECOMMENDED BUILD ORDER:                                                  │
│                                                                             │
│   1. UC-10 CLEAN (Nuisance Removal)                   ⏱️ 2-4 weeks          │
│      ────────────────────────────────────────────────────────────────       │
│      Why first:                                                             │
│      • Universal appeal (everyone hates popups)                             │
│      • Demonstrates proxy power beyond content classification               │
│      • Foundation for PRECISION rules engine                                │
│                                                                             │
│   2. UC-11 PRECISION (Mechanical Rules)               ⏱️ 3-6 weeks          │
│      ────────────────────────────────────────────────────────────────       │
│      Why second:                                                            │
│      • Builds on CLEAN infrastructure                                       │
│      • Power user feature (higher trust, lower cost)                        │
│      • Enterprise appeal (IT can define approved configs)                   │
│                                                                             │
│   3. UC-12 READER (Flat UI)                           ⏱️ 2-3 months         │
│      ────────────────────────────────────────────────────────────────       │
│      Why last:                                                              │
│      • Most complex (layout transformation vs. filtering)                   │
│      • Validate Tier 3 adoption first                                       │
│      • May inform design based on user feedback                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Summary Table

| ID | Codename | Use Case | Dev Effort | Timeline | Dependencies |
|----|----------|----------|------------|----------|--------------|
| UC-10 | CLEAN | Nuisance Removal | Medium | 2-4 weeks | None |
| UC-11 | PRECISION | Mechanical Rules | Medium-High | 3-6 weeks | Builds on CLEAN |
| UC-12 | READER | Flat UI Layout | High | 2-3 months | Independent |

---

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                                                                               ║
║   END OF TIER 3 & 4 USE CASES DOCUMENT                                        ║
║                                                                               ║
║   3 future use cases · Development required · Roadmap ready                   ║
║                                                                               ║
║   "Build the foundation. Expand the possibilities."                           ║
║                                                                               ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```
