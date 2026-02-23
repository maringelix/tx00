# Remediation Plan

> Prioritized remediation tasks across all portfolio repositories.

## Priority Definitions

| Priority | Criteria | SLA |
|----------|----------|-----|
| 🔴 P0 | Exposed secrets, credentials, or data that could be exploited | Immediate |
| 🟠 P1 | Security misconfigurations, broken functionality, false claims | Same day |
| 🟡 P2 | Best practice violations, code quality, documentation accuracy | Next batch |
| 🔵 P3 | Polish, hardening, professional presentation | Final pass |

---

## 🔴 P0 — Critical / Immediate ✅

### P0-1: Sanitize secrets from tx03 ✅
- [x] Remove database passwords from all `.md` files
- [x] Remove Grafana `admin/Admin123456` from README
- [x] Replace GCP Project ID with placeholder `<PROJECT_ID>` in 15+ files
- [x] Remove service account email from Terraform files
- **Note:** Git history still contains secrets — consider `git filter-repo` or repo recreation

### P0-2: Remove tfplan from tx02 ✅
- [x] Delete `terraform/prd/tfplan` binary
- [x] Add `*.tfplan` and `tfplan` to `.gitignore`
- [x] Remove from Git history with `git filter-repo`
- [x] Remove Grafana `adminPassword: admin` from K8s manifests

### P0-3: Fix SSH 0.0.0.0/0 in tx01 ✅
- [x] Change `cidr_blocks` to `var.ssh_allowed_cidr`
- [x] Fix `timestamp()` in `default_tags` (use static value)
- [x] Fix corrupted `.gitignore` encoding

### P0-4: Remove default passwords from dx* ✅
- [x] dx03: Remove `'admin123'` fallback from `config/index.js`
- [x] dx03: Remove hardcoded Cloud SQL IP from `k8s/db-secret.yaml`
- [x] dx02: Fix docker-compose PostgreSQL/mssql mismatch
- [x] dx01: Remove `'postgres'` fallback from `database.js`

---

## 🟠 P1 — High Priority ✅

### P1-1: Rewrite READMEs in English ✅
- [x] tx03: Rewritten (1,202 → 130 lines, English, professional)
- [x] tx02: Rewritten (777 → 95 lines, English, professional)
- [x] tx01: Rewritten (1,515 → 95 lines, removed ⭐ ratings)
- [x] dx03: Rewritten (856 → 80 lines, English)
- [x] dx02: Rewritten (English, professional)
- [x] dx01: Rewritten (409 → 95 lines, English)

### P1-2: Fix CI/CD pipelines ✅
- [x] tx03: Fix `destroy.yml` trigger (manual dispatch only)
- [x] tx03: Fix `deploy-app.yml` K8s manifest references
- [x] dx03: Replace `ls -la *.md` with real build/test steps
- [x] dx02: Make lint job actually run linter
- [x] dx01: Fix test runner mismatch (Jest imports → Vitest)

### P1-3: Fix security misconfigurations ✅
- [x] tx02: Close SSH to world (`networking/main.tf`)
- [x] tx02: Disable VM password auth
- [x] tx02: Disable SQL public access
- [x] tx02: Disable ACR admin user
- [x] tx01: Restrict EKS public endpoint
- [x] dx01: Fix SSL `rejectUnauthorized: true`
- [x] dx02: Enable CSP, restrict CORS

### P1-4: Fix fake tests ✅
- [x] tx01: Replace echo-based "tests" with real validation
- [x] dx02: Replace `expect(true).toBe(true)` with real tests
- [x] dx01: Fix `@jest/globals` → vitest imports

### P1-5: Clean up artifacts ✅
- [x] tx03: Delete `.bat`/`.ps1` Windows scripts
- [x] tx03: Delete `SESSION_CONTEXT.md` files
- [x] tx02: Remove duplicate K8s files (`.yaml` vs `.yml`)
- [x] tx01: Delete `SESSION_CONTEXT.md` files
- [x] dx03: Fix health check DB insertion

---

## 🟡 P2 — Medium Priority ✅

### P2-1: Container security ✅
- [x] dx03: Add `.dockerignore` to server and client
- [x] dx01: Add `USER node` directive to Dockerfile
- [x] dx01: Rewrite `SECURITY.md` to be accurate (145 → 23 lines)
- [x] All dx*: Add `securityContext` to K8s deployments (runAsNonRoot, drop ALL)

### P2-2: Infrastructure hardening ✅
- [x] tx02: Remove DB creds from cloud-init (translated comment, kept as placeholder)

### P2-3: Documentation cleanup ✅
- [x] All repos: Reduce docs/ to essential files only
- [x] tx01: Deleted 7 excess docs (1,880 lines)
- [x] tx02: Deleted 10 excess docs (4,800+ lines)
- [x] tx03: Deleted 8 excess docs + entire docs/ directory (5,800+ lines)
- [x] dx03: Deleted 2 excess docs (1,055 lines)
- [x] All repos: Translate remaining Portuguese to English (UI, comments, Terraform vars, workflow messages)

---

## 🔵 P3 — Polish ✅

### P3-1: Supply chain security ✅
- [x] All repos: Pin GitHub Actions to SHA (29 unique actions across ~60 workflow files)
- [x] dx01: Commit `package-lock.json` (removed from `.gitignore`)

### P3-2: Repository standards ✅
- [x] All repos: Add `.editorconfig` (utf-8, LF, 2-space indent)
- [x] All repos: Add `CODEOWNERS` (`* @maringelix`)
- [x] tx01, dx01: Add `LICENSE` (MIT)
- [ ] All repos: Add `.pre-commit-config.yaml` with secret scanning (optional)

### P3-3: Deploy workflow safety ✅
- [x] 10 deploy workflows converted to `workflow_dispatch` only across all repos
- [x] No workflow triggers infrastructure provisioning or destruction on push/PR
