# GDPR implications of deletion and correction rights for Google Gemini chat sessions in Google Workspace

## Executive context and scope

This report analyses the GDPR implications of *data deletion* and *data correction* rights in the specific context of Google Gemini chat sessions used under Google Workspace enterprise subscriptions. It focuses on whether the reported **inability for Workspace administrators to delete Gemini chat sessions** could create, or evidence, a **potential breach of GDPR compliance**, particularly for the **right to erasure (Article 17)** and **right to rectification (Article 16)**. The analysis reflects how Google describes Gemini’s handling of data across (i) **Gemini in Workspace apps** (for example, Gemini inside Gmail/Docs/Drive) and (ii) the **standalone Gemini app** (gemini.google.com) that can be made available to Workspace users. citeturn1view2turn11view0turn8view0

The key practical point is that **“Gemini” is not one single processing context**. Google distinguishes (a) *Gemini in Workspace apps* from (b) the *Gemini app*, and further distinguishes whether the Gemini app is used as a **“core service”** (covered by Workspace enterprise-grade terms and protections) or as an **“additional service”** (subject to Google consumer terms and different data-use practices). citeturn10view0turn11view0turn1view2

As of **May 2025**, Google states that Workspace admins can configure Gemini app “conversation history” and retention settings; by default, conversation history is ON and retention is 18 months, while turning history OFF leads to chats being saved up to 72 hours (for service provision and feedback processing). citeturn8view0

This is not legal advice; it is a structured GDPR analysis for professional governance and risk assessment.

## GDPR rights to rectification and erasure

### Right to rectification and what “correction” can mean in practice

GDPR Article 16 provides that a data subject has the right to obtain from the controller **“without undue delay”** the rectification of inaccurate personal data, and to have incomplete data completed, **“including by means of providing a supplementary statement.”** citeturn20view0

Two implications matter for Gemini chat transcripts:

First, **a chat log can contain personal data** (for example, names, contact details, assessments about a person, or other identifiers in prompts/responses). If that personal data is inaccurate, Article 16 requires a controller to have a workable mechanism to correct it without undue delay. citeturn20view0turn18search5

Second, Article 16 does *not* always imply that the original record must be rewritten. The “supplementary statement” language supports approaches such as (a) attaching an annotation, (b) adding a clarifying record, or (c) restricting use of disputed data while verifying accuracy (which connects to Article 18 restriction of processing where accuracy is contested). citeturn20view0turn22search5

In AI contexts, rectification disputes often arise where an LLM “hallucinates” incorrect claims about a person. Google’s Gemini Apps Privacy Hub explicitly recognises that LLM experiences can present inaccurate information as factual, and provides a channel for users to request correction of inaccurate personal data appearing in Gemini’s responses. citeturn3view0

### Right to erasure and its limitations

GDPR Article 17(1) establishes the right to obtain erasure of personal data **“without undue delay”** where one of the listed grounds applies (for example, data no longer necessary; consent withdrawn with no other legal ground; successful objection; unlawful processing; legal obligation to erase; or certain children’s data scenarios). citeturn19view1

Critically, Article 17 is **not absolute**. Article 17(3) lists situations where erasure does not apply to the extent processing is necessary, including for freedom of expression and information, compliance with legal obligations/public tasks, public health, archiving/research/statistics (under conditions), and establishing/exercising/defending legal claims. citeturn19view1turn7search2

In enterprise settings, the most operationally relevant exceptions tend to be:

* **Legal obligation / regulatory retention** (for example, where retention is mandated by sector regulation).
* **Legal claims** (litigation hold / investigation).
* **Restriction instead of erasure** where the data subject contests accuracy or opposes erasure and requests restriction (Article 18). citeturn19view1turn22search5turn7search2

Even where an exception applies, controllers must still comply with GDPR’s general principles, including **storage limitation** (keep personal data no longer than necessary) and **accuracy** (take reasonable steps to erase or rectify inaccurate data without delay). citeturn18search5

### Procedural duties that shape “can we comply?”

Rights are enforceable only if the organisation can act operationally. Article 12 requires controllers to **facilitate** rights requests and to respond **without undue delay and within one month** (extendable by two further months for complexity/volume, with reasons). citeturn18search8

Where personal data has been disclosed, controllers must communicate rectification/erasure/restriction to recipients unless impossible or disproportionate, and inform the data subject of recipients on request (Article 19). citeturn22search4

GDPR therefore creates a strong expectation that controllers choose systems, contracts, and configurations that make “rights fulfilment” realistically achievable, not theoretical.

