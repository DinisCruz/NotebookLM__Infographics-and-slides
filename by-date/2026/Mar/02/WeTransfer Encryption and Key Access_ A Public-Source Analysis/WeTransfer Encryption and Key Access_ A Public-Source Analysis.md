# WeTransfer Encryption and Key Access: A Public-Source Analysis

## Executive summary

WeTransfer publicly states that transfers are encrypted **in transit** using **TLS** and **at rest** using **AES‑256**. citeturn34view0 The available public documentation does **not** describe end‑to‑end encryption (E2EE) or client‑side/“zero‑knowledge” encryption where only the sender/recipient holds the decryption keys. Instead, multiple official statements and operational features strongly indicate **server-side encryption**, meaning WeTransfer’s systems (and, under controlled circumstances, authorised personnel) can access file plaintext. In particular, WeTransfer’s Terms explicitly permit using “human and automated means” to detect violations, to “review or screen” content, and to disclose content to authorities under certain conditions—capabilities incompatible with true E2EE. citeturn33view1turn33view3

Key access, at a high level, can be summarised as follows:

- **Transport encryption keys (TLS)**: Session keys are negotiated between the client and the server endpoint terminating TLS. Publicly observable configuration indicates WeTransfer traffic is fronted by an AWS CloudFront edge host (CloudFront domain visible in third‑party TLS scanning), implying TLS termination at a cloud CDN layer. citeturn18view0 CloudFront supports TLS 1.3 and enables it by default (configurable), but WeTransfer does not publicly publish its minimum TLS version, cipher suites, or certificate-key custody model. citeturn19search5turn19search1  
- **At‑rest encryption keys (AES‑256)**: WeTransfer confirms AES‑256 at rest but does not publicly document whether per‑file keys are used, whether envelope encryption is used, or whether keys are held in a provider KMS vs WeTransfer-controlled HSM/KMS. citeturn34view0  
- **Passwords and link controls**: Password protection is an access control on the download flow; WeTransfer says the password is stored “in a secure format” and “can’t be reversed,” suggesting hashing rather than storing plaintext. citeturn40view0 There is no public claim that link passwords perform cryptographic E2EE (e.g., deriving a file key exclusively from the password such that WeTransfer cannot decrypt). citeturn39view0turn40view0

Regarding third parties, WeTransfer publishes a sub‑processor list that includes providers for **cloud infrastructure**, **preview generation**, **content moderation**, **analytics**, **monitoring**, **email delivery**, and **payments**. This matters for confidentiality: some subprocessors (notably “preview generation” and “content moderation”) plausibly require access to file content in plaintext at some stage in processing. citeturn30view0

On retention, WeTransfer’s consumer‑facing help centre states that Free and Starter transfers can be kept online up to **3 days**, while Ultimate can keep transfers “for as long as you’d like”; it also describes recovery windows (up to one year for <256 MB files; 90 days for larger files). citeturn38view0 Separately, WeTransfer’s B2B Data Processing Agreement (DPA) describes deletion behaviour including deletion from servers within **48 hours after expiry** unless a transfer is set as “Recoverable,” in which case deletion occurs **one year** after expiry. citeturn28view0 These two sources are directionally consistent (data often persists beyond “expiry”) but differ on detail and scope; the difference should be treated as a **material ambiguity** for risk assessments. citeturn38view0turn28view0

Legally, WeTransfer’s Privacy Policy and Terms indicate WeTransfer may **access, preserve, and transmit** information (including user content) to law enforcement or other authorities based on legal obligations or good‑faith necessity (e.g., legal process). citeturn26view0turn33view3 The Privacy Policy also acknowledges operating as a “global business” with partners outside the EEA, including certain US-based third parties under the EU‑US Data Privacy Framework or Standard Contractual Clauses. citeturn27view0 Parallel to this, US law (CLOUD Act) can compel US service providers to produce data they control, even if stored outside the US—relevant because WeTransfer publicly lists multiple US-based subprocessors and US processing locations. citeturn30view0turn37search32turn37search20

## Scope and publicly available sources

This report is limited to **publicly published** materials (no NDA-only documents, no internal disclosures, no reverse engineering of client code). The core sources used are:

