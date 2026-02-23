# Consolidated Audit Report

> Full audit of the **maringelix** portfolio — 6 repositories, 3 cloud providers, ~79,000 lines of code.

---

## Table of Contents

- [tx03 — GCP Infrastructure](#tx03--gcp-infrastructure)
- [tx02 — Azure Infrastructure](#tx02--azure-infrastructure)
- [tx01 — AWS Infrastructure](#tx01--aws-infrastructure)
- [dx03 — GKE Application](#dx03--gke-application)
- [dx02 — AKS Application](#dx02--aks-application)
- [dx01 — EKS Application](#dx01--eks-application)

---

## tx03 — GCP Infrastructure

**Stack:** Terraform · GKE Autopilot · Cloud SQL · Cloud Armor · Istio · ArgoCD · Prometheus/Grafana  
**Size:** 24,538 lines across 74 files  

### 🔴 P0 — Critical

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Database passwords in plaintext** | Multiple `.md` files | Passwords `@qOck=1eUl0v` and `2iZ8RyrW7CIxSQB5dlwbMtns` committed to Git history |
| 2 | **Grafana admin credentials** | `README.md` | `admin/Admin123456` exposed in documentation |
| 3 | **GCP Project ID exposed** | 15+ files | `project-28e61e96-b6ac-4249-a21` hardcoded throughout codebase |
| 4 | **Service account email exposed** | Terraform files | Full SA email in source code |

### 🟠 P1 — High

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Windows scripts with hardcoded paths** | `.bat`/`.ps1` files | `c:\Users\tx01\` paths — platform-specific, unprofessional |
| 2 | **README bloat** | `README.md` | 1,202 lines in Portuguese — should be ~200 lines in English |
| 3 | **Documentation overload** | `docs/` | 30 markdown files / 15,086 lines — many are session logs |
| 4 | **Dangerous CI trigger** | `destroy.yml` | Triggers on push to `main` — could destroy infrastructure |
| 5 | **Broken CI reference** | `deploy-app.yml` | References non-existent K8s manifests |

### 🟡 P2 — Medium

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Session context files** | `SESSION_CONTEXT.md` | AI-generated session files committed to repo |
| 2 | **No .pre-commit-config** | Root | No pre-commit hooks for secret scanning |
| 3 | **Missing CODEOWNERS** | Root | No code ownership defined |

---

## tx02 — Azure Infrastructure

**Stack:** Terraform · AKS · Azure SQL · ACR · nginx-ingress · Prometheus/Grafana  
**Size:** 25,679 lines across 91 files  

### 🔴 P0 — Critical

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Terraform plan binary committed** | `terraform/prd/tfplan` | Contains full state with secrets — MUST be removed from Git history |
| 2 | **Grafana admin password** | K8s manifests | `adminPassword: admin` in plaintext |

### 🟠 P1 — High

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **SSH open to world** | `networking/main.tf` | Port 22 open to `0.0.0.0/0` |
| 2 | **VM password auth enabled** | `vm/main.tf` | Password authentication should be disabled |
| 3 | **SQL public access enabled** | `database/main.tf` | Database accessible from public internet |
| 4 | **ACR admin enabled** | `acr/main.tf` | Admin user should use service principal |
| 5 | **DB creds in cloud-init** | `vm/main.tf` | Database credentials in plaintext cloud-init script |
| 6 | **Duplicate K8s manifests** | `k8s/` | `service.yaml` vs `service.yml`, `hpa.yaml` vs `hpa.yml` |

### 🟡 P2 — Medium

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **README bloat** | `README.md` | 777 lines in Portuguese |
| 2 | **Staging copy-paste** | `terraform/stg/` | Broken copy-paste of prd configuration |
| 3 | **34 markdown files** | `docs/` | Excessive documentation, many session logs |
| 4 | **Missing .gitignore entries** | `.gitignore` | `tfplan` not in .gitignore |

---

## tx01 — AWS Infrastructure

**Stack:** Terraform · EKS · RDS · EC2 · VPC · WAF · ALB · GitHub Actions  
**Size:** 19,076 lines across 57 files  

### 🔴 P0 — Critical

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **SSH open to world** | `security_groups.tf:37-43` | Port 22 `cidr_blocks = ["0.0.0.0/0"]` |
| 2 | **Perpetual Terraform drift** | `providers.tf` | `timestamp()` in `default_tags` causes changes every apply |
| 3 | **Corrupted .gitignore** | `.gitignore` | Mixed UTF-8/UTF-16LE encoding — may not work correctly |

### 🟠 P1 — High

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Dead subnet code** | `vpc.tf` | Subnet CIDRs hardcoded, ignoring input variables entirely |
| 2 | **EKS public endpoint unprotected** | `eks.tf` | Public endpoint enabled with no IP restriction |
| 3 | **ECR repo name mismatch** | Terraform vs CI | Terraform: `dx01-app`, CI references: `tx01-nginx` |
| 4 | **ALB no HTTPS** | `alb.tf` | Only HTTP listener, no TLS termination |
| 5 | **Inflated README** | `README.md` | 1,515 lines with "⭐⭐⭐⭐⭐ Expert" self-ratings |
| 6 | **Fake tests** | CI pipeline | Tests are just `echo` statements |

### 🟡 P2 — Medium

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Session context files** | `SESSION_CONTEXT.md` | AI session logs committed |
| 2 | **Monitoring completeness** | Grafana dashboards | Dashboard JSON references may be incomplete |

---

## dx03 — GKE Application

**Stack:** React+TypeScript · Node.js/Express · PostgreSQL · GKE  
**Size:** 4,169 lines across 29 files  

### 🔴 P0 — Critical

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Default password in code** | `server/src/config/index.js` | `password: process.env.DB_PASSWORD \|\| 'admin123'` |
| 2 | **Cloud SQL IP in K8s secret** | `k8s/db-secret.yaml` | Hardcoded database IP address |
| 3 | **No .dockerignore** | Server & Client | `node_modules`, `.env` files copied into images |
| 4 | **Zero test files** | Entire repo | Claims test coverage but no test files exist |

### 🟠 P1 — High

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **CI pipeline is a no-op** | `.github/workflows/` | Pipeline only runs `ls -la *.md` |
| 2 | **Health check inserts DB row** | `server/src/routes/health.js` | Every health check = DB insert (~35k rows/day) |
| 3 | **Container runs as root** | `Dockerfile` | nginx Dockerfile has no `USER` directive |
| 4 | **No securityContext** | `k8s/*.yaml` | No `runAsNonRoot`, `readOnlyRootFilesystem`, etc. |

---

## dx02 — AKS Application

**Stack:** React · Node.js/Express · SQL Server (mssql) · AKS  
**Size:** 3,335 lines across 26 files  

### 🔴 P0 — Critical

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **docker-compose DB mismatch** | `docker/docker-compose.yml` | Uses PostgreSQL container but app uses SQL Server (`mssql`) |
| 2 | **Fake tests** | `server/__tests__/` | All tests are `expect(true).toBe(true)` |
| 3 | **Lint job never lints** | CI pipeline | Lint step doesn't actually run linter |
| 4 | **Phantom metrics endpoint** | K8s annotations | `/metrics` referenced but endpoint doesn't exist |

### 🟠 P1 — High

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **CSP disabled** | `server/index.js` | Content Security Policy entirely disabled |
| 2 | **CORS allows all** | `server/index.js` | `origin: '*'` — no domain restriction |
| 3 | **No securityContext** | `k8s/*.yaml` | No pod security settings |
| 4 | **Portuguese UI strings** | `client/src/` | UI text in Portuguese |

---

## dx01 — EKS Application

**Stack:** React · Node.js/Express · PostgreSQL · EKS  
**Size:** 1,880 lines across 19 files  

### 🔴 P0 — Critical

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **Container runs as root** | `Dockerfile` | `SECURITY.md` falsely claims non-root execution |
| 2 | **Default DB password** | `server/database.js` | `password: process.env.DB_PASSWORD \|\| 'postgres'` |

### 🟠 P1 — High

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| 1 | **SSL verification disabled** | `server/database.js` | `rejectUnauthorized: false` for RDS connections |
| 2 | **nginx intercepts health** | `docker/default.conf` | nginx captures `/health` before it reaches backend |
| 3 | **Wrong test runner** | `server/__tests__/` | Uses `@jest/globals` imports but `vitest` is the runner |
| 4 | **package-lock.json ignored** | `.gitignore` | Non-deterministic builds |
| 5 | **SECURITY.md false claims** | `SECURITY.md` | Claims non-root containers, TLS everywhere — none are true |