## Google Workspace and Gemini data retention, deletion, and control practices

### Product map: Gemini in Workspace apps vs the Gemini app

Google’s Workspace-facing documentation draws a sharp distinction between:

* **Gemini in Workspace apps** (Gemini features inside core Workspace services such as Docs/Gmail/Drive)
* **The standalone Gemini app** (gemini.google.com), which can be included in Workspace editions and configured by admins citeturn11view0turn1view2turn8view0

This distinction is fundamental for GDPR analysis because it directly affects whether chat “sessions” are retained and what deletion mechanisms exist.

### Gemini in Workspace apps: prompts/responses described as not retained after session

Google’s “Generative AI in Google Workspace Privacy Hub” indicates that for several generative AI features in Workspace (for example, “Help me write” in Docs/Gmail; “Help me organise” in Sheets; Gemini in Drive; NotebookLM), **prompts and responses are not retained after the user’s session ends**. citeturn1view2

A Google Workspace Blog explainer (“The life of a prompt”) similarly states that context and information passed to the Gemini model **“disappears after your Gemini session ends”** and is not stored or used to train the model. citeturn4view0

For this category of Gemini usage, the primary “persistent data” is typically **whatever the user inserts into a document/email**. That content then becomes ordinary Workspace content governed by existing Workspace retention and deletion mechanisms (Drive deletion controls, Vault retention, etc.), rather than a separate “Gemini chat session record.” citeturn4view0turn1view2

### The Gemini app under Workspace: retention settings and the 72-hour mode

For the Gemini app (gemini.google.com) provided to Workspace users, Google has described an admin-managed regime:

* From May 2025, Google stated that by default “Gemini conversation history” is ON and “Conversation retention” is 18 months. citeturn8view0  
* If “Gemini conversation history” is OFF, chats are saved in user accounts for **up to 72 hours**, and chat activity does not appear in Gemini Apps Activity. citeturn8view0turn3view1  
* If an admin turns history from ON to OFF, existing history from before the change remains stored for the length of time defined by the retention setting. citeturn8view0  
* End users cannot override admin-configured conversation history settings. citeturn8view0turn10view0  
* Google’s Gemini Apps Help page for work/school accounts states that “Keep Activity” (for Gemini Apps) is on by default and can only be turned off by the Workspace admin; conversation history is saved by default for 18 months and the admin can adjust auto-delete to 3/18/36 months or turn history off. citeturn10view0

These descriptions establish that, at least as designed in Google’s enterprise controls, chat retention is primarily managed via a *retention switch* and *auto-delete windows*, not necessarily via per-chat deletion.

### Core service vs additional service: why the terms matter

Google states that the Gemini app is now included as a **core service** with enterprise-grade data protection for many Workspace editions (including Business and Enterprise tiers). As a core service, Google says Gemini prompts/data/responses are not reviewed by human reviewers or used to improve generative AI models without permission. citeturn11view0turn10view0

However, Google also states that users without qualifying editions can access the Gemini app as an **additional service**, and Google’s “Use Gemini Apps with a work or school Google Account” help page explicitly contrasts:

* For some editions, Gemini Apps are an **additional service** subject to Google consumer Terms and the Gemini Apps Privacy Notice. citeturn10view0  
* For other editions, Gemini Apps are a **core service** subject to Google Workspace Terms, with enterprise-grade security & privacy protections. citeturn10view0turn11view0

This matters because an “additional service” framing is typically closer to **Google setting its own purposes** (for example, service improvement and model development), which affects the controller/processor analysis.

### Google’s contractual deletion framing in the Cloud Data Processing Addendum

Google’s **Cloud Data Processing Addendum (CDPA)** states (in the general roles clause) that **Google is a processor and the customer is a controller (or processor, as applicable) of Customer Personal Data**. citeturn5view3

On deletion, the CDPA states Google will enable the customer to delete Customer Data **“in a manner consistent with the functionality of the Services”**, and once instructed, Google will delete within a maximum of 180 days (subject to legal storage requirements). citeturn5view1turn5view3

Google’s GDPR resource page similarly explains that customers can delete customer data via service functionality, and that Google will delete relevant customer data from its systems within a maximum of 180 days when it receives a complete deletion instruction (unless retention obligations apply). citeturn12view0

This “consistent with functionality” qualifier is central to the compliance question: GDPR obligations are not waived just because a service interface does not support granular deletion.

## Controller, processor, and joint controller roles in this context

