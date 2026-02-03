# (Brief #1) Git Repository Reader Service - Project Brief

**Version**: v0.2.26 (Initial Design)  
**Purpose**: Pure-Python Git repository inspection directly from `.git` folder  
**Foundation**: `osbot_utils.type_safe` (Type_Safe architecture with safe primitives)  
**Target**: Zero external dependencies—no `git` CLI, no `GitPython`, no `dulwich`  

---

## Executive Summary

Build a **type-safe Git repository reader** that parses Git's internal object database directly from the `.git` folder. This enables:

1. **Inspect** repository state without `git` binary or external libraries
2. **List** all branches with their current commit SHAs
3. **Navigate** commit history within any branch
4. **Extract** commit details: message, author, timestamp, files changed
5. **Maintain** full type safety using the Type_Safe architecture—no raw primitives

### Why Direct `.git` Reading?

| External Dependency Approach | Direct `.git` Reading |
|------------------------------|----------------------|
| Requires `git` CLI installed | Zero system dependencies |
| Subprocess overhead per operation | Native Python—fast, no IPC |
| GitPython/dulwich dependencies | Self-contained, minimal footprint |
| Opaque error messages | Full control over parsing and errors |
| Security: subprocess injection risk | No shell execution at all |

**Key Insight**: Git's object database is simple—zlib-compressed files with a well-documented format. We can read it directly with Python's stdlib (`zlib`, `struct`, `pathlib`).

### Why Type_Safe Architecture?

| Raw Python Approach | Type_Safe Approach |
|--------------------|-------------------|
| `sha: str` (any string allowed) | `sha: Safe_Str__SHA1` (exactly 40 hex chars) |
| `branches: list` (any contents) | `branches: List__Git__Branches` (enforced Schema__Git__Branch) |
| Silent corruption from bad data | Runtime validation on every operation |
| Manual dict parsing | Automatic JSON serialization with type preservation |

---

## Core Objectives

### MVP Scope (This Brief)

1. **List Branches** → Return all local branches with their tip commit SHAs
2. **List Commits** → Walk commit history for a given branch (with depth limit)
3. **Read Commit Details** → Parse message, author, committer, timestamp, parent(s)
4. **Get Files Changed** → Compare commit tree with parent tree to identify A/M/D files

### Out of Scope (Future)

- Remote branches (`refs/remotes/*`)
- Tags (`refs/tags/*`)
- Blob content retrieval (file contents)
- Pack file delta reconstruction (partial support only)
- Index/staging area parsing
- Writing to repository

---

## Primitive Analysis: Reuse vs Create

### Existing OSBot-Utils Primitives (Reuse Directly)

These primitives already exist in `osbot_utils` and are suitable for our needs:

| Primitive | Location | Purpose | Validation |
|-----------|----------|---------|------------|
| `Safe_Str__SHA1` | `primitives.domains.cryptography` | Full 40-char SHA | `^[a-fA-F0-9]{40}$` (case-insensitive) |
| `Safe_Str__SHA1__Short` | `primitives.domains.cryptography` | Short 7-char SHA | `^[a-fA-F0-9]{7}$` (exactly 7) |
| `Safe_Str__Git__Branch` | `primitives.domains.git` | Branch names | git-check-ref-format rules |
| `Safe_Str__Git__Ref` | `primitives.domains.git` | Generic ref (user input) | Accepts SHA, branch, or tag |
| `Safe_Str__Git__Tag` | `primitives.domains.git` | Tag names | git-check-ref-format rules |

**Analysis of `Safe_Str__SHA1`**:
```python
regex             = re.compile(r'^[a-fA-F0-9]{40}$')  # Case-insensitive hex
regex_mode        = Enum__Safe_Str__Regex_Mode.MATCH  # Full string match
exact_length      = True                              # Must be exactly 40
allow_empty       = True                              # Handles None gracefully
```
✅ Perfect for commit SHA, tree SHA, blob SHA, parent SHA

**Analysis of `Safe_Str__SHA1__Short`**:
```python
regex = re.compile(r'^[a-fA-F0-9]{7}$')               # Exactly 7 chars only
```
⚠️ Limitation: Git short SHAs can be 7-12 chars, but this only accepts 7. 
   Acceptable for MVP—we'll use for display purposes only.

**Analysis of `Safe_Str__Git__Branch`**:
```python
# Extends Safe_Str__Git__Ref_Base with:
# - Cannot start with dash (branch-specific rule)
# - Full git-check-ref-format validation
```
✅ Perfect for branch names from `refs/heads/*`

