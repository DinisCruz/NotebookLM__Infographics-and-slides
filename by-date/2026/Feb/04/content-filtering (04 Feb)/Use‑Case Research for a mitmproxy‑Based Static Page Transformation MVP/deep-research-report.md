# Use‑Case Research for a mitmproxy‑Based Static Page Transformation MVP

## Scope and operating assumptions of the MVP

The MVP you described is best characterised as a **developer interception proxy that serves static HTML artefacts**—either the original page (unmodified) or a **pre‑generated modified version**—so the runtime browsing experience stays fast and predictable because the “heavy work” happens out of band. This “static serving” model is a recognised pattern in web delivery: for example, entity["company","Amazon Web Services","cloud provider"] documents hosting static sites using object storage where pages are served as static content (sometimes with client-side scripts, but still fundamentally static assets). citeturn5search0

A key enabling mechanism of the MVP is **TLS interception**: mitmproxy can decrypt encrypted traffic “on the fly” if the client device trusts a locally installed mitmproxy-generated certificate authority, which typically requires the user to configure proxy settings and install a CA certificate (for example via the `mitm.it` helper). citeturn0search0 This is important for usability research because it moves the product’s friction point to onboarding (certificate installation), not to everyday use. citeturn0search0

The use cases below are therefore intentionally framed around **content‑driven, mostly stable web pages** (for example documentation, blog posts, announcement listings) that can be transformed daily or on another schedule. Your own correction—excluding highly dynamic feeds like LinkedIn or YouTube—fits the MVP’s static‑artefact constraint (dynamic feeds are often personalised, session-bound, and change frequently, reducing the value of out‑of‑band static rewrites). citeturn10search0turn12search1

## Why these scenarios are worth testing

Your scenarios cluster around a single, well-studied human problem: **limited attention under high information load**. A 2024 scoping review in the *International Journal of Information Management Data Insights* summarises that information overload affects decision-making, productivity, and wellbeing, with causes spanning information characteristics, tasks, organisational context, and IT factors; it also lists mitigation strategies that include filtering, prioritisation, and tooling. citeturn3search2 A separate systematic review (published 2024 in *Management Review Quarterly*) frames overload in terms of “input, process, output” and highlights decision quality impacts and countermeasures that reduce cue volume or improve cue processing. citeturn2search1

Your MVP’s “filter marketing, highlight signal” approach aligns with long-standing web usability findings that people **scan rather than read**, especially when pages feel promotional or cluttered. Jakob Nielsen’s classic “How Users Read on the Web” describes that most users scan pages and recommends scannable structure (headings, highlighted keywords, one idea per paragraph, inverted pyramid). citeturn10search0 In the related empirical paper by John Morkes and Jakob Nielsen, concise, scannable, and objective writing substantially improved measured usability (and the “objective vs promotional” contrast is directly relevant to your “facts not marketing” criterion). citeturn10search48

This connects to two adjacent research lines that matter for your scenarios:

- **Banner blindness**: users learn to ignore elements that look like ads or “promo banners,” which can block both actual advertising and “internal marketing-like” site elements. The foundational banner blindness paper reports users often missed prominent banner-like links and instead chose less salient navigation options. citeturn4search42 Don Norman’s commentary links this behaviour to cognitive schemas and selective attention, reinforcing why “promo-like” blocks are prime candidates for removal or de-emphasis. citeturn4search3  
- **Intrusive interruptions (pop-ups, overlays)**: industry standards built from consumer research identify pop-up ads and other intrusive formats as especially unacceptable to users (and as drivers of ad-blocker adoption). entity["organization","Coalition for Better Ads","online ads standards group"] explicitly lists pop-up ads among the least acceptable desktop and mobile web experiences. citeturn1search0 Academic work on interruption-based ads finds timing, intent, and context shape outcomes—useful if your MVP ever shifts from “remove all” to “remove by rule.” citeturn6search6

image_group{"layout":"carousel","aspect_ratio":"16:9","query":["Banner blindness eye tracking heatmap F pattern","Website pop-up overlay example intrusive","mitmproxy mitm.it certificate installation","Cloudflare HTMLRewriter transform HTML response"],"num_per_query":1}