- WeTransfer Help Centre security statements (encryption claims, password protection, download verification, retention). citeturn34view0turn40view0turn35view0turn38view0  
- WeTransfer Terms of Service and Privacy Policy PDFs (access, content review, disclosures, international transfers, retention for legal reasons). citeturn33view3turn26view0turn27view7  
- WeTransfer Data Processing Agreement and the public “Sub-processors” list. citeturn28view0turn30view0  
- WeTransfer Trust Center entry points (to determine what is and is not publicly disclosed). citeturn3search0turn3search3  
- Reputable third-party material on governing legal reach (US CLOUD Act over service providers; general provider guidance). citeturn37search32turn37search20  

A notable limitation is that WeTransfer indicates its Trust Center hosts richer security/compliance documentation, but access is designed for business users and requires an NDA; therefore, **key‑management specifics are not publicly verifiable** through that channel. citeturn3search3turn3search0

## Product variants and feature comparison

WeTransfer’s public help documentation indicates that the consumer/prosumer plans were restructured starting December 2024, with older “Pro”/“Premium” consolidated into “Ultimate,” and additional business offerings including Teams and Enterprise. citeturn5search0 For enterprise controls (SSO/SCIM), WeTransfer documentation indicates these are available only on Enterprise. citeturn5search3turn5search5

The table below focuses on features most relevant to confidentiality, access control, and governance.

| Capability | Free | Starter | Ultimate | Teams | Enterprise |
|---|---|---|---|---|---|
| File size / monthly cap | Up to 3 GB per transfer; up to 10 transfers and 3 GB total per 30 days citeturn5search0 | Up to 300 GB per transfer; up to 10 transfers and 300 GB total per 30 days citeturn5search0 | “Unlimited option” replacing former Pro/Premium (quantitative limits not stated in this article) citeturn5search0 | Unlimited transfer size (shared workspace) citeturn10search1 | Custom / scalable; positioned as “advanced security and management” citeturn5search7 |
| Transfer availability / expiry | Up to 3 days (sender‑selected, within plan limit) citeturn38view0 | Up to 3 days citeturn38view0 | Can keep transfers active “as long as you’d like” citeturn38view0 | Not a single statement of “forever”; shared workspace positioning emphasises unlimited size/storage; separate “file request” uploads may have 30‑day periods depending on flow citeturn10search1turn5search8 | Not specified publicly; likely contractual citeturn5search7 |
| Password protection | Included (“password protection for every transfer”) citeturn5search0 | Included citeturn5search0 | Included (as a general WeTransfer feature; not plan‑limited in the password article) citeturn40view0 | Included (shared workspace uses same transfer mechanisms) citeturn10search1turn40view0 | Included; plus stronger identity controls via SSO/SCIM citeturn5search3turn5search5 |
| Download verification / access control options | Feature exists as “tracked” vs “restricted” downloads (availability by plan not stated; described as a general feature) citeturn35view0 | Same caveat citeturn35view0 | Same caveat citeturn35view0 | Admin can see/manage transfers; access logs described for access‑controlled transfers citeturn10search1turn35view0 | Same, plus central identity management (SSO/SCIM) citeturn5search3turn5search5 |
| Shared workspace, central admin visibility | No | No | No (single-user plan) citeturn5search0 | Yes; admins can view/manage all active transfers; members see only their own citeturn10search1 | Yes; and positioned for larger orgs with advanced management citeturn5search7 |
| SSO / SCIM | No | No | No | Not indicated as available; documentation states Enterprise-only citeturn5search3 | Yes (Enterprise-only) citeturn5search3turn5search5 |

Two implications for encryption and key access follow from this plan structure:

1. **Enterprise adds identity and admin controls, not publicly documented key isolation.** Enterprise appears aimed at governance (SSO/SCIM, multi-seat management) rather than a fundamentally different cryptographic model. citeturn5search3turn5search7  
2. **Admins and account administrators may have expanded content access.** WeTransfer’s Terms state that multi-seat accounts may permit administrators to “manage, access, and use” the account and associated content. citeturn32view0

## Transfer flow and key lifecycle

WeTransfer’s published model is best described as encrypted transport to WeTransfer, server-side storage, and link-based retrieval. WeTransfer asserts TLS during transfer and AES‑256 while stored. citeturn34view0 Retention and access controls (expiry, password, restricted downloads) sit around this core flow. citeturn38view0turn40view0turn35view0

A key architectural observation is that WeTransfer’s web endpoint is publicly observable as being served behind an AWS CloudFront host (CloudFront domain shown in TLS scanning output), which implies TLS is terminated at or near the CDN edge. citeturn18view0