### New Primitives (Must Create)

These primitives don't exist and are needed for the Git Reader:

| Primitive | Purpose | Constraints |
|-----------|---------|-------------|
| `Safe_Str__Git__Commit_Message` | Commit message body | Max 64KB, preserve formatting |
| `Safe_Str__Git__Tree_Path` | File path in tree entries | Max 4096 chars, allow most chars |
| `Safe_Str__Git__Author_Line` | "Name <email> ts tz" format | Max 1024 chars |

```python
# ═══════════════════════════════════════════════════════════════════════════════
# New Primitives to Create
# ═══════════════════════════════════════════════════════════════════════════════

class Safe_Str__Git__Commit_Message(Safe_Str):           # Commit message body
    max_length      = 65536                              # 64KB - git allows large messages
    allow_empty     = True                               # Empty messages are valid
    trim_whitespace = False                              # Preserve message formatting!

class Safe_Str__Git__Tree_Path(Safe_Str):                # File/dir path in tree
    max_length      = 4096                               # PATH_MAX on most systems
    allow_empty     = False                              # Tree entries must have names
    trim_whitespace = True
    # Note: Git allows almost any bytes in paths except NUL
    # We'll be permissive since we're reading, not writing

class Safe_Str__Git__Author_Line(Safe_Str):              # "Name <email> 1234567890 +0000"
    max_length      = 1024                               # Reasonable limit
    allow_empty     = True
    trim_whitespace = True
```

### Import Map for Implementation

```python
# ═══════════════════════════════════════════════════════════════════════════════
# Reuse from existing OSBot-Utils
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.cryptography.safe_str.Safe_Str__SHA1        import Safe_Str__SHA1
from osbot_utils.type_safe.primitives.domains.cryptography.safe_str.Safe_Str__SHA1__Short import Safe_Str__SHA1__Short
from osbot_utils.type_safe.primitives.domains.git.safe_str.Safe_Str__Git__Branch          import Safe_Str__Git__Branch
from osbot_utils.type_safe.primitives.domains.git.safe_str.Safe_Str__Git__Ref             import Safe_Str__Git__Ref

# ═══════════════════════════════════════════════════════════════════════════════
# New primitives (to be created in osbot_utils.helpers.git.primitives)
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.helpers.git.primitives.Safe_Str__Git__Commit_Message import Safe_Str__Git__Commit_Message
from osbot_utils.helpers.git.primitives.Safe_Str__Git__Tree_Path      import Safe_Str__Git__Tree_Path
from osbot_utils.helpers.git.primitives.Safe_Str__Git__Author_Line    import Safe_Str__Git__Author_Line
```

### Optional Enhancement: Variable-Length Short SHA

Current `Safe_Str__SHA1__Short` is exactly 7 chars. Git actually supports 7-12 chars for disambiguation. 

**Suggested change** (non-blocking for MVP):
```python
# Current:
regex = re.compile(r'^[a-fA-F0-9]{7}$')     # Exactly 7

# Suggested:
regex = re.compile(r'^[a-fA-F0-9]{7,12}$')  # 7-12 chars
```

---

## Technical Background

### Git Object Model

