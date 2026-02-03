# Semantic Data Charter deep research briefing

## Context, scope, and primary-source basis

This briefing analyses the **Semantic Data Charter (SDC)** as presented on the Semantic Data Charter website and its linked primary artefacts, with emphasis on **SDC4** (the current published generation of the Charter). The report is written for professional due diligence and architectural appraisal as of **29 January 2026 (Europe/Lisbon)**.

The most authoritative and directly relevant primary sources for this analysis are:

- The published **SDC4 Specification** hosted by Semantic Data Charter (technical framing, conformance rules, and architectural components). citeturn6view0  
- The **SemanticDataCharter GitHub organisation** and its repositories (implementation artefacts, licences, maturity signals, and governance documentation). citeturn2view0turn3view0turn29view0  
- Axius SDC’s official web properties describing SDC’s commercialisation and SDCStudio documentation (commercial model, platform architecture, pricing/terms surfaced via documentation). citeturn14view0turn14view1turn13view0turn18view0turn19view0  

Where the user requested specifics that are **not publicly disclosed or not technically retrievable**, this is stated explicitly (notably: detailed grant award amounts, and external customer case studies). citeturn17view0turn18view0turn16view0  

## Semantic Data Charter architecture and technical specification

The SDC website positions SDC as a **standards-based, “self-describing interoperability architecture”** in which schemas explicitly carry: (a) semantic mappings (RDF/OWL), (b) validation rules (XSD + SHACL/SPARQL), and (c) formats for graph extraction and multi-format exchange. citeturn1view0turn9view0  

### Design principles and architectural objectives

SDC4’s published technical narrative is anchored around three “pillars”: **enforce governance**, **embed meaning**, and **mandate quality**. citeturn6view0turn2view0turn29view1  

A recurring architectural principle is the **explicit separation of “structure (HOW)” from “semantics (WHAT)”**, described as a two-stage strategy: validate structure first, then enrich/interpret meaning. This is presented as a response to “conflated” models where structure and meaning are tightly interwoven, increasing migration and integration burdens. citeturn19view0turn14view1turn6view0  

A second major principle is **permanence/data longevity**: SDC4 proposes stable structural definitions plus immutable identifiers and namespace versioning to reduce forced migrations over decades. citeturn6view0turn19view0turn9view0  

### Core components in the SDC4 architecture

The SDC4 Specification defines the architecture as a set of interacting components:

- **Reference Model (RM)**: the reusable base “building blocks” from which all SDC-compliant models are derived. citeturn6view0turn29view1  
- **Data Models (DMs)**: domain-specific models created by *constraining* RM components. citeturn6view0turn29view1  
- **Model Components (MCs)**: component-level building blocks within a DM, derived from RM components. citeturn6view0turn29view1  
- **Semantic annotations**: schema-embedded metadata used to ground meaning and enable graph extraction (the website and spec point to XSD annotations and RDF/OWL). citeturn1view0turn6view0turn9view0  

The SDCStudio documentation mirrors this framing operationally, describing an ingestion → modelling → generation pipeline capable of emitting schemas and graph artefacts (XSD, JSON/JSON‑LD, RDF triples, SHACL, GQL statements, etc.). citeturn13view0turn14view1  

### Conformance model and “normative source of truth”

The SDC4 Specification defines conformance at two levels. The core technical rule is that **a conforming Data Model schema must be a valid `xsd:restriction` of the SDC Reference Model schema**, and data models **must not use `xsd:extension`** to add elements or attributes to SDC types. citeturn6view0  

The SDCRM repository strengthens this into a governance rule: **`sdc4.xsd` is declared the normative “source of truth”**; the markdown specification is described as descriptive, while examples and guides are downstream. It explicitly states that if documentation conflicts with `sdc4.xsd`, the schema is correct. citeturn29view0turn29view1  

This “schema-first” governance stance is consequential for adopters: it implies change control is fundamentally change control over the normative schema artefact, which the repository also states requires a more formal process (e.g., an RFC process for schema changes). citeturn29view0turn5view0  

### Technical specifications and standards stack

SDC4 is presented as a deliberate assembly of established standards. The SDC website highlights a **W3C semantic stack** (RDF, OWL, SPARQL, SHACL) and **XSD 1.1** for structural definitions and assertions. citeturn1view0turn9view0turn6view0  

