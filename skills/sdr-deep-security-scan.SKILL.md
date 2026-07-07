---
description: OPTIONAL deep security scan that enhances SDR security review with detailed vulnerability analysis using code-security, dependency-security, and infra-security skills. Run after sdr-combined-analysis for comprehensive vulnerability coverage with CVEs, OWASP Top 10, and infrastructure misconfigurations.
---

# SDR Deep Security Scan Skill (Optional Enhancement)

**Enhanced security analysis that integrates 5 specialized security skills into SDR workflow.**

## Purpose

This optional skill extends the high-level architectural security review from `sdr-combined-analysis` with deep, code-level vulnerability scanning. It provides:

- **Specific CVE identification** in dependencies with remediation versions
- **OWASP Top 10 vulnerability detection** (injection, XSS, deserialization, etc.)
- **Infrastructure misconfiguration analysis** (IAM wildcards, exposed resources, insecure defaults)
- **Supply chain risk assessment** (outdated packages, license compliance)
- **Hardcoded secrets detection** (API keys, credentials, tokens)

## When to Use

**Run this AFTER completing `sdr-combined-analysis` and BEFORE generating the report IF:**
- Application handles sensitive data (PII, financial, health)
- Production deployment imminent (need comprehensive security assessment)
- High-risk environment (external-facing, high-value target)
- Compliance requirements demand detailed vulnerability documentation
- Previous incidents or security concerns exist
- AI/ML application with LLM or agent components

**Skip this if:**
- Early architecture review only (not deployment-ready)
- Low-risk internal tool with no sensitive data
- Time-constrained rapid assessment
- Codebase is read-only reference architecture

## Prerequisites

**Required Files (must exist):**
- `security_review_<app>_<date>.txt` - High-level security review from combined analysis
- `architecture_summary_<app>_<date>.txt` - Architecture context for targeted scanning

**Required Copilot Skills:**
- `code-security` - Scan source code for vulnerabilities (SQL injection, XSS, OWASP Top 10)
- `dependency-security` - Review dependencies for CVEs and supply chain risks
- `infra-security` - Check IaC files for misconfigurations (Terraform, K8s, Docker)

**Note:** These security skills are **bundled with the SDR workspace** in the `skills/security/` folder. They are automatically available to Copilot when this workspace is open. No external installation required.

**Multi-folder support:**
- Detects `_multi_` in filenames automatically
- Applies deep scan to all component folders
- Tags findings by component

## Workflow

### Step 1: User Prompt (After Combined Analysis Completes)

When `sdr-combined-analysis` finishes, **prompt the user:**

```
✅ High-level SDR analysis complete.

📊 Found: X architecture observations, Y security findings

🔬 Optional Deep Security Scan Available:
   • Scan for specific CVEs in dependencies
   • Detect OWASP Top 10 vulnerabilities (injection, XSS, etc.)
   • Identify infrastructure misconfigurations (IAM, network, encryption)
   • Find hardcoded secrets and credentials
   • Assess supply chain risks

⏱️ Time: +10-15 minutes for comprehensive scanning

Would you like to run the deep security scan? (Recommended for production deployments)
[Yes] [No] [Ask me later]
```

If user says **Yes** → Proceed with Step 2
If user says **No** or **Ask me later** → Skip to report generation

### Step 2: Load Security Skills

Read the three bundled security analysis skills from `skills/security/` to understand their capabilities:

1. **Read** `skills/security/code-security.SKILL.md`
   - Scans for: vulnerability patterns, OWASP Top 10, severity assessment
   - Output: Finding ID, severity, CWE, file location, code excerpt, remediation
   
2. **Read** `skills/security/dependency-security.SKILL.md`
   - Scans for: manifest files, CVE lookup, outdated packages, license issues
   - Output: CVE IDs, affected package, current vs fixed version, severity
   
3. **Read** `skills/security/infra-security.SKILL.md`
   - Scans for: IaC misconfigurations, CIS benchmarks, security defaults
   - Output: Resource type, misconfiguration, risk level, remediation steps

**Note:** These skills are bundled in the SDR workspace at `skills/security/`. Copilot will read and apply their instructions automatically.

### Step 3: Determine Scan Scope

Based on architecture summary, identify which scans to run:

**Code Security Scan - Run if:**
- Source code exists (src/, lib/, app/ folders)
- Languages: Java, Python, JavaScript, TypeScript, C#, Go, Ruby, PHP
- Skip if: Infrastructure-only (pure Terraform), config-only repo