```mermaid
flowchart TD
  A[Sender device: file plaintext] -->|HTTPS/TLS| B[Edge/CDN terminates TLS]
  B --> C[WeTransfer application/API]
  C -->|generate link + access rules| D[Transfer link + policy]
  C -->|server-side processing| E[Scanning / previews / moderation pipeline]
  C -->|encrypt at rest (AES-256)| F[(Encrypted object storage)]
  F -->|decrypt for download| C
  C -->|HTTPS/TLS| G[Recipient device: file plaintext]

  D --> H[Password gate]
  D --> I[Download verification: tracked/restricted]
  I -->|email verification| C

  E --> J[Third-party services (e.g., preview generation, moderation)]
  C --> K[Metadata + logs]
  K --> L[Analytics/monitoring systems]
  C --> M[Legal request / preservation workflow]
```

### Where plaintext exists in this flow

Plaintext must exist at least at endpoints (sender device and recipient device). Additionally, plaintext must exist **inside WeTransfer-controlled execution contexts** to enable multiple documented features:

- Content review/screening for policy compliance and investigations (Terms). citeturn33view1turn33view3  
- Preview generation performed by a dedicated external service provider in WeTransfer’s own subprocessor list (suggesting the file content is transformed for previews). citeturn30view0  
- Content moderation using both automated and human means and a named moderation subprocessor. citeturn33view1turn30view0  

This is a critical point: if WeTransfer can “review or screen” content, then at least some internal services (and potentially humans under policy and access controls) can access file contents in a readable form. citeturn33view1turn33view3

## Cryptography and key management

### Encryption in transit

WeTransfer’s own security documentation states that files are encrypted “when they are being transferred (TLS).” citeturn34view0 WeTransfer also describes all transfers as “automatically encrypted using TLS encryption” during upload/download in its “encrypted files” guidance. citeturn39view0

What TLS version and cipher suites are used is not stated in WeTransfer’s public documentation. However, two public inferences can be made:

- The WeTransfer endpoint is observable behind CloudFront infrastructure. citeturn18view0  
- CloudFront supports TLS 1.3 and (by default) enables TLS 1.3 on distributions, with configurable security policies. citeturn19search5turn19search1  

Therefore, it is plausible that clients negotiating with WeTransfer’s edge endpoint may use TLS 1.3 when supported; but the **minimum version policy** (e.g., whether TLS 1.2 is required, whether older versions are disabled) is not publicly confirmed by WeTransfer. citeturn34view0turn19search5

### Encryption at rest

WeTransfer states: “Your files are encrypted… when they are stored (AES‑256).” citeturn34view0 The B2B DPA similarly asserts that transfers are encrypted while hosted and that transport uses secured HTTPS. citeturn28view0

What is not publicly specified (material gaps):

- Whether AES‑256 is implemented as per-object storage encryption, per-file keys, or an envelope-encryption model. citeturn34view0  
- Whether the encryption keys are controlled exclusively by WeTransfer vs a cloud‑provider managed key service, and whether there are split‑key or customer‑managed‑key options for Enterprise. citeturn34view0turn3search3  

Without those details, the strongest defensible conclusion is structural: **at-rest encryption is server-side**, and WeTransfer retains the ability (through systems and authorised access pathways) to decrypt stored data to deliver the service. This is supported by WeTransfer’s own Terms permitting content review and screening. citeturn33view1turn33view3

### Client-side encryption and end-to-end encryption

No public WeTransfer documentation in the sources above claims that files are encrypted **client-side** in a way that prevents WeTransfer from decrypting them, nor that WeTransfer lacks access to the necessary keys. citeturn34view0turn39view0turn33view1

To the contrary, WeTransfer’s Terms and policies describe operational capabilities (review/screen content; enforcement; disclosure) that require server-side visibility. citeturn33view1turn33view3 Therefore, based on publicly available evidence, WeTransfer should be treated as **not offering E2EE** for standard transfers.

### Password protection and link-sharing mechanics

WeTransfer password protection is an additional gate on access to the transfer. WeTransfer states the password is stored “in a secure format” and “can’t be reversed,” which is consistent with salted hashing for verification. citeturn40view0 The “encrypted files” guide positions password protection as a “second layer” alongside TLS; it does not describe password-derived cryptographic file encryption. citeturn39view0turn40view0

Implications:

- Passwords likely protect **who can download** (authentication/authorisation), not **whether WeTransfer can decrypt**. citeturn39view0turn33view1  
- Password resets are supported (users can “choose a new password”), which is consistent with storing verification material rather than encrypting the only copy of a file key with the password. citeturn40view0  

### Download verification

A newer feature (“Download Verification”) adds an access-control layer where downloads can be “tracked” or “restricted”; in restricted mode, downloaders must verify their email and only emails matching the intended recipient list can access the transfer. citeturn35view0 This is an identity control around link usage, not an encryption model change.

## Metadata handling, retention, and deletion

### What metadata WeTransfer collects

WeTransfer’s February 2026 Privacy Policy states it collects “User Content,” explicitly including **metadata** (alongside files, photos, video, audio, messages, and other content). citeturn26view0 It also collects technical information (device, IP address, approximate location) and usage information, including engagement and downloads. citeturn26view0

This means that even when content is encrypted at rest, WeTransfer still processes and stores a substantial amount of **non-content data** that can be sensitive in aggregate (e.g., who shared with whom, when, from where, how often). citeturn26view0turn35view0

### Retention and deletion

Publicly documented retention behaviours span multiple layers:

- **Transfer availability (consumer-facing)**: Free/Starter up to 3 days; Ultimate can keep transfers active “as long as you’d like.” citeturn38view0  
- **Expired transfer recovery**: recovery is possible; under‑256 MB up to one year; larger files within 90 days. citeturn38view0  
- **B2B deletion semantics (DPA)**: content is “permanently deleted” within 48 hours after expiration unless set “Recoverable,” in which case deleted one year after expiration; and the customer can delete content at any time. citeturn28view0  
- **Broader retention for legal/operational needs**: the Privacy Policy states WeTransfer may retain personal information to comply with laws (example: financial information retained for 7 years) and to preserve information after receiving government preservation orders, or to review/challenge legal requests. citeturn27view7  

A conservative interpretation for risk management is:

- “Expired” does not necessarily mean “immediately deleted.” It can mean “no longer available to download,” while the provider may still retain recoverable copies, backups, or preserved records for defined windows. citeturn38view0turn28view0turn27view7  
- Deletion timelines and recoverability may vary by plan, feature toggle (“Recoverable”), and whether a business agreement governs processing. citeturn28view0turn3search3  

### Subprocessors and operational metadata expansion

WeTransfer’s public subprocessor list includes multiple analytics/monitoring and service-improvement vendors, which likely increases the volume of derived telemetry (logs, events, performance data), even if not raw file contents. citeturn30view0turn26view0

Disclosed subprocessors (as of April 2025) include: entity["company","Amazon Web Services","cloud provider"], entity["company","Zendesk","customer support software"], entity["company","Google Cloud","cloud platform"], entity["company","Stripe","payments company"], entity["company","Calendly","scheduling software"], entity["company","Bending Spoons","software company"], entity["company","Databricks","data analytics platform"], entity["company","Snowplow","analytics platform"], entity["company","Fivetran","data integration company"], entity["company","Datadog","monitoring company"], entity["company","SendGrid","email delivery service"], entity["company","ProfitWell","subscription analytics company"], entity["company","Amplitude","product analytics company"], entity["company","RevenueCat","in-app subscriptions platform"], entity["company","Transloadit","file processing service"], entity["company","Checkstep","content moderation company"], entity["company","CrowdStrike","cybersecurity company"], entity["company","Slack","workplace messaging company"], entity["company","SupportYourApp","customer support outsourcing"], and entity["company","hCaptcha","captcha service"]. citeturn30view0turn34view0

Two subprocessors are especially relevant to content confidentiality:

- **Preview generation** (Transloadit): implies file transformation/processing, likely requiring content access during processing. citeturn30view0  
- **Content moderation** (Checkstep) plus WeTransfer’s own moderation systems: implies some level of content inspection, potentially including human review under defined circumstances. citeturn30view0turn33view1turn26view0  

## Access model: who can access plaintext or keys

This section distinguishes between (a) **technical capability** and (b) **documented policy/contractual authority**. “Can access” below means “can plausibly or explicitly access under some operational or legal pathway,” not “routinely accesses.”

### Parties with access pathways

WeTransfer is headquartered in entity["city","Amsterdam","netherlands"], entity["country","Netherlands","country"], and its Privacy Policy frames compliance with Dutch and EU law as well as laws from countries where it has local entities, including the entity["country","United Kingdom","country"] and the entity["country","United States","country"]. citeturn26view0

