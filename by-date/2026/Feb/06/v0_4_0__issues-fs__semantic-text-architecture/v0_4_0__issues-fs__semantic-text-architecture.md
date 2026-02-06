# Semantic Text Architecture: Text as Graph

**Document:** issues-fs__semantic-text-architecture  
**Version:** v1.0  
**Date:** 2026-02-05  
**Status:** Draft  
**Depends On:** issues-fs__thinking-in-graphs v1.0, issues-fs__lexicon-architecture v2.0  

---

## Executive Summary

This document defines the architecture for treating text as a graph within the Issues-FS ecosystem. The core insight is that the "meaning through connectivity" principle — which we apply to issues, roles, and workflows — must also apply to the text content within those structures. A title, description, paragraph, or document is not just a string; it is (or can be) an Issues-FS graph whose nodes are semantic concepts and whose edges are the relationships between them.

This architecture enables: semantic validation (does the text match its claimed structure?), change detection at the meaning level (not just text diff), hallucination detection in LLM transformations, translation validation across languages and cultures, and ultimately, testable content.

---

## Part 1: The Problem with Text as Strings

### Text Is Opaque to Graph Analysis

Currently, an issue in Issues-FS has a `title` and `description`. These are strings. They sit in the graph as leaf nodes — terminal values that cannot be further traversed.

```
Issue-42
    ├── type ──→ Bug
    ├── status ──→ open
    ├── title ──→ "Login fails on mobile devices"    ← Opaque string
    └── description ──→ "Users report that..."       ← Opaque string
```

The graph knows that Issue-42 has a title. It doesn't know what the title *means*. It can't verify that the title is consistent with the type (Bug vs Feature). It can't extract the concepts mentioned (login, mobile, failure). It can't link those concepts to definitions in the Lexicon.

### Inconsistency Hides in Plain Sight

Consider this issue:

```
Issue: Bug-123
Type: Bug
Title: "Add support for dark mode"
Description: "Users have requested a dark mode theme option for the UI. 
              This would improve accessibility and reduce eye strain."
```

A human immediately sees the problem: the type says "Bug" but the content describes a feature request. But the graph can't see this — the title and description are just strings. The inconsistency is invisible to automated analysis.

### The Same Problem at Every Scale

This isn't just about issue titles. The problem recurses:

- A **risk description** mentions threat actors, vulnerabilities, and remediations — but none of these are linked to anything
- A **document section** claims "X is effective" — but there's no connection to evidence
- A **paragraph** references "the attacker" — but which attacker? From what threat model?
- A **translated document** says something slightly different — but how different? Is the meaning preserved?

At every level, text is a black box. The graph stops at the string boundary.

### The Solution: Text Becomes Graph

The architectural response is direct: **text is a graph**. Every piece of text — title, description, paragraph, section, document — can be represented as an Issues-FS graph. The nodes are semantic concepts extracted from the text. The edges are relationships between those concepts. And those nodes and edges link to the broader ecosystem: the Lexicon, domain-specific ontologies, external references.

```
Issue-42
    ├── type ──→ Bug
    ├── status ──→ open
    ├── title ──→ Title-Graph-42
    │               ├── describes ──→ Failure-Event
    │               │                    ├── affects ──→ Login-Function
    │               │                    │                  └── links_to ──→ Auth-Ontology:Login
    │               │                    └── context ──→ Mobile-Device
    │               │                                       └── links_to ──→ Platform-Ontology:Mobile
    │               └── anchor_link ──→ Lexicon:anchor__bug_title
    │
    └── description ──→ Description-Graph-42
                          ├── reports ──→ User-Report
                          ├── describes ──→ Failure-Event (same node as title)
                          └── ...
```

Now the graph can see: the title describes a failure event affecting login on mobile. The description also references the same failure event. Both are consistent with type "Bug." The concepts link to domain ontologies. The graph is no longer blind to meaning.

---

## Part 2: The Fractal Document Model

### Documents as Graphs of Graphs

A document is not a monolithic blob of text. It has structure: sections, paragraphs, sentences. The fractal document model represents this structure as a hierarchy of Issues-FS graphs, each linking to its children and parents.