## Scenario set mapped to your MVP capabilities

Below, each scenario is written as a user-centred journey and includes a lightweight “flow diagram” (textual sequence) showing how it would run in a static-artefact architecture.

### Focus lens on a single page

**User story**  
As a user, I want to visit a content page and see only the parts that match my relevance criteria (for example factual and useful blocks), so that I do not waste time scanning promotional or low-signal material.

**Usability rationale (why users should care)**  
The strongest evidence fit here is the web scanning literature: users commonly scan pages for relevant cues instead of reading linearly, and usability increases materially when content is structured to be scannable and objective rather than promotional. citeturn10search0turn10search48 Banner blindness research strengthens the case that “promo-like” blocks are often skipped; removing them reduces visual noise and helps goal-driven navigation. citeturn4search42turn4search3

**How it works in your MVP (flow)**  
Out-of-band build: Schedule trigger → fetch page HTML → extract “content blocks” (titles, summaries, sections) → LLM classifies into A/B/C (keep/keep/filter-candidates) → rewrite HTML (hide/de-emphasise Group C; highlight Group A/B) → store modified static HTML. citeturn12search1turn0search4  
Browse-time: User opens configured browser → request intercepted by proxy → proxy serves stored modified HTML (or original HTML if no artefact exists / bypass is enabled). citeturn0search0turn5search0

**Success criteria to test with users**  
The user reports they can identify “worth opening” items faster, with fewer scrolls and less scanning fatigue, consistent with information overload mitigation strategies (filtering / prioritisation). citeturn3search2

### Focus lens across a section of a website

**User story**  
As a user, I want the same relevance filtering applied consistently across a defined subset of a website (for example “/announcements/” and “/blog/engineering/”), so that my reading experience remains coherent when I navigate between related pages.

**Usability rationale**  
Consistency matters because users rely on learned scanning patterns and conventions; when the filtering “lens” behaves predictably, users spend less cognitive effort re-learning what is on each page and more effort consuming the content that matters. This fits both the information overload framing (reducing processing burden) and classic web usability guidance (consistent structure supports scanning). citeturn3search2turn10search0

**How it works in your MVP (flow)**  
Configuration: Define an allowlist of URL paths / templates to transform → define policy prompt(s) and block extraction rules for each template.  
Out-of-band build: For each allowed path: schedule trigger → fetch HTML → extract blocks → classify → rewrite → store static artefact.  
Browse-time: User navigates within allowed section → proxy serves the corresponding stored modified artefact per URL; for out-of-scope paths, proxy passes through the original. citeturn0search0turn5search0

**Success criteria**  
Users can navigate multiple pages without “cognitive context switching,” and they can predict what will be filtered (and why). Filtering must remain transparent, otherwise it risks feeling like hidden editorial control. This is a known concern in personalisation debates (often discussed via “filter bubble” framing) and is mitigated by disclosure and easy toggles to view the original. citeturn3search2turn4search42

### Role- and stack-aligned filtering for AWS re:Invent announcements

**User story**  
As a development manager, I want my team to browse AWS re:Invent announcement pages (or recap posts) and only see announcements relevant to our organisation’s current technology stack, so that engineers are not distracted by unrelated services and can focus on actionable updates.

**Why this is a strong “MVP-fit” scenario**  
re:Invent content is high volume and broad. Even a single official “top announcements” recap covers many categories and services, illustrating the breadth a reader must triage. citeturn2search0 This makes it a canonical “information overload” environment where a relevance lens is plausibly valuable, especially for teams with well-defined stacks. citeturn3search2

**How it works in your MVP (flow)**  
Policy setup: Define “stack profile” (a controlled list of in-scope services, languages, platforms, compliance themes).  
Out-of-band build: Schedule trigger → fetch relevant re:Invent recap/announcement pages → extract announcement blocks → LLM classifies blocks as “in-stack / adjacent / out-of-scope” (or your A/B/C analogue) → rewrite page to show in-scope first, adjacent second, out-of-scope collapsed. citeturn2search0turn0search4  
Browse-time: Team uses configured browser when visiting those pages → proxy serves the pre-built filtered view. citeturn0search0turn5search0

