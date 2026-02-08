# Deployment Pods — Graph-Based Deployment Specifications

**Project:** Football Pilates Scheduler  
**Date:** 2026-02-07  
**Concept:** Self-contained, graph-based deployment units stored and tracked in Issues FS  

---

## 1. What is a Deployment Pod?

A **Deployment Pod** is a directed acyclic graph (DAG) of infrastructure resources, permissions, and configuration steps that together constitute a complete, deployable unit. Each pod is:

- **Self-contained** — everything needed to go from zero to running, in one graph
- **Costable** — every node has an associated cost model ($/month or $/request)
- **Auditable** — every node maps to an Issues FS issue (type: `Deploy`, `Task`, or `Spike`)
- **Idempotent** — can be applied repeatedly to reach the desired state
- **Scoped to one account** — the unit of blast-radius isolation is the cloud account

A pod has two representations:
1. **Abstract** (cloud-agnostic) — defines *what* is needed using generic resource types
2. **Concrete** (cloud-specific) — defines *how* to provision it on a specific provider

---

## 2. Issues FS Mapping

Every node in a deployment graph maps to an Issues FS issue:

```
issue.type    → Deploy | Task | Spike | Test
issue.state   → backlog | in-progress | in-review | done | verified
issue.links   → depends-on / depended-by (DAG edges)
                parent / child (grouping)
                tests / tested-by (verification)
                blocks / blocked-by (hard dependencies)
```

### Pod-Level Issue Types

| Type | Purpose | Example |
|------|---------|---------|
| `Deploy:Pod` | The pod itself (epic-level container) | `Deploy:Pod — pilates-api-eu-west-1-prod` |
| `Deploy:Resource` | A single infrastructure resource | `Deploy:Resource — S3 bucket (encrypted votes)` |
| `Deploy:Permission` | An IAM role, policy, or boundary | `Deploy:Permission — lambda-exec-role` |
| `Deploy:Config` | A configuration value or secret | `Deploy:Config — POLL_ENCRYPTION_PUBLIC_KEY` |
| `Deploy:Wire` | A connection between resources | `Deploy:Wire — API Gateway → Lambda integration` |
| `Deploy:Gate` | A verification gate (must pass before proceeding) | `Deploy:Gate — zero open S3 buckets` |
| `Deploy:Cleanup` | A resource/default to remove | `Deploy:Cleanup — delete default VPC` |
| `Test:Smoke` | Post-deploy smoke test | `Test:Smoke — GET /health returns 200` |
| `Test:Security` | Security validation | `Test:Security — no public S3 access` |

### Pod Metadata (stored as Issues FS front-matter)

```yaml
pod:
  name: pilates-api
  version: 1.0.0
  tier: prod                    # dev | qa | prod
  region: eu-west-1             # abstract: "eu-primary"
  provider: aws                 # abstract: "cloud-agnostic"
  account: "123456789012"       # concrete only
  cost_estimate:
    monthly_idle: "$0.00"       # scale-to-zero
    monthly_active: "$2.40"     # estimated 100k requests
    monthly_storage: "$0.03"    # 1GB S3
  dependencies:
    external:
      - "DNS zone (optional)"
    cross-pod:
      - "global-dns-pod (optional CloudFront mapping)"
```

---

## 3. Graph 1 — Abstract Cloud-Agnostic Pod

This graph defines **what** is needed, not **how**. It uses generic resource types that map to any cloud provider. This is the source-of-truth for what constitutes a complete deployment.