Git stores everything as **objects** in `.git/objects/`:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Git Object Database                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  .git/                                                                       │
│  ├── HEAD                    # "ref: refs/heads/main" or direct SHA          │
│  ├── refs/                                                                   │
│  │   └── heads/                                                              │
│  │       ├── main            # Contains: 40-char SHA of tip commit           │
│  │       ├── develop         # Contains: 40-char SHA of tip commit           │
│  │       └── feature-x       # Contains: 40-char SHA of tip commit           │
│  ├── objects/                                                                │
│  │   ├── ab/                 # First 2 chars of SHA                          │
│  │   │   └── cdef1234...     # Remaining 38 chars (zlib compressed)          │
│  │   └── pack/               # Packed objects (efficiency optimization)      │
│  │       ├── pack-xxx.pack   # Compressed object data                        │
│  │       └── pack-xxx.idx    # Index for fast SHA lookup                     │
│  └── packed-refs             # Fallback for refs not in refs/heads/          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Object Types and Formats

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          Git Object Types                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌──────────┐     Zlib decompress     ┌──────────────────────────────────┐  │
│  │ Raw File │  ─────────────────────▶ │ "{type} {size}\x00{content}"     │  │
│  └──────────┘                         └──────────────────────────────────┘  │
│                                                                              │
│  Type: COMMIT                                                                │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ tree {tree_sha}                                                      │   │
│  │ parent {parent_sha}               ◀── 0 or more parent lines         │   │
│  │ author {name} <{email}> {timestamp} {tz}                             │   │
│  │ committer {name} <{email}> {timestamp} {tz}                          │   │
│  │                                   ◀── blank line                     │   │
│  │ {commit message}                  ◀── rest is message                │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  Type: TREE (binary format)                                                  │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ {mode} {name}\x00{20-byte-sha}    ◀── repeated for each entry        │   │
│  │ {mode} {name}\x00{20-byte-sha}                                       │   │
│  │ ...                                                                  │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  Mode Values:                                                                │
│    100644 = regular file                                                     │
│    100755 = executable file                                                  │
│    120000 = symlink                                                          │
│    40000  = subdirectory (tree)                                              │
│                                                                              │
│  Type: BLOB                                                                  │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ {raw file contents}               ◀── just the bytes                 │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Reading Flow: HEAD → Commit → Files Changed

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     Data Flow: Get Latest Commit Details                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌────────────────┐                                                          │
│  │ .git/HEAD      │                                                          │
│  │ "ref: refs/... │                                                          │
│  └───────┬────────┘                                                          │
│          │ read symref                                                       │
│          ▼                                                                   │
│  ┌────────────────────┐                                                      │
│  │ .git/refs/heads/   │                                                      │
│  │ main               │                                                      │
│  │ "abc123def456..."  │◀── 40-char SHA                                       │
│  └───────┬────────────┘                                                      │
│          │ read SHA                                                          │
│          ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │ .git/objects/ab/c123def456...                                      │     │
│  │                                                                    │     │
│  │  zlib.decompress() ──▶  "commit 299\x00tree {tree_sha}\n           │     │
│  │                                         parent {parent_sha}\n      │     │
│  │                                         author ...\n               │     │
│  │                                         \n                         │     │
│  │                                         {message}"                 │     │
│  └───────┬────────────────────────────────────────────────────────────┘     │
│          │ parse commit                                                      │
│          ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │ Schema__Git__Commit                                                │     │
│  │   sha         : Safe_Str__SHA1            (40-char full)           │     │
│  │   sha_short   : Safe_Str__SHA1__Short     (7-char display)         │     │
│  │   tree_sha    : Safe_Str__SHA1                                     │     │
│  │   parent_shas : List__Git__SHAs                                    │     │
│  │   author      : Schema__Git__Author_Info                           │     │
│  │   committer   : Schema__Git__Author_Info                           │     │
│  │   message     : Safe_Str__Git__Commit_Message                      │     │
│  └───────┬────────────────────────────────────────────────────────────┘     │
│          │                                                                   │
│          │ compare tree_sha vs parent's tree_sha                             │
│          ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │ Flatten Trees → Dict[path, blob_sha]                               │     │
│  │                                                                    │     │
│  │   Current:  {"src/main.py": "aaa...", "README.md": "bbb..."}       │     │
│  │   Parent:   {"src/main.py": "ccc...", "old.txt": "ddd..."}         │     │
│  │                                                                    │     │
│  │   Diff:                                                            │     │
│  │     M src/main.py    (SHA changed: ccc → aaa)                      │     │
│  │     A README.md      (not in parent)                               │     │
│  │     D old.txt        (not in current)                              │     │
│  └───────┬────────────────────────────────────────────────────────────┘     │
│          │                                                                   │
│          ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │ List__Git__File_Changes                                            │     │
│  │   [Schema__Git__File_Change(path="src/main.py", status=MODIFIED),  │     │
│  │    Schema__Git__File_Change(path="README.md", status=ADDED),       │     │
│  │    Schema__Git__File_Change(path="old.txt", status=DELETED)]       │     │
│  └────────────────────────────────────────────────────────────────────┘     │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Type_Safe Schema Design

### Safe Primitive Types

