# MitmProxy Custom Authentication Addon - Implementation Brief (v0.8.21)

[← Back to mitmproxy-service](../README.md) · [Dev Briefs](../../README.md) · [By Topic](../../../README.md)

---

## Overview

A comprehensive implementation brief for a custom authentication addon for MitmProxy that provides multi-user proxy authentication with role-based access control, audit logging, and seamless integration with the existing FastAPI interceptor pipeline.

The addon transforms an open proxy into a secure, auditable service by enforcing HTTP Basic Authentication on all incoming connections before allowing traffic to flow.

## Contents

| File | Description |
|------|-------------|
| `v0.8.21__mitmproxy-auth-addon-brief.md` | Complete implementation specification with code |
| `22 Jan - Securing Flow with Custom Addon.jpg` | Architecture overview infographic |
| `22 Jan - MitmProxy_MultiUser_Authentication_Implementation_Brief.pdf` | NotebookLM deep dive presentation |
| `CONTENT.md` | Semantic Knowledge Graph metadata |

## Key Topics

- **Multi-User Authentication**: JSON-based user database with role assignment
- **HTTP Basic Auth Enforcement**: Custom mitmproxy addon intercepting all connections
- **Role-Based Access Control**: Roles (admin, developer, readonly, user) with different transformation modes
- **Audit Logging**: JSON Lines format logging of all auth success/failure events
- **User Context Propagation**: X-Proxy-User and X-Proxy-Role headers injected for downstream FastAPI

## Problem Addressed

| Problem | Risk Level | Impact |
|---------|------------|--------|
| Unauthorized Access | Critical | Anyone can use proxy to access internal resources |
| No Accountability | High | Cannot track who performed what actions |
| Resource Abuse | Medium | Unlimited usage without attribution |
| Compliance Gaps | High | No audit trail for security reviews |

## Solution Components

**1. Authentication Addon (`proxy_auth.py`):**
- HTTP Basic Auth enforcement
- SHA256 password hashing
- Failed attempt tracking and lockout
- User context injection to requests

**2. Configuration System:**
- JSON-based user database (`/etc/mitmproxy/users.json`)
- Environment file for secrets
- Secure file permissions (chmod 600)

**3. Integration Layer:**
- Seamless chain with `fastapi_interceptor.py`
- X-Proxy-User header propagation
- X-Proxy-Role header for authorization

**4. Operational Tooling:**
- User management CLI script
- systemd service configuration
- Log rotation setup

## Headers Injected After Authentication

| Header | Example | Purpose |
|--------|---------|---------|
| `X-Proxy-User` | `alice` | Username for audit trail |
| `X-Proxy-Role` | `admin` | Role for authorization |
| `X-Proxy-Auth-Time` | `2026-01-22T10:30:15Z` | When auth occurred |
| `X-Proxy-Source-IP` | `192.168.1.100` | Client IP |

## Source Information

| Field | Value |
|-------|-------|
| **Document Type** | Implementation Brief |
| **Version** | v1.0.0 |
| **Date** | January 2026 |
| **Format** | Markdown with Python code |
| **Integration** | MitmProxy, FastAPI |

---

*Part of the MGraph.ai MITM Proxy Service documentation*
