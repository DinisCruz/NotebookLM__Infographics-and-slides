# LLM Brief: Execution Modes (Two-Pass Workflow)

> Instructions for using Executor and Reviewer modes to ensure quality and catch blind spots

---

## Overview

All content creation/modification tasks should use a **two-pass workflow**:

| Pass | Mode | Purpose |
|------|------|---------|
| 1 | **EXECUTOR** | Create or modify files following workflow guides |
| 2 | **REVIEWER** | Validate output against checklists, flag issues |

This pattern catches errors that single-pass execution misses.

---

## Why Two Passes?

| Single Pass Problem | Two-Pass Solution |
|---------------------|-------------------|
| Easy to miss checklist items while focused on creation | Dedicated validation pass with fresh focus |
| Accumulated assumptions go unchallenged | Explicit critical review challenges assumptions |
| No structured quality gate | Clear pass/fail criteria before completion |
| Errors compound in batch operations | Each item validated before moving on |

---

## EXECUTOR Mode

### Mindset
- Focus on **creation and completion**
- Follow the relevant workflow guide precisely
- Document what you created (for Reviewer)

### Output Requirements

At the end of EXECUTOR mode, produce a **Change Log**:

```markdown
## 📋 Change Log

**Task**: [What was requested]
**Files Modified/Created**:
- `path/to/file1.md` — [what was done]
- `path/to/file2.png` — [what was done]

**Workflow Guide Used**: [e.g., create-semantic-graph.md]

**Notes/Decisions**:
- [Any judgment calls made]
- [Anything unusual encountered]

**Ready for Review**: Yes
```

---

## REVIEWER Mode

### Mindset
- Shift to **critical evaluation**
- Assume EXECUTOR may have missed something
- Check against the literal checklist, not memory of what was intended

### Process

1. **Load the relevant checklist** from `review-checklists.md`
2. **Examine each file** independently (re-read, don't assume)
3. **Check each item** — mark pass (✅), fail (❌), or warning (⚠️)
4. **Document findings** in a Review Report

### Review Report Template

```markdown
## 📊 Review Report

**Reviewing**: [Task/files from Change Log]
**Checklist Used**: [e.g., SEMANTIC-GRAPH.md Checklist]

### Results

| # | Check | Status | Notes |
|---|-------|--------|-------|
| 1 | Navigation links at top | ✅ | |
| 2 | Summary is 2-4 sentences | ✅ | |
| 3 | Key Concepts has 4-6 bolded terms | ⚠️ | Only 3 concepts listed |
| ... | ... | ... | ... |

### Summary

- **Passed**: X/Y checks
- **Warnings**: Z items need attention
- **Failed**: N items must be fixed

### Required Fixes
1. [Specific fix needed]
2. [Specific fix needed]

### Recommendations (Optional)
- [Suggestions for improvement beyond checklist]
```

---

## Workflow Integration

### Standard Two-Pass Flow

```
User Request
    ↓
┌─────────────────────────────┐
│  🔨 EXECUTOR MODE           │
│  - Follow workflow guide    │
│  - Create/modify files      │
│  - Produce Change Log       │
└─────────────────────────────┘
    ↓
┌─────────────────────────────┐
│  🔍 REVIEWER MODE           │
│  - Load checklist           │
│  - Validate each file       │
│  - Produce Review Report    │
└─────────────────────────────┘
    ↓
[If failures] → Fix and re-review
[If pass] → Task complete
```

### How to Invoke

**Option 1: Automatic (Recommended)**

Include in your request:
> "Create SEMANTIC-GRAPH.md for [folder]. Use two-pass workflow."

Claude will automatically:
1. Execute the creation
2. Switch to Reviewer mode
3. Output both Change Log and Review Report

**Option 2: Explicit Passes**

First message:
> "EXECUTOR MODE: Create SEMANTIC-GRAPH.md for [folder]"

Second message:
> "REVIEWER MODE: Validate the changes against the checklist"

**Option 3: Review Only**

For existing files:
> "REVIEWER MODE: Validate [path/to/SEMANTIC-GRAPH.md] against checklist"

---

## Batch Operations

For bulk tasks (e.g., "Create SEMANTIC-GRAPH.md for all folders in to-publish/"):

```
For each folder:
    1. EXECUTOR: Create file
    2. REVIEWER: Validate
    3. Log result
    4. Continue to next (even if issues found)

At end:
    - Summary of all results
    - List of folders needing fixes
```

This prevents one error from blocking the entire batch while still tracking issues.

---

## Mode Indicators

When operating in each mode, Claude should prefix significant outputs:

**EXECUTOR MODE:**
```
## 🔨 EXECUTOR: Creating SEMANTIC-GRAPH.md

[work output]

## 📋 Change Log
[structured log]
```

**REVIEWER MODE:**
```
## 🔍 REVIEWER: Validating SEMANTIC-GRAPH.md

[validation process]

## 📊 Review Report
[structured report]
```

---

## Limitations & Mitigations

| Limitation | Mitigation |
|------------|------------|
| Same context — Reviewer knows Executor's thinking | Explicitly re-read files; check against literal checklist text |
| Sequential — slower than parallel agents | Batch with per-item review; accept the tradeoff for quality |
| May self-bias toward passing | User can request "strict review" or "assume something is wrong" |

### Strict Review Mode

For critical content, request:
> "Use two-pass workflow with STRICT REVIEW — assume at least one thing is wrong and find it."

This prompts more aggressive fault-finding.

---

## Quick Reference

| Command | Effect |
|---------|--------|
| "Use two-pass workflow" | Auto Executor → Reviewer |
| "EXECUTOR MODE:" | Explicit creation pass |
| "REVIEWER MODE:" | Explicit validation pass |
| "STRICT REVIEW" | Aggressive fault-finding |
| "Review only [file]" | Skip execution, just validate existing |