```
Document-Graph (highest level: summary/overview)
    │
    ├── links_to ──→ Introduction-Graph
    │                   └── contains ──→ [Paragraph-Graphs]
    │
    ├── links_to ──→ Section-1-Graph
    │                   ├── title ──→ Section-1-Title-Graph
    │                   ├── contains ──→ Paragraph-1-Graph
    │                   │                   ├── contains ──→ Sentence-Graphs
    │                   │                   └── concepts ──→ [Concept-Nodes]
    │                   ├── contains ──→ Paragraph-2-Graph
    │                   └── contains ──→ Paragraph-3-Graph
    │
    ├── links_to ──→ Section-2-Graph
    │                   └── ...
    │
    └── links_to ──→ Conclusion-Graph
```

Each level is a complete Issues-FS graph (can be stored as its own zip/database). They link to each other through explicit edges.

### Bidirectional: Expansion and Reduction

The hierarchy supports two directions of traversal:

**Expansion (zoom in):** Start at the document level, traverse down to sections, paragraphs, sentences, concepts. Each level provides more detail. "What does this document say about X?" → traverse to the paragraph-graph that contains X → see the full context.

**Reduction (zoom out):** Start at the paragraph level, traverse up to section, document. Each level is a projection/summary. The document-level graph captures the essence of the full document in a smaller structure.

```
Full Document (10,000 words)
    │
    │ [reduction: extract key concepts and claims]
    ▼
Document Summary Graph (captures: main thesis, key arguments, conclusions)
    │
    │ [reduction: further compress]
    ▼
Abstract Graph (one-paragraph equivalent)
    │
    │ [reduction: compress to title]
    ▼
Title Graph (captures: core topic in minimal nodes)
```

At each level, the graph is smaller but linked to its source. The title graph's claims are **linked to evidence** in the abstract graph, which is linked to evidence in the document summary, which is linked to the full paragraphs.

### Each Level Is an Issues-FS Database

A critical architectural choice: each level is a standalone Issues-FS database (or zip file). This means:

- **Independent storage:** A paragraph graph can be loaded without loading the entire document
- **Independent versioning:** Section-2's graph can change without affecting Section-1
- **Composability:** Combine paragraph graphs from different documents into a new analysis
- **Scalability:** Don't load detail you don't need

The connections between levels are edges that cross database boundaries (like the cross-graph references in the Thinking in Graphs document).

---

## Part 3: Ontology and Taxonomy First

### The Contract for Extraction

Before extracting a single concept from text, we define the **Ontology and Taxonomy (O&T)** for that unit. The O&T is the contract: it specifies what node types and edge types are allowed in the resulting graph.

This prevents graph sprawl. Without an O&T, extraction produces whatever the LLM decides is relevant — an unstructured mess of concepts with inconsistent granularity. With an O&T, extraction is constrained: every node must belong to a defined type, every edge must be a defined relationship.

**Example O&T for a Risk Description:**

```yaml
ontology: Risk-Description-v1
types:
  nodes:
    - Risk                    # The risk being described
    - Vulnerability           # Technical weakness
    - Threat_Actor            # Who might exploit it
    - Attack_Vector           # How they might exploit it
    - Impact                  # What happens if exploited
    - Remediation             # How to fix it
    - Asset                   # What's being protected
    
  edges:
    - exploits: Threat_Actor → Vulnerability
    - uses: Threat_Actor → Attack_Vector
    - affects: Vulnerability → Asset
    - causes: Vulnerability → Impact
    - mitigates: Remediation → Vulnerability
    - reduces: Remediation → Impact

constraints:
  - every Risk must have at least one Vulnerability
  - every Vulnerability should have at least one Remediation
  - Threat_Actor should link to a known threat model (MITRE ATT&CK, etc.)

links_to_lexicon:
  - Vulnerability → Lexicon:anchor__vulnerability → CWE
  - Threat_Actor → Lexicon:anchor__threat_actor → MITRE ATT&CK
  - Remediation → Lexicon:anchor__remediation → NIST Controls
```

### Reuse and Extension

O&Ts are fractal, just like everything else in the system:

**Reuse existing O&Ts:** If someone has already defined an O&T for risk descriptions (perhaps curated by security experts), use it. Don't reinvent.

**Extend for specific needs:** If your risk descriptions include concepts not in the standard O&T (e.g., "regulatory_requirement"), extend it:

```yaml
ontology: Risk-Description-Extended
extends: Risk-Description-v1
additional_types:
  nodes:
    - Regulatory_Requirement
  edges:
    - mandated_by: Remediation → Regulatory_Requirement
```