```
┌─────────────────────────────────────────────────────────────────────┐
│  Deploy:Pod — pilates-api (abstract)                                │
│  "Everything needed to run the Pilates Scheduler API"               │
└─────────────────────────────────────────────────────────────────────┘
        │
        ├── COMPUTE ─────────────────────────────────────────────────
        │   │
        │   ├── Deploy:Resource — serverless-function
        │   │   type: function-as-a-service
        │   │   runtime: python:3.12
        │   │   framework: FastAPI (via M-Graph adapter)
        │   │   memory: 256MB
        │   │   timeout: 30s
        │   │   scaling: { min: 0, max: 100 }
        │   │   env:
        │   │     STORAGE_BUCKET: → ref(object-store.name)
        │   │     ENCRYPTION_PUBLIC_KEY: → ref(config.public-key)
        │   │     ENVIRONMENT: → ref(pod.tier)
        │   │
        │   └── Deploy:Resource — api-gateway
        │       type: http-api-gateway
        │       protocol: HTTPS (TLS 1.2+)
        │       routes:
        │         POST   /polls                → serverless-function
        │         GET    /polls/{id}           → serverless-function
        │         POST   /polls/{id}/vote      → serverless-function
        │         PATCH  /polls/{id}           → serverless-function
        │         GET    /polls/{id}/results   → serverless-function
        │       cors: { origins: [ref(static-site.url)], methods: [GET, POST, PATCH] }
        │       rate_limit: 100 req/s
        │
        ├── STORAGE ─────────────────────────────────────────────────
        │   │
        │   ├── Deploy:Resource — object-store
        │   │   type: s3-compatible-object-storage
        │   │   encryption: AES-256 (server-side, at rest)
        │   │   versioning: enabled
        │   │   lifecycle: { expire_incomplete_uploads: 7d }
        │   │   public_access: DENIED (block all)
        │   │   buckets:
        │   │     votes/          — encrypted vote payloads (ciphertext)
        │   │     metadata/       — poll definitions (public key, slots, status)
        │   │     counts/         — anonymous aggregated counts (plaintext integers)
        │   │     logs/           — access logs
        │   │
        │   └── Deploy:Resource — static-site
        │       type: static-file-hosting + CDN
        │       source: /frontend/dist/
        │       index: index.html
        │       error: index.html (SPA fallback)
        │       cache: { html: no-cache, assets: 1y immutable }
        │       custom_domain: optional → ref(dns-entry)
        │
        ├── NETWORKING ──────────────────────────────────────────────
        │   │
        │   ├── Deploy:Resource — dns-entry (optional)
        │   │   type: dns-record
        │   │   record: A/CNAME → ref(static-site.endpoint)
        │   │   note: "Only external dependency — requires domain ownership"
        │   │
        │   └── Deploy:Resource — tls-certificate
        │       type: managed-tls
        │       auto_renew: true
        │       scope: [ref(api-gateway.domain), ref(static-site.domain)]
        │
        ├── PERMISSIONS ─────────────────────────────────────────────
        │   │
        │   ├── Deploy:Permission — setup-role
        │   │   purpose: "Used ONLY during initial deployment (then revoked)"
        │   │   capabilities:
        │   │     - create/configure all resources in this pod
        │   │     - create execution roles
        │   │     - configure logging
        │   │     - set bucket policies
        │   │   lifetime: ephemeral (revoke after deploy completes)
        │   │
        │   ├── Deploy:Permission — execution-role
        │   │   purpose: "Runtime identity of the serverless function"
        │   │   capabilities:
        │   │     - object-store: PutObject, GetObject, ListBucket (scoped to pod bucket)
        │   │     - logging: write logs
        │   │   anti-capabilities:
        │   │     - CANNOT create/delete infrastructure
        │   │     - CANNOT modify IAM
        │   │     - CANNOT access other buckets
        │   │     - CANNOT access other accounts
        │   │
        │   └── Deploy:Permission — admin-read-role (optional)
        │       purpose: "Read-only access for monitoring/debugging"
        │       capabilities:
        │         - read logs
        │         - read metrics
        │         - read object-store (metadata only, votes are encrypted)
        │       anti-capabilities:
        │         - CANNOT write anything
        │         - CANNOT modify infrastructure
        │
        ├── OBSERVABILITY ───────────────────────────────────────────
        │   │
        │   ├── Deploy:Resource — logging
        │   │   type: structured-log-aggregation
        │   │   retention: 30d
        │   │   sources: [serverless-function, api-gateway, object-store]
        │   │   format: JSON (structured)
        │   │   queryable: true (via API)
        │   │
        │   ├── Deploy:Resource — metrics
        │   │   type: metrics-collection
        │   │   metrics:
        │   │     - request_count (by endpoint, status_code)
        │   │     - request_latency_p50, p95, p99
        │   │     - error_rate
        │   │     - cold_start_duration
        │   │     - storage_size_bytes
        │   │   dashboards: true
        │   │
        │   └── Deploy:Resource — alerting
        │       type: alert-rules
        │       rules:
        │         - error_rate > 5% for 5min → notify
        │         - p99_latency > 5s for 10min → notify
        │         - storage_size > 500MB → notify
        │
        ├── CONFIGURATION ───────────────────────────────────────────
        │   │
        │   ├── Deploy:Config — public-key
        │   │   value: "organiser's RSA/X25519 public key"
        │   │   storage: environment-variable (not secret — it's a public key)
        │   │
        │   ├── Deploy:Config — environment
        │   │   value: ref(pod.tier) → dev | qa | prod
        │   │
        │   └── Deploy:Config — allowed-origins
        │       value: ref(static-site.url)
        │
        └── VERIFICATION ────────────────────────────────────────────
            │
            ├── Deploy:Gate — pre-deploy
            │   checks:
            │     - all permissions created
            │     - storage accessible
            │     - no public access on object-store
            │
            ├── Test:Smoke — post-deploy
            │   checks:
            │     - GET /health → 200
            │     - POST /polls → 201 (with valid payload)
            │     - GET /polls/{id} → 200 (returns metadata + counts)
            │     - static-site loads in browser
            │
            └── Test:Security — post-deploy
                checks:
                  - object-store: no public access
                  - api-gateway: CORS restricted to static-site origin
                  - serverless-function: cannot access resources outside pod
                  - votes stored as ciphertext (not readable without private key)
                  - no PII in logs
```

### Abstract Pod — Dependency DAG (execution order)