**Dependency Security Scan - Run if:**
- Manifest files exist: package.json, requirements.txt, pom.xml, build.gradle, go.mod, *.csproj, Gemfile, composer.json, Cargo.toml
- Skip if: No dependencies (standalone scripts)

**Infrastructure Security Scan - Run if:**
- IaC files exist: *.tf, Dockerfile, docker-compose.yml, K8s manifests (*.yaml in k8s/, helm/), CloudFormation templates
- Skip if: No infrastructure code

**Report scope determination:**
```
🔬 Deep Security Scan Scope:
   ✅ Code Security: Yes (Python, TypeScript source detected)
   ✅ Dependency Security: Yes (requirements.txt, package.json found)
   ✅ Infrastructure Security: Yes (Terraform, Dockerfile detected)
   
   📁 Scanning: <app-name> codebase
   [Multi-folder: Component 1, Component 2, Component 3]
```

### Step 4: Execute Security Scans

**For single folder:**
Run scans sequentially on the codebase:

**4a. Code Security Scan**
- Analyze source files per `code-security` skill
- Detect: SQL injection, XSS, command injection, path traversal, insecure deserialization, hardcoded secrets, weak crypto, OWASP Top 10
- Output: Findings with file:line references, severity, CWE/OWASP mapping

**4b. Dependency Security Scan**
- Parse manifest files per `dependency-security` skill
- Check for: Known CVEs, outdated versions, supply chain risks, license issues
- Output: Vulnerability table with CVE IDs, affected packages, current vs fixed versions

**4c. Infrastructure Security Scan**
- Review IaC per `infra-security` skill
- Check for: IAM wildcards, public resources, missing encryption, insecure defaults, CIS benchmark violations, container hardening issues
- Output: Misconfiguration findings with resource references, severity, remediation

**For multi-folder:**
Run scans per component, tag findings with component label:
- Scan component 1 → Tag findings as `[Component: agent]`
- Scan component 2 → Tag findings as `[Component: microservice]`
- Scan component 3 → Tag findings as `[Component: terraform]`
- Detect cross-component issues (shared credentials, common misconfigurations)

**Progress reporting:**
```
🔍 Running Deep Security Scans...

[1/3] Code Security Scan
   ├─ Scanned 45 source files
   ├─ Found: 3 HIGH, 5 MEDIUM, 8 LOW vulnerabilities
   └─ Time: 3m 22s

[2/3] Dependency Security Scan
   ├─ Analyzed 127 dependencies
   ├─ Found: 2 CRITICAL (CVEs), 4 HIGH (outdated), 1 MEDIUM (license)
   └─ Time: 2m 10s

[3/3] Infrastructure Security Scan
   ├─ Reviewed 23 IaC resources
   ├─ Found: 1 CRITICAL, 3 HIGH, 6 MEDIUM misconfigurations
   └─ Time: 1m 45s

✅ Deep scan complete: 6 CRITICAL, 10 HIGH, 14 MEDIUM, 8 LOW findings
```

### Step 5: Merge Findings into Security Review

**Read existing `security_review_<app>_<date>.txt` file.**

**Append new sections at the end (before Consolidated Findings & Roadmap):**