**Unit-specific additions:** Sometimes a specific document introduces concepts that don't fit any O&T. That's allowed — create unit-specific node types. But flag them: these are candidates for O&T expansion if they recur, or signals of drift if they shouldn't exist.

### Drift Detection

The O&T enables drift detection:

**Structural drift:** The O&T says every Risk should have a Remediation. This risk description has no Remediation node → structural drift.

**Vocabulary drift:** The extraction produces a node of type "Countermeasure" but the O&T only defines "Remediation" → vocabulary drift. Either the O&T needs updating, or the extraction is using inconsistent terminology.

**Scope drift:** The O&T for a risk description doesn't include "Business_Justification" but the extraction found one → the text is including content outside the expected scope.

Drift isn't necessarily wrong — it's information. The system surfaces drift for human review. Maybe the O&T is incomplete. Maybe the text is wandering off-topic. Either way, visibility into drift is valuable.

---

## Part 4: Multi-Pass Extraction

### Why Multiple Passes?

Extracting a complete semantic graph from text in one pass is error-prone. Different concerns require different focus. A single LLM prompt trying to extract entities, relationships, claims, evidence, and structure simultaneously will do all of them poorly.

Instead: multiple passes, each with a focused agent and a specific O&T subset.

### Example Pass Structure

**Pass 1: O&T Selection/Definition**
- Focus: What ontology governs this text?
- Agent: Selects from existing O&Ts or creates extensions
- Output: The O&T contract for subsequent passes

**Pass 2: Entity Extraction**
- Focus: Identify the key entities (nouns, concepts, actors)
- O&T subset: Node types only (no edges yet)
- Agent: Optimized for NER (Named Entity Recognition)
- Output: A graph with nodes, no edges

**Pass 3: Relationship Extraction**
- Focus: Identify relationships between entities from Pass 2
- O&T subset: Edge types
- Input: Pass 2 graph + original text
- Agent: Optimized for relation extraction
- Output: Pass 2 graph + edges

**Pass 4: Claim and Evidence Extraction**
- Focus: What claims does the text make? What evidence supports them?
- O&T subset: Claim, Evidence, Support_Relationship
- Input: Pass 3 graph + original text
- Agent: Optimized for argumentation analysis
- Output: Pass 3 graph + claim/evidence layer

**Pass 5: Lexicon Linking**
- Focus: Link extracted nodes to Lexicon anchors and external references
- O&T subset: Anchor links
- Input: Pass 4 graph
- Agent: Optimized for semantic matching
- Output: Pass 4 graph + anchor links

**Pass 6: Pruning and Consolidation**
- Focus: Remove low-confidence nodes, merge duplicates, simplify structure
- Input: Pass 5 graph
- Agent: Optimized for graph cleanup
- Output: Final semantic graph

**Pass 7: Validation**
- Focus: Check graph against O&T constraints
- Input: Pass 6 graph + O&T
- Agent: Validation rules engine
- Output: Validation report (complete, incomplete, gaps, drift)

### Each Pass Has Its Own Agent

Different passes may use different:

- LLM models (cheaper model for entity extraction, better model for claim analysis)
- Prompts (each pass has a focused prompt with specific instructions)
- Validation rules (each pass checks its specific O&T constraints)
- Human review triggers (flag uncertain extractions for human verification)

This is separation of concerns applied to semantic extraction.

---

## Part 5: Link Dynamics — Static vs Auto-Generated

### Two Types of Links

Not all edges in a semantic graph are equal:

**Static Links** — Created intentionally by a human. Represent deliberate connections. Persist unless explicitly removed.

```
Edge: Risk-1 --mitigated_by--> Remediation-2FA
    └── link_type: static
    └── created_by: security-analyst
    └── created_at: 2026-01-15
```

**Auto-Generated Links** — Extracted from text by LLM/NLP. Represent inferred connections from source content. Should be re-evaluated when source changes.

```
Edge: Risk-1 --describes_threat--> Attacker-Node
    └── link_type: auto-generated
    └── source: risk-description.md, paragraph 3
    └── source_span: [char 145, char 203]
    └── source_hash: sha256:abc123...
    └── extracted_by: semantic-text-service v1.2
    └── extracted_at: 2026-02-05T14:30:00Z
    └── confidence: 0.87
```

### Why the Distinction Matters

**Reactive workflows:** When source text changes, query for auto-generated links from that source. Re-run extraction. Compare new graph to old. Flag differences.

