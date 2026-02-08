# Model Abstraction Layer for Safer Agentic Access to Real Systems

## Abstract and problem framing

Agentic systems (LLMs with tool access) are increasingly being connected to the places where “real work” happens—email, calendars, filesystems, SaaS applications, internal APIs, and developer tooling—because connected context and tools materially increase usefulness. citeturn5view2turn8view0 Yet the same integration makes security failures higher-impact: protocols and connectors can expose arbitrary data access and “code execution paths”, and tool calls can become the mechanism by which unintended actions occur. citeturn8view0turn0search1turn0search9

This white paper proposes a **Model Abstraction Layer (MAL)**: a deliberately *capability-attenuating* and *data-minimising* layer that sits between an LLM (or an LLM tool-connection protocol such as MCP) and fundamental resources (inboxes, calendars, storage, databases, line-of-business systems). The MAL is designed to look and feel like the underlying resource (a “transparent mode”), but it only exposes the minimum data and minimum actions required for a specific workflow—thereby reducing attack surface, limiting blast radius, and improving resilience in the face of prompt injection, model mistakes, and overly-broad permissions. citeturn0search1turn8view0turn7search4turn2search0turn0search2

The MAL is intended to be composable with the **Model Context Protocol (MCP)**—for example: *MCP client → MAL (as an MCP server, or as a proxy) → underlying systems*—and can itself be layered (MAL-of-MAL) to progressively refine exposure. citeturn8view0turn5view1turn0search22

## Why agentic access is qualitatively riskier than “normal” integrations

Modern tool-using LLMs introduce failure modes that legacy systems were not designed to withstand—especially systems built for humans, coarse-grained app integrations, or trusted internal automation.

First, **prompt injection is a distinct social-engineering class targeting agents**: an attacker embeds malicious instructions in content the agent processes (webpages, documents, emails, tickets), aiming to override the user’s intent and induce the agent to leak data or misuse tools. citeturn7search1turn0search13turn0search5 OpenAI explicitly characterises prompt injection as an “open challenge” for agent security and recommends limiting agent access to only the sensitive data or credentials required for the task (e.g., use logged-out modes when possible). citeturn7search1turn7search0 This is aligned with the OWASP emphasis on prompt injection (LLM01) and “Excessive Agency” (LLM06): damaging actions can be triggered by unexpected, ambiguous, or manipulated model outputs. citeturn0search1turn0search9turn0search13

Second, **tool ecosystems create compositional risk**: even if individual tools appear safe, chaining them can enable high-impact attacks. In early 2026, multiple vulnerabilities in the official Git MCP server (`mcp-server-git`) demonstrated how argument injection and path validation issues could be exploited—potentially via prompt injection—and chained with filesystem access to enable file tampering or code execution. citeturn1search7turn1search0turn1search26turn1search19turn1search3 This is not merely a theoretical concern; it is an empirical reminder that “connector surfaces” are security-critical and that least-privilege boundaries must exist outside the model. citeturn1search19turn0search9turn7search4

Third, **legacy authorisation is often too coarse for agent workflows**. Many systems are built around broad, user-level access (“read the inbox”, “access the drive”), because human users can (usually) be socially and procedurally constrained. Agentic workflows invert the risk: the system is exposed to a highly persistent, creatively problem-solving process that will keep acting until it believes the task is done, which increases the probability of unintended access and unintended side effects. This is consistent with OWASP’s framing of autonomous action risk and with MCP’s own explicit warnings about arbitrary data access and code execution paths. citeturn0search9turn8view0turn7search4

## The Model Abstraction Layer concept and core capabilities

The MAL is a *policy-defined façade* that sits “in front of” a resource. It can be inserted:

- **Directly in front of an asset** (e.g., email datastore, calendar API, filesystem, database), or  
- **Between MCP layers**: an MPC client talks to an MAL-backed MCP server, which then talks to real systems (possibly via downstream MCP or conventional APIs). citeturn8view0turn5view1turn0search22

### Core principle: minimise both data exposure and capability exposure

