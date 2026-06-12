# Azure Platform Engineering Lab

This repository demonstrates an enterprise-style Azure DevOps workflow for infrastructure automation using Azure DevOps Pipelines and Terraform.

## Objectives

- Use Azure DevOps Repos for source control
- Build a multi-stage YAML pipeline
- Separate Terraform validation, plan and apply stages
- Use Azure service connections securely
- Prepare for environment approvals before production deployment
- Document enterprise DevOps practices

## Repository Structure

```text
.
├── pipelines/
│   └── azure-pipelines.yml
├── terraform/
│   ├── main.tf
│   ├── providers.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── terraform.tfvars.example
└── docs/
    ├── 01-pipeline-overview.md
    ├── 02-service-connection.md
    ├── 03-variable-groups.md
    ├── 04-terraform-plan-apply.md
    └── 05-production-approvals.md