```
setup-role ─────────────┐
                        ▼
              ┌── object-store ──┐
              │                  │
              ├── logging ◄──────┤
              │                  │
              ▼                  ▼
        execution-role    static-site
              │                  │
              ▼                  │
      serverless-function        │
              │                  │
              ▼                  │
        api-gateway              │
              │                  │
              ▼                  ▼
        tls-certificate    dns-entry (optional)
              │                  │
              ▼                  ▼
        Deploy:Gate — pre-deploy
              │
              ▼
        Test:Smoke + Test:Security
              │
              ▼
        revoke setup-role
```

---

## 4. Graph 2 — AWS MVP (Deploy on Any Existing Account)

This is the **minimum viable deployment** on an existing AWS account. Assumes the account already exists with a root user and basic billing set up. No hardening, no multi-region, just get it running.

```
┌─────────────────────────────────────────────────────────────────────┐
│  Deploy:Pod — pilates-api-mvp (AWS)                                 │
│  Target: Any existing AWS account, single region                    │
│  Estimated cost: $0.00–$2.40/mo (free tier eligible)               │
└─────────────────────────────────────────────────────────────────────┘

PHASE 1: BOOTSTRAP (one-time, with admin credentials)
══════════════════════════════════════════════════════

  1.  Deploy:Resource — S3 Bucket: pilates-{tier}-{region}-votes
      │   BucketEncryption: SSE-S3 (AES-256)
      │   PublicAccessBlock: ALL (BlockPublicAcls, BlockPublicPolicy,
      │                          IgnorePublicAcls, RestrictPublicBuckets)
      │   Versioning: Enabled
      │   LifecycleRule: AbortIncompleteMultipartUpload → 7 days
      │   Tags: { project: pilates, tier: {tier}, pod: pilates-api }
      │
      └── Deploy:Resource — S3 Bucket Policy
          Effect: Deny s3:* if NOT from execution-role ARN
          Effect: Deny s3:* if aws:SecureTransport == false

  2.  Deploy:Resource — S3 Bucket: pilates-{tier}-{region}-frontend
      │   StaticWebsiteHosting: Enabled
      │   IndexDocument: index.html
      │   ErrorDocument: index.html
      │   PublicAccessBlock: Conditionally open for static hosting
      │   OR: Use CloudFront OAC (preferred)
      │
      └── Deploy:Resource — CloudFront Distribution (optional but recommended)
          Origins: [S3-frontend (OAC), API Gateway (/api/*)]
          DefaultCacheBehavior: S3 frontend
          CacheBehavior /api/*: API Gateway (no cache, all methods)
          ViewerProtocolPolicy: redirect-to-https
          PriceClass: PriceClass_100 (cheapest — US/EU only)
          → Output: CloudFront domain name (or custom domain)

  3.  Deploy:Permission — IAM Role: pilates-{tier}-lambda-exec
      │   AssumeRolePolicy: lambda.amazonaws.com
      │   ManagedPolicies: NONE (no AWS managed policies)
      │   InlinePolicy — pilates-s3-access:
      │     Allow: s3:GetObject, s3:PutObject, s3:ListBucket
      │     Resource: arn:aws:s3:::pilates-{tier}-{region}-votes/*
      │   InlinePolicy — pilates-logging:
      │     Allow: logs:CreateLogStream, logs:PutLogEvents
      │     Resource: arn:aws:logs:{region}:{account}:log-group:/aws/lambda/pilates-{tier}-api:*
      │   PermissionsBoundary: (see Graph 4 for hardened version)
      │
      └── Deploy:Permission — IAM Role: pilates-{tier}-deploy
          AssumeRolePolicy: sts:AssumeRole (from CI/CD or admin user)
          InlinePolicy — deploy-pilates:
            Allow: lambda:UpdateFunctionCode, lambda:UpdateFunctionConfiguration
            Allow: s3:PutObject (frontend bucket only)
            Allow: cloudfront:CreateInvalidation
          Note: "This is the ONLY role that can deploy. Scoped to this pod only."

  4.  Deploy:Resource — CloudWatch Log Group: /aws/lambda/pilates-{tier}-api
      RetentionInDays: 30
      KmsKeyId: (optional, see Graph 4)

PHASE 2: APPLICATION DEPLOYMENT
═══════════════════════════════

  5.  Deploy:Resource — Lambda Function: pilates-{tier}-api
      │   Runtime: python3.12
      │   Handler: mgraph.handler (Mangum adapter for FastAPI)
      │   MemorySize: 256
      │   Timeout: 30
      │   Role: → ref(pilates-{tier}-lambda-exec)
      │   Environment:
      │     VOTES_BUCKET: → ref(S3 votes bucket name)
      │     ENCRYPTION_PUBLIC_KEY: {organiser's public key}
      │     ENVIRONMENT: {tier}
      │     ALLOWED_ORIGIN: → ref(CloudFront domain or S3 website URL)
      │   ReservedConcurrentExecutions: 10 (cost protection)
      │   Architectures: [arm64] (Graviton — cheaper + faster)
      │
      └── Deploy:Resource — Lambda Function URL (alternative to API Gateway)
          AuthType: NONE (public API — auth is app-level)
          Cors:
            AllowOrigins: [ref(static-site URL)]
            AllowMethods: [GET, POST, PATCH]
            AllowHeaders: [Content-Type]

  6.  Deploy:Resource — API Gateway (HTTP API): pilates-{tier}-api
      │   Protocol: HTTP
      │   Routes:
      │     POST   /polls               → Lambda integration
      │     GET    /polls/{id}          → Lambda integration
      │     POST   /polls/{id}/vote     → Lambda integration
      │     PATCH  /polls/{id}          → Lambda integration
      │     GET    /polls/{id}/results  → Lambda integration
      │     GET    /health              → Lambda integration
      │   DefaultRouteSettings:
      │     ThrottlingRateLimit: 100
      │     ThrottlingBurstLimit: 50
      │   CorsConfiguration:
      │     AllowOrigins: [ref(static-site URL)]
      │     AllowMethods: [GET, POST, PATCH, OPTIONS]
      │     AllowHeaders: [Content-Type]
      │
      └── Deploy:Resource — API Gateway Stage: $default
          AutoDeploy: true
          AccessLogSettings:
            DestinationArn: → ref(CloudWatch Log Group)

  7.  Deploy:Task — Upload frontend to S3
      aws s3 sync ./frontend/dist/ s3://pilates-{tier}-{region}-frontend/ \
        --delete --cache-control "public, max-age=31536000, immutable"
      aws s3 cp s3://pilates-{tier}-{region}-frontend/index.html \
        s3://pilates-{tier}-{region}-frontend/index.html \
        --cache-control "no-cache, no-store, must-revalidate"

PHASE 3: VERIFICATION
═════════════════════

  8.  Test:Smoke — API health check
      curl -s https://{api-endpoint}/health | jq .status == "ok"

  9.  Test:Smoke — Full voter flow
      POLL_ID=$(curl -s -X POST https://{api-endpoint}/polls \
        -d '{"slots":["2026-02-14T10:00","2026-02-14T14:00"]}' | jq -r .id)
      curl -s https://{api-endpoint}/polls/$POLL_ID | jq .status == "open"
      curl -s -X POST https://{api-endpoint}/polls/$POLL_ID/vote \
        -d '{"encrypted_payload":"...","slot_ids":["2026-02-14T10:00"]}'

  10. Test:Security — No public S3 access
      aws s3api get-public-access-block --bucket pilates-{tier}-{region}-votes \
        | jq 'all(.PublicAccessBlockConfiguration[]; . == true)'

  11. Test:Security — Lambda cannot escalate
      # From within Lambda execution context:
      # Attempt to call iam:CreateRole → should get AccessDenied
      # Attempt to access other S3 buckets → should get AccessDenied
```

