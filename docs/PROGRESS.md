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

### Remaining P2/P3 (Nice-to-Have)
- [ ] Pin GitHub Actions to SHA instead of tags
- [ ] Add `.pre-commit-config.yaml` to all repos
- [ ] Add `.editorconfig` to all repos
- [ ] Add `CODEOWNERS` to all repos
- [ ] Remaining cleanup docs in tx01/tx02/tx03 (OBSERVABILITY.md, QUICK_REFERENCE.md, etc.)
