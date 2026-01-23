# Publishing an AMI on AWS Marketplace: Process, Pricing Models & Considerations

> *Semantic Knowledge Graph (SKG) - markdown serialization for search, discovery, and graph database integration*

---

## Summary

This comprehensive guide details the process of publishing custom Amazon Machine Images (AMIs) on AWS Marketplace, covering seller onboarding, AMI preparation, security requirements, and commercialization options. The document specifically addresses listing MITM proxy appliances and caching services, explaining pricing models from free/BYOL to hourly, annual, monthly, and usage-based metering. Key insight: AWS handles all billing and payments (taking ~20% commission), enabling global distribution without building a billing system — but sellers must carefully choose their pricing model upfront since changing from free to paid later requires special approval.

---

## Key Concepts

- **AWS Marketplace Seller Registration**: Onboarding process requiring business details, tax forms (W-9 for US, W-8 for non-US), and banking information to access the Marketplace Management Portal.

- **IAM Role for AMI Ingestion**: Role with "AWSMarketplace – AMI Assets Ingestion" trusted entity and AWSMarketplaceAmiIngestion policy, allowing AWS to copy and scan AMIs for vulnerability assessment.

- **Security Scanning**: Automated process where AWS copies AMI to their account, checks for vulnerabilities, open ports, malware, and hardcoded credentials — typically taking about one hour per AMI version.

- **Repackaging Requirements**: When monetizing open-source software, sellers must add value beyond base software, clearly disclose charges in short description, and include statement in long description about additional charges.

- **Usage-Based Metering**: Custom billing via Marketplace Metering Service based on metrics like users, data, bandwidth, or hosts — requires integrating AWSMarketplaceMetering SDK into software to report usage hourly.

- **Private Offers**: Custom negotiated deals with specific customers for multi-year contracts, volume discounts, or flexible payment schedules outside standard public listing pricing.

---

## Core Arguments

1. AWS Marketplace enables one-click deployment of custom AMIs as commercial products, handling billing, distribution, and global reach — sellers concentrate on improving their software and supporting users.

2. AMI preparation must follow strict guidelines: us-east-1 region, supported OS, no hardcoded credentials, SSH/RDP access enabled, latest security patches, and IAM role for scanning access.

3. Multiple pricing models accommodate different use cases — hourly for temporary testing environments (MITM proxy), monthly/annual subscriptions for always-on services (caching), usage-based for value-aligned billing.

4. Transparency requirements mandate clear disclosure of charges in product descriptions, especially when repackaging open-source software — product title should indicate value-add, not just base project name.

5. Usage instructions are critical for appliances — assume users are "interested but uninformed," document configuration steps, default credentials, network ports, and any external dependencies like S3 buckets or IAM roles.

6. Post-publication considerations include version updates (without full re-review), analytics dashboard access, customer support response, and the limitation that pricing model changes from free to paid require special approval.

---

## Key Quotes

> "AWS handles all billing and payments... customers are charged through their AWS account, and AWS then remits the revenue to you."

> "You cannot change from free to paid model later without special approval, so choose your model carefully up front."

> "The short description must explicitly state that charges apply and what they are for."

> "Components in the Product/Commodity stage can often be outsourced or purchased cost-effectively."

---

## Tags

`aws-marketplace` `ami-publishing` `seller-registration` `pricing-models` `hourly-billing` `annual-subscription` `usage-metering` `byol` `security-scanning` `iam-role` `mitm-proxy` `caching-service` `open-source-repackaging` `private-offers`

---

## Search Phrases

- "AWS Marketplace AMI publishing process"
- "seller registration AWS Marketplace"
- "AMI security scanning requirements"
- "hourly vs annual pricing AWS Marketplace"
- "usage-based metering Marketplace Metering Service"
- "repackaging open-source AWS Marketplace"
- "IAM role AMI ingestion policy"
- "private offers multi-year contracts"
- "MITM proxy AMI listing"
- "caching service AWS Marketplace"

---

## Metadata

| Field | Value |
|-------|-------|
| **Content Type** | Technical Guide / Process Documentation |
| **Domain** | Strategic Partnerships / AWS |
| **Sub-domain** | Marketplace Publishing / Commercialization |
| **Format** | PDF (11 pages) |
| **Date** | January 2026 |
| **Authors** | Dinis Cruz, ChatGPT Deep Research |
| **Target Audience** | Product Managers, DevOps Engineers, ISVs |

---

## Related Content

| Relationship | Content |
|--------------|---------|
| `related_to` | AWS Well-Architected Framework Review |
| `related_to` | Standalone (Air-Gapped) MITM Proxy Service |
| `uses` | MitmProxy Service |
| `uses` | Cache Service |
| `part_of` | MyFeeds.ai AWS Deployment Strategy |

---

## Semantic Knowledge Graph

<details>
<summary>Click to expand SKG structure (for graph database import)</summary>

### Ontology

#### Node Types

| Ref | Description |
|-----|-------------|
| `process_step` | A step in the publishing workflow |
| `pricing_model` | A commercialization model |
| `requirement` | A technical or documentation requirement |
| `artifact` | A produced output or document |
| `service` | An AWS service or component |

#### Predicates

| Ref | Inverse | Description |
|-----|---------|-------------|
| `precedes` | `follows` | Workflow order |
| `requires` | `required_by` | Dependency relationship |
| `enables` | `enabled_by` | Capability enablement |
| `produces` | `produced_by` | Output generation |

### Taxonomy

```
aws_marketplace_publishing
├── prerequisites
│   ├── seller_registration
│   ├── ami_preparation
│   ├── iam_role_creation
│   └── security_compliance
├── listing_process
│   ├── product_information
│   ├── ami_configuration
│   ├── usage_instructions
│   └── pricing_setup
├── pricing_models
│   ├── free
│   ├── byol
│   ├── hourly
│   ├── annual
│   ├── monthly
│   └── usage_based
└── post_publication
    ├── version_updates
    ├── analytics
    └── customer_support
```

### Graph

#### Nodes

| ID | Type | Name |
|----|------|------|
| `seller_registration` | `process_step` | Seller Registration |
| `ami_preparation` | `process_step` | AMI Preparation |
| `security_scanning` | `process_step` | Security Scanning |
| `hourly_pricing` | `pricing_model` | Hourly Pay-As-You-Go |
| `usage_metering` | `pricing_model` | Usage-Based Metering |
| `iam_role` | `requirement` | IAM Ingestion Role |
| `usage_instructions` | `artifact` | Usage Instructions |
| `marketplace_portal` | `service` | Marketplace Management Portal |

#### Edges

| From | Predicate | To |
|------|-----------|-----|
| `seller_registration` | `precedes` | `ami_preparation` |
| `ami_preparation` | `precedes` | `security_scanning` |
| `ami_preparation` | `requires` | `iam_role` |
| `security_scanning` | `enables` | `public_listing` |
| `listing` | `produces` | `usage_instructions` |
| `usage_metering` | `requires` | `sdk_integration` |

</details>