**Reusing Existing Primitives:**
```python
# ═══════════════════════════════════════════════════════════════════════════════
# From osbot_utils.type_safe.primitives.domains.cryptography
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.cryptography.safe_str.Safe_Str__SHA1        import Safe_Str__SHA1
from osbot_utils.type_safe.primitives.domains.cryptography.safe_str.Safe_Str__SHA1__Short import Safe_Str__SHA1__Short

# Safe_Str__SHA1:       ^[a-fA-F0-9]{40}$  - Full 40-char SHA (commit, tree, blob)
# Safe_Str__SHA1__Short: ^[a-fA-F0-9]{7}$  - Short 7-char SHA (display)

# ═══════════════════════════════════════════════════════════════════════════════
# From osbot_utils.type_safe.primitives.domains.git
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.domains.git.safe_str.Safe_Str__Git__Branch import Safe_Str__Git__Branch
from osbot_utils.type_safe.primitives.domains.git.safe_str.Safe_Str__Git__Ref    import Safe_Str__Git__Ref

# Safe_Str__Git__Branch: git-check-ref-format compliant branch names
# Safe_Str__Git__Ref:    Accepts SHA, branch, or tag (for user input)
```

**New Primitives to Create:**
```python
# ═══════════════════════════════════════════════════════════════════════════════
# New Git-Specific Primitives (to be created)
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.primitives.core.Safe_Str  import Safe_Str
from osbot_utils.type_safe.primitives.core.Safe_UInt import Safe_UInt

class Safe_Str__Git__Commit_Message(Safe_Str):           # Commit message body
    max_length      = 65536                              # 64KB max
    allow_empty     = True
    trim_whitespace = False                              # Preserve formatting!

class Safe_Str__Git__Tree_Path(Safe_Str):                # File path in tree entry
    max_length      = 4096
    allow_empty     = False
    trim_whitespace = True

class Safe_Str__Git__Author_Line(Safe_Str):              # "Name <email> ts tz"
    max_length      = 1024
    allow_empty     = True
    trim_whitespace = True

class Safe_UInt__Git__Timestamp(Safe_UInt):              # Unix timestamp (seconds)
    min_value       = 0
    max_value       = 4294967295                         # Max 32-bit uint
```

### Enum Types

```python
# ═══════════════════════════════════════════════════════════════════════════════
# Git Enums - Fixed sets of values
# ═══════════════════════════════════════════════════════════════════════════════

from enum import Enum

class Enum__Git__File_Change_Status(str, Enum):          # File change types
    ADDED    = 'A'
    MODIFIED = 'M'
    DELETED  = 'D'

class Enum__Git__Object_Type(str, Enum):                 # Git object types
    COMMIT = 'commit'
    TREE   = 'tree'
    BLOB   = 'blob'
    TAG    = 'tag'
```

### Schema Classes (Pure Data - No Methods)

```python
# ═══════════════════════════════════════════════════════════════════════════════
# Git Schemas - Pure data containers
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.Type_Safe import Type_Safe

class Schema__Git__File_Change(Type_Safe):               # Single file change
    path   : Safe_Str__Git__Tree_Path                    # File path in repo
    status : Enum__Git__File_Change_Status               # A/M/D

class Schema__Git__Author_Info(Type_Safe):               # Parsed author/committer
    name      : Safe_Str                                 # Display name
    email     : Safe_Str__Email                          # Email address
    timestamp : Safe_UInt__Git__Timestamp                # Unix timestamp
    tz_offset : Safe_Str                                 # "+0000" format

class Schema__Git__Commit(Type_Safe):                    # Full commit data
    sha         : Safe_Str__SHA1                         # Full 40-char SHA
    sha_short   : Safe_Str__SHA1__Short                  # Short 7-char SHA (for display)
    tree_sha    : Safe_Str__SHA1                         # Tree object SHA
    parent_shas : List__Git__SHAs                        # 0+ parent SHAs
    author      : Schema__Git__Author_Info               # Author details
    committer   : Schema__Git__Author_Info               # Committer details
    message     : Safe_Str__Git__Commit_Message          # Full message

class Schema__Git__Branch(Type_Safe):                    # Branch reference
    name       : Safe_Str__Git__Branch                   # Branch name (validated)
    commit_sha : Safe_Str__SHA1                          # Tip commit SHA

class Schema__Git__Commit_Summary(Type_Safe):            # Commit with files changed
    commit        : Schema__Git__Commit                  # Commit details
    files_changed : List__Git__File_Changes              # Changed files
```

**Key Design Decision: `sha_short` Field**

The `Schema__Git__Commit` includes both `sha` (full 40-char) and `sha_short` (7-char) to avoid repetitive slicing in user code:

```python
# ✗ WITHOUT sha_short - requires manual slicing everywhere
for commit in commits:
    print(f"{commit.sha[:7]} {commit.message}")          # Manual slice
    log_entry = f"[{commit.sha[:7]}] {commit.author}"    # Repeated
    
# ✓ WITH sha_short - clean, type-safe access
for commit in commits:
    print(f"{commit.sha_short} {commit.message}")        # Direct access
    log_entry = f"[{commit.sha_short}] {commit.author}"  # Consistent
```

The service populates `sha_short` automatically during commit parsing:
```python
sha_short = Safe_Str__SHA1__Short(sha[:7])               # Derived from full SHA
```

### Collection Subclasses (Pure Type Definitions - No Methods)

```python
# ═══════════════════════════════════════════════════════════════════════════════
# Git Collection Types - Type-safe containers
# ═══════════════════════════════════════════════════════════════════════════════

from osbot_utils.type_safe.type_safe_core.collections.Type_Safe__List import Type_Safe__List
from osbot_utils.type_safe.type_safe_core.collections.Type_Safe__Dict import Type_Safe__Dict

class List__Git__SHAs(Type_Safe__List):                  # List of commit SHAs
    expected_type = Safe_Str__SHA1                       # Uses existing primitive

class List__Git__Branches(Type_Safe__List):              # List of branches
    expected_type = Schema__Git__Branch

class List__Git__Commits(Type_Safe__List):               # List of commits
    expected_type = Schema__Git__Commit

class List__Git__File_Changes(Type_Safe__List):          # List of file changes
    expected_type = Schema__Git__File_Change

class Dict__Git__Commits__By_SHA(Type_Safe__Dict):       # SHA → Commit lookup
    expected_key_type   = Safe_Str__SHA1                 # Uses existing primitive
    expected_value_type = Schema__Git__Commit

class Dict__Git__Files__By_Path(Type_Safe__Dict):        # Path → Blob SHA mapping
    expected_key_type   = Safe_Str__Git__Tree_Path
    expected_value_type = Safe_Str__SHA1                 # Uses existing primitive
```

---

## Service Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      Git_Repo__Reader__Service                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │ Git_Repo__Reader__Service (Type_Safe)                               │    │
│  │                                                                     │    │
│  │   repo_path : Safe_Str__File__Path          # Path to repository   │    │
│  │   git_dir   : Safe_Str__File__Path          # Path to .git folder  │    │
│  │                                                                     │    │
│  │   ┌─────────────────────────────────────────────────────────────┐  │    │
│  │   │ Core Methods                                                │  │    │
│  │   ├─────────────────────────────────────────────────────────────┤  │    │
│  │   │ branches()        → List__Git__Branches                     │  │    │
│  │   │ commits(branch, depth) → List__Git__Commits                 │  │    │
│  │   │ commit(sha)       → Schema__Git__Commit                     │  │    │
│  │   │ files_changed(sha)→ List__Git__File_Changes                 │  │    │
│  │   │ head_sha()        → Safe_Str__Git__SHA                      │  │    │
│  │   │ head_commit()     → Schema__Git__Commit_Summary             │  │    │
│  │   └─────────────────────────────────────────────────────────────┘  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              │ uses                                          │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │ Git_Object__Reader (Type_Safe)           # Low-level object access  │    │
│  │                                                                     │    │
│  │   git_dir : Safe_Str__File__Path                                   │    │
│  │                                                                     │    │
│  │   read_object(sha) → (Enum__Git__Object_Type, bytes)               │    │
│  │   read_loose_object(sha) → (type, bytes)                           │    │
│  │   read_packed_object(sha) → (type, bytes)                          │    │
│  │   find_in_pack_index(idx_path, sha) → offset                       │    │
│  │   apply_delta(base, delta) → bytes                                 │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              │ uses                                          │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │ Git_Object__Parser (Type_Safe)           # Parse raw objects        │    │
│  │                                                                     │    │
│  │   parse_commit(sha, data) → Schema__Git__Commit                    │    │
│  │   parse_tree(sha, data) → List[Schema__Git__Tree_Entry]            │    │
│  │   parse_author_line(line) → Schema__Git__Author_Info               │    │
│  │   flatten_tree(tree_sha) → Dict__Git__Files__By_Path               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │ Git_Ref__Reader (Type_Safe)              # Read refs/HEAD           │    │
│  │                                                                     │    │
│  │   git_dir : Safe_Str__File__Path                                   │    │
│  │                                                                     │    │
│  │   head_sha() → Safe_Str__Git__SHA                                  │    │
│  │   branch_names() → List[Safe_Str__Git__Branch_Name]                │    │
│  │   branch_sha(name) → Safe_Str__Git__SHA                            │    │
│  │   read_packed_refs() → Dict[ref, sha]                              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Implementation Details