### Baseline: controllers bear primary responsibility for Articles 16 and 17

Articles 16 and 17 are rights **against the controller**. citeturn20view0turn19view1

That means a Workspace customer (often the employer/organisation) typically remains the primary accountable party for complying with rectification and erasure for processing that is done “on its behalf” within the Workspace service boundary. This is reinforced by GDPR’s accountability principle (Article 5(2)) and controller responsibility duties (Article 24). citeturn18search5turn19view3

### Processors must assist; “technical impossibility” is a weak defence

Where Google is acting as processor, GDPR Article 28(3)(e) requires that the processor assists the controller, by appropriate measures “insofar as possible,” to fulfil obligations to respond to data subjects’ rights requests. citeturn19view2turn18search8

The EDPB’s **Guidelines 07/2020** underscore that controllers must only use processors with sufficient guarantees, and that a processor becomes a controller if it goes beyond instructions and determines its own purposes/means. They also emphasise that Article 28(3) imposes **direct obligations on processors**, including duties to assist controllers. citeturn25view0

Separately, the EDPB **ChatGPT Taskforce report** states a principle highly relevant to “we can’t delete because the system doesn’t allow it”: in the accountability and data protection by design framing, **technical impossibility cannot be invoked to justify non-compliance**, and controllers must take necessary steps to ensure compliance when processing personal data in LLM contexts. citeturn25view2turn19view3

Applied to Gemini in Workspace, this strongly suggests that if an organisation cannot comply with Articles 16/17 in practice because the system lacks deletion capabilities, that becomes a **controller governance issue** (and potentially a processor issue if Google is acting as processor and has not provided adequate assistive functionality).

### When Google may be a controller (or joint controller) for Gemini app data

Google’s own Workspace/Gemini documentation creates conditions where Google may be acting as controller for some Gemini app operations, particularly where Gemini is treated as an “additional service” subject to consumer terms and practices. citeturn10view0

Under GDPR Article 28(10), if a processor determines the purposes and means, it is considered a controller in respect of that processing. citeturn19view2

The boundary between “customer’s instructions” and “Google’s own purposes” is therefore decisive. Google’s Workspace blog post describing Gemini in Workspace apps explicitly says: “Google is a processor of your data, and acts only according to your instructions.” citeturn4view0  
By contrast, Google’s consumer-facing Gemini Apps Privacy Hub describes broad use of Gemini Apps data to provide, maintain, improve, and develop services and models, including human review of a subset of chats. citeturn2view1turn3view1

For joint controllership risk, case law is instructive. The CJEU has endorsed broad interpretations of “controller” and “joint controller,” including where an entity makes another controller’s processing possible and benefits from it. In **Wirtschaftsakademie**, the Court held that the administrator of a Facebook fan page is jointly responsible with Facebook for processing visitors’ data. citeturn31view1  
In practice, this indicates that “lack of direct access” or “platform constraints” do not necessarily remove controller responsibilities where an organisation chooses to deploy and benefit from the processing.

## Regulatory guidance and case law relevant to AI services and data subject rights

### Regulators increasingly emphasise “rights must be exercisable” even for AI

European regulators have recently focused on how GDPR rights can be exercised in AI contexts:

* The EDPB’s **Opinion 28/2024** concludes that AI models trained on personal data **cannot in all cases be considered anonymous**, and that anonymity must be assessed case-by-case, including considering the likelihood of extracting personal data from the model. citeturn25view1  
* The French CNIL’s AI guidance (published in January 2026) explicitly states that data subjects must be able to exercise rights on training datasets and **on AI models if they are not considered anonymous**, referencing EDPB Opinion 28/2024; CNIL also reiterates rights to rectification, erasure, and objection in AI development contexts. citeturn13search1turn13search8  
* The EDPB ChatGPT Taskforce report highlights accountability expectations for controllers using LLMs and, as noted, rejects “technical impossibility” as a blanket justification. citeturn25view2

While these materials focus on model training and public LLM services (not just enterprise SaaS chat logs), they are relevant because they show the regulatory direction of travel: **rights enforcement is expected to be operationalised**, not sidelined as an engineering inconvenience.

### Enforcement signals: generative AI scrutiny is real

Regulatory scrutiny of GenAI GDPR compliance has included significant enforcement outcomes. For example, Reuters reported that Italy’s data protection authority fined OpenAI €15 million over privacy rule breaches relating to ChatGPT (including legal basis and transparency issues). citeturn13search12