### AWS MVP — Dependency DAG

```
S3 votes bucket ──────────────────┐
S3 frontend bucket ───────────┐   │
CloudWatch Log Group ─────┐   │   │
                          │   │   │
                          ▼   ▼   ▼
                   IAM Lambda Exec Role
                          │
                          ▼
                    Lambda Function
                          │
                ┌─────────┴──────────┐
                ▼                    ▼
          API Gateway        Lambda Function URL
                │                    │
                ▼                    ▼
         CloudFront Distribution (optional)
                │
                ▼
         Upload Frontend to S3
                │
                ▼
         Test:Smoke + Test:Security
```

### AWS MVP — Cost Model

| Resource | Free Tier (12 months) | Post-Free-Tier | Notes |
|----------|----------------------|----------------|-------|
| Lambda | 1M requests/mo, 400K GB-s | $0.20/1M req + $0.0000166/GB-s | arm64 = 20% cheaper |
| API Gateway (HTTP) | — | $1.00/1M requests | HTTP API, not REST API |
| S3 | 5GB, 20K GET, 2K PUT | $0.023/GB + $0.005/1K PUT | Negligible at this scale |
| CloudFront | 1TB transfer, 10M requests | $0.085/GB | PriceClass_100 |
| CloudWatch Logs | 5GB ingest, 5GB storage | $0.50/GB ingest | 30-day retention |
| **Total (100K req/mo)** | **$0.00** | **~$2.40/mo** | |

---

## 5. Graph 3 — AWS Clean Account Setup

This graph is for deploying onto a **brand new, pristine AWS account**. It covers everything that must happen BEFORE the MVP pod can be deployed, including removing dangerous defaults and establishing the security baseline.