### Git Object Reading

```python
# ═══════════════════════════════════════════════════════════════════════════════
# Git_Object__Reader - Read objects from .git/objects
# ═══════════════════════════════════════════════════════════════════════════════

class Git_Object__Reader(Type_Safe):                     # Low-level object access
    git_dir : Safe_Str__File__Path                       # Path to .git folder
    
    @type_safe
    def read_object(self, sha: Safe_Str__SHA1) -> tuple:
        """Read git object, returns (type, content_bytes)."""
        # Try loose object first
        result = self.read_loose_object(sha)
        if result:
            return result
        
        # Fall back to packfiles
        return self.read_packed_object(sha)
    
    @type_safe  
    def read_loose_object(self, sha: Safe_Str__SHA1) -> tuple:
        """Read from .git/objects/ab/cdef123..."""
        obj_path = Path(self.git_dir) / "objects" / sha[:2] / sha[2:]
        
        if obj_path.exists() is False:
            return None
        
        with open(obj_path, 'rb') as f:
            compressed = f.read()
        
        raw         = zlib.decompress(compressed)
        null_idx    = raw.index(b'\x00')
        header      = raw[:null_idx].decode('utf-8')
        obj_type, _ = header.split(' ', 1)
        content     = raw[null_idx + 1:]
        
        return (Enum__Git__Object_Type(obj_type), content)
```

### Commit Parsing

```python
# ═══════════════════════════════════════════════════════════════════════════════
# Git_Object__Parser - Parse raw git objects into schemas
# ═══════════════════════════════════════════════════════════════════════════════

class Git_Object__Parser(Type_Safe):                     # Parse objects to schemas
    object_reader : Git_Object__Reader                   # For reading nested objects
    
    @type_safe
    def parse_commit(self                              , # Parse commit object
                     sha    : Safe_Str__SHA1           ,
                     content: bytes                    ) -> Schema__Git__Commit:
        text  = content.decode('utf-8', errors='replace')
        lines = text.split('\n')
        
        tree_sha    = Safe_Str__SHA1()
        parent_shas = List__Git__SHAs()
        author      = Schema__Git__Author_Info()
        committer   = Schema__Git__Author_Info()
        msg_start   = 0
        
        for i, line in enumerate(lines):
            if line == '':
                msg_start = i + 1
                break
            if line.startswith('tree '):
                tree_sha = Safe_Str__SHA1(line[5:])
            elif line.startswith('parent '):
                parent_shas.append(Safe_Str__SHA1(line[7:]))
            elif line.startswith('author '):
                author = self.parse_author_line(line[7:])
            elif line.startswith('committer '):
                committer = self.parse_author_line(line[10:])
        
        message = Safe_Str__Git__Commit_Message('\n'.join(lines[msg_start:]).strip())
        
        return Schema__Git__Commit(sha         = sha                                ,
                                   sha_short   = Safe_Str__SHA1__Short(sha[:7])     , # Derive short SHA
                                   tree_sha    = tree_sha                           ,
                                   parent_shas = parent_shas                        ,
                                   author      = author                             ,
                                   committer   = committer                          ,
                                   message     = message                            )
```

### File Change Detection

```python
@type_safe
def files_changed(self                         ,         # Get files changed in commit
                  sha: Safe_Str__SHA1          ) -> List__Git__File_Changes:
    commit = self.commit(sha)
    if commit is None:
        return List__Git__File_Changes()
    
    current_files = self.flatten_tree(commit.tree_sha)
    
    # Initial commit: all files are added
    if len(commit.parent_shas) == 0:
        return List__Git__File_Changes([
            Schema__Git__File_Change(path   = path                           ,
                                     status = Enum__Git__File_Change_Status.ADDED)
            for path in sorted(current_files.keys())
        ])
    
    # Compare with first parent
    parent        = self.commit(commit.parent_shas[0])
    parent_files  = self.flatten_tree(parent.tree_sha)
    changes       = List__Git__File_Changes()
    
    # Added and modified
    for path, blob_sha in current_files.items():
        if path not in parent_files:
            changes.append(Schema__Git__File_Change(path   = path,
                                                    status = Enum__Git__File_Change_Status.ADDED))
        elif parent_files[path] != blob_sha:
            changes.append(Schema__Git__File_Change(path   = path,
                                                    status = Enum__Git__File_Change_Status.MODIFIED))
    
    # Deleted
    for path in parent_files:
        if path not in current_files:
            changes.append(Schema__Git__File_Change(path   = path,
                                                    status = Enum__Git__File_Change_Status.DELETED))
    
    return changes
```

