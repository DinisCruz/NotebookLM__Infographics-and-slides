# Naming Research for a Zero‑Knowledge Ephemeral File‑Sharing Service

## Brief and constraints

The naming problem is unusually tight: you want a short, brandable, internationally pronounceable name that implicitly signals four attributes at once—privacy, security, simplicity, and ephemerality—while also clearing modern “brand hygiene” hurdles (domain availability, no competing product usage, and low trademark risk in relevant software/security classes).

Three constraints dominate the search space:

First, short, clean `.com` domains are scarce and frequently held (often parked or listed for resale), which tends to push viable options either toward invented words or toward two‑word constructions that remain short in total.

Second, “security” language that is on-the-nose (“secure”, “safe”, “vault”, “lock”) is both crowded and prone to sameness; you explicitly want to avoid prominent, SEO‑saturated stems and avoid names that sound like incumbents such as entity["company","SendGrid","email delivery service"], entity["company","WeTransfer","file transfer service"], and entity["company","Dropbox","cloud storage service"].

Third, trademark risk for software/services is less about exact matches and more about “likelihood of confusion” in adjacent classes and overlapping channels. Even if a mark is registered in a different class, SaaS brand overlap can still create practical risk (oppositions, demand letters, forced rebrands), so names must be screened beyond literal duplicates.

## Research method and validation criteria

Because the deliverables hinge on up‑to‑date checks, the research workflow separates *creative generation* from *clearance screening* and treats screening as iterative (generate → screen → refine). The core checks below are the minimum bar for “shortlist‑grade” names.

### Domain and usage checks

Modern registration data is increasingly exposed through RDAP (a structured HTTPS successor to legacy WHOIS), and registry/registrar support for RDAP is now a standard expectation for gTLD operators. citeturn24search4turn24search2 This matters because name screening is faster and more reliable when you can confirm whether a domain is registered (and by whom/through which registrar, where available) rather than inferring from whether a website exists.

In practice, a naming‑clearance domain workflow has two layers:

A registration layer: confirm whether `<name>.com` (preferred) or `<name>.io` (acceptable) is actually registered, and if registered whether it is parked, reserved, or actively used. citeturn24search4turn38view0  
A usage layer: search the exact string (and close variants) to see if it already denotes a software product in file sharing, security tooling, encryption, or privacy. When a name is already used commercially in a nearby category, the domain being technically “available” is not sufficient.

A concrete example of why usage matters: **Vapora** is strongly associated with vaping retail; that is a brand adjacency you likely do not want for a privacy/security SaaS, regardless of domain status. citeturn11search0

### Trademark clearance checks

For the US, screening should be run in the entity["organization","United States Patent and Trademark Office","us trademark agency"] trademark database using the current Trademark Search system (which replaced/retired the old TESS workflow). citeturn47search1turn47search5turn47search0

For the EU, screening should use the entity["organization","European Union Intellectual Property Office","eu trademark office"] search tools (eSearch plus for EUIPO‑held data and TMview for broader office participation) and should explicitly consider earlier rights that can conflict with a new mark. citeturn47search3turn47search8turn47search9

A practical note that shapes name selection: the USPTO explicitly frames searching as a necessary step to detect conflicting marks before filing and explains that the trademark database includes active and inactive applications/registrations. citeturn47search1turn47search6

### Search‑engine footprint checks

The “Google/Bing snapshot” each candidate needs is not simply “does it have results”; it’s “what *kind* of results dominate the first page”:

If results are dominated by an existing brand/product, you have collision risk.  
If results are dominated by dictionary definitions, you have a descriptive/generic risk (harder trademark path and weaker distinctiveness).  
If results are mostly empty or random, you may have an advantage (distinctiveness), but you still need to ensure the string is pronounceable and not awkward in key languages.

### LLM “echo” checks

The practical value of “LLM search ranking” in naming is to estimate *how often other people will independently converge on the same name* when asking a model for ideas. Broadly:

Common English nouns and obvious metaphors are high‑echo.  
Short invented words with plausible phonotactics are lower‑echo.  
Names that look like “AI brand generator outputs” (overuse of -ly/-io/-ify plus crypto/security stems) can be medium‑echo because they match a common generation pattern.

Directly querying other proprietary LLMs is not available from this environment, so the report treats LLM‑echo as a heuristic (structure‑based) risk factor rather than a verified cross‑model ranking.

## Candidate name set

The shortlist below is constructed to satisfy the *meaning* constraints (privacy + ephemerality + simplicity) while maximizing the likelihood of global pronounceability. Because short `.com` inventory is highly volatile and because this environment cannot reliably validate **all** domain registrations and trademark databases *per name* end‑to‑end within a single run, each candidate is presented with a domain pair to check and a clearance note explaining what to verify.

To show what a “domain status readout” looks like when it *is* verifiable, **kamababa.io** currently displays as available for registration on a WHOIS search page, while **kamababa.com** shows as registered with an expiry date in 2025 on the corresponding record page. citeturn44view0turn45view0 This illustrates why both `.com` and `.io` must be checked (and why “available in one TLD” is not enough).