The MAL’s central design goal is to shrink (1) **the data plane** and (2) **the capability plane** to the smallest surface consistent with the user’s requested workflow.

- **Data protection (read plane)**: expose only the minimum data needed for the task, consistent with data minimisation principles (collect/share “adequate, relevant and limited to what is necessary”). citeturn2search0turn2search12  
- **Capability protection (write/action plane)**: expose only the minimum actions needed for the task. This aligns with least privilege (“restrict access privileges… to the minimum necessary to accomplish assigned tasks”). citeturn0search28turn0search2

This dual-plane framing also maps cleanly to the two most common sources of agent harm: accidental or induced **sensitive information disclosure** and accidental or induced **damaging actions** via over-permissive tools. citeturn0search1turn0search9

### The inbox example as a canonical “legacy system not fit for agentic exposure”

The transcript’s inbox example is a strong illustration of why MAL is needed: giving an agent access to an entire inbox is rarely what the user intends, and even “read-only” inbox access can be a stepping stone to broader compromise.

A compromised email account (or an agent misled into handling email content unsafely) can enable account takeovers via password resets: UK security guidance explicitly warns that if a hacker gets into your email, they can reset passwords for other accounts using “forgot password.” citeturn2search21 Separately, OpenAI’s own prompt injection examples for agents repeatedly highlight the risk that a malicious email or webpage can trick an agent into retrieving password reset codes or other sensitive data and exfiltrating it. citeturn7search0turn7search9

Yet many user requests are narrow—e.g., “check my latest emails and give me an overview.” The MAL’s job is to offer an “inbox-like” interface while enforcing a policy such as:
- Only emails from the last day/week/month (progressive trust).  
- Filter out password reset emails (or any messages matching sensitive patterns).  
- Remove or redact confidential fields before they reach the model. citeturn7search1turn2search0turn7search4turn0search1

### Two operating modes: transparent and normalised

The MAL should support two complementary modes (both explicitly called out in the transcript):

**Transparent mode** (“looks just like the real thing”): the calling agent or tool layer interacts with a resource-shaped interface that resembles the original system, but the MAL enforces strict policy boundaries behind the scenes. This supports compatibility with existing tool schemas and MCP Resources/Tools patterns. citeturn5view1turn8view0

**Normalised mode** (“clean up the data a little bit”): instead of passing through raw artifacts (e.g., full MIME email payloads), the MAL emits a standard structured representation (e.g., a clean JSON schema with subject/sender/time/body excerpt and safety annotations). This aligns with guidance to use structured outputs to constrain data flow and reduce free-form channels for injection and unintended propagation. citeturn7search4turn0search5

### Chaining and “abstraction layers of abstraction layers”

A practical benefit of MAL is that it supports composition: one MAL can sit behind another, each adding constraints or converting representations. This mirrors established “defence in depth” thinking (multiple controls to overcome, increasing attacker work factor and detection likelihood). citeturn6search6turn6search18 It also dovetails with Zero Trust goals of limiting lateral movement by enforcing per-request checks and minimising implicit trust. citeturn6search0

## Reference architecture with MCP integration

### Positioning MAL relative to MCP

MCP standardises how LLM applications connect to external context and tools, offering **Resources** (context/data), **Tools** (callable functions), and **Prompts** (templates/workflows), over a JSON-RPC based protocol. citeturn8view0turn5view1 MCP’s specification also explicitly calls out security principles: user consent and control, data privacy, tool safety, and sampling controls—while noting that the protocol itself cannot enforce these at the protocol layer, so implementers must build robust authorisation and consent mechanisms. citeturn8view0

The MAL can be viewed as a concrete mechanism to enforce these MCP principles, especially:

- **Data privacy**: the MAL prevents over-sharing by constraining what becomes an MCP Resource payload. citeturn8view0turn2search0  
- **Tool safety**: the MAL gates or attenuates tools so that “Tools represent arbitrary code execution” is operationally constrained to workflow-necessary operations. citeturn8view0turn0search9  
- **User consent and control**: the MAL can implement step-up consent (progressive trust) and human-in-the-loop approvals for high-impact operations. citeturn8view0turn6search0turn7search9