---

## Usage Examples

### Example 1: List All Branches

```python
from osbot_utils.helpers.git.Git_Repo__Reader__Service import Git_Repo__Reader__Service

service  = Git_Repo__Reader__Service(repo_path='/path/to/repo')
branches = service.branches()

for branch in branches:
    print(f"{branch.name}: {branch.commit_sha}")         # Full SHA available
# main: abc123def456789012345678901234567890abcd
# develop: 789012345678901234567890123456789012abcd
```

### Example 2: Get Latest Commit with Files Changed

```python
service = Git_Repo__Reader__Service(repo_path='.')
summary = service.head_commit()

print(f"Commit:  {summary.commit.sha_short}")            # 7-char short SHA
print(f"Author:  {summary.commit.author.name}")
print(f"Message: {summary.commit.message.splitlines()[0]}")
print(f"\nFiles changed ({len(summary.files_changed)}):")

for change in summary.files_changed:
    print(f"  {change.status.value} {change.path}")
```
```

### Example 3: Walk Commit History

```python
service = Git_Repo__Reader__Service(repo_path='.')
commits = service.commits(branch='main', depth=10)

for commit in commits:
    first_line = commit.message.splitlines()[0][:50]
    print(f"{commit.sha_short} {first_line}")            # sha_short for display
# abc1234 Initial commit: add project structure
# def5678 Add user authentication module
# 9012abc Fix login validation bug
```

### Example 4: JSON Serialization (Automatic)

```python
service = Git_Repo__Reader__Service(repo_path='.')
commit  = service.commit(service.head_sha())

# Type_Safe provides automatic JSON serialization
json_data = commit.json()

# Deserialize back to fully-typed object
restored = Schema__Git__Commit.from_json(json_data)
assert type(restored.sha)       is Safe_Str__SHA1        # Full SHA type preserved
assert type(restored.sha_short) is Safe_Str__SHA1__Short # Short SHA type preserved
```

---

## Implementation Roadmap

### Milestone 1: Core Object Reading (Priority: P0)

**Deliverables:**
- [ ] New primitives: `Safe_Str__Git__Commit_Message`, `Safe_Str__Git__Tree_Path`, `Safe_Str__Git__Author_Line`
- [ ] `Git_Object__Reader` with loose object support
- [ ] `Git_Object__Parser.parse_commit()` (populates `sha` and `sha_short`)
- [ ] Basic unit tests with synthetic git objects

**Primitives Reused:** `Safe_Str__SHA1`, `Safe_Str__SHA1__Short`, `Safe_Str__Git__Branch`

**Key Challenge**: Handling zlib edge cases and malformed objects gracefully

### Milestone 2: Reference Reading (Priority: P0)

**Deliverables:**
- [ ] `Git_Ref__Reader.head_sha()`
- [ ] `Git_Ref__Reader.branch_names()`
- [ ] `Git_Ref__Reader.read_packed_refs()` fallback
- [ ] Tests with detached HEAD scenario

**Key Challenge**: Symbolic ref resolution and packed-refs format

### Milestone 3: Tree Parsing & File Changes (Priority: P0)

**Deliverables:**
- [ ] `Git_Object__Parser.parse_tree()`
- [ ] `Git_Object__Parser.flatten_tree()` (recursive)
- [ ] `Git_Repo__Reader__Service.files_changed()`
- [ ] Tests comparing tree diffs

**Key Challenge**: Binary tree format parsing, recursive tree flattening

### Milestone 4: Packfile Support (Priority: P1)

**Deliverables:**
- [ ] `Git_Object__Reader.read_packed_object()`
- [ ] Pack index (v2) parsing
- [ ] Delta reconstruction (`OFS_DELTA`, `REF_DELTA`)
- [ ] Tests with real packed repositories

**Key Challenge**: Delta instruction application, offset calculations

### Milestone 5: Service API & Integration (Priority: P0)

**Deliverables:**
- [ ] `Git_Repo__Reader__Service` with all public methods
- [ ] Integration tests with real git repositories
- [ ] Error handling for missing/corrupt repos
- [ ] Documentation and examples

---

## File Organization

```
osbot_utils/
└── helpers/
    └── git/
        ├── __init__.py
        │
        ├── primitives/                              # NEW primitives only
        │   ├── __init__.py
        │   ├── Safe_Str__Git__Commit_Message.py     # New: commit message body
        │   ├── Safe_Str__Git__Tree_Path.py          # New: file path in tree
        │   ├── Safe_Str__Git__Author_Line.py        # New: author/committer line
        │   └── Safe_UInt__Git__Timestamp.py         # New: unix timestamp
        │
        ├── enums/
        │   ├── __init__.py
        │   ├── Enum__Git__File_Change_Status.py
        │   └── Enum__Git__Object_Type.py
        │
        ├── schemas/
        │   ├── __init__.py
        │   ├── Schema__Git__File_Change.py
        │   ├── Schema__Git__Author_Info.py
        │   ├── Schema__Git__Commit.py               # Includes sha + sha_short
        │   ├── Schema__Git__Branch.py
        │   └── Schema__Git__Commit_Summary.py
        │
        ├── collections/
        │   ├── __init__.py
        │   ├── List__Git__SHAs.py                   # Uses Safe_Str__SHA1
        │   ├── List__Git__Branches.py
        │   ├── List__Git__Commits.py
        │   ├── List__Git__File_Changes.py
        │   ├── Dict__Git__Commits__By_SHA.py        # Uses Safe_Str__SHA1
        │   └── Dict__Git__Files__By_Path.py
        │
        ├── Git_Object__Reader.py
        ├── Git_Object__Parser.py
        ├── Git_Ref__Reader.py
        └── Git_Repo__Reader__Service.py