Even where enterprise Gemini differs (not using customer data to train by default in core-service mode), regulators’ posture increases organisational risk where rights fulfilment appears structurally constrained.

### Case law touchpoints for erasure and role attribution

The classic “right to be forgotten” jurisprudence arose before the GDPR but remains conceptually influential. In **Google Spain**, the Court held that the operator of a search engine performs processing and, as described in the Court’s press release, the case resulted in an obligation to withdraw data from the index and render access impossible in the future under particular conditions. citeturn31view0

For roles, **Wirtschaftsakademie** (above) illustrates joint responsibility even where the platform does most of the processing. citeturn31view1

For “what is personal data” and why rights can attach to content records, **Nowak** confirms that information recorded in answers given by a candidate during an examination can constitute personal data, reinforcing that content artefacts in systems can be within rights scope. citeturn31view2

## Compliance risk assessment of non-deletable Gemini chat sessions

### Why “admins can’t delete chats” can become a GDPR problem

The GDPR does not require a *specific UI feature* called “Delete chat.” It requires that the **controller can erase personal data without undue delay when Article 17 grounds apply**, and can rectify inaccurate data without undue delay under Article 16. citeturn19view1turn20view0turn18search8

Therefore, the compliance question is not “does the admin console have a button?” but:

* **Can the controller actually comply** with valid Article 17/16 requests within GDPR timelines? citeturn18search8turn19view1turn20view0  
* If Google is processor, does Google provide **sufficient assistive measures** to enable compliance (Article 28(3)(e))? citeturn19view2turn25view0  
* Are retention defaults and constraints compatible with the **storage limitation** principle, given the organisation’s use case and purposes? citeturn18search5turn19view3

The risk is highest where:

* Gemini chat transcripts contain personal data beyond what is necessary (common in free-text prompts).
* The organisation has a valid erasure obligation (for example, processing was unlawful, or data is no longer necessary, or an objection succeeds).
* The system only supports deletion on a fixed schedule (for example, 3 months minimum), or only through global settings, not targeted removal.

Google’s Workspace documentation indicates conversation history is saved by default for 18 months, and that admins can set auto-delete to 3/18/36 months or turn history off (72-hour retention mode). citeturn8view0turn10view0  
If it is *not possible* for admins to delete specific chat sessions promptly when required, then an organisation could face an argument that it has failed to implement appropriate technical and organisational measures to ensure GDPR compliance (Article 24) and data protection by design (Article 25), especially in light of EDPB’s warning against claiming technical impossibility for LLM compliance. citeturn19view3turn25view2

### Data flow and control responsibility diagram

The diagram below illustrates the key difference between “Gemini in Workspace apps” and the “Gemini app” retention/control pattern described by Google.

```text
Employee / user (data subject)
   |
   |  (A) Gemini in Workspace apps (Docs/Gmail/Drive side panel)
   |      Prompt + authorised Workspace context  ---> Gemini model inference ---> Response
   |                                                   |
   |                                                   |  Google states prompts/responses
   |                                                   |  are not retained after session ends
   |                                                   v
   |      Inserted output becomes normal Workspace content (Doc/Email/etc.)
   |      -> governed by Workspace retention/deletion tooling (e.g., content deletion, Vault)
   |
   |  (B) Gemini app (gemini.google.com) under Workspace account
          Chat prompts/responses -> Conversation history store (admin-controlled)
           - Default ON; retention default 18 months
           - Retention options described: 3/18/36 months or history OFF (72 hours)
           - End users cannot override admin setting
```

This split is supported by Google’s Workspace privacy hub and admin update communications. citeturn1view2turn8view0turn10view0turn4view0

### Comparison table: GDPR requirements vs Google Gemini/Workspace capabilities

The table below summarises key legal requirements against the capabilities and policies Google describes. It is necessarily high-level; an organisation’s precise obligations depend on its role (controller vs processor), configuration, and whether Article 17 grounds apply.