The “Standards Compliance” documentation further asserts SDC4’s scope, explicitly listing: RDF 1.1, OWL 2, XSD 1.0/1.1, SHACL 1.0, SPARQL 1.1, ISO 21090 for exceptional values (“NULL flavours”), ISO 8601 for temporal formats, and language-tag standards (RFC 3066/4646/5646), among others. It also states SDC4 RM status as **“Proprietary Standard (Axius-SDC, Inc.)”** while simultaneously enumerating open standards implemented/enabled. citeturn9view0turn7view0  

The SDC4 Specification enumerates key RM constructs, including the **Xd\*** “extended data types” (e.g., XdAnyType, XdStringType, XdTemporalType, XdLinkType, XdFileType), “ClusterType” for hierarchical grouping, and “XdAdapterType” for adaptation patterns. citeturn6view0turn29view1  

A central data-quality pattern is a “quarantine-and-tag” approach where invalid or missing data is represented using explicit exceptional value semantics (framed as ISO 21090 NULL flavour types). This concept is reinforced in standards documentation and in validator/tooling narratives. citeturn9view0turn22search1  

### Architecture diagram for briefing use

The following diagram synthesises the published specification + repo governance stance into an implementer-oriented view (analytical reconstruction derived from cited sources).

```text
                 ┌──────────────────────────────────────────────┐
                 │          SDC4 Reference Model (RM)            │
                 │  Normative artefact: sdc4.xsd (“source truth”)│
                 │  Semantic layer: sdc4.owl (per SDCRM listing) │
                 └──────────────────────────────────────────────┘
                                   │
                          Constrain via xsd:restriction
                        (xsd:extension is prohibited)
                                   │
                 ┌──────────────────────────────────────────────┐
                 │      Domain Data Models (DMs) + Components     │
                 │  - MCs derived from RM types/patterns          │
                 │  - Embedded semantic annotations (RDF/OWL)     │
                 └──────────────────────────────────────────────┘
                                   │
          ┌────────────────────────┼─────────────────────────┐
          │                        │                         │
  Structural validation       Semantic generation       Quality semantics
      (XSD 1.1)             (RDF/OWL/SPARQL)          (ISO 21090 patterns)
          │                        │                         │
          └────────────────────────┴──────────────┬──────────┘
                                                   │
                                     ┌─────────────────────────┐
                                     │ SDCStudio toolchain      │
                                     │  Upload → Model → Generate│
                                     │  XSD/XML/JSON/JSON-LD/RDF │
                                     │  SHACL/GQL + app scaffolds│
                                     └─────────────────────────┘
```

Key supporting sources include: SDC4 specification (components + conformance), SDCRM governance statement (`sdc4.xsd` as source of truth), and SDCStudio docs (pipeline outputs and platform decomposition). citeturn6view0turn29view0turn14view1turn13view0  

## Licensing model, restrictions, and implications

### Overview of how SDC is licensed in practice

Across the SDC ecosystem, licensing is **permissive** but **not uniform**. The SemanticDataCharter GitHub organisation’s repositories show a split between **MIT** and **Apache License 2.0**, with an organisation-level statement claiming “Apache 2.0 (specification and open source tools)” that does not, by itself, override repository-level licences. citeturn2view0turn3view0turn29view0  

In addition, the organisation’s `.github` repository (community health files) states **CC0 1.0 Universal** for those files in its README, and indicates that spec licensing is Apache 2.0 “see individual repositories”. citeturn5view0  

Finally, the Semantic Data Charter documentation site contains two important legal signals: it states the site content is **licensed under Apache 2.0**, and it explicitly asserts **“Semantic Data Charter™” is a trademark** of Axius SDC, Inc. (a reminder that open-source copyright licences do not automatically grant trademark usage rights). citeturn7view0  

### Licence terms comparison table

The table below summarises the *core* legal characteristics of licences used across the SDC ecosystem, using official licence texts as primary references.

