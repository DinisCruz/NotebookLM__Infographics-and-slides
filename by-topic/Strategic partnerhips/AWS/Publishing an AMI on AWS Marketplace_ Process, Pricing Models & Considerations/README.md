# Publishing an AMI on AWS Marketplace: Process, Pricing Models & Considerations

[Home](../../../../../README.md) > [By Topic](../../../../README.md) > [Strategic Partnerships](../../README.md) > [AWS](../README.md) > Publishing an AMI on AWS Marketplace

---

## Overview

Comprehensive guide for publishing custom Amazon Machine Images (AMIs) on AWS Marketplace, covering seller registration, AMI preparation, security scanning, pricing models, and post-publication considerations. Specifically addresses listing MITM proxy appliances and caching services with practical guidance on configuration, documentation, and commercialization strategies.

## Contents

| File | Description |
|------|-------------|
| `Publishing an AMI on AWS Marketplace_ Process, Pricing Models & Considerations.pdf` | Full guide (11 pages) |
| `22 Jan - Marketplace_AMI_Playbook.pdf` | Extended marketplace playbook |
| `22 Jan - AWS Marketplace AMI Publishing Path.jpg` | Visual publishing path diagram |

## Key Topics

- **Seller Registration**: AWS Marketplace Management Portal setup, tax forms (W-9/W-8), banking details
- **AMI Preparation**: Base AMI in us-east-1, security patches, OS credentials, IAM role for scanning
- **Security Scanning**: Automated vulnerability scanning, port verification, credential checks
- **Product Listing**: Descriptions, logos, usage instructions, EULA, security group rules
- **Pricing Models**: Free, BYOL, hourly, annual, monthly, usage-based metering, contract pricing
- **Repackaging Requirements**: Adding value beyond base software, disclosure statements for open-source

## Pricing Models Summary

| Model | Description |
|-------|-------------|
| Free | No software charges, customers pay only EC2 costs |
| BYOL | License distributed externally, AMI shows as free |
| Hourly | Pay-as-you-go metered by instance-hours |
| Annual | 1-year upfront commitment, typically discounted |
| Monthly | Fixed monthly fee regardless of runtime |
| Usage-Based | Custom metering (users, data, bandwidth, hosts) |
| Contract | Upfront fee for fixed term, often via private offers |

## Key Process Steps

| Step | Description |
|------|-------------|
| 1 | Build and test AMI thoroughly |
| 2 | Register as Marketplace seller |
| 3 | Create server product listing |
| 4 | Configure product info and branding |
| 5 | Set AMI details and IAM role ARN |
| 6 | Define security group rules |
| 7 | Write usage instructions |
| 8 | Configure pricing model |
| 9 | Submit for review and scanning |
| 10 | Approve and publish listing |

## Source

- **Authors**: Dinis Cruz and ChatGPT Deep Research
- **Date**: January 2026
- **Platform**: MyFeeds.ai / NotebookLM content pipeline

---

*Generated for NotebookLM content pipeline*
