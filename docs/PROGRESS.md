# Progress Log

> Chronological record of all audit and remediation activities.

---

## Session 1 — Initial Audit

**Date:** 2025-02-23

### Completed
- [x] Cloned all 6 portfolio repositories (tx01, tx02, tx03, dx01, dx02, dx03)
- [x] Performed comprehensive audit of each repository
- [x] Created consolidated audit report (`docs/AUDIT.md`)
- [x] Created prioritized remediation plan (`docs/REMEDIATION.md`)
- [x] Defined portfolio standards (`docs/STANDARDS.md`)
- [x] Created tx00 tracking repository

### Key Findings
- **8 P0 (Critical)** issues — primarily exposed secrets and default passwords
- **14 P1 (High)** issues — security misconfigs, fake tests, broken CI
- **10 P2 (Medium)** issues — missing securityContext, documentation issues
- **6 P3 (Low)** issues — supply chain, repository polish

---

## Session 2 — Full Remediation

**Date:** 2025-02-23

### Safety: Deploy Workflows (All 6 Repos)
- [x] Converted all deploy workflows to `workflow_dispatch` (manual-only)
- Affected: tx01, tx02, tx03, dx01, dx03 (dx02 was already manual)
- Reason: all cloud infrastructure destroyed; no cloud credits remain

### tx03 (GCP) — Committed `68aeb74`
- [x] Deleted 19 excess files (SESSION_CONTEXT, TROUBLESHOOTING, cost reports, .bat/.ps1)
- [x] Sanitized secrets: GCP project ID, Grafana admin password, passwords in markdown
- [x] Fixed: `destroy.yml` push trigger removed (was auto-destroying on push)
- [x] Prometheus values: project ID replaced with placeholder
- [x] Rewrote README.md (1202→130 lines, English)
- [x] Rewrote SECURITY.md (1191→40 lines)
- [x] Rewrote REFERENCE.md (1090→38 lines)

### tx02 (Azure) — Committed `eba8696`
- [x] Removed `terraform/prd/tfplan` binary from git (contained secrets)
- [x] Fixed .gitignore encoding (CRLF→LF) + added `tfplan` entry
- [x] SSH NSG: `source_address_prefix = "*"` → `var.ssh_allowed_cidr`
- [x] VM: `disable_password_authentication = true` (was false)
- [x] Database: `public_network_access_enabled = false` (was true)
- [x] ACR: `admin_enabled = false` (was true)
- [x] Grafana: hardcoded `adminPassword: admin` → comment referencing Helm override
- [x] Deleted 21 excess .md files + 4 .ps1 scripts + duplicate K8s files
- [x] Rewrote README.md (777→95 lines, English)

### tx01 (AWS) — Committed `4b78cca`
- [x] SSH security group: `0.0.0.0/0` → `var.ssh_allowed_cidr`
- [x] Removed `timestamp()` from provider default_tags (caused perpetual drift)
- [x] RDS `final_snapshot_identifier`: replaced `timestamp()` with static format
- [x] Fixed .gitignore encoding (CRLF→LF)
- [x] Deleted 13 excess .md files + 7 .ps1 scripts
- [x] Rewrote README.md (1515→95 lines, English; removed self-rating badges)
- [x] `docker-build.yml` converted to manual dispatch

### dx03 (GKE App) — Committed `bb30504`
- [x] Removed default password `admin123` from config/index.js
- [x] Fixed health check: removed DB INSERT on every hit (was creating ~35k rows/day)
- [x] Sanitized k8s/db-secret.yaml: replaced hardcoded Cloud SQL IP with placeholder
- [x] Added securityContext to K8s deployments (runAsNonRoot, readOnlyRootFilesystem, drop ALL)
- [x] Added .dockerignore for server/ and client/
- [x] Rewrote CI workflow (was just `ls -la *.md`, now builds and tests)
- [x] Deleted DEPLOYMENT_STATUS.md, dx03.jpg
- [x] Rewrote README.md (856→80 lines, English)

