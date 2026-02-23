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

## 🔴 P0 — Critical / Immediate

### P0-1: Sanitize secrets from tx03
- [ ] Remove database passwords from all `.md` files
- [ ] Remove Grafana `admin/Admin123456` from README
- [ ] Replace GCP Project ID with placeholder `<PROJECT_ID>` in 15+ files
- [ ] Remove service account email from Terraform files
- **Note:** Git history still contains secrets — consider `git filter-repo` or repo recreation

### P0-2: Remove tfplan from tx02
- [ ] Delete `terraform/prd/tfplan` binary
- [ ] Add `*.tfplan` and `tfplan` to `.gitignore`
- [ ] Remove from Git history with `git filter-repo`
- [ ] Remove Grafana `adminPassword: admin` from K8s manifests

### P0-3: Fix SSH 0.0.0.0/0 in tx01
- [ ] Change `cidr_blocks` to specific IP or remove SSH rule
- [ ] Fix `timestamp()` in `default_tags` (use static value)
- [ ] Fix corrupted `.gitignore` encoding

### P0-4: Remove default passwords from dx*
- [ ] dx03: Remove `'admin123'` fallback from `config/index.js`
- [ ] dx03: Remove hardcoded Cloud SQL IP from `k8s/db-secret.yaml`
- [ ] dx02: Fix docker-compose PostgreSQL/mssql mismatch
- [ ] dx01: Remove `'postgres'` fallback from `database.js`

---

## 🟠 P1 — High Priority

### P1-1: Rewrite READMEs in English
- [ ] tx03: Rewrite from 1,202 → ~200 lines, English, professional
- [ ] tx02: Rewrite from 777 → ~200 lines, English, professional
- [ ] tx01: Rewrite from 1,515 → ~200 lines, remove ⭐ ratings
- [ ] dx03: Review and standardize
- [ ] dx02: Review and standardize
- [ ] dx01: Review and standardize

### P1-2: Fix CI/CD pipelines
- [ ] tx03: Fix `destroy.yml` trigger (require manual dispatch only)
- [ ] tx03: Fix `deploy-app.yml` K8s manifest references
- [ ] dx03: Replace `ls -la *.md` with real build/test steps
- [ ] dx02: Make lint job actually run linter
- [ ] dx01: Fix test runner mismatch (Jest imports vs Vitest)

### P1-3: Fix security misconfigurations
- [ ] tx02: Close SSH to world (`networking/main.tf`)
- [ ] tx02: Disable VM password auth
- [ ] tx02: Disable SQL public access
- [ ] tx02: Disable ACR admin user
- [ ] tx01: Restrict EKS public endpoint
- [ ] tx01: Add HTTPS listener to ALB
- [ ] dx01: Fix SSL `rejectUnauthorized: false`
- [ ] dx02: Enable CSP, restrict CORS

### P1-4: Fix fake tests
- [ ] tx01: Replace echo-based "tests" with real validation
- [ ] dx02: Replace `expect(true).toBe(true)` with real tests
- [ ] dx03: Add actual test files or remove test claims
- [ ] dx01: Fix `@jest/globals` → vitest imports

### P1-5: Clean up artifacts
- [ ] tx03: Delete `.bat`/`.ps1` Windows scripts
- [ ] tx03: Delete `SESSION_CONTEXT.md` files
- [ ] tx02: Remove duplicate K8s files (`.yaml` vs `.yml`)
- [ ] tx01: Delete `SESSION_CONTEXT.md` files
- [ ] dx03: Fix health check DB insertion

---

## 🟡 P2 — Medium Priority

### P2-1: Container security
- [ ] dx03: Add `.dockerignore` to server and client
- [ ] dx03: Add `USER` directive to Dockerfiles
- [ ] dx01: Add `USER` directive to Dockerfile
- [ ] dx01: Update `SECURITY.md` to be accurate
- [ ] All dx*: Add `securityContext` to K8s deployments

### P2-2: Infrastructure hardening
- [ ] tx01: Fix dead subnet code (hardcoded CIDRs vs variables)
- [ ] tx01: Fix ECR repo name mismatch
- [ ] tx02: Fix staging environment (copy-paste of prd)
- [ ] tx02: Remove DB creds from cloud-init

### P2-3: Documentation cleanup
- [ ] All repos: Reduce docs/ to essential files only
- [ ] All repos: Remove AI session logs
- [ ] dx02: Translate Portuguese UI strings

---

## 🔵 P3 — Polish

### P3-1: Supply chain security
- [ ] All repos: Pin GitHub Actions to SHA (not tag)
- [ ] dx01: Commit `package-lock.json` (remove from `.gitignore`)

### P3-2: Repository standards
- [ ] All repos: Add `.pre-commit-config.yaml` with secret scanning
- [ ] All repos: Add `.editorconfig`
- [ ] All repos: Add `CODEOWNERS`
- [ ] All repos: Add `CONTRIBUTING.md`

### P3-3: Metrics & observability
- [ ] dx02: Implement `/metrics` endpoint or remove annotations
- [ ] dx03: Implement proper Prometheus metrics