**Trust levels:** Static links are higher trust (human verified). Auto-generated links are provisional (may be wrong, may become stale).

**Stale link detection:** If the source paragraph is deleted but auto-generated links from it still exist → inconsistency. The graph is referencing content that no longer exists.

**Audit trails:** For compliance, you may need to distinguish between "a human said this risk has this remediation" vs "the system inferred this from the text."

### Reactive Workflows on Change

```
Workflow: On text change in document.md

1. Detect change (source_hash differs from stored hash)

2. Find affected edges:
   SELECT edges 
   WHERE link_type = 'auto-generated' 
   AND source = 'document.md'

3. For each affected edge:
   a. Re-run extraction on new source text
   b. Does this edge still exist in new extraction?
      - Yes, unchanged → update extracted_at, keep edge
      - Yes, changed → update edge, flag for review
      - No → mark edge as stale, flag for review

4. Check for new edges:
   - Edges in new extraction not in old graph → add, flag for review

5. Generate change report:
   - Edges added: [...]
   - Edges removed: [...]
   - Edges changed: [...]
   - Review required: yes/no
```

---

## Part 6: Change Detection as Graph Diff

### Beyond Text Diff

Traditional diff shows what text changed:

```diff
- Users report that login fails on mobile
+ Users report that login fails on mobile and tablet devices
```

This tells you the words changed. It doesn't tell you if the *meaning* changed.

### Graph Diff Shows Meaning Changes

Semantic graph diff shows what concepts and relationships changed:

```
Graph Diff: description-v1 → description-v2

Nodes Added:
  + Tablet-Device (type: Platform)
    └── evidence: "tablet devices" mentioned in v2

Edges Added:
  + Failure-Event --affects--> Tablet-Device

Nodes Unchanged:
  - Mobile-Device, Login-Function, Failure-Event

Edges Unchanged:
  - Failure-Event --affects--> Mobile-Device

Assessment:
  Semantic change: Bug scope expanded to include tablets.
  Impact: May affect test coverage, device matrix, timeline.
```

Now you know: the change matters. The bug scope expanded.

### Three Change Scenarios

**Text change, no meaning change:**
```
Text v1: "Users report that login fails"
Text v2: "Users have reported that login is failing"

Graph diff: No change (same Symptom, same Component)

Action: None. Cosmetic edit only.
```

**Text change, meaning change:**
```
Text v1: "login fails on mobile"
Text v2: "login fails on mobile and tablet"

Graph diff: Added Platform node (Tablet)

Action: Review scope expansion.
```

**No text change, meaning change (external drift):**
```
Text (unchanged): "This is a HIGH severity vulnerability"
External reference (CVSS 3.0 → 3.1): Score recalculated, now MEDIUM

Graph diff: Severity classification inconsistent with linked reference

Action: Review and update severity classification.
```

### Hallucination and Misrepresentation Detection

**Source document:**
```
Conclusion: "Treatment X showed NO statistically significant improvement"
  → Graph: Finding(subject: X, result: no_improvement, qualifier: not_significant)
```

**LLM-generated summary:**
```
Summary: "Research shows Treatment X improves outcomes"
  → Graph: Finding(subject: X, result: improvement, qualifier: none)
```

**Graph diff:**
```
Changed: Finding.result: no_improvement → improvement
Changed: Finding.qualifier: not_significant → none
Missing: Negation link

Assessment: Summary misrepresents source. 
  Source says "no improvement"; summary says "improvement".
  This is either hallucination or misrepresentation.
```

### Translation Validation

**English source:**
```
"The system MUST NOT store passwords in plain text"
  → Graph: Requirement(type: prohibition, modal: must, subject: password_storage)
```

**Japanese translation:**
```
"システムはパスワードを平文で保存すべきではない"
  → Graph: Requirement(type: recommendation, modal: should, subject: password_storage)
```

**Graph diff:**
```
Changed: Requirement.type: prohibition → recommendation
Changed: Requirement.modal: must → should

Assessment: Translation weakened a requirement.
  "MUST NOT" (prohibition) became "すべきではない" (should not).
  Compliance-critical change.
```

---

## Part 7: Version History as Graphs

### Every Version Is a Graph