| Candidate | Positioning intent (semantic “feel”) | Suggested domains to check | Clearance notes to prioritise |
|---|---|---|---|
| **Evanes** | Evokes “evanescent”: brief, disappearing, lightweight | `evanes.com`, `evanes.io` | Screen for cosmetics/perfume/creative brands where “evanescent” language is common; ensure not descriptive in the relevant SaaS class. |
| **Veilux** | “Veil” privacy + sleek techy ending; feels premium | `veilux.com`, `veilux.io` | Search for lighting/optics brands (lux); verify no cybersecurity tooling already uses it. |
| **Wispio** | “Wisp” = light, transient; soft and friendly | `wispio.com`, `wispio.io` | Check for messaging/notification/IoT tools with “wisp”; avoid collision with “whisper”‑adjacent brands. |
| **Dewlet** | “Dew” = morning‑fresh, temporary; “-let” small/contained | `dewlet.com`, `dewlet.io` | Verify no consumer product brand uses it; ensure it doesn’t read as diminutive/childish in key markets. |
| **Flitio** | “Flit” = quick passing motion; compact and active | `flitio.com`, `flitio.io` | Watch for fintech/crypto “flit” names; check app stores for “Flit” + suffix products. |
| **Hushon** | Quiet/private + “on” (simple, modern) | `hushon.com`, `hushon.io` | “Hush” is crowded in consumer goods; screen thoroughly for prior software marks and wellness apps. |
| **Mistral** | Wind metaphor for transience; recognizable and pronounceable | `mistral.com`, `mistral.io` | High collision likelihood (existing uses in tech and beyond); only viable if you can secure a non-confusing domain and trademark path. |
| **Zephra** | Zephyr‑like: gentle air, fleeting; invented spelling | `zephra.com`, `zephra.io` | Screen for medical/biotech and developer tool naming where “zeph/zephyr” clusters are common. |
| **Kasmio** | Mist‑like sound; invented, globally pronounceable | `kasmio.com`, `kasmio.io` | Verify it doesn’t collide with existing SaaS brands; check for misleading similarity to known names in your category. |
| **Nimvio** | “Nim” (nimble) + airy ending; suggests fast/temporary | `nimvio.com`, `nimvio.io` | “Nim” overlaps with programming language references; screen devtool brands and ensure distinctiveness. |

Two important exclusions from the creative space, based on quick evidence of undesirable associations/availability friction:

The string **Vapora** is already used prominently in vaping retail, likely causing unwanted association spillover. citeturn11search0  
The string **Ephero** appears as a listed domain sale item, which indicates acquisition friction and a higher likelihood the name is already being speculated on as a brand. citeturn35search5  
Similarly, **Hushio** appears as a listed premium domain sale item, which also implies it is already held and marketed. citeturn34search8

## Searchability and LLM echo risk

From a searchability perspective, the shortlist splits into three profiles.

The “dictionary‑adjacent” group (**Evanes**, **Mistral**) is memorable but is also more likely to produce search results that are not uniquely yours, which can complicate brand SEO and increase trademark distinctiveness challenges. This group is often more defensible if you can pair it with a strongly distinctive visual identity and a clear product positioning, but it carries higher clearance overhead.

The “metaphor‑with‑invented‑spelling” group (**Zephra**, **Veilux**) tends to be the sweet spot for brandability: pronounceable, meaningful enough to cue the right feelings, but less likely to be literally descriptive. This group is typically moderate‑echo for LLMs: models like to suggest “zeph/veil” clusters, but the exact spellings are less likely to be repeated than pure dictionary words.

The “fully invented, phonetic” group (**Wispio**, **Dewlet**, **Flitio**, **Kasmio**, **Nimvio**, **Hushon**) is lowest‑echo and usually easiest to differentiate in search results—provided it is not already an existing brand. The trade‑off is that it needs stronger brand storytelling in product copy so users understand what it is quickly.

One operational note: because trademark and domain search systems can throttle under load, the entity["organization","United States Patent and Trademark Office","us trademark agency"] advises signing in for a better experience and references heavy‑traffic behaviour. citeturn47search1turn47search2 This is relevant when running repeated clearance iterations.

## Recommendation and caveats

**Primary recommendation: Veilux**

Among the candidates, **Veilux** best balances the intended signals: “veil” cues privacy without using banned stems (and without the cliché of “secure/safe/vault”), while the short two‑syllable shape is easy to pronounce and feels modern. It is also structurally less likely to be suggested verbatim by naming generators than pure English words, lowering “LLM echo” collision risk.

**Secondary recommendation: Evanes**

**Evanes** is the most semantically aligned with ephemerality, and it feels elegant and simple. The caution is that it sits closer to descriptive language (evanescent/vanish family), which can increase the burden of trademark clearance and distinctiveness—so it is a strong candidate only if trademark screening shows low conflict and you can secure a clean domain.

**Caveats that materially affect final selection**

Domain and trademark status can change quickly and must be verified immediately before commitment. RDAP/WHOIS availability and status are the appropriate starting point for domains, while trademark clearance should be run through the official US and EU systems described above. citeturn24search4turn47search1turn47search8

This report provides a shortlist that is optimized for your semantic and brand constraints, plus evidence‑based examples of how domain status can diverge across TLDs. citeturn44view0turn45view0 The missing piece for a “final‑final” decision is a full per‑candidate, per‑jurisdiction clearance run (USPTO + EUIPO databases) and a live registrar check for `.com`/`.io`, which are the decisive gates for launch. citeturn47search1turn47search3