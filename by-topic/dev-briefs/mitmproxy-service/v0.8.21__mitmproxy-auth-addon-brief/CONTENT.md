# MitmProxy Custom Authentication Addon - Implementation Brief (v0.8.21)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This implementation brief specifies a custom authentication addon for MitmProxy that transforms an open proxy into a secure, multi-user service with role-based access control, audit logging, and FastAPI integration. The addon intercepts all incoming connections and enforces HTTP Basic Authentication before allowing traffic to flow, using a JSON-based user database with SHA256 password hashing. After successful authentication, the addon injects X-Proxy-User and X-Proxy-Role headers for downstream services, enabling per-user policies and audit trails. The implementation includes account lockout protection, failed attempt tracking, and hot-reload capability for the configuration file.

---

## Key Concepts

- **Custom MitmProxy Addon**: Native Python addon running inside mitmproxy's event loop with zero latency overhead, providing full control over authentication flow versus built-in `--proxyauth` or reverse proxy alternatives.

- **HTTP Basic Authentication Enforcement**: All incoming proxy connections must provide valid credentials via `Proxy-Authorization` header; unauthenticated requests receive 407 Proxy Authentication Required response.

- **JSON-Based User Database**: User accounts stored in `/etc/mitmproxy/users.json` with SHA256 password hashes, roles, enabled flags, allowed hosts patterns, and rate limits per user.

- **Role-Based Access Control**: Predefined roles (admin, developer, readonly, user) determine which transformation modes are accessible and whether cache bypass is permitted.

- **User Context Propagation**: After successful authentication, addon injects X-Proxy-User, X-Proxy-Role, X-Proxy-Auth-Time, and X-Proxy-Source-IP headers for downstream FastAPI services to use in authorization and logging.

- **Audit Logging**: JSON Lines format log file recording all auth success/failure events with timestamp, username, source IP, target host, and failure reasons for compliance and security review.

---

## Core Arguments

1. An open proxy creates critical security issues (unauthorized access, no accountability, resource abuse, compliance gaps) that are resolved by enforcing authentication on all incoming connections.

2. A custom addon was chosen over built-in `--proxyauth` (single user, no logging, no roles), reverse proxy (adds infrastructure, doesn't integrate with internals), or mTLS (complex setup, certificate overhead) for its full control and integration capabilities.

3. Native integration in mitmproxy's event loop means zero latency overhead compared to external authentication mechanisms while enabling user context propagation to downstream services.

4. The JSON-based configuration supports hot reload without restart, allowing user management (add/remove/disable accounts) without service interruption.

5. Account lockout protection (max failed attempts, lockout duration) prevents brute-force attacks while audit logging provides complete visibility for security reviews.

6. Header injection pattern (X-Proxy-User, X-Proxy-Role) enables the downstream FastAPI service to implement per-user policies and logging without modifying the core interceptor logic.

---

## Key Quotes

> "The MitmProxy service currently operates as an open proxy, meaning anyone who knows the hostname and port can route traffic through it."

> "Native Integration — Runs inside mitmproxy's event loop, zero latency overhead"

> "A developer shares the proxy URL with a colleague for debugging. That colleague shares it further. Soon, unknown parties are routing traffic through the proxy."

> "Can inject X-Proxy-User headers for downstream FastAPI... Full visibility into auth success/failure events."

---

## Tags

`mitmproxy-addon` `proxy-authentication` `http-basic-auth` `multi-user` `role-based-access` `audit-logging` `json-config` `sha256-hashing` `account-lockout` `fastapi-integration` `x-proxy-user` `header-injection`

---

## Search Phrases

- "mitmproxy custom authentication addon"
- "multi-user proxy authentication Python"
- "HTTP Basic Auth proxy enforcement"
- "role-based access control mitmproxy"
- "proxy audit logging JSON Lines"
- "X-Proxy-User header injection"
- "JSON user database proxy authentication"
- "account lockout brute force protection"
- "mitmproxy FastAPI integration"
- "proxy authentication configuration hot reload"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Implementation Brief |
| **Domain** | Dev Briefs / MitmProxy Service |
| **Sub-domain** | Authentication / Security |
| **Format** | Markdown with Python code |
| **Version** | v0.8.21 / v1.0.0 |
| **Date** | January 2026 |
| **Target Audience** | Security Engineers, Backend Developers, DevOps |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `secures` | MITM Proxy Platform Architecture |
| `integrates_with` | FastAPI Interceptor Pipeline |
| `related_to` | Mitmproxy Solution Architecture Debrief |
| `enables` | Per-User Policy Enforcement |
| `part_of` | MyFeeds.ai Service Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `addon` | MitmProxy addon component |
| `config` | Configuration file/structure |
| `header` | HTTP header |
| `role` | Access control role |
| `event` | Audit log event type |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `enforces` | `enforced_by` | Security enforcement |
| `injects` | `injected_by` | Header injection |
| `logs` | `logged_by` | Audit logging |
| `grants` | `granted_by` | Access granting |

### Taxonomy

```
mitmproxy_auth_addon
├── authentication
│   ├── http_basic_auth
│   ├── sha256_password_hash
│   ├── credential_extraction
│   └── 407_response_handling
├── configuration
│   ├── users_json
│   ├── roles_definition
│   ├── settings
│   └── hot_reload
├── access_control
│   ├── role_admin
│   ├── role_developer
│   ├── role_readonly
│   └── role_user
├── headers_injected
│   ├── x_proxy_user
│   ├── x_proxy_role
│   ├── x_proxy_auth_time
│   └── x_proxy_source_ip
├── security_features
│   ├── account_lockout
│   ├── failed_attempt_tracking
│   └── allowed_hosts_filtering
└── audit_logging
    ├── auth_success
    ├── auth_failure
    └── user_locked
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `proxy_auth` | `addon` | ProxyAuthenticator |
| `users_json` | `config` | users.json |
| `x_proxy_user` | `header` | X-Proxy-User |
| `x_proxy_role` | `header` | X-Proxy-Role |
| `admin_role` | `role` | Admin |
| `developer_role` | `role` | Developer |
| `auth_success` | `event` | Authentication Success |
| `auth_failure` | `event` | Authentication Failure |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `proxy_auth` | `enforces` | `http_basic_auth` |
| `proxy_auth` | `injects` | `x_proxy_user` |
| `proxy_auth` | `injects` | `x_proxy_role` |
| `proxy_auth` | `logs` | `auth_success` |
| `proxy_auth` | `logs` | `auth_failure` |
| `admin_role` | `grants` | `all_modes` |
| `developer_role` | `grants` | `standard_modes` |

</details>
