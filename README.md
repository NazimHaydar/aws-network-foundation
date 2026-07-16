# AWS Network Foundation (Terraform)

A foundational AWS network built entirely with Terraform — VPC, subnets, routing, and security, following Infrastructure as Code practices.

## What this creates
- VPC (10.0.0.0/16)
- Public subnet (10.0.1.0/24) and private subnet (10.0.2.0/24)
- Internet Gateway + public route table for internet access
- Security group allowing SSH and HTTP
- Remote state stored in S3 with state locking (no local state files)

## Why these decisions
- **Public/private subnet split**: separates internet-facing resources from internal ones, a standard security pattern
- **Remote state**: enables team collaboration and prevents state corruption from concurrent changes — a production requirement, not just a local convenience
- **Security group scoping**: SSH/HTTP currently open broadly for development; production use would restrict SSH to specific IPs (planned in a follow-up security hardening project)

## Tech used
Terraform, AWS (VPC, EC2 networking, S3, IAM)

## How to run
\`\`\`
terraform init
terraform plan
terraform apply
\`\`\`

## Author
Nazim Haydar