```
┌─────────────────────────────────────────────────────────────────────┐
│  Deploy:Pod — aws-account-baseline                                  │
│  Target: Brand new AWS account (post root-user MFA setup)           │
│  Purpose: Harden account to "pod-ready" state                       │
│  ONE-TIME EXECUTION — then this pod is archived                     │
└─────────────────────────────────────────────────────────────────────┘

PHASE 0: ROOT USER LOCKDOWN (manual, console-only)
═══════════════════════════════════════════════════

  0a. Deploy:Task — Enable MFA on root user
      Type: Hardware token or Authenticator app
      Gate: MUST complete before anything else

  0b. Deploy:Task — Create bootstrap IAM user: pilates-bootstrap
      Policy: AdministratorAccess (TEMPORARY — will be removed in Phase 3)
      MFA: Required
      Purpose: "All subsequent steps use this user, never root"

  0c. Deploy:Task — Set account alias
      aws iam create-account-alias --account-alias pilates-{purpose}
      Example: pilates-prod, pilates-dev, pilates-qa

  0d. Deploy:Config — Enable billing alerts
      aws ce put-anomaly-monitor (or console: Billing → Budgets)
      Budget: $10/month alert, $50/month hard stop

PHASE 1: DELETE DANGEROUS DEFAULTS
═══════════════════════════════════

  1a. Deploy:Cleanup — Delete default VPCs (ALL regions)
      for region in $(aws ec2 describe-regions --query 'Regions[].RegionName' --output text); do
        aws ec2 describe-vpcs --region $region \
          --filters Name=isDefault,Values=true \
          --query 'Vpcs[].VpcId' --output text | \
        xargs -I {} aws ec2 delete-vpc --region $region --vpc-id {}
      done
      Rationale: Default VPCs have public subnets, IGWs — attack surface for Lambda-less setup

  1b. Deploy:Cleanup — Deactivate unused regions via SCP (if in AWS Org) or via IAM
      Keep ONLY: {target-region} + us-east-1 (required for IAM/CloudFront/global services)
      InlinePolicy on all non-root users:
        Deny: "*"
        Condition: StringNotEquals aws:RequestedRegion [{target-region}, us-east-1]

  1c. Deploy:Cleanup — Disable STS endpoints in unused regions
      for region in ap-southeast-1 ap-northeast-1 ...; do  # all except target + us-east-1
        aws iam set-security-token-service-preferences \
          --global-endpoint-token-version v2Token
        # Note: Can't fully disable, but restrict via IAM policy above
      done

  1d. Deploy:Cleanup — Delete default CloudWatch rules/alarms (if any)
      aws events list-rules --region {region} → delete any defaults
      aws cloudwatch describe-alarms → delete any defaults

PHASE 2: ESTABLISH SECURITY BASELINE
═════════════════════════════════════

  2a. Deploy:Resource — S3 Bucket: pilates-{account}-cloudtrail
      Encryption: SSE-S3
      PublicAccess: ALL blocked
      LifecycleRule: Transition to Glacier after 90 days, expire after 365 days
      BucketPolicy: Allow only cloudtrail.amazonaws.com

  2b. Deploy:Resource — CloudTrail: pilates-management-trail
      IsMultiRegionTrail: true
      EnableLogFileValidation: true
      S3BucketName: → ref(cloudtrail bucket)
      EventSelectors:
        - ReadWriteType: All
          IncludeManagementEvents: true
      Note: "This is your forensic audit log. Non-negotiable."

  2c. Deploy:Permission — IAM Account-Level Settings
      │   aws iam update-account-password-policy \
      │     --minimum-password-length 14 \
      │     --require-symbols --require-numbers \
      │     --require-uppercase-characters --require-lowercase-characters \
      │     --max-password-age 90 \
      │     --password-reuse-prevention 24
      │
      └── Deploy:Config — Block public S3 access (account level)
          aws s3control put-public-access-block \
            --account-id {account} \
            --public-access-block-configuration \
              BlockPublicAcls=true,IgnorePublicAcls=true,\
              BlockPublicPolicy=true,RestrictPublicBuckets=true

  2d. Deploy:Permission — IAM Permissions Boundary: pilates-pod-boundary
      Purpose: "Maximum permission envelope — NO role in this account can exceed this"
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "AllowOnlyPilatesResources",
            "Effect": "Allow",
            "Action": [
              "s3:GetObject", "s3:PutObject", "s3:ListBucket", "s3:DeleteObject",
              "lambda:InvokeFunction", "lambda:UpdateFunctionCode",
              "lambda:UpdateFunctionConfiguration",
              "logs:CreateLogGroup", "logs:CreateLogStream", "logs:PutLogEvents",
              "logs:DescribeLogGroups", "logs:GetLogEvents",
              "apigateway:*",
              "cloudfront:CreateInvalidation", "cloudfront:GetDistribution",
              "cloudwatch:PutMetricData", "cloudwatch:GetMetricData",
              "xray:PutTraceSegments", "xray:PutTelemetryRecords"
            ],
            "Resource": "*",
            "Condition": {
              "StringEquals": { "aws:ResourceTag/project": "pilates" }
            }
          },
          {
            "Sid": "DenyDangerousActions",
            "Effect": "Deny",
            "Action": [
              "iam:CreateUser", "iam:DeleteUser", "iam:CreateLoginProfile",
              "iam:AttachUserPolicy", "iam:DetachUserPolicy",
              "organizations:*", "account:*",
              "ec2:RunInstances", "ec2:CreateVpc",
              "rds:CreateDBInstance", "rds:CreateDBCluster",
              "eks:CreateCluster", "ecs:CreateCluster",
              "s3:CreateBucket"
            ],
            "Resource": "*"
          }
        ]
      }

PHASE 3: CREATE MINIMAL ROLE SET
═════════════════════════════════

  Only TWO roles should exist for ongoing operations:

  3a. Deploy:Permission — IAM Role: pilates-deployer
      AssumeRolePolicy:
        - sts:AssumeRole from CI/CD (GitHub Actions OIDC or specific IAM user)
      PermissionsBoundary: → ref(pilates-pod-boundary)
      InlinePolicy: (see Graph 2, deploy role)
      Tags: { project: pilates, role-type: deployer }
      MFA: Required for console access

  3b. Deploy:Permission — IAM Role: pilates-lambda-exec
      AssumeRolePolicy: lambda.amazonaws.com
      PermissionsBoundary: → ref(pilates-pod-boundary)
      InlinePolicy: (see Graph 2, execution role)
      Tags: { project: pilates, role-type: execution }

  3c. Deploy:Cleanup — Delete bootstrap user
      aws iam delete-user --user-name pilates-bootstrap
      Rationale: "No permanent IAM users. Access is via role assumption only."

  3d. Deploy:Gate — Final account state verification
      EXPECTED ROLES: exactly 2 (pilates-deployer, pilates-lambda-exec)
      EXPECTED USERS: 0 (root only, MFA-protected, no API keys)
      EXPECTED VPCs: 0
      EXPECTED S3 BUCKETS: 1 (cloudtrail)
      EXPECTED TRAILS: 1

PHASE 4: READY FOR MVP POD
═══════════════════════════

  → Execute Graph 2 (AWS MVP) using pilates-deployer role
```

