# Terraform Multi-Environment AWS Infrastructure + GitHub Actions CI/CD

## 概要 / Overview

**EN:** Production-grade AWS infrastructure provisioned entirely with Terraform, featuring a complete multi-environment setup (dev/prod) with automated CI/CD via GitHub Actions.

**JP:** TerraformとGitHub ActionsによるフルスタックAWSインフラ構成。開発環境と本番環境を分離した、実務レベルのIaCプロジェクトです。

---

## アーキテクチャ / Architecture
```
VPC (10.0.0.0/16)
├── Public Subnet  → EC2 App Server
└── Private Subnet → RDS MySQL 8.0

S3  → App Storage (encrypted, versioned)
S3  → Terraform Remote State
DynamoDB → State Locking
```

---

## 構成リソース / Resources

| Resource | Details |
|----------|---------|
| VPC | Custom VPC with public and private subnets |
| EC2 | Amazon Linux 2023, encrypted gp3 volume |
| RDS | MySQL 8.0, private subnet, encrypted |
| S3 | App bucket with versioning and AES256 encryption |
| S3 | Remote state backend |
| DynamoDB | Terraform state locking |
| IAM | EC2 role with SSM access |
| Security Groups | RDS only accessible from EC2 security group |

---

## 環境差分 / Environment Differences

| Setting | Dev | Prod |
|---------|-----|------|
| EC2 | t3.micro | t3.medium |
| RDS | db.t3.micro | db.t3.small |
| Availability Zones | 2 | 3 |
| Multi-AZ RDS | No | Yes |
| Deletion Protection | No | Yes |
| Final Snapshot | No | Yes |

---

## CI/CD パイプライン / CI/CD Pipeline

**terraform-plan.yml**
- Triggers on Pull Request to main
- Runs: init, validate, plan
- Posts plan output as PR comment

**terraform-apply.yml**
- Triggers on merge to main
- Runs: init, apply -auto-approve
- Deploys infrastructure automatically

---

## セットアップ / Getting Started

### 前提条件 / Prerequisites
- Terraform >= 1.5.0
- AWS CLI configured
- AWS account (free tier works for dev)

### 手順 / Steps

1. Clone the repository
```bash
git clone https://github.com/amishanita/terraform-multi.git
cd terraform-multi
```

2. Initialize Terraform
```bash
terraform init
```

3. Deploy
```bash
terraform apply -auto-approve
```

### GitHub Actions Secrets

Add these in Settings → Secrets → Actions:

| Secret | Description |
|--------|-------------|
| AWS_ACCESS_KEY_ID | IAM user access key |
| AWS_SECRET_ACCESS_KEY | IAM user secret key |

---

## 技術スタック / Tech Stack

- IaC: Terraform (HCL)
- Cloud: AWS (VPC, EC2, RDS, S3, DynamoDB, IAM)
- CI/CD: GitHub Actions
- OS: Amazon Linux 2023
- Database: MySQL 8.0

---

## 作者 / Author

Anita Tamang
GitHub: https://github.com/amishanita