| GDPR requirement | Source | What compliance requires in practice | Google-described Gemini/Workspace behaviour | Potential gap/risk if admins cannot delete individual chats |
|---|---|---|---|---|
| Rectify inaccurate personal data without undue delay; allow completion via supplementary statement | Art 16 citeturn20view0 | Ability to correct inaccurate personal data stored in transcripts/metadata, or add a supplementary statement; often paired with restriction while verifying accuracy | Google provides a “request” pathway for correcting inaccurate personal data in Gemini responses (general Gemini Apps context); Workspace admins control activity settings for work accounts citeturn3view0turn10view0 | If chat transcripts persist and cannot be annotated/exported/restricted effectively, practical rectification may be hard; however, “supplementary statement” can mitigate if the system supports an audit trail approach citeturn20view0turn22search5 |
| Erase personal data without undue delay when Art 17 grounds apply | Art 17(1) citeturn19view1 | Ability to delete specific personal data items promptly, unless an exception applies | Gemini app history: default ON with 18-month retention; admins can set auto-delete (3/18/36) or turn history OFF (72-hour retention) citeturn8view0turn10view0 | If retention is only on fixed schedules and does not allow targeted early deletion for valid requests, controller may be unable to comply “without undue delay” in some cases citeturn19view1turn18search8 |
| Recognise and apply Art 17 exceptions (legal obligation, legal claims, etc.) | Art 17(3) citeturn19view1 | Controllers must assess whether erasure must be refused/limited; if so, consider restriction instead and document reasoning | Google describes retention modes (72 hours / months) mainly as product settings; does not itself decide the controller’s Art 17 assessment for customer-controlled processing citeturn8view0turn10view0 | A rigid inability to delete might be *over*-retentive (risking storage limitation) or *under*-retentive (if legal hold is needed). Where exceptions apply, restriction (Art 18) may be needed citeturn22search5turn18search5 |
| Facilitate rights exercise; respond within one month; explain refusals | Art 12 citeturn18search8 | A DSAR workflow that can locate, evaluate, and act on Gemini chat personal data quickly | Google says end users cannot override admin conversation settings; admins can pre-configure history/retention settings citeturn8view0turn10view0 | If the organisation cannot export/search/delete Gemini conversations efficiently, DSAR response timeliness and completeness are at risk citeturn18search8turn19view3 |
| Storage limitation and accuracy principles | Art 5(1)(d)–(e) citeturn18search5 | Keep data no longer than necessary; erase/rectify inaccuracies without delay | Default retention described as 18 months (Gemini app history); option for 3 months or 72 hours if history OFF citeturn8view0turn10view0 | Default 18 months may be hard to justify for many enterprise prompt logs unless there is a defined purpose; inability to shorten below 3 months (if true in practice) can create minimisation/storagelimit tension citeturn18search5turn19view3 |
| Processor assistance for rights requests | Art 28(3)(e) citeturn19view2 | If Google is processor, it must provide assistance measures “insofar as possible” | Google CDPA: deletion enabled consistent with service functionality; roles: Google processor, customer controller citeturn5view3turn5view1 | If service functionality does not support necessary deletion actions, customers may argue processor assistance is inadequate; EDPB guidance emphasises processors’ direct obligations citeturn25view0turn19view2 |
| Data protection by design and accountability | Arts 24–25 citeturn19view3 | Choose/configure tools so GDPR compliance is achievable; document and review controls | Google provides admin settings for conversation history/retention; EDPB ChatGPT TF says technical impossibility cannot justify noncompliance citeturn8view0turn25view2turn19view3 | If “no deletion” is a structural limitation, the safer position is to disable or minimise retention; otherwise the organisation may be seen as failing to implement appropriate measures citeturn19view3turn25view2 |

### A reasoned conclusion on “potential GDPR violation”

On the information Google publishes, the **existence of fixed retention settings (72 hours / 3–36 months / default 18 months)** is not automatically unlawful. GDPR allows retention where (a) there is a lawful basis and purpose, and (b) retention is limited to what is necessary under storage limitation. citeturn18search5turn19view1turn8view0

However, if in practice Workspace admins **cannot delete** specific Gemini chat sessions when required (for example, to comply with a valid Article 17 erasure request that should be actioned sooner than the configured auto-delete window), that can create a **material compliance risk**:

* For the customer as controller: risk of failing to comply with Articles 17/16/12/24/25. citeturn19view1turn20view0turn18search8turn19view3  
* For Google as processor (in core-service mode): risk that processor assistance (Article 28(3)(e)) and the customer’s contractual expectations (“enable deletion consistent with functionality”) may be inadequate relative to GDPR compliance needs, especially given regulatory statements that technical constraints are not a blanket excuse. citeturn19view2turn5view1turn25view2turn25view0  
* For additional-service use cases: risk that role allocation and rights request routing are unclear, and that the organisation may inadvertently become a joint controller for enabling/benefiting from Google’s processing (as joint-controller case law indicates in analogous platform settings). citeturn10view0turn31view1

Accordingly, the inability to delete chats is best characterised as a **high-impact governance and compliance risk** that could become a GDPR infringement **in particular factual scenarios**, rather than a per se automatic GDPR breach in every case.