### Clean Account — Execution Order

```
[ROOT USER MFA] ──► [Bootstrap IAM user] ──► [Account alias + billing]
                                                      │
                          ┌───────────────────────────┘
                          ▼
              ┌── Delete default VPCs (all regions)
              ├── Restrict to target regions (IAM policy)
              ├── Disable STS in unused regions
              └── Delete default CW rules
                          │
                          ▼
              ┌── CloudTrail bucket
              ├── CloudTrail trail
              ├── Password policy
              ├── Account-level S3 public access block
              └── Permissions boundary
                          │
                          ▼
              ┌── Create pilates-deployer role
              ├── Create pilates-lambda-exec role
              └── DELETE bootstrap user
                          │
                          ▼
              Deploy:Gate — verify minimal state
                          │
                          ▼
              READY → Execute Graph 2 (MVP Pod)
```

---

## 6. Graph 4 — AWS Enterprise Multi-Region (Hardening + Observability)

This graph **extends** the MVP pod (Graph 2) with enterprise-grade features: multi-region deployment, per-region dev/qa/prod, advanced logging, security controls, and cost optimization.

### 6.1 Topology

```
                      ┌──────────────────────────┐
                      │   Global Resources        │
                      │   (us-east-1 always)      │
                      │                           │
                      │   • Route 53 Hosted Zone  │
                      │   • CloudFront (global)   │
                      │   • ACM Certificate       │
                      │   • IAM Roles (global)    │
                      │   • CloudTrail (global)   │
                      └──────────┬───────────────┘
                                 │
          ┌──────────────────────┼──────────────────────┐
          │                      │                      │
          ▼                      ▼                      ▼
   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
   │  eu-west-1   │    │  eu-west-2   │    │  eu-central-1│
   │  (Ireland)   │    │  (London)    │    │  (Frankfurt) │
   │              │    │              │    │              │
   │  ┌────────┐  │    │  ┌────────┐  │    │  ┌────────┐  │
   │  │  dev   │  │    │  │  dev   │  │    │  │  dev   │  │
   │  │  Pod   │  │    │  │  Pod   │  │    │  │  Pod   │  │
   │  ├────────┤  │    │  ├────────┤  │    │  ├────────┤  │
   │  │  qa    │  │    │  │  qa    │  │    │  │  qa    │  │
   │  │  Pod   │  │    │  │  Pod   │  │    │  │  Pod   │  │
   │  ├────────┤  │    │  ├────────┤  │    │  ├────────┤  │
   │  │  prod  │  │    │  │  prod  │  │    │  │  prod  │  │
   │  │  Pod   │  │    │  │  Pod   │  │    │  │  Pod   │  │
   │  └────────┘  │    │  └────────┘  │    │  └────────┘  │
   └──────────────┘    └──────────────┘    └──────────────┘

   Optional additional regions:
   • us-east-1 (Virginia)  — if US users needed
   • ap-southeast-1 (Singapore) — if APAC users needed
```

### 6.2 Additional Resources per Region

