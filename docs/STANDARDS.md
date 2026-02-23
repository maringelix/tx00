# Portfolio Standards

> Enforced standards across all **maringelix** portfolio repositories.

## 1. Repository Structure

Every repo MUST contain:

```
.
├── README.md            # English, <300 lines, structured
├── LICENSE              # MIT or Apache-2.0
├── .gitignore           # Comprehensive, platform-aware
├── .editorconfig        # Consistent formatting
├── .pre-commit-config.yaml  # Secret scanning, linting
├── CODEOWNERS           # Code ownership
├── CONTRIBUTING.md      # Contribution guidelines
└── .github/
    └── workflows/       # CI/CD pipelines
```

## 2. Documentation Standards

| Rule | Requirement |
|------|-------------|
| Language | English only |
| README length | 150–300 lines |
| README structure | Title → Badges → Architecture → Quick Start → Configuration → Deployment → Monitoring → License |
| No session logs | Never commit AI session transcripts |
| No credentials | Never reference real passwords, tokens, or project IDs |

## 3. Security Standards

### Secrets Management
- **NEVER** hardcode passwords, API keys, or tokens in source code
- Use environment variables with **no default fallbacks** for sensitive values
- Use placeholder values like `<DB_PASSWORD>`, `<PROJECT_ID>` in documentation
- Add `.pre-commit-config.yaml` with `detect-secrets` or `gitleaks`

### Network Security
- SSH access: Restrict to known CIDRs or use bastion hosts — **never** `0.0.0.0/0`
- Database access: Private subnets only, no public endpoints
- API endpoints: HTTPS only, TLS 1.2+
- CORS: Explicit domain allowlist, never `*`

### Container Security
- All containers MUST run as non-root (`USER` directive in Dockerfile)
- K8s deployments MUST include `securityContext`:
  ```yaml
  securityContext:
    runAsNonRoot: true
    readOnlyRootFilesystem: true
    allowPrivilegeEscalation: false
    capabilities:
      drop: ["ALL"]
  ```
- Add `.dockerignore` to exclude `node_modules`, `.env`, `.git`

### Infrastructure
- Terraform state: Remote backend only (S3/GCS/Azure Blob)
- Terraform plans: Never commit `.tfplan` files
- Tags: Static values only — never use `timestamp()` in default tags
- Environments: DRY configuration, never copy-paste between envs

## 4. CI/CD Standards

### Pipeline Requirements
- Every pipeline MUST perform real work (build, lint, test)
- Pin all GitHub Actions to SHA, not tag
- Destructive operations (`terraform destroy`) require manual trigger only (`workflow_dispatch`)
- Deploy pipelines must reference existing manifests

### Test Requirements
- Tests MUST contain real assertions, not `expect(true).toBe(true)`
- Test runner imports must match the configured test runner
- Health checks must be lightweight (no DB writes)

## 5. Code Quality

### General
- No platform-specific scripts (`.bat`, `.ps1`) in cross-platform repos
- No duplicate files (e.g., `service.yaml` AND `service.yml`)
- `.gitignore` must be valid UTF-8

### Application Code
- CSP headers enabled and configured
- Proper error handling (no silent catches)
- Database connections with proper SSL verification
- Environment-based configuration (12-Factor App)

## 6. Git Practices

- Conventional commits: `feat:`, `fix:`, `docs:`, `security:`, `chore:`
- Branch protection on `main`
- No binary files in repository
- No generated files (lock files should be committed, build artifacts should not)
