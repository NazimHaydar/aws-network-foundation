# AWS Network Foundation (Terraform)

A production-style AWS network foundation built entirely with Terraform, following Infrastructure as Code practices — with remote state management and an automated CI pipeline.

## Architecture

- **VPC** (10.0.0.0/16)
- **Public subnet** (10.0.1.0/24) — internet-facing resources
- **Private subnet** (10.0.2.0/24) — internal resources, no direct internet exposure
- **Internet Gateway** — enables outbound/inbound internet access for the public subnet
- **NAT Gateway** — enables outbound-only internet access for the private subnet (demonstrated and validated; destroyed after testing to avoid ongoing cost — see note below)
- **Route tables** — separate public and private routing, explicitly associated with their respective subnets
- **Security group** — allows SSH (22) and HTTP (80) inbound, all outbound

## Infrastructure as Code practices

- **Remote state**: Terraform state is stored in an S3 bucket with state locking (via `use_lockfile`), rather than kept locally — this prevents state corruption from concurrent runs and enables team collaboration
- **Version control**: all infrastructure changes are tracked in Git with descriptive commit history
- **CI pipeline** (GitHub Actions): every push/PR automatically runs:
  - `terraform fmt -check` — enforces consistent code formatting
  - `terraform validate` — catches syntax/configuration errors
  - `terraform plan` — previews infrastructure changes before they're applied

## Design decisions

- **Public/private subnet split**: separates internet-facing resources from internal ones — a standard security pattern that limits attack surface
- **Remote state over local state**: local state files are a single point of failure and can't be safely shared across a team; S3 + locking is the production-standard approach
- **NAT Gateway tested, then torn down**: NAT Gateways incur hourly charges even when idle. It was provisioned, validated (private subnet successfully routed outbound traffic through it), and then destroyed to keep this a cost-conscious learning project. The Terraform code remains in this repo and can be re-applied at any time with `terraform apply`
- **Security group scope**: SSH/HTTP are currently open broadly (0.0.0.0/0) for development purposes. A production deployment would restrict SSH to specific trusted IPs — this hardening is planned as a follow-up project

## Tech stack

Terraform · AWS (VPC, EC2 networking, NAT Gateway, S3, IAM) · GitHub Actions

## Project structure

```
.
├── main.tf                          # All infrastructure resources
├── .gitignore                       # Excludes state files and secrets from Git
├── .github/workflows/terraform.yml  # CI pipeline definition
└── README.md
```

## How to run

```
terraform init
terraform plan
terraform apply
```

To tear down all resources:
```
terraform destroy
```

## Prerequisites

- AWS account with an IAM user (programmatic access)
- AWS CLI configured (`aws configure`)
- Terraform >= 1.5

## What I learned building this

- How to structure a Terraform project for real collaborative use (remote state, locking) rather than just local experimentation
- The practical mechanics of VPC networking as code — translating concepts I already knew from hands-on network administration (subnetting, routing, NAT) into declarative infrastructure
- How to wire infrastructure changes into a CI pipeline so that every change is validated automatically before being applied
- Cost-awareness as part of infrastructure design — provisioning resources deliberately, validating them, and tearing them down rather than leaving them running

## Author

**Nazim Haydar**
IT Support / Network Engineer transitioning into Cloud & DevOps Engineering
[GitHub](https://github.com/NazimHaydar)
