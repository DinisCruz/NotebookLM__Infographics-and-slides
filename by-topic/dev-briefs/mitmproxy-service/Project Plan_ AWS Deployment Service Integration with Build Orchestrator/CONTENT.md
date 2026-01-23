# Project Plan: AWS Deployment Service Integration with Build Orchestrator

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This project plan details the creation of an AWS integration web service that works with an existing build orchestrator to automate complete AWS environment setup and teardown on-demand. The service exposes fine-grained AWS operations (primitives) as REST API endpoints—S3 buckets, EC2/AMI, Lambda functions, Auto Scaling Groups, Target Groups—while the build orchestrator sequences these operations. A key innovation is the secure credential handling via public key encryption: the orchestrator encrypts AWS credentials using the service's public key, passes the encrypted blob with each API call, and only the AWS service can decrypt them. This "primitives not workflows" approach keeps orchestration logic in the build service while the AWS service executes atomic operations.

---

## Key Concepts

- **Primitives-Based API Design**: Each AWS operation exposed as a dedicated REST endpoint (create-bucket, launch-instance, create-lambda, etc.) enabling the orchestrator to compose complex deployment workflows from atomic building blocks.

- **Secure Credential Forwarding**: Public key encryption pattern where AWS service provides its public key, orchestrator encrypts credentials per-call, and only the AWS service can decrypt—credentials never stored, purged after each operation.

- **Orchestrator-Driven Flow**: Build service acts as the "brain" knowing the desired deployment workflow, calling AWS service endpoints in sequence, handling dependencies and conditional logic—the AWS service executes, doesn't orchestrate.

- **OSBot-AWS Library Integration**: Leverages OWASP Security Bot's high-level Pythonic wrappers around boto3, providing simplified APIs for S3, EC2, Lambda, Auto Scaling without boilerplate SDK code.

- **Multi-Mode Deployment Support**: Architecture supports both serverless (Lambda with Function URLs) and serverful (EC2 instances behind Auto Scaling Groups with ALB) deployment modes—orchestrator chooses which primitives to call.

- **Verification Endpoints Pattern**: Each create action paired with check endpoint allowing orchestrator to confirm resource creation before proceeding (e.g., check-bucket, check-lambda, check-asg-health).

---

## Core Arguments

1. Manual AWS setup tasks (configuring serverless components, infrastructure) are error-prone and time-consuming; automation via the build orchestrator calling the AWS service enables one-click environment provisioning with consistency and repeatability.

2. The primitives approach provides maximum flexibility—the orchestrator can implement conditional logic, loops, parallelism, and custom error handling that's harder to achieve with purely declarative templates like CloudFormation.

3. Secure credential forwarding via public key encryption ensures the build orchestrator never handles raw AWS secrets, aligning with the proven pattern already used for GitHub integration in the existing system.

4. Each API call carries encrypted credentials, allowing the service to be stateless and enabling each call to potentially target different AWS accounts/regions—no hard-coded account details, everything parameter-driven.

5. The separation of concerns (orchestrator owns workflow logic, AWS service owns execution) makes the system more flexible and testable—components can be developed, tested, and scaled independently.

6. OSBot-AWS accelerates development by providing type-safe wrappers over boto3, reducing complexity and ensuring reliability; the existing osbot-fast-api integration enables rapid REST API scaffolding.

---

## Key Quotes

> "The build service will handle orchestration using fine-grained API calls, with a strong emphasis on security (no direct credential exposure) and flexibility in deployment targets."

> "The build orchestrator is like the conductor, and the AWS service provides the instruments."

> "This design ensures secure credential forwarding – the build orchestrator never needs to handle raw AWS secrets."

> "By exposing these granular operations, we allow not only the orchestrated full deployment but also the possibility to run or test individual actions in isolation."

---

## Tags

`aws-automation` `build-orchestrator` `primitives-api` `public-key-encryption` `credential-security` `osbot-aws` `boto3` `fastapi` `infrastructure-as-code` `ec2` `lambda` `auto-scaling` `s3` `stateless-service`

---

## Search Phrases

- "AWS deployment service orchestrator integration"
- "primitives-based infrastructure API design"
- "secure credential forwarding public key encryption"
- "build orchestrator AWS automation pattern"
- "OSBot-AWS library boto3 wrappers"
- "stateless AWS operations service"
- "Lambda function URL deployment automation"
- "EC2 Auto Scaling Group orchestration"
- "one-click environment provisioning"
- "imperative infrastructure-as-code approach"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Project Plan |
| **Domain** | Dev Briefs / MitmProxy Service |
| **Sub-domain** | AWS Deployment / Build Automation |
| **Format** | PDF (14 pages) |
| **Date** | January 2026 |
| **Generated By** | ChatGPT |
| **Target Audience** | DevOps Engineers, Platform Engineers, Backend Developers |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `implements` | HTML UI Service for AWS Deployment |
| `uses` | OSBot-AWS Library |
| `integrates_with` | Build Orchestrator Service |
| `reviewed_by` | AWS Well-Architected Framework Review |
| `part_of` | MyFeeds.ai Service Architecture |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `service` | System service component |
| `endpoint` | REST API endpoint |
| `workflow` | Orchestration workflow |
| `phase` | Implementation phase |
| `resource` | AWS resource type |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `exposes` | `exposed_by` | API exposure |
| `orchestrates` | `orchestrated_by` | Workflow orchestration |
| `encrypts` | `decrypts` | Credential handling |
| `creates` | `created_by` | Resource creation |

### Taxonomy

```
aws_deployment_service
├── api_primitives
│   ├── s3_management
│   │   ├── create_bucket
│   │   ├── check_bucket
│   │   └── delete_bucket
│   ├── ec2_operations
│   │   ├── create_ami
│   │   ├── launch_instance
│   │   └── run_command
│   ├── lambda_functions
│   │   ├── create_lambda
│   │   ├── check_lambda
│   │   └── delete_lambda
│   └── auto_scaling
│       ├── create_target_group
│       ├── create_asg
│       └── check_asg_health
├── security
│   ├── public_key_retrieval
│   ├── credential_encryption
│   └── credential_purging
├── integration
│   ├── build_orchestrator
│   ├── osbot_aws_library
│   └── boto3_sdk
└── implementation_phases
    ├── api_specification
    ├── service_setup
    ├── endpoint_development
    ├── orchestrator_integration
    └── end_to_end_testing
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `aws_service` | `service` | AWS Integration Service |
| `orchestrator` | `service` | Build Orchestrator |
| `public_key_endpoint` | `endpoint` | GET /aws/public-key |
| `create_bucket` | `endpoint` | POST /aws/s3/bucket/create |
| `create_lambda` | `endpoint` | POST /aws/lambda/create |
| `create_asg` | `endpoint` | POST /aws/autoscaling/group/create |
| `deployment_workflow` | `workflow` | Full Deployment Flow |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `aws_service` | `exposes` | `create_bucket` |
| `aws_service` | `exposes` | `create_lambda` |
| `aws_service` | `exposes` | `create_asg` |
| `orchestrator` | `orchestrates` | `deployment_workflow` |
| `orchestrator` | `encrypts` | `credentials` |
| `aws_service` | `decrypts` | `credentials` |
| `deployment_workflow` | `creates` | `aws_resources` |

</details>