### dx02 (AKS App) — Committed `f746929`
- [x] Enabled CSP: `contentSecurityPolicy: false` → detailed CSP directives
- [x] Restricted CORS: `origin: '*'` → `process.env.CORS_ORIGIN`
- [x] Fixed docker-compose.yml: replaced PostgreSQL with SQL Server (matches app's `mssql` package)
- [x] Added securityContext to K8s deployment (runAsNonRoot, drop ALL)
- [x] Rewrote tests with real assertions (were all `expect(true).toBe(true)`)
- [x] Deleted DEPLOYMENT_GUIDE.md, SONARQUBE.md, docs/images/
- [x] Rewrote README.md (English, professional)

### dx01 (EKS App) — Committed `5f9b8d5`
- [x] Removed default DB password fallback in database.js
- [x] Enabled SSL certificate validation (`rejectUnauthorized: true`)
- [x] Added `USER node` to Dockerfile (non-root container)
- [x] Fixed .gitignore: allow package-lock.json to be committed
- [x] Fixed nginx health check: proxy to backend instead of static 200
- [x] Fixed client tests: migrated from `@jest/globals` to `vitest` imports
- [x] Translated Portuguese comments/strings to English
- [x] Rewrote README.md (409→95 lines, English)
- [x] Rewrote SECURITY.md (145→23 lines, accurate claims)

---

## Summary

| Repo | Status | Commit |
|------|--------|--------|
| tx03 | ✅ Done | `68aeb74` |
| tx02 | ✅ Done | `eba8696` |
| tx01 | ✅ Done | `4b78cca` |
| dx03 | ✅ Done | `bb30504` |
| dx02 | ✅ Done | `f746929` |
| dx01 | ✅ Done | `5f9b8d5` |
| tx00 | ✅ Done | tracking repo |

### Remaining
- [ ] Add `.pre-commit-config.yaml` to all repos (optional)

---

## Session 3 — Polish & Hardening

**Date:** 2025-02-24

### Excess Documentation Cleanup
- [x] **tx01**: Deleted 7 files (1,880 lines) — BACKUP_STRATEGY, OBSERVABILITY, QUICK_REFERENCE, k8s/METRICS_SERVER_CONDITIONAL, k8s/MICRO_STACK, k8s/T3_MICRO_OPTIMIZATIONS, terraform/IMPORT_EBS_CSI
- [x] **tx02**: Deleted 10 files (4,800+ lines) — ARGOCD, BACKUP_STRATEGY, DEPLOYMENT_GUIDE, QUICK_REFERENCE, SECURITY_INFRASTRUCTURE, SERVICE_MESH, k8s/SERVICE_MESH_README, k8s/observability/{ACCESS, IMPLEMENTATION, SLACK_SETUP}
- [x] **tx03**: Deleted 8 files + entire docs/ directory (5,800+ lines) — ARCHITECTURE, ARGOCD, OBSERVABILITY, docs/{BACKUP-RESTORE, ISTIO-IMPLEMENTATION, SERVICE-MESH-COMPARISON, SERVICE-MESH-FINAL-DECISION, SERVICE-MESH-IMPLEMENTATION-SUMMARY, WORKLOAD_IDENTITY_SETUP}
- [x] **dx03**: Deleted 2 files (1,055 lines) — ARCHITECTURE, docs/WORKLOAD_IDENTITY_SETUP
- [x] Rewrote bloated READMEs to concise English tables: tx01 SECURITY.md (140→22L), tx01 k8s/policies/README.md (440→24L), tx02 k8s/{policies,observability}/README.md, tx03 k8s/{security,argocd,observability}/README.md, tx03 k8s/istio/{asm/README-ASM,ambient-mesh/README}

### Repository Standards
- [x] Added `.editorconfig` to all 7 repos (utf-8, LF, 2-space indent, trim trailing whitespace)
- [x] Added `LICENSE` (MIT) to tx01 and dx01 (others already had one)
- [x] Added/standardized `.github/CODEOWNERS` across all 7 repos (`* @maringelix`)

### Supply-Chain Security: SHA Pinning
- [x] Pinned **all GitHub Actions to SHA** across every workflow file (~60 files, 29 unique actions)
- Actions pinned: actions/checkout, actions/setup-node, actions/setup-python, actions/cache, actions/upload-artifact, actions/download-artifact, docker/login-action, docker/build-push-action, docker/setup-buildx-action, docker/metadata-action, azure/login, azure/webapps-deploy, google-github-actions/auth, google-github-actions/setup-gcloud, google-github-actions/get-gke-credentials, google-github-actions/deploy-cloudrun, aws-actions/configure-aws-credentials, aws-actions/amazon-ecr-login, hashicorp/setup-terraform, aquasecurity/trivy-action, github/codeql-action/init, github/codeql-action/autobuild, github/codeql-action/analyze, gitleaks/gitleaks-action, slackapi/slack-github-action, zaproxy/action-full-scan, peter-evans/create-pull-request, dorny/paths-filter, softprops/action-gh-release
- Final verification: `grep -rnP 'uses:.*@v\d'` returns **zero results** across all repos

### Additional Deploy Safety Fixes
- [x] Found and fixed **4 more deploy workflows** with push triggers (missed in Session 2):
  - `tx02/deploy-argocd.yml` → workflow_dispatch only
  - `tx02/terraform-apply.yml` → workflow_dispatch only (was deploying infra on push!)
  - `dx02/deploy-aks.yml` → workflow_dispatch only
  - `dx02/docker-build.yml` → workflow_dispatch only
- Total deploy workflows secured: **10** (6 from Session 2 + 4 new)

### Portuguese → English Translation
- [x] **Terraform**: All variable descriptions and comments across tx01 (vpc.tf, ecr.tf, provider.tf, modules/*.tf), tx02 (all modules, prd/, stg/, root vars/outputs), tx03 workflows
- [x] **Workflows**: Echo/log messages in tx01 (terraform-bootstrap, manage-environment), tx02 (chaos-engineering, terraform-apply, security-infrastructure), tx03 (cost-management, backup-restore, istio-apply-configs, istio-fix-sidecar)
- [x] **App Source**: dx01 server/index.js (3 API messages), dx01 client/App.jsx (6 UI labels), dx02 server/index.js (2 comments + 1 message), dx02 client/App.jsx (6 UI labels)
- Final verification: `grep -rnP "[àáâãéêíóôõúçÁÀÂÃÉÊÍÓÔÕÚÇ]"` returns **zero results** across all repos

### Commits

| Repo | Commit | Stats |
|------|--------|-------|
| tx01 | `bcfd792` | 40 files changed, +186 −2,567 |
| tx02 | `7e6dfb2` | 49 files changed, +233 −5,281 |
| tx03 | `441a7c9` | 32 files changed, +228 −6,273 |
| dx01 | `7b309bc` | 7 files changed, +71 −21 |
| dx02 | `8ed14fe` | 8 files changed, +60 −63 |
| dx03 | `f542f79` | 6 files changed, +36 −1,063 |

**Session total: −14,757 lines removed (net)**

---

## Session 4 — History Cleanup & Final Hardening

**Date:** 2026-02-23

### P0: Git History Cleanup
- [x] **tx02**: Removed `terraform/prd/tfplan` binary from entire git history using `git filter-repo --path --invert-paths`
- [x] **tx02**: Scrubbed `adminPassword: admin` / `adminPassword=admin` from entire git history using `git filter-repo --replace-text`
- [x] **tx03**: Scrubbed all secrets from entire git history using `git filter-repo --replace-text`:
  - `Admin123456` (Grafana password) → `REDACTED_PASSWORD`
  - `tx03-444615` (GCP project ID) → `REDACTED_PROJECT_ID`
  - `project-28e61e96-b6ac-4249-a21f` (GCP project UUID) → `REDACTED_PROJECT_UUID`
  - All service account emails → `REDACTED_SA@REDACTED_PROJECT.iam.gserviceaccount.com`
- [x] Both repos force-pushed with rewritten history
- [x] tx02 branch protection temporarily removed, then restored (required_pull_request_reviews + dismiss_stale_reviews)
- Verification: `git log --all -p -S "<secret>"` returns zero results for all scrubbed strings

### P3: Pre-commit Hooks
- [x] Added `.pre-commit-config.yaml` to all 6 repos:
  - **tx01/tx02/tx03** (Terraform): `pre-commit-hooks` v6.0.0 + `gitleaks` v8.30.0 + `pre-commit-terraform` v1.105.0 (terraform_fmt, terraform_validate)
  - **dx01/dx02/dx03** (App): `pre-commit-hooks` v6.0.0 + `gitleaks` v8.30.0 + `no-commit-to-branch`
  - All hooks: trailing-whitespace, end-of-file-fixer, check-yaml, check-json, check-merge-conflict, detect-private-key

### P3: Package Lock Files
- [x] **dx01**: Generated and committed `package-lock.json` for root, server/, client/
- [x] **dx02**: Removed `package-lock.json` from `.gitignore`, generated and committed lock files for root, server/, client/

### Commits

| Repo | Commit | Description |
|------|--------|-------------|
| tx01 | `63554c9` | .pre-commit-config.yaml |
| tx02 | `19cb7f0` | .pre-commit-config.yaml (history rewritten: tfplan + adminPassword scrubbed) |
| tx03 | `abb1258` | .pre-commit-config.yaml (history rewritten: all secrets scrubbed) |
| dx01 | `08a2b5e` | .pre-commit-config.yaml + package-lock.json |
| dx02 | `065ce77` | package-lock.json + .gitignore fix |
| dx02 | `b99667d` | .pre-commit-config.yaml |
| dx03 | `c46a10a` | .pre-commit-config.yaml |

---

## Final Status

All remediation items from the original audit are now **complete**:

| Priority | Items | Status |
|----------|-------|--------|
| P0 | Secrets sanitized (files + git history), tfplan removed | ✅ Done |
| P1 | READMEs rewritten, CI fixed, SSH hardened, artifacts cleaned | ✅ Done |
| P2 | Container security, SECURITY.md, DB mismatch, docs cleanup | ✅ Done |
| P3 | SHA pinning, .pre-commit-config, .editorconfig, CODEOWNERS, package-lock | ✅ Done |

Only optional/cosmetic item remaining:
- [ ] Add `CONTRIBUTING.md` to repos (not required for portfolio)