# ═══════════════════════════════════════════════════════════════════════════════
# Reused from existing osbot_utils (no changes needed)
# ═══════════════════════════════════════════════════════════════════════════════
# osbot_utils.type_safe.primitives.domains.cryptography.safe_str.Safe_Str__SHA1
# osbot_utils.type_safe.primitives.domains.cryptography.safe_str.Safe_Str__SHA1__Short
# osbot_utils.type_safe.primitives.domains.git.safe_str.Safe_Str__Git__Branch
# osbot_utils.type_safe.primitives.domains.git.safe_str.Safe_Str__Git__Ref
```

---

## Success Criteria

### Minimum Viable Product (MVP)

- [ ] Read HEAD and resolve to commit SHA
- [ ] List all local branches with tip SHAs
- [ ] Parse commit objects (message, author, timestamp, sha + sha_short)
- [ ] Get files changed between commit and parent
- [ ] All data returned as Type_Safe schemas—no raw primitives
- [ ] Works without any external dependencies
- [ ] Reuses existing `Safe_Str__SHA1`, `Safe_Str__Git__Branch` primitives

### Full Feature Set

- [ ] Packfile support (read objects from .pack files)
- [ ] Walk commit history with configurable depth
- [ ] Handle detached HEAD state
- [ ] Handle packed-refs fallback
- [ ] Graceful error handling for corrupt/missing objects

### Quality Criteria

- [ ] 100% Type_Safe compliance—no raw `str`, `int`, `list`, `dict` in public API
- [ ] Full JSON serialization round-trip for all schemas
- [ ] Works on repos with 1000+ commits
- [ ] Comprehensive unit tests with synthetic git data
- [ ] Integration tests with real public repositories
- [ ] `sha_short` populated automatically for all commits

---

## Open Questions

1. **Scope of packfile support**: Full delta reconstruction is complex—acceptable to return None for packed objects initially?

2. **Error handling strategy**: Return None vs raise exceptions for missing objects?

3. **Caching**: Should we cache parsed commits/trees for repeated access within a session?

4. **Encoding**: How to handle non-UTF-8 commit messages (legacy repos)?

5. **Large repos**: Any special handling needed for repos with millions of objects?

---

## References

- [Git Internals - Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
- [Git Pack Format](https://git-scm.com/docs/pack-format)
- [Git Index Format](https://git-scm.com/docs/index-format)
- OSBot-Utils Type_Safe Guide (`v3_63_4__for_llms__type_safe.md`)
- OSBot-Utils Python Formatting Guide (`v3_63_4__for_llms__python_formatting_guide.md`)