| Licence | Core permissions | Key obligations / restrictions | Notable implications for adopters and contributors |
|---|---|---|---|
| MIT License | Broad permission to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies. citeturn20search0 | Must include copyright and licence notice in copies/substantial portions; includes warranty disclaimer. citeturn20search0 | Common choice for maximal reuse; **no explicit patent grant language** in the OSI MIT text (in contrast to Apache 2.0’s explicit patent grant). citeturn20search0turn20search1 |
| Apache License 2.0 | Broad rights to reproduce, prepare derivative works, distribute, etc.; includes explicit patent licence grant from contributors. citeturn20search1 | Requires preservation of notices; conditions for redistribution; **explicit patent licence** with termination conditions in defined cases. citeturn20search1turn20search5 | Often preferred by enterprises due to explicit patent grant; requires careful notice management and adherence to terms (especially when redistributing). citeturn20search1turn20search5 |
| CC0 1.0 Universal | Mechanism to waive copyright and related rights to the fullest extent allowed by law (public domain-like dedication). citeturn20search2turn20search17 | CC0 legal code clarifies scope and limitations (e.g., not all jurisdictions treat waivers identically). citeturn20search2turn20search17 | Often used for templates/metadata/examples where frictionless reuse is desired; organisations still commonly pair CC0 with governance and attribution norms as a matter of practice. citeturn20search2turn5view0 |

### Practical restrictions and due-diligence considerations

The SDC artefacts present two “restriction” vectors that adopters should treat separately:

The first is **copyright/patent licensing**, which appears permissive at the repository level (MIT/Apache 2.0) for the open artefacts and tooling in the SemanticDataCharter org. citeturn3view0turn4view0turn29view0  

The second is **brand governance**, signalled by the explicit trademark statement for “Semantic Data Charter™”. Even where code/spec artefacts are permissively licensed, branding and naming rights are typically governed under trademark law and any published trademark policy (not prominently presented in the sources reviewed). citeturn7view0turn1view0  

A further interpretive issue is that the standards compliance page labels SDC4 RM as **“Proprietary Standard (Axius-SDC, Inc.)”**, which may indicate a governance/ownership posture (e.g., stewardship remains with Axius SDC) rather than a standards-body administered open standard, notwithstanding the open licensing of specific artefacts. This distinction matters for procurement frameworks that require a neutral standards body. citeturn9view0turn7view0turn2view0  

## Commercial model, monetisation, and partnerships

### Open artefacts versus proprietary platform

The Semantic Data Charter website and Axius SDC material adopt a clear posture: the **reference model** and validator libraries are framed as open source and “free forever”, whereas the principal monetised product is **SDCStudio**, a proprietary platform positioned as the operational environment to author, manage, and generate SDC4-compliant models and applications. citeturn1view0turn14view0turn2view0turn25search3  

The GitHub organisation README explicitly labels **SDCStudio** as a “commercial platform” and “proprietary.” citeturn2view0turn25search5  

### SDCStudio pricing and packaging signals

SDCStudio documentation frames it as a **cloud-based SaaS** (the “primary method”), with subscription tiers and a free trial model:

- Basic: **$29.99/month**  
- Team: **$129.99/month**  
- Pro: **$799/month**  
- Enterprise: **custom pricing**  
- Trial: **60-day free trial** on paid plans, with credit card required and cancellation before the end to avoid charges. citeturn14view0  

The same documentation describes a **local installation / developer setup** for contributors or custom deployments, with a Docker-based stack and explicit caveats that local installation is “for developers only” and not recommended for end users or production compared with the managed cloud platform. citeturn14view0  

### Product architecture as part of the commercial differentiator

SDCStudio’s system overview describes a three-part platform architecture:

- **Uploader app** for data ingestion and AI analysis (CSV and Markdown template support),  
- **DMGEN app** for core data modelling and component management,  
- **Generator app** for multi-format output generation and application scaffolding. citeturn14view1turn13view0  

The platform is positioned as “AI-powered,” with documentation indicating use of Google-hosted infrastructure and AI services (e.g., Cloud Run scaling and Google Vertex AI Gemini model references). citeturn14view0  

This matters commercially: if SDCStudio is an adoption accelerator, then the Charter functions as both a specification and a product-led ecosystem (schemas + tooling + generated deployables). citeturn13view0turn14view1  

### Partnerships and ecosystem relationships

Axius SDC publicly lists ecosystem positioning statements including:

- “Building with **Google for Startups Cloud Program**”  
- “**Graphwise OEM Partner**” citeturn19view0turn0search8  

