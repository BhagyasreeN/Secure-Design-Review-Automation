---
description: Analyze code repository to produce a detailed Security Review .txt file with findings and remediation guidance for JPMorganChase applications. Use this skill for Step 2 of a Secure Design Review.
---

# Security Review Analysis Skill

Analyze codebase and produce **Security Review** `.txt` file. Run as **Step 2** after Architecture Summary.

## When to Use
- Step 2 of SDR workflow
- Identify vulnerabilities and misconfigurations for AppSec engineers
- Assess: Java/Python, microservices, AI/ML agents, MCP, AWS ECS/EKS/GAIA/GAP/GKP

## Inputs
- Complete code repository access
- Architecture Summary `.txt` (from Step 1) - optional but helpful

### Artifact Coverage
See [common/artifact-handling.md](common/artifact-handling.md) for preflight procedures.

## File Scanning Strategy

See [common/file-scanning.md](common/file-scanning.md) for skip patterns, priority file list, and scanning order.

## Output
**File:** `security_review_<app_name>_<YYYY-MM-DD>.txt`  
**Location:** Codebase root (NOT in SDR folder)

## Sections (with exact file:line references)

1. **Executive Summary** - Posture rating, findings count table, top 3 critical, narrative
2. **Authentication & Identity** - OAuth2/OIDC/SSO/JWT, session mgmt, hardcoded creds
3. **Authorization & Access Control** - RBAC/ABAC, privilege escalation, least privilege
4. **API Security** - Input validation, injections, rate limiting, CORS, error leakage
5. **Transport & Communication** - TLS version, cert validation, mTLS, insecure protocols
6. **Infrastructure & Cloud** - IAM wildcards, network exposure, container security, K8s policies
7. **Secrets & Configuration** - Hardcoded secrets, `.env` in repo, rotation, prod/dev segregation
8. **Data Protection & Privacy** - PII identification, encryption, masking, retention, deserialization
9. **Logging & Monitoring** - PII in logs, log injection, DEBUG mode, audit trails, alerting
10. **AI & Agent Security** - Prompt injection, system prompt exposure, tool validation, LLM output execution
11. **MCP Security** - Unauth endpoints, tool sanitization, transport TLS, overly broad scope
12. **Dependency & Supply Chain** - CVEs, outdated deps, licenses
    - Table: `Library | Version | CVE | Severity | Recommendation`
13. **Secure Coding Practices** - Injection flaws, weak crypto, insecure randomness, path traversal
14. **Consolidated Findings & Roadmap**
    - Master table: `ID | Domain | Finding | File:Line | Severity | Recommendation | Effort`
    - Roadmap: CRITICAL (fix before deploy), HIGH (current sprint), MEDIUM (2 sprints), LOW (backlog)

## Rules
- Scan priority files per File Scanning Strategy above (skip node_modules, build outputs, etc.)
- **Reference exact file:line in EVERY finding**
- Use severity: `CRITICAL | HIGH | MEDIUM | LOW | INFO`
- Missing controls = findings (e.g., no rate limiting = HIGH)
- Use `[NOT IDENTIFIED IN CODEBASE]` for missing data — NEVER guess
- Format: Plain text, UTF-8, max 120 chars/line, ASCII tables
- Technical tone for AppSec engineers

## Progress Reporting (MANDATORY)

Emit a 📁 discovery report (total / skipped / to-analyze counts) at the start, then a 🔎 progress update every 20-25 files (file counts by category, running findings tally by severity, ETA). Keep each update under 10 lines.

## Severity Guidelines
- **CRITICAL**: Immediate exploitation risk, data breach, compliance violation
- **HIGH**: Significant weakness, privilege escalation, sensitive data exposure
- **MEDIUM**: Config weakness, missing control, moderate risk
- **LOW**: Improvement, hardening, best practice
- **INFO**: Observation, note, future consideration

## After Completion
Tell user: "Security Review complete. Run **Step 3** — SDR Report."