### Minimal components of an MAL implementation

A robust MAL typically decomposes into five components:

**Policy decision point and policy language**: policies should be externalised (“policy as code”) so they can be reviewed, tested, versioned, and audited. citeturn0search3turn0search11 This can leverage:
- **entity["organization","Open Policy Agent","policy engine, cncf"]** (OPA) as a general-purpose policy engine with a structured policy language (Rego). citeturn0search3turn0search11  
- **Cedar** as an authorisation policy language and decision engine designed for expressive, safe, analysable policies, with tooling to analyse policies across scenarios. citeturn3search1turn3search33turn3search25  
- ABAC concepts (subject/object/action/environment attributes) from NIST SP 800-162, to model fine-grained authorisation decisions. citeturn3search2turn3search10

**Adapters/connectors**: per-resource connectors that translate from real systems (email providers, calendar APIs, file stores) to MAL’s internal representation and enforcement hooks, and optionally to MCP Resources/Tools. citeturn5view1turn8view0

**Transformers/sanitizers**: canonicalisation, redaction, classification, normalisation, and “safe summarisation” transforms, to reduce sensitive information disclosure risk and reduce injection payload surface. citeturn0search1turn7search4

**Capability gate and step-up authorisation**: an explicit gate for write operations and other high-impact actions (send email, delete file, share document, modify calendar entry). This directly addresses Excessive Agency by preventing ambiguous model output from being interpreted as authority to act. citeturn0search9turn7search9turn8view0

**Telemetry and audit**: logs suitable for detection and incident response (what data was accessed, what was withheld, what actions were attempted/denied, what policies fired). This aligns with MCP’s mention of logging utilities and with Zero Trust’s emphasis on continuously evaluating and authorising each request. citeturn8view0turn6search0

### Capability attenuation, not just allow/deny

Where integrations require delegating authority (for example, letting an agent run a limited workflow autonomously), the MAL should favour *attenuated delegation* rather than broad tokens. This is a well-known strength of “caveated” capability credentials such as **macaroons**, which can confine when/where/by whom/for what purpose a service should authorise requests, and can be tightened via additional caveats without weakening integrity. citeturn9search4turn9search0

In practice, MAL can implement capability attenuation in multiple ways, depending on environment maturity:

- **Ephemeral, workflow-bounded capability tokens** (time-bounded, scope-bounded, object-bounded). citeturn9search4turn9search1  
- **“Two-man rule” / human confirmation** for destructive actions, consistent with MCP’s consent-and-control principle and common agent safety patterns. citeturn8view0turn7search9  
- **Read-only-by-default** with explicit step-up to write, mirroring least-privilege scope design in modern authorisation frameworks. citeturn0search2turn9search1turn9search5

## Security model and policy design patterns

### Data minimisation as a first-class engineering invariant

Data minimisation is not only a compliance principle; it is a practical security design tool. The UK GDPR framing (via the ICO) is direct: identify the minimum personal data needed for the purpose and hold/share no more. citeturn2search0 For agentic systems, the MAL turns “purpose limitation” into executable policy: each workflow declares its purpose, and policy constrains which records, fields, and time windows are in-scope. citeturn2search0turn7search4

Concrete examples (explicitly preserving your transcript’s ideas):

- **Inbox overview workflow**: expose only the last *N* days of email; exclude password-reset messages and other high-risk categories; provide only normalised fields (subject, sender, timestamp, short excerpt). citeturn2search21turn7search0turn7search4  
- **Calendar “what’s next” workflow**: expose only upcoming events for the next week; strip attendee emails unless required; redact meeting links unless actioned. citeturn2search0turn0search1  
- **Filesystem “summarise this folder” workflow**: expose a single directory tree, not the whole disk; provide metadata and selected files only; deny dotfiles, keys, credential stores by policy. This is consistent with the practical lesson from MCP server vulnerabilities: filesystem adjacency can turn connector flaws into high-impact compromise. citeturn1search19turn1search7

### Least privilege and “granularity repair” for legacy authorisation