However, the **commercial terms, scope, and financial value** of these relationships are **not disclosed** in the reviewed primary sources. citeturn19view0turn31view0  

The Google for Startups Cloud Program itself advertises benefits such as Google Cloud/Firebase costs covered up to **$200,000** (up to **$350,000** for AI startups) over the first two years, but this is programme-level information and does not confirm what (if anything) Axius SDC received. citeturn0search6turn0search4turn19view0  

## Funding sources, institutional backing, and stakeholders

### Declared ownership and investor posture

Axius SDC’s “Our Story” page includes an explicit statement: **“100% founder-owned. No outside investors. No board pressure.”** citeturn18view0  

This implies a commercialisation path that is not driven by disclosed venture capital financing (at least as claimed on their official site). citeturn18view0turn17view0  

### Research-era funding and institutional support

The most concrete public references to funding sources are found in Timothy W. Cook’s published CV page:

- A claim that the work is “fully bootstrapped with **$8M+ in R&D investment over 25+ years**.” (No breakdown or third-party verification is provided in the cited artefact.) citeturn17view0  
- “Grant Recipient – **MLHIM to S3Model Transition**” (2014–2018), associated with **Rio de Janeiro State University (UERJ), Brazil**. citeturn17view0  
- “Grant Recipient – **MLHIM Development**” (09/2014–10/2016), associated with **FAPERJ (Carlos Chagas Filho Foundation), Brazil**. citeturn17view0  

Additional institutional affiliations and academic foundations are cited on Axius SDC’s site (e.g., Karolinska Institutet and university affiliations), but these should be interpreted as credibility signals rather than direct financial support unless explicitly framed as funding. citeturn19view0turn17view1turn16view0  

### Funding sources table

| Source / mechanism | Nature of support | Period (publicly stated) | Stakeholders named | Amount disclosed publicly? | Notes |
|---|---|---|---|---|---|
| Bootstrapped R&D investment claim | Self-funded / internal R&D (claimed) | “25+ years” citeturn17view0 | Timothy W. Cook / Axius SDC | **$8M+ claimed**, no breakdown citeturn17view0 | Self-reported; no public audit trail provided in sources reviewed. |
| UERJ grant (MLHIM → S3Model transition) | Research grant support | 2014–2018 citeturn17view0 | UERJ (Brazil) | Not disclosed | Grant ID, award size, and conditions not provided in the cited CV. citeturn17view0 |
| FAPERJ grant (MLHIM development) | Research grant support | 09/2014–10/2016 citeturn17view0 | FAPERJ (Brazil) | Not disclosed | As above, no award magnitude or grant identifier disclosed in cited material. citeturn17view0 |
| Google for Startups Cloud Program | Programme support (potential in-kind credits/mentoring; programme-level) | Not dated on Axius SDC site citeturn19view0 | Google; Axius SDC | Not disclosed for Axius SDC | Programme advertises up to $200k–$350k in credits over two years, but Axius SDC’s received amount is not stated. citeturn0search6turn19view0 |
| Graphwise OEM partnership | Commercial partner relationship | Not dated on Axius SDC site citeturn19view0 | Graphwise; Axius SDC | Not disclosed | Partnership exists as a public claim; commercial scope/terms not public. citeturn19view0turn0search8 |

### Explicit gaps in public disclosure

The user requested “amounts, duration, and stakeholders.” Durations and stakeholders are partially stated for the UERJ and FAPERJ grants, but **award amounts and grant identifiers are not provided** in the cited primary sources. citeturn17view0  

Similarly, while Axius SDC claims a large cumulative R&D investment, the public artefacts do not provide a verifiable ledger of that spend. citeturn17view0turn18view0  

## Adoption, uptake signals, and community engagement

### Observable adoption indicators in primary artefacts

As of 29 January 2026, the strongest publicly inspectable “uptake” evidence for SDC is **tooling availability, documentation depth, and ecosystem scaffolding**, rather than independently documented third-party deployments.

The SemanticDataCharter GitHub organisation shows:

- **11 public repositories** in the organisation repository list. citeturn3view0  
- **3 followers** (organisation profile). citeturn2view0  
- A small but non-zero OSS engagement footprint, with one notable datapoint: the `sdcvalidator` repository shows **81 forks** (as per the repo list snapshot). citeturn3view0  