**Success criteria**  
A team member should be able to answer: “What changed that affects us?” faster, and should be able to show a manager “the subset that matters” without manual summarisation. This is directly aligned to overload countermeasures (reducing cues, improving prioritisation). citeturn3search2

### Removal of ads, pop-ups, and aggressive calls-to-action on content-driven sites

**User story**  
As a user, I want to read content pages without pop-ups, overlays, and distracting calls-to-action, so that I can consume the content without interruptions.

**Research grounding**  
Multiple independent sources converge on pop-ups as a high-friction experience. The Better Ads Standards explicitly flag pop-up ads as a below-threshold experience for both desktop and mobile web, based on large-scale consumer preference research. citeturn1search0turn1search9 Academic work on interruption-based advertising shows that interruptions can influence outcomes depending on timing and perceived intent—supporting the simpler product thesis that avoiding interruption altogether protects the reading task. citeturn6search6 Even research exploring “motivating” pop-ups treats pop-ups as UX interventions whose design materially affects user experience, reinforcing that the modality is powerful and potentially disruptive. citeturn6search0

**How it works in your MVP (flow)**  
Out-of-band build: Schedule trigger → fetch HTML → identify nuisance patterns (modal divs, overlay scripts, newsletter banners, sticky CTA blocks) via deterministic rules and/or LLM detection → remove/neutralise in rewritten HTML → store clean static artefact. citeturn1search0turn1search7  
Browse-time: User visits the page through proxy → receives the “clean” static version. citeturn0search0turn5search0

**Success criteria**  
Users complete reading tasks with fewer interruptions and fewer premature exits. In product testing, you can measure reduced “rage clicks” (repeated close attempts) and fewer abandonments at the start of pages where pop-ups normally appear, consistent with why ad standards bodies discourage these experiences. citeturn1search0turn6search6

### Site-specific “mechanical” filtering with a clear formula

**User story**  
As a user of a specific website I visit often, I want a mechanically consistent rule set for what to hide and what to keep, so that every visit returns the same “signal-only” view (for example: always remove right-rail widgets, always collapse vendor promos, always keep tables and code blocks).

**Why this is distinct from the “focus lens” story**  
This scenario is not primarily about semantic classification; it is about **predictable, deterministic content surgery**. That predictability is valuable because it reduces relearning and supports strong user trust (“it always removes *that* block”). Trust matters because with mediated content, users can otherwise feel the system is editorialising invisibly. This connects back to the information overload literature’s emphasis on strategy/tooling and the banner blindness framing of learned avoidance: users want a stable environment that matches their goal-driven scanning. citeturn3search2turn4search42

**How it works (flow)**  
Configuration: Define selectors/regions to always keep/remove + a small number of semantic rules for ambiguous areas.  
Out-of-band build: Schedule trigger → fetch HTML → apply deterministic transforms first → apply LLM only for fuzzy zones (optional) → store rewritten artefact. citeturn1search7turn12search1  
Browse-time: Serve static “mechanically cleaned” version via proxy. citeturn0search0turn5search0

**Success criteria**  
Users can describe the rules in plain language and predict the output. In usability sessions, this often produces higher trust than purely model-driven filtering because the user can anticipate what will be removed before they even load the page. citeturn4search3turn3search2

### Alternate “flat” UI for easier reading and re-use of content

**User story**  
As a user of a content-heavy site, I want a simplified layout that keeps only top-level navigation and the primary content in a more readable structure (for example centred, wider text column, fewer sidebars), so that it is easier to read, copy/paste, and process.

**Research grounding**  
This scenario is strongly supported by web reading research: Nielsen’s guidance that users scan rather than read implies that layout and prioritisation should reduce extraneous clutter and foreground key information. citeturn10search0 The Morkes & Nielsen study shows large usability gains when content is made concise and scannable and when promotional language is replaced with objective language—these are effectively “content flattening” principles. citeturn10search48 Government content design guidance (for example GOV.UK content principles background) explicitly references this body of research and reinforces scanning patterns and the F-shaped reading model. citeturn10search4turn10search47

