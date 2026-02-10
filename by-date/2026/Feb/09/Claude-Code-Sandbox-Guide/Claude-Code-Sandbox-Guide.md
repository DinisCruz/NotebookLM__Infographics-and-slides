# Claude Code Sandbox: OS-Level Isolation for AI-Assisted Development on macOS

**Security Architecture & Setup Guide — Version 1.0 — February 2026**

---

## Table of Contents

1. [The Problem](#1-the-problem)
2. [The Solution: OS-Level Sandboxing](#2-the-solution-os-level-sandboxing)
3. [Architecture Overview](#3-architecture-overview)
4. [Included Files](#4-included-files)
5. [Step-by-Step Setup](#5-step-by-step-setup)
6. [Threat Model: What This Prevents](#6-threat-model-what-this-prevents)
7. [Limitations and Caveats](#7-limitations-and-caveats)
8. [Maintenance](#8-maintenance)
9. [Defense in Depth: Combining Application and OS Controls](#9-defense-in-depth-combining-application-and-os-controls)
10. [Quick Reference](#10-quick-reference)

---

## 1. The Problem

Claude Code is a powerful AI coding assistant that runs commands on your local machine. By default, it executes as your user account, which means it inherits all of your filesystem permissions, SSH keys, credentials, and access to every directory on the system.

This creates a significant attack surface. Even with Claude Code's application-level permission allowlist, a prompt injection, misconfigured rule, or simple AI misjudgment could result in commands like:

```bash
find /Users/diniscruz -name "*.env" -type f    # scans your entire home
cat ~/.ssh/id_rsa                                # reads SSH keys
rm -rf ~/Documents                               # accidental deletion
```

The Claude Code permission system (`.claude.json`) is an application-level safeguard. It's valuable, but it's a single layer of defense that can be bypassed if the application itself is compromised or misbehaves. What we need is **defense in depth**: OS-level enforcement that the AI process literally cannot circumvent, regardless of what commands it tries to run.

---

## 2. The Solution: OS-Level Sandboxing

The approach is straightforward: create a dedicated, unprivileged macOS user account that Claude Code runs as. This user has no default access to your files, credentials, or system directories. Access is then explicitly granted only to the specific directories and executables that Claude Code needs.

### 2.1 Security Layers

This sandbox provides three independent layers of protection that work together:

| Layer | Mechanism | What It Prevents |
|-------|-----------|-----------------|
| 1. User Isolation | Dedicated macOS user | No implicit access to any other user's files, credentials, or processes |
| 2. Filesystem ACLs | POSIX ACLs with inheritance | Fine-grained read/write or read-only access only to explicitly listed directories |
| 3. PATH Restriction | Restricted bin with symlinks | Only approved executables are available; no access to curl, sudo, ssh, brew, etc. |

On top of these OS-level controls, Claude Code's own `.claude.json` permission allowlist provides a fourth, application-level layer. The combination means an attacker (or a confused AI) would need to bypass both the application permission system AND the OS permission system to cause damage.

---

## 3. Architecture Overview

### 3.1 How macOS Users and Permissions Work

macOS is built on a Unix foundation. Every process runs as a specific user, and every file/directory has an owner and permission bits that control who can read, write, or execute it. By default, your home directory (`/Users/yourname`) has mode `700`, meaning only your user can access it.

When we create a new user called `claude-code`, that user starts with **zero access** to your files. It can't read your home directory, can't see your SSH keys, can't access your documents. We then use Access Control Lists (ACLs) to selectively punch holes in this wall, granting the `claude-code` user access to exactly the directories it needs.

### 3.2 Access Control Lists (ACLs)

macOS ACLs extend traditional Unix permissions with fine-grained per-user and per-group rules. Unlike Unix permission bits (which only support owner/group/other), ACLs let you grant specific access to specific users without changing ownership.

Key ACL concepts used in this setup:

- **`file_inherit`**: New files created in the directory automatically get this ACL entry.
- **`directory_inherit`**: New subdirectories created in the directory automatically get this ACL entry.
- **`read, write, execute, delete`**: Standard permission flags for file operations.
- **`add_file, add_subdirectory, list, search`**: Directory-specific operations (create files, create folders, list contents, traverse).

### 3.3 PATH Restriction

Unix systems find executables by searching directories listed in the `PATH` environment variable. By setting a custom PATH that contains only a curated set of symlinked executables, we control exactly which commands the sandboxed user can run.

Even if Claude Code somehow constructs a command outside its allowlist, the binary won't be found if it's not in the restricted PATH. This is particularly important for blocking access to network tools (`curl`, `wget`, `ssh`), system administration tools (`sudo`, `dscl`, `diskutil`), and package managers (`brew`, `gem`).

---

## 4. Included Files

| File | Purpose |
|------|---------|
| `config.env` | Central configuration file. All paths, usernames, and executable lists are defined here. Edit this before running anything else. |
| `setup-claude-code-user.sh` | Creates the macOS user, sets up the restricted bin directory, applies ACLs, and copies Claude Code configuration. |
| `run-claude-code.sh` | Launches Claude Code as the sandboxed user with the restricted PATH. Pass any claude CLI arguments through. |
| `verify-sandbox.sh` | Runs automated tests to verify permissions are correct: checks R/W access, R/O access, confirms sensitive directories are blocked, and verifies restricted executables. |
| `refresh-acls.sh` | Re-applies ACLs to all configured directories. Run this after creating new project directories or when files created by your main user aren't accessible to the sandbox. |
| `teardown-claude-code-user.sh` | Completely removes the sandbox: kills processes, removes ACLs, deletes the user account and home directory. Requires confirmation. |
| `claude-settings.json` | Template for Claude Code's application-level permission allowlist. Copied to the sandbox user's `~/.claude/` directory during setup. |

---

## 5. Step-by-Step Setup

### 5.1 Prerequisites

- macOS 12 (Monterey) or later
- Administrator access (sudo)
- Claude Code CLI installed and accessible from your terminal
- Node.js installed (required by Claude Code)

### 5.2 Configure

Edit `config.env` to match your environment. The critical settings are:

- **`CC_RW_DIRECTORIES`**: Comma-separated list of project directories Claude Code needs full read/write access to. This is where your code lives.
- **`CC_RO_DIRECTORIES`**: Comma-separated list of directories Claude Code needs to read but not modify. Typically virtual environments, SDKs, or tool configs.
- **`CC_ALLOWED_EXECUTABLES`**: Comma-separated list of command names to make available. Only these will be on the sandbox user's PATH.

> ⚠️ **Important: Review the Default Password**
>
> The default `CC_PASSWORD` in `config.env` uses `openssl rand` to generate a random password. If you need to authenticate as the sandbox user interactively (e.g., for testing), change this to a known value. The password is only needed for sudo operations — the user is hidden from the login screen.

### 5.3 Run Setup

```bash
cd /path/to/claude-code-sandbox
chmod +x *.sh
sudo ./setup-claude-code-user.sh
```

The script will print a summary showing the created user, ACL'd directories, and available executables. Review this to confirm everything looks correct.

### 5.4 Verify

```bash
sudo ./verify-sandbox.sh
```

This runs a comprehensive test suite that verifies positive access (can read/write to project dirs), negative access (cannot access `~/.ssh`, `~/Documents`, etc.), and executable availability. All tests should pass. If any fail, review the ACL configuration in `config.env` and re-run `refresh-acls.sh`.

### 5.5 Run Claude Code

```bash
./run-claude-code.sh
```

This launches Claude Code as the sandboxed user with the restricted PATH. All Claude arguments are passed through, so you can use it exactly as you normally would.

---

## 6. Threat Model: What This Prevents

| Threat | Without Sandbox | With Sandbox |
|--------|----------------|--------------|
| Broad filesystem scan (`find /Users/...`) | Returns all files you can access | Permission denied everywhere except ACL'd dirs |
| Read SSH keys (`~/.ssh/`) | Full access to all keys | Permission denied |
| Read/modify other projects | Full access to all your code | Only configured projects accessible |
| Download and execute malware | `curl`/`wget` available | No network tools in PATH |
| Modify system configuration | Some access via your groups | No sudo, no admin tools |
| Exfiltrate credentials from shell history/env | Can read `~/.zsh_history`, `~/.env`, etc. | Separate user = separate history, no access to yours |
| Accidental `rm -rf` in wrong directory | Can delete anything you own | Can only delete files in R/W ACL'd dirs |

---

## 7. Limitations and Caveats

### 7.1 ACL Inheritance

When your main user creates new files or directories inside an ACL'd project folder, macOS `file_inherit` and `directory_inherit` flags should propagate the ACL. However, this doesn't always work perfectly, especially with tools that create temporary files and rename them (e.g., some editors, git operations). If you notice permission issues, run `refresh-acls.sh` to re-apply ACLs.

### 7.2 Git Credentials

The sandbox user has its own home directory and therefore its own git configuration. Local-only git operations (`diff`, `add`, `stash`, `log`) work fine. However, `push` and `pull` require authentication. Options include setting up a read-only deploy key for the repo, using a git credential helper scoped to the sandbox user, or performing push/pull from your main user account.

### 7.3 Package Installation

If Claude Code needs to install Python/Node packages, the virtual environment directory needs R/W access (not just R/O). Update `CC_RW_DIRECTORIES` to include the venv path if needed. Be mindful that this expands the sandbox's write surface.

### 7.4 macOS System Integrity Protection (SIP)

SIP already prevents modification of system-level directories (`/System`, `/usr/bin`, etc.) regardless of user. This sandbox is complementary: SIP protects the OS from all users; this sandbox protects your data from Claude Code specifically.

### 7.5 Not a Full Container

This is user-level isolation, not a container or VM. The sandboxed process shares the kernel, network stack, and some system resources. For higher isolation, consider running Claude Code inside a Docker container or a full macOS VM. However, for the use case of preventing accidental damage and limiting blast radius, user-level isolation is a practical and effective balance of security and usability.

---

## 8. Maintenance

### 8.1 Adding a New Project Directory

To grant Claude Code access to a new project, add the path to `CC_RW_DIRECTORIES` in `config.env` and run:

```bash
sudo ./refresh-acls.sh
```

### 8.2 Adding a New Executable

Add the command name to `CC_ALLOWED_EXECUTABLES` in `config.env` and re-run the setup script. Existing configuration is preserved; the script only adds new symlinks.

### 8.3 Updating Claude Code

When Claude Code updates, the binary path may change. If `run-claude-code.sh` can't find the binary, update `CC_CLAUDE_BIN` in `config.env` or ensure the new binary location is in the standard PATH.

### 8.4 Complete Removal

```bash
sudo ./teardown-claude-code-user.sh
```

This removes the user, home directory, and all ACLs. The operation is irreversible and requires confirmation.

---

## 9. Defense in Depth: Combining Application and OS Controls

The strongest posture combines both layers. Here's how they interact:

| Control | Application Layer (.claude.json) | OS Layer (Sandbox User) |
|---------|----------------------------------|------------------------|
| Scope | Claude Code process only | Any process running as the sandbox user |
| Bypass difficulty | Possible via prompt injection or app bugs | Requires kernel exploit or root escalation |
| Granularity | Per-command patterns (`Bash(git:*)`) | Per-directory, per-user, per-executable |
| Configuration | JSON file in project | OS-level ACLs + PATH + user accounts |

Neither layer alone is sufficient for high-security environments. Together, they provide a robust defense where the application layer catches most cases through intent-based filtering, and the OS layer provides a hard stop that no amount of prompt engineering can circumvent.

---

## 10. Quick Reference

| Task | Command |
|------|---------|
| Initial setup | `sudo ./setup-claude-code-user.sh` |
| Verify permissions | `sudo ./verify-sandbox.sh` |
| Run Claude Code | `./run-claude-code.sh` |
| Refresh ACLs after changes | `sudo ./refresh-acls.sh` |
| Add project directory | Edit `config.env`, then `sudo ./refresh-acls.sh` |
| Check ACLs on a dir | `ls -le /path/to/dir` |
| Remove sandbox completely | `sudo ./teardown-claude-code-user.sh` |
| Test as sandbox user | `sudo -u claude-code ls /some/path` |