These indicators suggest early-stage community visibility; the fork count for `sdcvalidator` may reflect reuse or downstream experimentation. However, stars/forks are weak proxies for production adoption and should not be treated as customer validation. citeturn3view0turn29view1  

### “Adoption accelerators” inside SDCStudio

SDCStudio documentation describes **public component libraries** that are intended to speed adoption by providing standard-aligned reusable components:

- NIEM Foundation: **3,800+ components**  
- FHIR Clinical: **150+ resources**  
- NIH Common Data Elements: **500+ CDEs**  
- Default library: **50+ components** citeturn13view1  

This is concrete product work and directly supports the claim that the platform is engineered to be content-compliant with major standard ecosystems, but it does not—by itself—prove external organisations are using it in production. citeturn13view1turn9view0  

### Demonstrators and example packages

The `SDCStudio_Examples` repository is positioned as a set of example applications generated by SDCStudio, which serves as a quasi “proto case study” resource. citeturn3view0turn4view3  

Separately, Axius SDC’s public narrative explicitly states that case studies are **“coming soon”** and that they are documenting real-world implementations across domains. This is a candid statement that publicly available third-party case studies are not yet a mature part of the published evidence base. citeturn16view0turn25search3  

### Academic validation claims and limits

Axius SDC’s “Our Story” page claims a long research foundation with quantified indicators (e.g., “165+ citations,” “12+ peer-reviewed publications,” and “2,200+ total commits” over a historical arc). citeturn18view0turn2view0  

These claims are meaningful as signalling and may be verifiable externally, but this report constrains itself to the accessible primary sources in scope. Importantly, the same page indicates parts of the historical archive may be available only to “verified investors upon request,” which limits independent verification by the general public. citeturn18view0turn2view0  

### Adoption metrics snapshot table

| Metric category | Indicator | Observed value in primary sources | Interpretive note |
|---|---|---|---|
| OSS footprint | Repositories in SemanticDataCharter org | 11 repositories citeturn3view0 | Indicates a small but structured ecosystem (spec, validators, templates, examples). |
| OSS footprint | Organisation followers | 3 followers citeturn2view0 | Early-stage visibility signal. |
| OSS engagement | `sdcvalidator` forks | 81 forks citeturn3view0 | Potential downstream reuse; not equivalent to production adoption. |
| Product accelerators | Public component libraries | NIEM 3,800+; FHIR 150+; NIH CDE 500+; default 50+ citeturn13view1 | Strong evidence of engineering effort to align with established standards ecosystems. |
| Published roadmap maturity | Rust validator status | Explicit placeholder / not functional; scheduled Q2 2026 citeturn22search1 | Indicates active multi-language expansion, but not yet production-ready for Rust. |
| Case studies | Published third-party case studies | “Coming soon” stated | Reflects limited public third‑party deployment evidence at time of review. citeturn16view0turn25search3 |

### Community engagement mechanisms

The SemanticDataCharter organisation publishes community health files (code of conduct, contributing guidance, security policy) via its `.github` repository and provides contact routes. citeturn5view0turn2view0  

This offers basic governance scaffolding consistent with open-source community norms, though scale indicators (contributors, discussions activity, etc.) were not prominent in the sources surfaced here. citeturn5view0turn3view0  

## Annex: SemanticDataCharter GitHub repositories inventory

The following annex lists the repositories under the **SemanticDataCharter** GitHub organisation (11 repositories shown in the organisation’s repository list). Links were verified by opening each repository page in-scope for this research. citeturn3view0turn4view0turn4view1turn4view2turn4view3turn4view4turn4view5turn4view6turn4view7turn4view8turn4view9turn4view10  

Maturity/status classifications below are derived from explicit statements in repository descriptions/READMEs and/or SDC/Axius documentation (e.g., “planning Q2 2026”, “placeholder release”, “templates”). Where no explicit maturity statement exists, classification is conservative (e.g., “supporting artefact”). citeturn3view0turn22search1turn5view0turn14view0  

### Repository annex table