```
ENHANCEMENT: LOGGING & TRACING (per region, per tier)
═════════════════════════════════════════════════════

  Deploy:Resource — X-Ray Tracing
      Lambda: TracingConfig Mode=Active
      API Gateway: TracingEnabled=true
      Purpose: End-to-end request tracing across Lambda + S3

  Deploy:Resource — CloudWatch Logs Insights Queries (saved)
      Queries:
        - "Cold starts": filter @type = "REPORT" | stats avg(@initDuration)
        - "Errors": filter @message like /ERROR/
        - "Slow requests": filter @duration > 5000
        - "Vote activity": filter @message like /POST.*vote/

  Deploy:Resource — CloudWatch Alarms (per tier)
      Lambda Errors > 5 in 5min → SNS → email
      Lambda Duration p99 > 10s → SNS → email
      API Gateway 5xx > 10 in 5min → SNS → email
      S3 bucket size > 1GB → SNS → email (cost protection)
      Monthly cost > $10 → SNS → email

  Deploy:Resource — CloudWatch Dashboard: pilates-{region}-{tier}
      Widgets:
        - Lambda invocations (line chart, 5min resolution)
        - Lambda duration p50/p95/p99
        - Lambda errors count
        - API Gateway request count by status code
        - S3 bucket size
        - Estimated monthly cost

ENHANCEMENT: SECURITY HARDENING (per region)
════════════════════════════════════════════

  Deploy:Resource — KMS Key: pilates-{region}-encryption
      KeyPolicy: only pilates-lambda-exec can Encrypt/Decrypt
      Usage: S3 SSE-KMS (instead of SSE-S3)
      Rotation: Automatic (annual)
      Note: "Server-side encryption upgrade — the client-side encryption
             with the organiser's key is SEPARATE and remains the primary
             privacy mechanism"

  Deploy:Permission — Lambda Resource Policy
      Allow: apigateway.amazonaws.com (source API Gateway only)
      Deny: all other invocation sources
      Purpose: "Lambda cannot be invoked directly, only via API Gateway"

  Deploy:Resource — WAF v2 WebACL (on API Gateway or CloudFront)
      Rules:
        - AWSManagedRulesCommonRuleSet (XSS, SQLi, etc.)
        - RateLimit: 1000 req/5min per IP
        - GeoMatch: Allow only target countries (EU for this project)
      Cost: ~$5/mo + $0.60/1M requests

  Deploy:Resource — AWS Config Rules
      Rules:
        - s3-bucket-public-read-prohibited
        - s3-bucket-ssl-requests-only
        - iam-root-access-key-check
        - lambda-function-public-access-prohibited
        - cloudtrail-enabled
      Remediation: Auto-remediate where possible

ENHANCEMENT: MULTI-REGION DNS (global)
═══════════════════════════════════════

  Deploy:Resource — Route 53 Hosted Zone: pilates.example.com

  Deploy:Resource — Route 53 Health Checks (per region prod)
      Type: HTTPS
      ResourcePath: /health
      Regions: us-east-1, eu-west-1, ap-southeast-1

  Deploy:Resource — Route 53 Records
      pilates.example.com → CloudFront (global frontend)
      api.pilates.example.com → Latency-based routing:
        eu-west-1:   api-gw-eu-west-1.execute-api.eu-west-1.amazonaws.com
        eu-west-2:   api-gw-eu-west-2.execute-api.eu-west-2.amazonaws.com
        eu-central-1: api-gw-eu-central-1.execute-api.eu-central-1.amazonaws.com
      Failover: If health check fails, route to next-closest region

ENHANCEMENT: DEPLOYMENT PIPELINE (global)
═════════════════════════════════════════

  Deploy:Resource — S3 Bucket: pilates-artifacts-{account}
      Purpose: Lambda deployment packages (shared across regions)
      Replication: CRR to all target regions

  Deploy:Config — Deployment Order
      1. Deploy to ALL dev pods (parallel across regions)
      2. Run smoke tests on ALL dev pods
      3. Deploy to ALL qa pods (parallel)
      4. Run full test suite on ALL qa pods
      5. Deploy to prod eu-west-1 (canary region)
      6. Run smoke + security tests
      7. Deploy to remaining prod regions (parallel)
      8. Run full verification suite

  Deploy:Wire — GitHub Actions OIDC
      Provider: token.actions.githubusercontent.com
      Role: pilates-deployer
      Condition: repo:org/pilates-scheduler:ref:refs/heads/main
      Purpose: "Keyless deployment — no stored AWS credentials in CI"
```

### 6.3 Enterprise — Full Issues FS Tree