**How it works in your MVP (flow)**  
Out-of-band build: Schedule trigger → fetch HTML → extract main content + essential nav → rebuild a “flat” page template in the site’s visual style (or a close approximation) → optionally rewrite headings/paragraphs for concision and objectivity → store static artefact. citeturn10search48turn1search7  
Browse-time: User navigates to the original URL via proxy → proxy serves the alternate-layout static version (with a clear toggle to view original). citeturn0search0turn5search0

**Success criteria**  
Users report improved readability and easier extraction of key content. Measurable proxies include longer uninterrupted scroll depth on the main content, fewer lateral eye movements to sidebars (qualitative in tests), and faster “copy a snippet / capture a definition” tasks. These map to scanning-oriented design assumptions in the Nielsen research lineage. citeturn10search0turn10search48

## Feasibility guardrails and research questions to validate market pull

### Governance, security, and user trust in a TLS-intercepting product

Because your MVP relies on TLS interception and a trusted proxy CA, the usability journey must include security and governance considerations. mitmproxy’s own documentation makes clear that interception depends on the client trusting the proxy’s CA. citeturn0search0 In organisational environments, “break-and-inspect” visibility is a known pattern but comes with security trade-offs and must be managed deliberately; entity["organization","National Institute of Standards and Technology","us standards agency"] has published guidance (SP 1800-37) describing approaches for gaining visibility into TLS 1.3 traffic within controlled enterprise environments while acknowledging the complexity introduced by TLS 1.3’s security properties. citeturn7search3

This connects directly to your user-research agenda: users may love the “focus lens,” but balk at the trust model unless it is packaged as an enterprise-managed tool (often compared to a secure web gateway). entity["organization","Gartner","research advisory firm"] defines secure web gateways as tools that filter internet traffic and enforce corporate policy (URL filtering, malware detection, compliance). citeturn7search1 Your MVP is functionally adjacent (mediates traffic) but differentiated (optimises content experience rather than primarily security blocking), so customer interviews should explicitly test which mental model resonates. citeturn7search1turn3search2

### Legal and terms-of-use constraints on rewriting third‑party pages

A practical market constraint is whether the target site permits downloading, caching, or modification. entity["company","Amazon Web Services","cloud provider"] “AWS Site Terms” (last updated June 4, 2025) grant a limited licence for personal use and explicitly prohibit downloading other than “page caching” and prohibit modification or derivative use without consent, as well as prohibiting framing techniques. citeturn8search0 This does not automatically prevent internal, personal experimentation, but it is load-bearing for any commercial offering: research with prospective customers should include their legal/compliance posture regarding modified third-party content. citeturn8search0

### Reliability of LLM-driven transforms in a static pipeline

Even though transforms are out-of-band, the pipeline still needs predictable outputs. If you rely on “JSON-only classification” from an LLM, you should treat schema adherence as a product requirement, not a hope. entity["company","OpenAI","ai company"] documents Structured Outputs as a mechanism to make model outputs reliably conform to developer-supplied JSON Schemas, specifically addressing the historical unreliability of “JSON-only prompting” alone. citeturn11search0turn11search1 This matters for your use cases because inconsistent grouping or invalid JSON becomes user-visible as missing or mis-grouped content the next day. citeturn11search0

### Research prompts to validate demand across your scenarios

These questions are designed to directly test “market pull” for each scenario family:

For the focus lens (single page / site section): do users prefer removal (hide Group C entirely) or de-emphasis (collapse, move to bottom, or label)? Banner blindness research implies users already ignore ad-like areas, but your product changes the page itself—users may want reassurance and reversibility. citeturn4search42turn4search3

For role/stack filtering (AWS announcements): do teams want a centrally managed “stack profile” (manager-defined), or per-user profiles? Information overload research suggests organisational factors matter, so control structures may influence adoption. citeturn3search2turn2search1

For nuisance removal: which formats most reliably trigger abandonment—pop-ups, sticky banners, autoplay video, or ad density? The Better Ads Standards provide a starting shortlist of “most annoying” experiences to prioritise. citeturn1search0turn1search9

For alternate UI (flat pages): what “processing task” matters most—reading, copying snippets, summarising, saving for later, or sharing with colleagues? Nielsen’s scanning research suggests the first two lines and left edge dominate attention; your redesign should explicitly optimise that behaviour. citeturn10search0turn10search47