## Practical recommendations for organisations and administrators

### Establish which “Gemini” you are actually operating

Create a short internal position paper (and keep it current) identifying:

* Whether users are using **Gemini in Workspace apps**, the **Gemini app**, or both. citeturn1view2turn11view0  
* Whether the Gemini app is a **core service** or an **additional service** for your licences, and whether enterprise-grade data protections apply (Google describes a shield icon indicator and licence-dependent handling). citeturn10view0turn11view0  
* Whether any Workspace extensions (“connect to Workspace apps”) are enabled for Gemini app usage, because this changes what data can flow into prompts/responses. citeturn10view0turn11view0

This classification drives your controller/processor mapping and your DSAR playbook.

### Configure retention to minimise risk, not to satisfy defaults

Where your use case does not need long-lived prompt logs:

* Consider switching **Gemini app conversation history OFF** (72-hour retention mode) for most users, keeping it ON only for a narrowly defined cohort with a documented need. citeturn8view0turn10view0turn3view1  
* If history must remain ON, set **retention to the shortest available window (3 months)** unless you can justify a longer period under necessity and proportionality. citeturn8view0turn10view0turn18search5  
* Document the purpose of retaining Gemini chat history at all (for example, user productivity continuity, auditability, quality control), and link it to your storage limitation analysis. citeturn18search5turn19view3

Be explicit that turning history from ON to OFF does not necessarily purge prior stored chats immediately; Google states prior history remains stored for the configured retention period. citeturn8view0

### Build a DSAR workflow that does not depend on UI deletion

Assume a data subject request may require targeted removal within weeks, not months. Prepare a procedure that:

* Can identify whether the request relates to **Workspace content** (documents/emails) or **Gemini app conversation history**. citeturn1view2turn10view0  
* Uses Article 18 **restriction** as a managed fallback where immediate erasure is contested or not technically available and you are verifying accuracy or assessing legal claims. citeturn22search5turn19view1  
* Includes an escalation route to Google’s **Cloud Data Protection Team** or support channels where you believe processor assistance is needed to comply (consistent with the CDPA’s “instructions” framework and Google’s GDPR support posture). citeturn12view0turn5view3  
* Tracks deadlines and communications under Article 12, including extensions and refusal reasoning. citeturn18search8

If you cannot technically comply in time, treat this as a compliance incident requiring remediation (configuration change, service disablement, contract escalation), not as a routine refusal.

### Treat rectification in chat logs as a records-management problem

For many enterprise “chat transcript” disputes:

* If the transcript is accurate as a record of what was said, but contains *inaccurate assertions about someone*, you may be better positioned to (a) restrict processing/use, (b) attach a supplementary statement, and/or (c) erase the relevant processing where Article 17 grounds apply. citeturn20view0turn22search5turn19view1  
* Where inaccurate personal data appears in Gemini-generated output, consider user-facing correction mechanisms (Google provides a “report legal issue / help centre request” path for correcting inaccurate data in responses in the Gemini Apps context). citeturn3view0

### Update governance controls for prompt content

Because prompts can easily include special category data or HR-sensitive content:

* Update your acceptable use policy to prohibit entering sensitive personal data into Gemini unless there is a defined lawful basis, a defined purpose, and appropriate safeguards.
* Provide training and just-in-time guidance that “prompt text is data processing,” and run awareness campaigns analogous to secure email handling.
* Consider technical controls such as DLP patterns and endpoint guidance where feasible (especially for regulated sectors).

These steps support Articles 24–25 accountability and privacy by design. citeturn19view3turn18search5

### Re-evaluate vendor/feature choice if deletion remains impossible

If (after confirming actual product behaviour in your tenant) you cannot delete required data when rights grounds apply, then risk-based options include:

* Disabling the Gemini app entirely for Workspace users where you cannot justify retention or comply with rights obligations. (Google describes admin controls to turn the Gemini app on/off for users.) citeturn2view3turn11view0  
* Restricting Gemini usage to **Gemini in Workspace apps** features where Google describes no retention of prompts/responses beyond the session, leaving only standard Workspace content to manage. citeturn1view2turn4view0  
* Selecting alternative AI tooling that provides admin-grade targeted deletion, if your DSAR risk profile is high.

Given the EDPB’s position that technical impossibility is not a blanket excuse, “we can’t delete because the product won’t let us” is an increasingly weak posture, particularly for high-risk processing. citeturn25view2turn19view3