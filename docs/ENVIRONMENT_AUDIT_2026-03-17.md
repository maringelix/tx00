# Environment Audit — March 17, 2026

> Full pre-deployment audit of tx01 (AWS/EKS IAC) and dx01 (application) including
> GitHub Actions health, secrets inventory, infrastructure cost model, and AWS credits status.

---

## 1. Cloud CLI Status

| CLI | User / Account | Status |
|-----|---------------|--------|
| AWS | `devops-tx01` / `REDACTED` | ✅ Active |
| GCP | `redacted@example.com` / `REDACTED` | ✅ Active () |
| Azure | Not installed | ❌ Not applicable for portfolio |

**AWS Access Keys:**
- `REDACTED` — Active, created 2025-11-29 (~3.5 months old)
- `REDACTED` — Active, created 2025-11-29 (~3.5 months old)

> ⚠️ Two active keys — best practice is to keep only one. Rotate/delete one before going to production.

---

## 2. GitHub — TX01 (`maringelix/tx01`)

### 2.1 Repo Secrets

| Secret | Status |
|--------|--------|
| `AWS_ACCESS_KEY_ID` | ✅ |
| `AWS_SECRET_ACCESS_KEY` | ✅ |
| `GRAFANA_PASSWORD` | ✅ |
| `SLACK_WEBHOOK_URL` | ✅ |

### 2.2 Environments

| Environment | Status |
|-------------|--------|
| `stg` | ✅ Configured |
| `staging` | ✅ Configured (duplicate) |
| `prd` | ❌ **Missing** — `tf-deploy.yml` accepts `prd` as input but no GitHub environment exists |

### 2.3 Workflows (20 total — all `active`)

| Workflow | Last Status |
|----------|------------|
| 🚀 Terraform Deploy | — (manual trigger) |
| ✅ Terraform Plan and Validate | ❌ Failing (fmt check) |
| 🧪 Terraform Tests | ❌ Failing (tflint) |
| 🏗️ Terraform Bootstrap | — (manual trigger) |
| ⚡ Manage Environment (EKS + RDS) | — (manual trigger) |
| 🗑️ Destroy AWS Environment | ✅ Last run: success (2025-12-12) |
| All others (14 workflows) | — (manual trigger, never run) |

---

## 3. GitHub — DX01 (`maringelix/dx01`)

### 3.1 Repo Secrets

| Secret | Status |
|--------|--------|
| `AWS_ACCESS_KEY_ID` | ✅ |
| `AWS_SECRET_ACCESS_KEY` | ✅ |
| `AWS_ACCOUNT_ID` | ✅ |
| `EC2_SSH_KEY` | ✅ |

### 3.2 Environments

| Environment | Status |
|-------------|--------|
| Any | ❌ **None configured** — deploy workflow has env input but no GitHub environments created |

### 3.3 Workflows (2 total — all `active`)

| Workflow | Last Status |
|----------|------------|
| 🚀 Deploy DX01 Application | ❌ Last run: failure (2025-12-09) |
| 🧪 Tests | ❌ Failing (2026-02-23) |

---

## 4. Blockers — Must Fix Before Deploy

### 4.1 `terraform fmt` — 12 files out of format
CI fails immediately at the format check step. Files:
```
terraform/modules/alb.tf
terraform/modules/ec2.tf
terraform/modules/ecr.tf
terraform/modules/eks.tf
terraform/modules/rds.tf
terraform/modules/vpc.tf
terraform/modules/waf.tf
terraform/prd/main.tf
terraform/prd/terraform.tfvars
terraform/stg/main.tf
terraform/stg/terraform.tfvars
terraform/variables.tf
```
**Fix:** `terraform fmt -recursive terraform/`

### 4.2 `tflint` — wrong flag syntax
```
Failed to parse CLI options; Invalid value 'ormat' for option -f, --format
```
Workflow uses `-format compact` (single dash) → must be `--format compact` (double dash).