Least privilege is a long-established security principle: grant only the accesses necessary for tasks. citeturn0search28turn0search2 The MAL acts as a “granularity repair layer” for systems whose native authorisation model is too coarse. Instead of giving the agent a top-level grant (entire mailbox, entire drive), the MAL becomes the *enforcement point* that constrains data and actions to task-shaped slices. This is conceptually aligned with ABAC (“evaluate subject/object/action/environment attributes against policy”) and with large-scale authorisation systems that separate enforcement from business logic. citeturn3search2turn3search0

### Defence against injection and exfiltration: reduce surface, constrain channels

Because prompt injection is expected to remain a persistent industry-wide challenge, the MAL should assume models can be tricked sometimes and engineers must design for containment. citeturn7search1turn0search13 Two patterns are particularly important:

**Reduce the accessible data/capability surface**: OpenAI’s agent safety guidance explicitly recommends reducing attack surface and limiting sensitive access; MCP likewise emphasises user consent and careful tool handling. citeturn7search4turn8view0turn7search1

**Constrain interfaces with structure**: OpenAI’s agent-builder guidance recommends structured outputs between nodes to eliminate free-form channels that attackers can exploit. citeturn7search4 In MAL terms, normalised mode is not just convenience—it is an injection-resistance strategy.

Where additional controls are warranted, MAL can integrate classic **data loss prevention (DLP)** style checks: DLP is explicitly framed as identifying and helping prevent unsafe or inappropriate sharing/transfer of sensitive data. citeturn9search2turn9search18 A MAL that performs output-boundary DLP checks before a tool call (e.g., before sending email, sharing a link, uploading a file) materially reduces accidental or induced data leakage. citeturn0search1turn0search9

## Implementation blueprint: multi-agent build plan, roles, and test-first delivery

This section turns the concept into an implementable programme, explicitly designed to be executed by a **series of bot agents**, using a mix of OpenAI Codex and “cloud code” tooling such as Claude Code.

### Delivery shape: an open reference implementation + exemplar connectors + documentation site

A credible open release should include:

- A core MAL runtime (policy evaluation, transforms, audit, step-up gate). citeturn0search3turn3search33turn8view0  
- At least three “interesting implementations” (as you requested), showing the same ideas applied to different resource types:
  1) Inbox summariser façade,  
  2) Calendar assistant façade,  
  3) Filesystem-limited research/summarisation façade. citeturn2search21turn5view1turn7search11turn1search19  
- MCP integration demonstrating MAL as a server that exposes MAL-defined Resources/Tools, with explicit consent and tool gating. citeturn8view0turn5view1  
- A test suite: unit tests for policies/transforms, integration tests for connectors, and adversarial tests for injection-like payloads (shift-left). citeturn2search3turn2search19turn7search4turn0search5

### Agent roles (including “librarian”) and responsibilities

Below is a practical role decomposition that matches your requested roles and is compatible with modern agentic coding tooling.

**Architect / Orchestrator / Conductor agent**  
Owns the system design, module boundaries, and sequencing. Produces an architecture spec, threat model, and the “Definition of Done” for each milestone. Should explicitly bake in MCP security principles (consent/control, data privacy, tool safety) and Zero Trust assumptions (no implicit trust, per-request checks). citeturn8view0turn6search0

**Librarian agent (connect-the-dots + mapping)**  
Maintains a living knowledge base of decisions: how requirements map to patterns (least privilege, ABAC, defence in depth), how MAL maps to MCP constructs (Resources/Tools), and how each connector’s policy “purpose” is declared. Also curates citations and terminology consistency for the public white paper and site. This mirrors NIST AI RMF practice of structured risk thinking across lifecycle and provides continuity across many specialised agents. citeturn6search1turn6search5

**Implementation agents (developers, specialised by subsystem)**  
Split by subsystems:
- Policy engine integration (OPA/Rego or Cedar evaluator). citeturn0search3turn3search1  
- MCP server/proxy integration and schema definitions. citeturn8view0turn5view1  
- Connector implementations (email/calendar/filesystem). citeturn5view1turn1search19  
- Transformation/normalisation pipeline (redaction, structured output). citeturn7search4turn0search5  
- Audit/logging and policy traceability. citeturn8view0turn6search0