```
EPIC: Deployment Pods — Pilates Scheduler
│
├── STORY: Abstract Pod Definition
│   ├── TASK: Define resource ontology (compute, storage, network, permissions, observability)
│   ├── TASK: Define permission model (setup-role vs execution-role)
│   ├── TASK: Define verification gates
│   ├── TASK: Define cost model schema
│   └── TEST: Validate abstract pod completeness (all resources have concrete mappings)
│
├── STORY: AWS Clean Account Baseline (Graph 3)
│   ├── TASK: Root user MFA + bootstrap user
│   ├── TASK: Delete default VPCs (all regions)
│   ├── TASK: Region restriction IAM policy
│   ├── TASK: CloudTrail setup
│   ├── TASK: Account-level S3 public access block
│   ├── TASK: Permissions boundary definition
│   ├── TASK: Create minimal role set (deployer + executor)
│   ├── TASK: Delete bootstrap user
│   ├── DEPLOY:GATE: Verify minimal account state
│   └── TEST: Account hardening verification
│
├── STORY: AWS MVP Pod (Graph 2) — per {region} × {tier}
│   ├── TASK: S3 votes bucket (per region, per tier)
│   ├── TASK: S3 frontend bucket (per region, per tier)
│   ├── TASK: CloudWatch log group
│   ├── TASK: Lambda function (M-Graph/FastAPI)
│   ├── TASK: API Gateway (HTTP API)
│   ├── TASK: CloudFront distribution
│   ├── TASK: Upload frontend
│   ├── TEST:SMOKE: Health check
│   ├── TEST:SMOKE: Full voter flow
│   └── TEST:SECURITY: S3 access, Lambda isolation
│
├── STORY: Enterprise Hardening (Graph 4)
│   ├── TASK: KMS key per region
│   ├── TASK: X-Ray tracing
│   ├── TASK: CloudWatch dashboards
│   ├── TASK: CloudWatch alarms
│   ├── TASK: WAF v2 WebACL
│   ├── TASK: AWS Config rules
│   ├── TASK: Route 53 hosted zone + latency routing
│   ├── TASK: Route 53 health checks
│   ├── TASK: GitHub Actions OIDC federation
│   ├── TASK: Deployment pipeline (dev → qa → canary-prod → all-prod)
│   ├── TEST:SECURITY: Full penetration test
│   └── TEST:SECURITY: IAM policy simulation (all deny paths)
│
└── STORY: Cost Model & Reporting
    ├── TASK: Tag all resources (project, tier, region, pod)
    ├── TASK: AWS Cost Explorer — cost allocation by tag
    ├── TASK: Monthly cost report per pod
    └── SPIKE: Evaluate Savings Plans for Lambda (if >$50/mo)
```

### 6.4 Enterprise Cost Model (3 regions × 3 tiers = 9 pods)

| Component | Per Pod (idle) | Per Pod (100K req/mo) | 9 Pods Total |
|-----------|---------------|----------------------|--------------|
| Lambda (arm64) | $0.00 | $0.42 | $3.78 |
| API Gateway | $0.00 | $0.10 | $0.90 |
| S3 (votes + frontend) | $0.05 | $0.08 | $0.72 |
| CloudWatch Logs | $0.00 | $0.25 | $2.25 |
| CloudFront | $0.00 | $0.10 | $0.90 |
| CloudTrail | $0.00 | $0.00 | $0.00 (free tier) |
| X-Ray | $0.00 | $0.50 | $4.50 |
| WAF (prod only) | $5.00 | $5.60 | $16.80 (3 prod) |
| Route 53 | — | — | $2.00 (zone + checks) |
| KMS | $1.00/key | $1.00 | $3.00 (3 regions) |
| **Total** | | | **~$35/mo** |

*Dev/QA pods will have near-zero traffic. Prod pods dominate cost. WAF is the largest line item and is optional.*

---

## 7. Mapping to Scaleway (Migration Path)

For reference, the abstract pod (Graph 1) maps to Scaleway as follows:

| Abstract Resource | AWS (Graphs 2–4) | Scaleway |
|-------------------|-------------------|----------|
| serverless-function | Lambda (Python 3.12) | Serverless Container (Docker/FastAPI) |
| api-gateway | API Gateway (HTTP API) | Built into Serverless Container endpoint |
| object-store | S3 | Object Storage (S3-compatible, same boto3) |
| static-site | S3 + CloudFront | Object Storage (static website hosting) |
| logging | CloudWatch Logs | Cockpit (Grafana/Loki) |
| metrics | CloudWatch Metrics | Cockpit (Grafana/Mimir) |
| permissions (setup) | IAM Role (deployer) | IAM API Key (Project-scoped) |
| permissions (exec) | IAM Role (lambda-exec) | Inherent (container identity) |
| dns | Route 53 | Scaleway Domains or external |
| tls | ACM | Auto-managed by Serverless Containers |
| account isolation | AWS Account | Scaleway Project |

The abstract pod ensures your deployment logic is portable. The `boto3` S3 calls work identically — only the endpoint URL changes.

---

## 8. Next Steps

1. **Implement Graph 3** (clean account) as a CloudFormation / CDK stack or Terraform module
2. **Implement Graph 2** (MVP pod) as a parameterised M-Graph deployment, accepting `{region}` and `{tier}` as inputs
3. **Create the Issues FS tree** (Section 6.3) and use it to track execution of each graph
4. **Build the cost reporter** — tag-based Cost Explorer query, runnable as a Lambda on a CRON
5. **Wire up Graph 4** features incrementally (X-Ray first, then WAF, then multi-region DNS)

*Each graph is independently executable. Start with Graph 3 + Graph 2 (single region, single tier) to validate the pod concept, then scale out.*