```
================================================================================
DEEP SECURITY SCAN RESULTS (SECTIONS 15-17)
================================================================================

The following sections contain detailed vulnerability findings from specialized
security scans. These complement the architectural security review above.

Scan Date: <YYYY-MM-DD HH:MM>
Scope: [Code, Dependencies, Infrastructure]
Tools: code-security, dependency-security, infra-security skills

IMPORTANT: These sections use a standardized marker for detection:
  - Section header includes "(Deep Scan)" suffix
  - Wrapping header includes "SECTIONS 15-17" marker
  - This ensures robust detection by report generation and publishing skills

--------------------------------------------------------------------------------
15. CODE VULNERABILITIES (Deep Scan)
--------------------------------------------------------------------------------

Detailed code-level vulnerability analysis using OWASP Top 10 patterns.

[Finding Format]
ID: CODE-001
Title: SQL Injection in User Query Endpoint
Severity: HIGH
CWE: CWE-89 (SQL Injection)
OWASP: A03:2021 - Injection
File: src/api/user_controller.py:142-145
Component: [agent] (if multi-folder)

Description:
User-supplied input is directly concatenated into SQL query without 
parameterization or escaping, allowing SQL injection attacks.

Code Reference:
```python
142 | def get_user_by_name(name):
143 |     query = f"SELECT * FROM users WHERE name = '{name}'"
144 |     cursor.execute(query)  # VULNERABLE
145 |     return cursor.fetchone()
```

Risk:
Attackers can manipulate the query to extract sensitive data, modify 
records, or execute arbitrary database commands. Given the application
handles PII, this could lead to data breach and compliance violations.

Recommendation:
Use parameterized queries with placeholders:
```python
query = "SELECT * FROM users WHERE name = ?"
cursor.execute(query, (name,))
```

Effort: LOW (2-4 hours)
Priority: FIX BEFORE DEPLOYMENT

[Repeat for all code vulnerabilities found]

Summary Table:
┌─────────┬───────────────────────────┬──────────────────────────┬──────────┐
│ ID      │ Vulnerability Type        │ File:Line                │ Severity │
├─────────┼───────────────────────────┼──────────────────────────┼──────────┤
│ CODE-001│ SQL Injection             │ user_controller.py:142   │ HIGH     │
│ CODE-002│ Hardcoded API Key         │ config.py:23             │ CRITICAL │
│ CODE-003│ XSS in Template Rendering │ dashboard.html:87        │ MEDIUM   │
│ ...     │ ...                       │ ...                      │ ...      │
└─────────┴───────────────────────────┴──────────────────────────┴──────────┘

--------------------------------------------------------------------------------
16. DEPENDENCY VULNERABILITIES (CVE Details)
--------------------------------------------------------------------------------

Known CVEs and security issues in third-party dependencies.

[Finding Format]
ID: DEP-001
Package: requests
Current Version: 2.25.0
Fixed Version: 2.31.0
Severity: HIGH
CVE: CVE-2023-32681
CVSS: 7.5 (High)
File: requirements.txt:line 12

Description:
The `requests` library contains a vulnerability that allows unintended 
proxy credential exposure in redirects.

Impact:
If the application uses authenticated proxies, credentials may leak to
third-party servers during HTTP redirects.

Recommendation:
Upgrade to requests>=2.31.0:
```bash
pip install requests==2.31.0
```

Update requirements.txt:
```
requests==2.31.0
```

Effort: LOW (30 minutes - test authentication flows)
Priority: HIGH (Current Sprint)

[Repeat for all dependency vulnerabilities]

Summary Table:
┌─────────┬──────────────┬─────────┬───────────┬────────────────┬──────────┐
│ ID      │ Package      │ Current │ Fixed     │ CVE            │ Severity │
├─────────┼──────────────┼─────────┼───────────┼────────────────┼──────────┤
│ DEP-001 │ requests     │ 2.25.0  │ 2.31.0    │ CVE-2023-32681 │ HIGH     │
│ DEP-002 │ flask        │ 1.1.2   │ 2.3.0     │ CVE-2023-30861 │ CRITICAL │
│ DEP-003 │ pillow       │ 8.2.0   │ 10.0.0    │ CVE-2023-44271 │ MEDIUM   │
│ ...     │ ...          │ ...     │ ...       │ ...            │ ...      │
└─────────┴──────────────┴─────────┴───────────┴────────────────┴──────────┘

License Compliance Issues:
┌──────────────┬──────────┬─────────────────────────────────────────────┐
│ Package      │ License  │ Issue                                       │
├──────────────┼──────────┼─────────────────────────────────────────────┤
│ some-lib     │ GPL-3.0  │ Incompatible with proprietary distribution  │
└──────────────┴──────────┴─────────────────────────────────────────────┘

--------------------------------------------------------------------------------
17. INFRASTRUCTURE MISCONFIGURATIONS (IaC Deep Scan)
--------------------------------------------------------------------------------

Security misconfigurations in infrastructure-as-code and deployment configs.

[Finding Format]
ID: INFRA-001
Title: IAM Role with Wildcard Actions
Severity: CRITICAL
Resource: aws_iam_policy.app_policy
File: terraform/iam.tf:45-52
Component: [terraform] (if multi-folder)
CIS Benchmark: 1.16 - Ensure IAM policies follow least privilege

Description:
IAM policy grants wildcard permissions (s3:*) to the application role,
violating least privilege principle.

Code Reference:
```hcl
45 | resource "aws_iam_policy" "app_policy" {
46 |   name = "app-s3-policy"
47 |   policy = jsonencode({
48 |     Statement = [{
49 |       Effect   = "Allow"
50 |       Action   = "s3:*"              # VULNERABLE - Too broad
51 |       Resource = "*"                  # VULNERABLE - All resources
52 |     }]
```

Risk:
Application can read, write, delete ANY S3 object in ANY bucket, including
production data, backups, and other applications' data. Compromised app
could lead to data breach or destruction.

Recommendation:
Restrict to specific actions and resources:
```hcl
Action   = ["s3:GetObject", "s3:PutObject"]
Resource = "arn:aws:s3:::my-app-bucket/*"
```

Effort: MEDIUM (4-8 hours to test all app operations)
Priority: FIX BEFORE DEPLOYMENT

[Repeat for all infrastructure misconfigurations]

Summary Table:
┌──────────┬────────────────────────────┬──────────────────┬──────────┐
│ ID       │ Misconfiguration           │ File:Line        │ Severity │
├──────────┼────────────────────────────┼──────────────────┼──────────┤
│ INFRA-001│ IAM Wildcard Permissions   │ iam.tf:45        │ CRITICAL │
│ INFRA-002│ Public S3 Bucket           │ s3.tf:23         │ HIGH     │
│ INFRA-003│ Missing ECS Task Encryption│ ecs.tf:67        │ MEDIUM   │
│ ...      │ ...                        │ ...              │ ...      │
└──────────┴────────────────────────────┴──────────────────┴──────────┘

Container Security Issues (Dockerfile):
┌──────────┬────────────────────────────┬──────────────────┬──────────┐
│ ID       │ Issue                      │ File:Line        │ Severity │
├──────────┼────────────────────────────┼──────────────────┼──────────┤
│ DOCK-001 │ Running as root user       │ Dockerfile:15    │ HIGH     │
│ DOCK-002 │ Outdated base image        │ Dockerfile:1     │ MEDIUM   │
└──────────┴────────────────────────────┴──────────────────┴──────────┘

Kubernetes Security Issues:
┌──────────┬────────────────────────────┬──────────────────┬──────────┐
│ ID       │ Issue                      │ File:Line        │ Severity │
├──────────┼────────────────────────────┼──────────────────┼──────────┤
│ K8S-001  │ Privileged container       │ deployment.yaml:45│ CRITICAL│
│ K8S-002  │ No resource limits         │ deployment.yaml:33│ MEDIUM  │
└──────────┴────────────────────────────┴──────────────────┴──────────┘

================================================================================
END DEEP SECURITY SCAN RESULTS
================================================================================
```

**Update Consolidated Findings & Roadmap section:**
- Re-number findings to include deep scan results (original + CODE-xxx + DEP-xxx + INFRA-xxx)
- Update severity counts in Executive Summary
- Integrate deep scan findings into remediation roadmap with priorities

### Step 6: Write Enhanced File

**Overwrite** `security_review_<app>_<date>.txt` with:
1. Original sections 1-14 (unchanged)
2. New sections 15-17 (deep scan results)
3. Updated Consolidated Findings & Roadmap (merged)

### Step 7: Confirmation

Report completion to user:

```
✅ Deep Security Scan Complete

📊 Enhanced Security Review Generated:
   • Original findings: X
   • Code vulnerabilities: Y (A CRITICAL, B HIGH, C MEDIUM)
   • Dependency CVEs: Z (D CRITICAL, E HIGH)
   • Infrastructure misconfigs: W (F CRITICAL, G HIGH)
   
   Total: N CRITICAL, M HIGH, P MEDIUM, Q LOW findings

📄 Updated File: security_review_<app>_<date>.txt
   • Sections 15-17 added with deep scan results
   • Consolidated findings table updated
   • Remediation roadmap enhanced

➡️ Next Step: Generate SDR report with enhanced security findings
   Command: "Generate the SDR report for <app>"
```

## Output Format

**Enhanced `security_review_<app>_<date>.txt` structure:**

```
[Original Sections 1-14 from combined analysis]
1. Executive Summary (UPDATED with new counts)
2. Authentication & Identity
3. Authorization & Access Control
4. API Security
5. Transport & Communication
6. Infrastructure & Cloud
7. Secrets & Configuration
8. Data Protection & Privacy
9. Logging & Monitoring
10. AI & Agent Security
11. MCP Security
12. Dependency & Supply Chain (high-level)
13. Secure Coding Practices
14. [Reserved]

[New Deep Scan Sections]
15. Code Vulnerabilities (Deep Scan)
    ├─ Detailed OWASP Top 10 findings with code excerpts
    ├─ CWE/OWASP mappings
    └─ Exploit scenarios and remediation code

16. Dependency Vulnerabilities (CVE Details)
    ├─ CVE IDs with CVSS scores
    ├─ Current vs fixed versions
    ├─ License compliance issues
    └─ Upgrade paths and breaking changes

17. Infrastructure Misconfigurations (IaC Deep Scan)
    ├─ IAM policy issues
    ├─ Network exposure findings
    ├─ Encryption gaps
    ├─ Container/K8s security issues
    └─ CIS benchmark violations

18. Consolidated Findings & Roadmap (UPDATED)
    ├─ Master table with ALL findings (original + deep scan)
    ├─ Remediation roadmap with priorities
    └─ Effort estimates
```

## Quality Standards

**Each deep scan finding must include:**
1. **Unique ID** - CODE-xxx, DEP-xxx, or INFRA-xxx
2. **Exact file:line reference** - No generic descriptions
3. **Code excerpt** - Show vulnerable code (3-5 lines)
4. **Risk explanation** - Business impact, not just technical
5. **Concrete recommendation** - Working fix code or config
6. **Severity justification** - Why this severity level?
7. **Effort estimate** - Hours or days to remediate
8. **Component tag** - (if multi-folder) Which component affected

**Severity consistency:**
- **CRITICAL** - Exploitable remotely, data breach risk, compliance violation
- **HIGH** - Significant weakness, privilege escalation, sensitive exposure
- **MEDIUM** - Configuration issue, missing control, moderate risk
- **LOW** - Hardening opportunity, best practice gap
- **INFO** - Observation, informational note

**Multi-folder requirements:**
- Tag every finding with `[Component: label]`
- Identify cross-component issues (shared vulns, common misconfigs)
- Prioritize findings affecting multiple components

## Error Handling

**If security skills not found:**
```
❌ Error: Bundled security skills missing from workspace.

Expected location: skills/security/
Required files:
- skills/security/code-security.SKILL.md
- skills/security/dependency-security.SKILL.md
- skills/security/infra-security.SKILL.md

Cannot proceed with deep security scan. Options:
1. Ensure the SDR workspace is properly cloned from the repository
2. Skip deep scan and generate report with high-level findings only
3. Re-clone the SDR repository: https://github.com/F723193_jpmcgb/Automated-Secure-Design-Review

Note: These security skills should be bundled with the SDR workspace.
If files are missing, the workspace may be incomplete or corrupted.
```

**If security_review file not found:**
```
❌ Error: High-level security review not found.

Required file: security_review_<app>_<date>.txt

You must run "sdr-combined-analysis" first to generate the base
security review before enhancing with deep scan.

Command: "Run combined SDR analysis on <codebase-path>"
```

**If no scannable files found:**
```
⚠️ Warning: No files found for deep security scan.

Checked for:
- Source code: None found
- Dependencies: No manifest files
- Infrastructure: No IaC files

Skipping deep scan. Proceeding with existing security review.
```

## Notes

**Performance:**
- Adds 10-15 minutes to SDR workflow
- Scans are run sequentially (not parallel) for coherent finding IDs
- Large codebases (1000+ files) may take 20-30 minutes

**Coverage:**
- Code security: Detects common vulnerabilities, may miss complex logic flaws
- Dependency security: Uses public CVE databases, may have false positives
- Infrastructure security: Checks static configs, cannot test runtime behavior

**Limitations:**
- Cannot run dynamic analysis or penetration testing
- Cannot execute code to verify exploitability
- May report false positives (requires manual verification)
- Does not replace manual security review by AppSec team

**Integration with other skills:**
- `sdr-report-generator` automatically includes deep scan sections if present
- `sdr-confluence-publisher` publishes enhanced findings to Confluence
- `sdr-knowledge-base` stores deep scan results for pattern analysis

## See Also

- [sdr-combined-analysis.SKILL.md](sdr-combined-analysis.SKILL.md) - Run this first
- [sdr-report-generator.SKILL.md](sdr-report-generator.SKILL.md) - Generate report after deep scan
- [security/code-security.SKILL.md](security/code-security.SKILL.md) - Code vulnerability patterns (OWASP Top 10, SQL injection, XSS)
- [security/dependency-security.SKILL.md](security/dependency-security.SKILL.md) - CVE scanning and supply chain analysis
- [security/infra-security.SKILL.md](security/infra-security.SKILL.md) - IaC misconfiguration checks (Terraform, K8s, Docker)