**QA / Security test agents**  
Build automated tests and adversarial harnesses:
- Policy unit tests (positive/negative cases). citeturn3search25turn0search7  
- Connector integration tests with mocked resources. citeturn5view1  
- Prompt-injection regression corpus and “misuse case” tests inspired by OWASP and MITRE ATLAS. citeturn0search1turn2search19turn2search3  
- “Canary” tests for high-impact actions (ensure step-up required; ensure denial pathways). citeturn0search9turn7search9

### Tooling fit: Codex + Claude Code as parallel implementation engines

The requested mix is technically well-matched to the project shape:

- OpenAI’s Codex supports multi-agent and parallel workflows (Codex app; cloud tasks running in isolated environments/worktrees). citeturn4search0turn4search3turn4search4  
- Codex documentation explicitly highlights security defaults such as network access off by default and sandboxed operation, alongside cautions about enabling internet access due to prompt injection and exfiltration risk. citeturn4search27turn7search13turn7search14  
- Claude Code is positioned as an agentic coding tool across terminal/IDE/desktop, and Anthropic describes “agent teams” that can work in parallel—useful for splitting the MAL into independent read-heavy tasks (spec writing, connector scaffolds, docs, tests). citeturn4search2turn4search13

A practical division of labour is:
- Use Codex Cloud for longer-running implementation threads (connectors, MCP integration, policy engine modules). citeturn4search4turn4search19  
- Use Claude Code “agent teams” to accelerate documentation, examples, and test harness generation, plus refactors that require fast local feedback loops. citeturn4search13turn4search2

### A concrete, test-first milestone plan designed for bot agents

This is written as a sequence of artefact-producing milestones that agent teams can execute without ambiguity.

**Milestone: Define the MAL contract and policy schema**  
Deliver:
- A canonical “Resource Contract” describing: allowed fields, time windows, query parameters, and redaction rules.  
- A “Capability Contract” describing: which write/actions exist, required approvals, and rate limits.  
- A self-describing endpoint (or MCP metadata) where the MAL can describe itself and its policy boundaries so the user can validate “is this good enough for what you want to do?”. MCP already emphasises capability discovery and metadata, making this approach ecosystem-aligned. citeturn5view1turn8view0turn0search26

**Milestone: Build the core MAL runtime (policy + transforms + audit)**  
Deliver:
- Policy evaluation integrated with OPA or Cedar. citeturn0search3turn3search1  
- Transformation pipeline supporting transparent and normalised modes, explicitly adopting structured output constraints. citeturn7search4turn0search5  
- Audit logging and policy traces, consistent with Zero Trust’s emphasis on continuous authorisation and monitoring. citeturn6search0

**Milestone: Implement three exemplar façades (email/calendar/filesystem)**  
Deliver:
- Inbox summary façade implementing the “last week only + exclude password reset / sensitive patterns” policy. citeturn2search21turn7search0  
- Calendar façade implementing “next 7 days only” with normalised event schema. citeturn2search0  
- Filesystem façade implementing strict roots/boundaries and denying sensitive directories by default; this is directly motivated by evidence that filesystem adjacency can amplify connector vulnerabilities. citeturn1search19turn8view0

**Milestone: MCP integration and consent gates**  
Deliver:
- MAL as an MCP server exposing Resources/Tools, with per-tool consent requirements and explicit user-visible descriptions of tool behaviour (treating annotations as untrusted unless server is trusted, per MCP guidance). citeturn8view0turn5view1  
- If MAL is deployed as a proxy in front of another MCP server, ensure it can enforce policy regardless of downstream server behaviour, reflecting the reality that MCP cannot enforce principles solely at protocol level. citeturn8view0

**Milestone: Security regression suite (prompt injection + excessive agency)**  
Deliver:
- OWASP-derived misuse cases for prompt injection and excessive agency. citeturn0search1turn0search9  
- MITRE ATLAS mapping for the project’s threat model and tests; ATLAS is positioned as a living knowledge base of adversary tactics/techniques against AI-enabled systems. citeturn2search19turn2search23  
- Automated “shift-left” prompt injection mitigation testing approach (e.g., as advocated in SANS material). citeturn2search3