| Repository (name) | Primary purpose (as described in sources) | Licence (as surfaced by GitHub/repo) | Maturity / development status | Direct link |
|---|---|---|---|---|
| ProvGov | Provenance/governance templates used to create model components in SDCStudio. citeturn4view0 | Apache-2.0 citeturn3view0turn4view0 | Supporting artefact (templates; recently updated Jan 2026). citeturn3view0 | `https://github.com/SemanticDataCharter/ProvGov` |
| sdcvalidator | Python validator library for SDC4 XML Schema validation. citeturn3view0turn1view0 | MIT citeturn3view0 | Publicly described as “production-ready” on SDC site; active maintenance indicated by recent updates in repo list. citeturn1view0turn3view0 | `https://github.com/SemanticDataCharter/sdcvalidator` |
| TemplateExamples | Templates used to create content-compliant model components in SDCStudio. citeturn3view0turn4view2 | Apache-2.0 citeturn3view0 | Supporting artefact (templates; updated Jan 2026). citeturn3view0 | `https://github.com/SemanticDataCharter/TemplateExamples` |
| SDCStudio_Examples | Example applications generated by SDCStudio. citeturn3view0turn4view3 | Apache-2.0 citeturn3view0turn4view3 | Demonstrator / learning artefacts (examples; updated Dec 2025). citeturn3view0 | `https://github.com/SemanticDataCharter/SDCStudio_Examples` |
| SDCRM | Reference model implementations and specifications; includes governance statement that `sdc4.xsd` is the normative “source of truth”. citeturn3view0turn29view0 | MIT citeturn3view0turn29view0 | Normative core artefact (spec + schemas). citeturn29view0turn6view0 | `https://github.com/SemanticDataCharter/SDCRM` |
| Form2SDCTemplate | Markdown document that can be uploaded to an LLM to produce an SDCStudio template. citeturn3view0turn4view5 | Apache-2.0 citeturn3view0 | Adoption accelerator (template/prompting workflow artefact). citeturn19view0turn3view0 | `https://github.com/SemanticDataCharter/Form2SDCTemplate` |
| sdcvalidatorRust | Rust validator; explicitly described as planning for Q2 2026 and (in README excerpt) a placeholder release / not functional. citeturn3view0turn22search1 | MIT citeturn3view0turn22search1 | **Planned / placeholder** (explicit “NOT FUNCTIONAL”; scheduled Q2 2026). citeturn22search1turn3view0 | `https://github.com/SemanticDataCharter/sdcvalidatorRust` |
| sdcvalidatorJS | TypeScript validator; framed as an “npm package for a SDC validator” (npm access blocked during review) and described as “IN DEVELOPMENT” on SDC site. citeturn3view0turn1view0 | MIT citeturn3view0 | In development (explicit on SDC site; repo last updated Nov 2025 in repo list). citeturn1view0turn3view0 | `https://github.com/SemanticDataCharter/sdcvalidatorJS` |
| SDCObsidianTemplate | Obsidian/Templater template for creating SDCStudio Markdown templates. citeturn3view0turn4view8 | Apache-2.0 citeturn3view0turn4view8 | Adoption accelerator (authoring template). citeturn16view0turn3view0 | `https://github.com/SemanticDataCharter/SDCObsidianTemplate` |
| .github | Organisation-wide community health files (code of conduct, contributing, security); README states CC0 for community health files. citeturn4view9turn5view0 | **Licence ambiguity:** GitHub repo listing does not surface a standard licence badge; README asserts **CC0 1.0** for community health files. citeturn3view0turn5view0 | Governance/support repo (community defaults). citeturn5view0 | `https://github.com/SemanticDataCharter/.github` |
| sdc-xml2graph | Python tool to create knowledge graphs from SDC4 models and XML data. citeturn3view0turn4view10 | MIT citeturn3view0turn4view10 | Early/planning signal (org README references Q1-2026 timeline; repo list shows Nov 2025 update). citeturn2view0turn3view0 | `https://github.com/SemanticDataCharter/sdc-xml2graph` |

### Note on link verification and access limitations

All GitHub repository links above were directly opened during this research session (used as verification). citeturn4view0turn4view1turn4view2turn4view3turn4view4turn4view5turn4view6turn4view7turn4view8turn4view9turn4view10  

By contrast, attempts to access the npm package page linked from the SDC site returned a **403 Forbidden** in this environment; therefore, npm-hosted adoption metrics could not be independently confirmed here and were excluded from quantitative uptake claims. citeturn26view0