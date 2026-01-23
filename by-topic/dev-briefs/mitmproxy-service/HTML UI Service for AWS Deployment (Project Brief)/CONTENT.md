# HTML UI Service for AWS Deployment (Project Brief)

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This project brief describes an HTML UI service for automated AWS environment deployment, integrating with an existing build orchestrator. The service exposes AWS primitives (S3, EC2, Lambda, Auto Scaling, etc.) as individual REST API endpoints rather than encoding complex workflows internally. A key innovation is the secure credential handling via public key encryption—the UI service provides its public key, the orchestrator encrypts AWS credentials, and only the UI service can decrypt them for execution. This "primitives not workflows" approach ensures flexibility in how resources are composed and sequenced while keeping the UI service stateless and the orchestrator in control of deployment logic.

---

## Key Concepts

- **AWS Primitives as API Calls**: Each key AWS action exposed as a dedicated endpoint (create-s3-bucket, launch-ami-instance, create-lambda-function, etc.) enabling granular control without hardcoding orchestration into the service.

- **Build Orchestrator Integration**: External orchestrator acts as the "brain" calling UI service endpoints in sequence; orchestration logic (when/what to execute) remains in build service while UI service performs individual AWS operations.

- **Secure Credential Pass-Through**: Public key encryption pattern where UI service provides its public key, orchestrator encrypts AWS credentials, and encrypted credentials are passed for decryption only within the secure UI service context.

- **Primitive Operations with Verification**: Each create action paired with a check/verify endpoint allowing orchestrator to confirm resource creation before proceeding to next step (e.g., create-s3-bucket + check-s3-bucket).

- **Stateless Service Design**: UI service maintains no complex deployment state; resource state tracked by orchestrator or queried from AWS directly, enabling scalability and crash recovery via simple re-invocation.

- **OSBot-AWS Library**: Leverages OWASP Security Bot toolkit's high-level AWS wrappers over Boto3 SDK, abstracting low-level AWS complexities for cleaner implementation.

---

## Core Arguments

1. On-demand environment deployment enables spinning up or tearing down entire application infrastructure from scratch in any AWS region through a simple UI or API call, supporting true infrastructure-as-code automation.

2. The "primitives not workflows" design ensures maximum flexibility—the UI service provides building blocks while the orchestrator controls composition, enabling complex pipelines or even AI agent-driven deployment.

3. Secure credential handling via public key encryption significantly reduces secret exposure—the build orchestrator never stores raw AWS credentials, and credentials are only usable within the UI service's secure context.

4. Stateless service design enables horizontal scaling, parallel deployments, and simple crash recovery since each orchestrator request is independent and context can be passed in API calls.

5. Verification endpoints for each primitive enable robust error handling—orchestrator confirms each step before proceeding, allowing conditional flows and rollback logic without burdening the UI service.

6. OSBot-AWS library accelerates development by providing convenient, high-level AWS operations, ensuring consistency and reducing boilerplate Boto3 code throughout the service.

---

## Key Quotes

> "Rather than define complex workflows internally, the UI service's scope is to provide primitive operations for AWS."

> "The build orchestrator itself will not store or directly handle sensitive AWS credentials in plain text."

> "The orchestration logic (the 'when and what to execute' ordering of steps) remains in the build service, while the UI service focuses on performing individual AWS operations."

> "This design makes the system flexible and easy to integrate into complex pipelines or even to be driven by an AI agent or script."

---

## Tags

`aws-automation` `infrastructure-as-code` `build-orchestrator` `rest-api` `credential-encryption` `public-key-crypto` `osbot-aws` `stateless-service` `deployment-primitives` `on-demand-infrastructure` `boto3` `fastapi`

---

## Search Phrases

- "AWS deployment automation HTML UI service"
- "infrastructure primitives REST API pattern"
- "secure credential pass-through public key"
- "build orchestrator AWS integration"
- "stateless infrastructure deployment service"
- "OSBot-AWS library Boto3 wrapper"
- "on-demand environment provisioning"
- "verification endpoints deployment pipeline"
- "primitives not workflows architecture"
- "AWS credential encryption pattern"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Project Brief |
| **Domain** | Dev Briefs / MitmProxy Service |
| **Sub-domain** | AWS Deployment / Infrastructure Automation |
| **Format** | PDF (5 pages) |
| **Date** | January 2026 |
| **Generated By** | ChatGPT |
| **Target Audience** | DevOps Engineers, Platform Engineers, Backend Developers |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `integrates_with` | Build Orchestrator Service |
| `uses` | OSBot-AWS Library |
| `related_to` | Project Plan: AWS Deployment Service Integration |
| `related_to` | AWS Well-Architected Framework Review |
| `part_of` | MyFeeds.ai Service Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `endpoint` | REST API endpoint |
| `component` | System component |
| `pattern` | Architectural pattern |
| `feature` | Service feature |
| `deliverable` | Project deliverable |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `exposes` | `exposed_by` | API exposure |
| `integrates` | `integrated_by` | System integration |
| `enables` | `enabled_by` | Capability enablement |
| `secures` | `secured_by` | Security relationship |

### Taxonomy

```
html_ui_service
├── api_endpoints
│   ├── create_s3_bucket
│   ├── check_s3_bucket
│   ├── create_ami
│   ├── launch_ami_instance
│   ├── create_target_group
│   ├── create_auto_scaling_group
│   └── create_lambda_function
├── integration_points
│   ├── build_orchestrator
│   ├── osbot_aws_library
│   └── aws_services
├── security_features
│   ├── public_key_encryption
│   ├── credential_pass_through
│   └── secure_decryption_context
└── deployment_modes
    ├── fresh_deployment
    ├── update_existing
    └── secure_mode
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `ui_service` | `component` | HTML UI Service |
| `orchestrator` | `component` | Build Orchestrator |
| `credential_pattern` | `pattern` | Public Key Credential Encryption |
| `primitives_pattern` | `pattern` | Primitives Not Workflows |
| `s3_endpoint` | `endpoint` | Create S3 Bucket |
| `lambda_endpoint` | `endpoint` | Create Lambda Function |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `ui_service` | `exposes` | `s3_endpoint` |
| `ui_service` | `exposes` | `lambda_endpoint` |
| `orchestrator` | `integrates` | `ui_service` |
| `credential_pattern` | `secures` | `ui_service` |
| `primitives_pattern` | `enables` | `orchestrator` |

</details>