```
Document-v1 (graph)
    │
    ├── diff ──→ v1-to-v2 Diff-Graph
    │               ├── added_nodes: [...]
    │               ├── removed_edges: [...]
    │               └── semantic_summary: "Scope expanded"
    │
Document-v2 (graph)
    │
    ├── diff ──→ v2-to-v3 Diff-Graph
    │               └── ...
    │
Document-v3 (graph)
```

Each version is a complete semantic graph. Diffs are also graphs (nodes for changes, edges for relationships between changes).

### Git-Like Operations on Semantic Graphs

```bash
# Semantic diff between versions
issues-fs semantic diff doc-v1.md doc-v2.md

# Semantic blame (who added this claim?)
issues-fs semantic blame doc.md --claim "X is effective"

# Semantic log (history of meaning changes)
issues-fs semantic log doc.md

# Semantic cherry-pick (copy a specific claim/structure)
issues-fs semantic cherry-pick Claim-15 --from v1 --to v2
```

### Action Triggers Based on Semantic Change

```yaml
workflow: on_semantic_change
trigger: graph_diff detected

rules:
  - if: new_claims_added
    then: trigger_evidence_review
    
  - if: requirements_changed
    then: trigger_compliance_review
    
  - if: security_scope_changed
    then: trigger_security_review
    
  - if: graph_unchanged  # Text changed but meaning didn't
    then: no_action
```

Text-only changes don't trigger heavy reviews. Meaning changes always do.

---

## Part 8: Integration with the Ecosystem

### Integration with Lexicon

Every extracted concept should (eventually) link to Lexicon anchor nodes:

```
Extracted: "Threat Actor"
    └── links_to ──→ Lexicon:anchor__threat_actor
                        └── links_to ──→ MITRE ATT&CK vocabulary
```

Without this link, "Threat Actor" is just a label. With it, the concept has verified meaning and can be compared across documents.

The O&T specifies required links:
```yaml
links_to_lexicon:
  required:
    - Threat_Actor → Lexicon:anchor__threat_actor
    - Vulnerability → Lexicon:anchor__vulnerability
  recommended:
    - Impact → Lexicon:anchor__impact
```

### Integration with Roles

Different roles have different extraction and validation focus:

| Role | Extraction Focus | Validation Focus |
|------|------------------|------------------|
| Dev | Technical terms, components | Technical accuracy |
| QA | Test cases, criteria | Completeness, testability |
| Architect | Decisions, trade-offs | Consistency with design |
| Librarian | Cross-references, citations | Documentation standards |
| Security | Threats, vulnerabilities | Compliance, coverage |

A document might go through multiple role-specific passes.

### Integration with CLI

```bash
# Extract semantic graph
issues-fs semantic extract document.md --ontology risk-description

# Validate against O&T
issues-fs semantic validate document.md

# Analyze connectivity
issues-fs semantic connectivity document.md

# Find gaps
issues-fs semantic gaps document.md

# Diff two versions
issues-fs semantic diff v1.md v2.md

# Check for drift
issues-fs semantic drift document.md --ontology risk-description
```

---

## Decisions Log

| # | Decision | Rationale |
|---|----------|-----------|
| ST1 | **Text is a graph, not a string** | Applying "meaning through connectivity" to content enables analysis that strings can't support. |
| ST2 | **O&T first, extraction second** | The O&T is a contract that prevents sprawl and enables drift detection. |
| ST3 | **Multi-pass extraction** | Different concerns require different focus. Single-pass extraction conflates concerns. |
| ST4 | **Static vs auto-generated links** | Enables reactive workflows, trust assessment, and stale link detection. |
| ST5 | **Each level is an independent database** | Fractal storage enables independent loading, versioning, and composition. |
| ST6 | **Graph diff, not text diff** | Meaning changes matter; surface changes often don't. Actions trigger on meaning. |
| ST7 | **Concepts must link to Lexicon** | Unlinked concepts are just labels. Links provide verified meaning. |
| ST8 | **Version history as graphs** | Enables semantic git operations: blame, log, diff at the meaning level. |

---

## References

- [Thinking in Graphs: Meaning Through Connectivity](./v0_4_0__issues-fs__thinking-in-graphs.md) — Foundational philosophy
- [Issues-FS Lexicon Architecture](./v0_4_0__issues-fs__lexicon-architecture-v2.md) — Anchor nodes and vocabulary
- [Issues-FS Architecture Overview](./v0_4_0__issues-fs__architecture-overview.md) — Ecosystem architecture

---

*Semantic Text Architecture v1.0*  
*Date: 2026-02-05*