### Where your “Issues–FS–Pipe–I” project fits

You asked to manage development using your **issues, FS, pipe, I** project. Even without assuming its internal mechanics, the MAL programme naturally fits an issue-driven, artefact-centric agent workflow:

- **Issues**: each milestone decomposed into small, verifiable tickets with explicit artefacts (policy schema, connector module, test suite, doc page).  
- **FS (filesystem as source of truth)**: agents commit artefacts (specs, policy code, fixtures) as versioned files; structured docs reduce ambiguity and support reproducibility. citeturn7search4turn0search3  
- **Pipe (CI/CD pipeline)**: every PR triggers policy tests, connector integration tests, and adversarial suites; publish site on green builds. citeturn2search3turn6search6  
- **I (integration/index/intent layer)**: whichever “I” stands for in your system, MAL benefits from an explicit integration layer that indexes policies, workflows, and connectors so agents can discover the correct abstraction for a given purpose (mirroring MCP’s discovery of Resources/Tools). citeturn5view1turn8view0

## Publishing as a community white paper and website

A public-facing release should have two complementary deliverables: (1) a PDF-like white paper (this document can be the initial draft) and (2) a navigable website that functions like a “reading page” and contribution hub.

### Website information architecture

A strong structure, designed for community comprehension and contribution, is:

- **Principles**: why legacy resources are risky for agents; data minimisation + least privilege; defence in depth; progressive trust; transparent vs normalised modes. citeturn2search0turn0search28turn6search6turn7search4  
- **Architecture**: MAL positioning relative to MCP; components (policy engine, transformers, capability gate, audit). citeturn8view0turn5view1turn0search3  
- **Threat model and mitigations**: prompt injection, tool chaining, excessive agency; include a short case study of the MCP Git server vulnerabilities as a real-world demonstration of connector-layer risk. citeturn1search19turn1search7turn0search9turn7search1  
- **Reference implementations**: inbox/calendar/filesystem demos; show transparent mode vs normalised JSON mode outputs and how policies change the accessible surface. citeturn7search4turn5view1  
- **Contributing**: how to add a connector, how to write a policy, how to add tests, how policy analysis/verification works (especially if Cedar analysis is adopted). citeturn3search25turn0search3

Static documentation generators (e.g., Docusaurus, MkDocs) are commonly used to publish developer-focused documentation sites with versioning and search, and would suit this “presentation-like reading flow.” citeturn6search11turn5view2

### Community credibility: emphasise testing, not just philosophy

Because prompt injection and agentic misuse are ongoing challenges, the community will look for evidence of *operational* safety. citeturn7search1turn0search1 Publishing a strong test suite is therefore central to the project’s legitimacy:
- Include a “known-bad” corpus and demonstrate how MAL policies constrain blast radius even when the model is fooled. citeturn7search0turn0search1  
- Publish a threat-model mapping to MITRE ATLAS techniques and keep it updated as ATLAS evolves. citeturn2search19turn2search23  
- Treat security as compositional: add tests that combine connector capabilities (because real failures often arise from composition). citeturn1search19turn1search26

### Positioning statement for the community

A crisp message, faithful to the transcript and supported by current security guidance, is:

- MCP standardises connection—but implementers still need strong consent, least privilege, and safe tool boundaries. citeturn8view0turn5view2  
- Prompt injection and excessive agency make “broad access” unsafe; we must engineer for containment by default. citeturn0search9turn7search1turn7search4  
- MAL provides that containment by making *workflow-specific, policy-defined abstractions* the default way models touch real systems—reducing exposed data, reducing enabled capability, and enabling progressive trust. citeturn2search0turn0search28turn6search0

This is, in effect, a practical bridge between established security ideas (least privilege, ABAC, defence in depth, zero trust) and the unique realities of agentic automation. citeturn0search28turn3search2turn6search6turn6search0