A practical confidentiality model can be summarised in the table below.

| Actor category | Access to plaintext? | Access to encryption keys? | Public evidence and reasoning |
|---|---|---|---|
| WeTransfer automated systems (core service) | Yes (required to deliver features such as download, notifications, moderation and previews) | Yes/indirectly (systems must decrypt server-side encrypted storage to deliver content) | Terms allow investigating violations and to “review or screen” content; Privacy Policy includes automated + human moderation; DPA asserts encryption in transit/at rest but does not claim keys are customer-held. citeturn33view1turn26view0turn28view0 |
| WeTransfer employees (authorised staff) | Yes, under controlled circumstances | Potentially (depending on internal controls and role) | Privacy Policy describes safeguards limiting access to those who “need access to do their job,” monitoring activity on servers/devices, and staff confidentiality obligations; DPA security measures state only a select authorised group can read/copy/modify/remove data and access is logged. citeturn27view0turn28view0 |
| Workspace/account administrators (customer-side admins) | Yes for their org’s workspace content | No (generally) | Terms state multi-seat accounts may enable administrators to manage, access, and use the account and associated content, implying admins can access recipient/sender-visible content via the UI. citeturn32view0turn10search1 |
| Third-party subprocessors for preview/moderation | Plausibly yes (especially preview generation and moderation) | Unclear; more likely plaintext access via WeTransfer-mediated processing rather than key custody | WeTransfer subprocessor list explicitly includes preview generation and content moderation providers, implying content is shared/processed by vendors; Terms/Privacy emphasise enforcement and moderation. citeturn30view0turn33view1turn26view0 |
| Cloud infrastructure providers / CDNs | Plausibly yes in limited ways (e.g., platform-level access, compromise, misconfiguration); operationally they host encrypted objects and terminate TLS at edge | Plausibly yes for TLS private key custody at CDN layer; at-rest key custody model is unknown | Public scanning shows WeTransfer served via CloudFront host; CloudFront supports TLS 1.3 and handles TLS termination. WeTransfer states it works with AWS and uses AES‑256 at rest, but does not publish key ownership detail. citeturn18view0turn19search5turn34view0 |
| Governments / law enforcement | Yes, if compelled disclosure succeeds against WeTransfer or providers | Indirectly (usually require provider to decrypt/produce plaintext rather than obtaining keys) | Terms allow disclosure of content/registration info to authorities; Privacy Policy says WeTransfer may access/preserve/transmit info including user content to law enforcement based on legal process and good faith necessity, and may retain data under preservation orders. citeturn33view3turn26view0turn27view7 |

### Legal and operational access routes

WeTransfer’s Terms explicitly contemplate disclosure of content to authorities and third parties, where legally required or deemed necessary to comply with legal obligations or protect interests/safeguard others. citeturn33view3 The Privacy Policy provides additional detail: it states WeTransfer may “access, preserve, and transmit” information (including user content) to law enforcement/public authorities/IP rights holders/others based on a good‑faith belief of necessity, including to comply with legal process. citeturn26view0

Because WeTransfer discloses US-based subprocessors and US processing locations in its subprocessor list, one should also consider extraterritorial access regimes that apply to US service providers. Under the US CLOUD Act (as described by the entity["organization","United States Department of Justice","federal executive department"] and by AWS), US legal process can compel a service provider to produce data within its possession, custody, or control, even if stored outside the US. citeturn37search32turn37search20turn30view0

This does not automatically mean US authorities can directly read WeTransfer files; it means there are plausible routes where US authorities could seek data from:

- WeTransfer directly (if jurisdictionally available, or through cross‑border cooperation), or citeturn26view0turn33view3  
- US-based vendors that store or process WeTransfer-related data (depending on what categories of data each vendor receives). citeturn30view0turn26view0  

Cross‑border requests can also proceed via mutual legal assistance processes, which can be slower and more procedurally constrained than direct domestic orders; Dutch MLA guidance describes requirements and constraints for coercive measures and evidence use. citeturn37search1

## Scenarios

### Scenario: WeTransfer suffers a storage-layer data breach

Assume an attacker gains read access to object storage (e.g., via compromised credentials or misconfiguration). In that case:

- **At-rest AES‑256 encryption** may protect against immediate plaintext exposure if the attacker only obtains encrypted blobs and cannot access corresponding keys. citeturn34view0  
- However, because WeTransfer must be able to serve downloads and perform moderation/previewing, keys (or decryption capability) necessarily exist within WeTransfer-controlled systems. If the breach expands to application servers, key management services, or privileged internal access, then plaintext exposure becomes plausible. citeturn33view1turn28view0turn27view0  
- Link-based sharing adds an additional practical risk: if attackers exfiltrate link tokens, recipient lists, or download-verification logs, they may be able to access content without breaking encryption—even more so if recipients reuse passwords or if passwords are phished elsewhere. citeturn35view0turn40view0turn26view0  

Bottom line: at-rest encryption reduces risk from “raw disk/object theft,” but it does not provide the strong confidentiality guarantees of client-side E2EE against a platform compromise. citeturn33view1turn34view0

### Scenario: US government subpoena or warrant seeks WeTransfer-hosted content

A realistic pathway analysis splits into two cases:

- **Request to WeTransfer itself**: WeTransfer’s Privacy Policy and Terms indicate it may disclose content when legally required or deemed necessary, and it may preserve data under preservation orders. citeturn26view0turn33view3turn27view7 If the request must cross borders, it may proceed via MLA mechanisms (not instantaneous). citeturn37search1  
- **Request to a US-based service provider**: Because WeTransfer lists US-based vendors and US processing locations, US authorities could attempt to compel those vendors for data they hold. The CLOUD Act framework (as summarised by the DOJ and AWS) clarifies that compelled production can include data stored outside the US if the provider has control. citeturn37search32turn37search20turn30view0  

What a requester could obtain depends on each provider’s role:

- Payment processors may hold billing and transaction metadata. citeturn26view0turn30view0  
- Analytics/monitoring vendors may hold telemetry, identifiers, and event logs. citeturn26view0turn30view0  
- Cloud infrastructure (and any preview/moderation pipeline) may hold or transiently process content, raising the stakes if content is within their scope. citeturn30view0turn34view0  

## Consolidated conclusions and key ambiguities

### Conclusions supported by public evidence

WeTransfer is best modelled as a **server-side encrypted file transfer service**: TLS protects data in transit; AES‑256 protects data at rest; but WeTransfer retains the technical means to decrypt content to provide the service and to conduct enforcement and moderation. citeturn34view0turn33view1turn26view0

Password protection and download verification improve access control for link-based sharing, but public documentation does not support treating these mechanisms as E2EE or “zero‑knowledge” encryption. citeturn40view0turn35view0turn33view1

WeTransfer discloses a broad subprocessor ecosystem, and at least some subprocessors’ functions (preview generation, content moderation) plausibly require content visibility, expanding the set of parties who could access plaintext under some circumstances. citeturn30view0turn33view1

WeTransfer’s Privacy Policy and Terms are explicit that the company can access/preserve/transmit content for legal compliance and enforcement. This means governments can obtain content **by compelling the service**, and WeTransfer is not architected to technically prevent that class of access (as E2EE would). citeturn26view0turn33view3

### Material gaps and plausible assumptions

The largest unresolved questions for a rigorous cryptographic assurance assessment are:

- **Key management architecture** (per‑file keys vs shared keys; envelope encryption; KMS/HSM design; key rotation; access boundaries between staff and systems). There is no public, detailed description; Trust Center details appear NDA‑gated. citeturn3search3turn34view0  
- **Precise TLS configuration** (minimum version, cipher suites, certificate/private key custody). CloudFront capability is known publicly, but WeTransfer’s exact policy is not stated. citeturn19search5turn18view0turn34view0  
- **Exact content-sharing scope with subprocessors** (which transfers are previewed; what content moderation entails for encrypted archives; whether processing is opt‑in). Function names imply access, but exact mechanics are not publicly documented. citeturn30view0turn33view1  
- **Retention reconciliation** between consumer help content (expiry/recovery) and B2B DPA deletion terms (48 hours after expiry unless recoverable). The safest assumption is that “expiry” is a user-facing availability state, and deletion can occur later depending on settings/legal holds. citeturn38view0turn28view0turn27view7  

If your threat model includes platform compromise, insider risk, or compelled disclosure, the public evidence supports a conservative conclusion: **do not assume WeTransfer provides E2EE confidentiality**; treat it as a secure-but-provider-accessible transfer system with typical cloud service trust dependencies. citeturn33view1turn30view0turn26view0