**File:** `.github/workflows/terraform-validate.yml`

### 4.3 GitHub environment `prd` missing in TX01
`tf-deploy.yml` references `environment: ${{ inputs.environment }}` which supports `stg` and `prd`.
Only `stg`/`staging` exist — `prd` environment was never created on GitHub.

### 4.4 Terraform State Backend — not verified
S3 bucket `tx01-terraform-state-maringelix-2025` and DynamoDB table
`tx01-terraform-state-maringelix-2025-locks` must exist before any `terraform init`.
If it is the first run after destroy, **bootstrap must run first** via the
`terraform-bootstrap.yml` workflow.

---

## 5. Infrastructure Inventory (STG — as declared in tfvars)

| Resource | Spec | Notes |
|----------|------|-------|
| EKS Control Plane | 1 cluster | `enable_eks = true` |
| EKS Nodes | 4× `t3.small` desired (min 2, max 6) | Full observability stack |
| EC2 Instances | 0 | `instance_count = 0` — EKS only |
| RDS | `db.t4g.micro`, 20 GB, PostgreSQL, single-AZ | `multi_az = false` |
| ALB | 1× Application Load Balancer | — |
| NAT Gateway | **2×** (one per AZ, hardcoded) | Major cost driver |
| Elastic IPs | 2× | Tied to NAT GWs |
| WAF | 1× WebACL | RateLimit + AWSManagedRulesCommonRuleSet |
| ECR | 1 repository (`dx01-app`) | Used by dx01 deploy |

---

## 6. AWS Credits & Cost Estimation

### 6.1 AWS Free Plan Status (as of 2026-03-17)

| Item | Value |
|------|-------|
| Credits remaining | **$68.25 USD** |
| Free plan expiry | **May 26, 2026 (71 days)** |
| Current spend (March) | $0.00 |
| Spend since Nov 2025 | ~$0 net (credits absorbed all costs) |

### 6.2 Daily Cost Breakdown — Scenario: STG with EKS (current tfvars)

| Service | Rate | $/day |
|---------|------|-------|
| EKS Control Plane | $0.10/hr | $2.40 |
| 4× t3.small nodes | $0.0208/hr each | $2.00 |
| 2× NAT Gateway | $0.045/hr each | $2.16 |
| RDS db.t4g.micro | $0.016/hr | $0.38 |
| ALB | $0.023/hr | $0.55 |
| WAF (WebACL + 2 rules) | ~$0.23/day | $0.23 |
| ECR / S3 / CloudWatch / SM | misc | ~$0.10 |
| **Total** | | **~$7.82/day** |

### 6.3 Alternative Scenarios

| Scenario | $/day | Days with $68.25 |
|----------|-------|-----------------|
| STG — EKS enabled (current) | ~$7.82 | **~8.7 days** |
| STG — EKS disabled (2× EC2 t3.micro) | ~$3.66 | **~18.6 days** |
| STG — EKS disabled + 1 NAT GW | ~$2.50 | **~27.3 days** |
| STG — EKS disabled + no NAT GW | ~$1.34 | **~50.9 days** |

> **Recommendation:** With only $68.25 and expiry on May 26, the EKS scenario gives
> only ~8 days of runtime. Disabling EKS and reducing NAT GWs to 1 stretches the
> credits to ~27 days — better for a portfolio demo cycle.

---

## 7. Next Steps (when ready to proceed)

1. Fix `terraform fmt` (tx01)
2. Fix `tflint --format` flag in `terraform-validate.yml` (tx01)
3. Create `prd` environment in GitHub for tx01
4. Create `stg`/`prd` environments in GitHub for dx01
5. Decide: EKS vs EC2-only to optimize credit burn
6. Run `terraform-bootstrap.yml` to provision S3 state bucket + DynamoDB
7. Run `tf-deploy.yml` (action: apply, env: stg)
8. Run `deploy-dx01.yml` to deploy application
9. Delete one of the two active AWS IAM access keys
