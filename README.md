# tx00 — Portfolio Audit & Remediation Tracker

> Centralized audit, remediation tracking, and best-practices enforcement for the **maringelix** DevOps/SRE/DevSecOps portfolio.

## Purpose

This repository documents a comprehensive security and quality audit performed across six portfolio repositories, tracks remediation progress, and enforces consistent standards going forward.

## Portfolio Architecture

| Repo | Cloud | Stack | Role |
|------|-------|-------|------|
| [tx01](https://github.com/maringelix/tx01) | AWS | Terraform · EKS · RDS · EC2 · WAF · ALB | Infrastructure-as-Code |
| [tx02](https://github.com/maringelix/tx02) | Azure | Terraform · AKS · Azure SQL · ACR · nginx-ingress | Infrastructure-as-Code |
| [tx03](https://github.com/maringelix/tx03) | GCP | Terraform · GKE Autopilot · Cloud SQL · Istio · ArgoCD | Infrastructure-as-Code |
| [dx01](https://github.com/maringelix/dx01) | AWS | React · Node.js · Express · PostgreSQL · EKS | Full-Stack Application |
| [dx02](https://github.com/maringelix/dx02) | Azure | React · Node.js · Express · SQL Server · AKS | Full-Stack Application |
| [dx03](https://github.com/maringelix/dx03) | GCP | React+TS · Node.js · Express · PostgreSQL · GKE | Full-Stack Application |

## Audit Summary

| Severity | Count | Status |
|----------|-------|--------|
| 🔴 P0 — Critical (Secrets / Data Leak) | 8 | ✅ Resolved |
| 🟠 P1 — High (Security / Functionality) | 14 | ✅ Resolved |
| 🟡 P2 — Medium (Best Practices) | 10 | ✅ Resolved |
| 🔵 P3 — Low (Polish / Hardening) | 6 | ✅ Resolved |

## Documentation

| Document | Description |
|----------|-------------|
| [AUDIT.md](docs/AUDIT.md) | Consolidated audit findings across all repos |
| [REMEDIATION.md](docs/REMEDIATION.md) | Remediation plan with priorities and status |
| [STANDARDS.md](docs/STANDARDS.md) | Coding and infrastructure standards enforced |
| [PROGRESS.md](docs/PROGRESS.md) | Chronological progress log |

## Methodology

1. **Static Analysis** — Full codebase review for secrets, misconfigurations, and anti-patterns
2. **Security Audit** — IAM, network policies, secrets management, container security
3. **CI/CD Review** — Pipeline integrity, test coverage, deployment safety
4. **Documentation Review** — README quality, accuracy of claims, professionalism
5. **Best Practices** — Industry standards compliance (CIS, OWASP, 12-Factor)

## License

MIT
