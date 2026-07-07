---
description: Analyze code repository ONCE to produce BOTH Architecture Summary and Security Review .txt files for JPMorganChase applications. This combined analysis is faster and more efficient than running Steps 1 and 2 separately.
---

# Combined SDR Analysis Skill

Analyze codebase **in a single pass** and generate **BOTH** output files:
- `architecture_summary_<app_name>_<YYYY-MM-DD>.txt`
- `security_review_<app_name>_<YYYY-MM-DD>.txt`

**Use this for faster SDR workflow instead of running separate architecture and security steps.**

## Prerequisites

### BEFORE Running This Skill

**If the codebase has an `artifacts/` folder with PDFs, DOCX, or images:**

1. **Run artifacts conversion first:**
   ```
   Convert artifacts for <codebase-path>
   ```
   This uses the `artifacts-conversion.SKILL.md` skill to extract text from binary files.

2. **Wait for conversion to complete** - You'll see confirmation message with converted file count

3. **Then proceed with this analysis** - The skill will automatically include converted artifacts

**If no `artifacts/` folder exists or all docs are already text:**
- Skip artifacts conversion
- Proceed directly with this analysis

## When to Use
- Starting a new SDR from scratch
- Want fastest analysis time (50% faster than separate steps)
- Need both architecture and security outputs
- Large repositories where double-scanning is inefficient

## Input Formats

### Single Folder (Traditional)
```
"Run combined SDR analysis on <codebase-path>"
```
- Analyzes one codebase folder
- Outputs written to that folder's root
- Backward compatible with existing workflow

### Multiple Folders (Multi-Component System)
```
"Run combined SDR analysis on:
- <component-label>: <path-1>
- <component-label>: <path-2>
- <component-label>: <path-3>"
```

**Example:**
```
"Run combined SDR analysis on:
- agent: C:\repos\smart-query-agent
- microservice: C:\repos\document-processor
- terraform: C:\repos\infrastructure-tf"
```

**Component labels can be ANY descriptive name:**
- Technical: `agent`, `microservice`, `api-gateway`, `worker`, `scheduler`
- Layer: `frontend`, `backend`, `database`, `cache`
- Function: `auth-service`, `payment-processor`, `notification-engine`
- Infrastructure: `terraform`, `helm-charts`, `k8s-manifests`
- Shared: `shared-lib`, `common-utils`, `proto-definitions`

### Multi-Folder Analysis Features
- **Cross-component detection**: Identifies imports, API calls, shared configs between folders
- **Unified output**: Single architecture summary + security review covering all components
- **Component tracking**: Every finding/observation tagged with source component
- **Integration mapping**: Documents how components interact with each other
- **Output location priority:**
  1. **If in VS Code workspace** → Workspace root directory
  2. **Else** → First component folder from user's list
  3. **Fallback** → Current working directory

## Outputs

### Single Folder Output
**Two files in codebase root:**
1. `architecture_summary_<app_name>_<YYYY-MM-DD>.txt`
2. `security_review_<app_name>_<YYYY-MM-DD>.txt`

### Multi-Folder Output
**Two files in first component folder OR workspace root:**
1. `architecture_summary_<app_name>_multi_<YYYY-MM-DD>.txt`
2. `security_review_<app_name>_multi_<YYYY-MM-DD>.txt`

Both formats have same section structure and quality.

## File Scanning Strategy

See [common/file-scanning.md](common/file-scanning.md) for skip patterns, priority file list, and scanning order.

## Combined Analysis Workflow

### 1. Discovery Phase

#### Single Folder Discovery
Report initial discovery:
```
📁 Repository Discovery
   ├─ Total files found: 1,247
   ├─ Skipped: 1,105 (node_modules, build outputs, caches)
   └─ To analyze: 142 files (source + config + IaC + docs)
```

#### Multi-Folder Discovery
Report discovery per component with totals:
```
📁 Multi-Component Repository Discovery
   
   Component: agent (C:\repos\smart-query-agent)
   ├─ Total files: 234
   ├─ Skipped: 198 (node_modules, __pycache__)
   └─ To analyze: 36 files
   
   Component: microservice (C:\repos\document-processor)
   ├─ Total files: 189
   ├─ Skipped: 152 (vendor, dist)
   └─ To analyze: 37 files
   
   Component: terraform (C:\repos\infrastructure-tf)
   ├─ Total files: 47
   ├─ Skipped: 3 (.terraform)
   └─ To analyze: 44 files
   
   📊 Combined Totals:
   ├─ Total files: 470
   ├─ Skipped: 353
   └─ To analyze: 117 files across 3 components
```

### 2. Dual-Purpose Scanning Phase

**For EACH file scanned, extract BOTH architecture AND security data simultaneously:**

#### Architecture Data to Extract:
- Components, modules, services, classes
- Dependencies and imports
- API endpoints and protocols
- Database connections and queries
- Infrastructure patterns (containers, K8s, IaC)
- AI/ML models, agents, tools, MCP servers
- Integration points and external services
- Technology stack and frameworks

#### Security Data to Extract (from same file):
- Authentication mechanisms (OAuth, JWT, SSO)
- Authorization logic (RBAC, ABAC, permissions)
- Input validation patterns
- Secrets and credentials (hardcoded, env vars)
- API security (rate limiting, CORS, validation)
- TLS/encryption usage
- Logging patterns (PII exposure, verbosity)
- IAM roles and policies
- Network configurations
- Vulnerabilities and misconfigurations
- Missing security controls

#### Multi-Folder Specific: Cross-Component Analysis
**When analyzing multiple folders, also detect:**
- **Imports/references** between components (e.g., agent imports shared-lib)
- **API calls** from one component to another (HTTP, gRPC endpoints)
- **Shared configuration** (common env vars, config files)
- **Data flow** (which component writes data consumed by another)
- **Deployment dependencies** (Terraform provisions resources for microservice)
- **Authentication flow** (auth-service validates tokens for other services)
- **Shared secrets/keys** (multiple components using same credentials)

**Track component membership for every finding:**
- File path → Component label
- Cross-component findings tagged with both components

**Progress indicator (every 20-25 files):**
Single folder: 🔄 emoji, file counts by category, running architecture tally (components/services/integrations/DBs), running findings tally by severity, ETA. Keep under 10 lines.
Multi-folder: same format, but show per-component file progress (✓ done / ⏳ in-progress / ⏱ pending) and break findings tally by component. Keep under 15 lines.

### 3. Architecture Summary Generation

After scanning complete, generate architecture output file using data collected:

**Sections (same as architecture-analysis.SKILL.md):**

1. **Header** - App name, date, repo(s), audience
2. **Application Overview** - Name, purpose, tech stack
   - **Multi-folder addition:** List all components with their roles
3. **Architecture & Components**
   - Pattern, modules/services table, protocols, APIs, data layer
   - **Multi-folder addition:** Component table with cross-component interactions
4. **Infrastructure & Deployment**
   - Platforms (GAIA/GAP/GKP/AWS), containers, CI/CD, observability
   - Resource table
   - **Multi-folder addition:** Map resources to components
5. **AI/ML Elements**
   - Models, LLMs, agents, MCP, RAG, MLOps
   - Element table: `Element | Component | Type | Framework | Location | Notes`
6. **Integrations & Dependencies**
   - **Multi-folder addition:** Internal integrations (between components)
   - JPMC platforms, brokers, external feeds
   - Library table: `Library | Version | Component | Purpose | License`
7. **Architecture Diagram** - Mermaid flowchart (wrap in `===== MERMAID DIAGRAM START/END =====`)
   - **Multi-folder:** Show all components and connections between them
8. **Footer** - End marker, validation disclaimer

> **Boundary rule:** Security findings, control gaps, and architectural recommendations belong
> exclusively in the Security Review output (section 4 below). Do not include a Security &
> Compliance section or an Observations & Recommendations table in the architecture summary output.

#### Multi-Folder Architecture Format Example

**Section: Application Overview (Multi-Component)**
```
Application Name: Smart Query System
Purpose: AI-powered document analysis and query processing platform
Technology Stack: Python (FastAPI, LangChain), TypeScript (Node.js), Terraform

This application is composed of 3 components:

| Component    | Type        | Path                          | Role                           |
|--------------|-------------|-------------------------------|--------------------------------|
| agent        | AI Agent    | C:\repos\smart-query-agent    | LangChain agent, query orchestration |
| microservice | API Service | C:\repos\document-processor   | Document ingestion, vector DB  |
| terraform    | IaC         | C:\repos\infrastructure-tf    | AWS infrastructure provisioning|

Component Interactions:
- agent → microservice: HTTP API calls to /api/documents, /api/search
- terraform → agent: Provisions Lambda execution role, API Gateways
- terraform → microservice: Provisions RDS, ElasticSearch cluster
```

**Section: Architecture & Components (Multi-Component)**
```
Component-Based Architecture

The system follows a distributed microservices pattern with infrastructure-as-code:

Components/Services Table:

| Component    | Module/Service      | Technology       | Location          | Purpose              |
|--------------|---------------------|------------------|-------------------|----------------------|
| agent        | QueryAgent          | Python/LangChain | src/agent.py      | Query orchestration  |
| agent        | ToolExecutor        | Python           | src/tools/        | Tool invocation      |
| microservice | DocumentService     | Node.js/Express  | src/services/doc  | Document management  |
| microservice | SearchService       | Node.js          | src/services/srch | Vector search        |
| terraform    | ECS Cluster         | Terraform        | modules/ecs/      | Container runtime    |
| terraform    | RDS Instance        | Terraform        | modules/rds/      | PostgreSQL database  |

Cross-Component Integrations:
- agent QueryAgent calls microservice DocumentService via HTTPS (port 8080)
- agent retrieves secrets from AWS Secrets Manager (provisioned by terraform)
- microservice connects to RDS (provisioned by terraform)
```

**Write:** 
- Single folder: `architecture_summary_<app_name>_<YYYY-MM-DD>.txt`
- Multi-folder: `architecture_summary_<app_name>_multi_<YYYY-MM-DD>.txt`

### 4. Security Review Generation

Using security data collected from same scan, generate security output file:

**Sections (same as security-review-analysis.SKILL.md):**

1. **Executive Summary**
   - Posture rating, findings count table, top 3 critical, narrative
   - **Multi-folder addition:** Findings breakdown by component
2. **Authentication & Identity** - OAuth2/OIDC/SSO/JWT, session mgmt, hardcoded creds
   - **Multi-folder:** Tag findings with component
3. **Authorization & Access Control** - RBAC/ABAC, privilege escalation, least privilege
   - **Multi-folder:** Tag findings with component
4. **API Security** - Input validation, injections, rate limiting, CORS, error leakage
   - **Multi-folder:** Tag findings with component + cross-component API security
5. **Transport & Communication** - TLS version, cert validation, mTLS, insecure protocols
   - **Multi-folder:** Include inter-component communication security
6. **Infrastructure & Cloud** - IAM wildcards, network exposure, container security, K8s policies
   - **Multi-folder:** Map infrastructure to components it serves
7. **Secrets & Configuration** - Hardcoded secrets, `.env` in repo, rotation, prod/dev segregation
   - **Multi-folder:** Shared secrets across components flagged as HIGH risk
8. **Data Protection & Privacy** - PII identification, encryption, masking, retention, deserialization
   - **Multi-folder:** Tag findings with component
9. **Logging & Monitoring** - PII in logs, log injection, DEBUG mode, audit trails, alerting
   - **Multi-folder:** Tag findings with component
10. **AI & Agent Security** - Prompt injection, system prompt exposure, tool validation, LLM output execution
    - **Multi-folder:** Tag findings with component
11. **MCP Security** - Unauth endpoints, tool sanitization, transport TLS, overly broad scope
    - **Multi-folder:** Tag findings with component
12. **Dependency & Supply Chain** - CVEs, outdated deps, licenses
    - Table: `Library | Version | Component | CVE | Severity | Recommendation`
13. **Secure Coding Practices** - Injection flaws, weak crypto, insecure randomness, path traversal
    - **Multi-folder:** Tag findings with component
14. **Consolidated Findings & Roadmap**
    - Master table: `ID | Component | Domain | Finding | File:Line | Severity | Recommendation | Effort`
    - **Multi-folder addition:** Component column for filtering
    - Roadmap: CRITICAL (fix before deploy), HIGH (current sprint), MEDIUM (2 sprints), LOW (backlog)

#### Multi-Folder Security Format Example

**Section: Executive Summary (Multi-Component)**
```
Security Posture: MODERATE RISK

Overall Findings:

| Severity  | Count | Breakdown by Component                    |
|-----------|-------|-------------------------------------------|
| CRITICAL  | 2     | agent: 1, microservice: 1                 |
| HIGH      | 6     | agent: 2, microservice: 3, cross-comp: 1  |
| MEDIUM    | 11    | agent: 3, microservice: 5, terraform: 3   |
| LOW       | 15    | agent: 5, microservice: 8, terraform: 2   |

Cross-Component Security Issues:
- HIGH: Unencrypted HTTP traffic between agent and microservice
- MEDIUM: Shared hardcoded API key in both agent and terraform

Top Critical Findings:
1. [CRITICAL] Hardcoded AWS credentials in agent/config.py:45
2. [CRITICAL] SQL injection vulnerability in microservice/src/db/queries.js:128
```

**Section: Consolidated Findings & Roadmap (Multi-Component)**
```
Master Findings Table:

| ID | Component    | Domain          | Finding                  | File:Line              | Severity | Recommendation             | Effort |
|----|--------------|-----------------|--------------------------|------------------------|----------|----------------------------|--------|
| 1  | agent        | Secrets         | Hardcoded AWS creds      | agent/config.py:45     | CRITICAL | Use AWS Secrets Manager    | 2h     |
| 2  | microservice | API Security    | SQL injection            | microservice/db.js:128 | CRITICAL | Use parameterized queries  | 4h     |
| 3  | [CROSS]      | Transport       | HTTP not HTTPS           | agent/client.py:89     | HIGH     | Enable TLS for all APIs    | 8h     |
|    |              |                 | (agent → microservice)   | microservice/app.js:12 |          |                            |        |
| 4  | terraform    | IAM             | Wildcard S3 permissions  | terraform/iam.tf:34    | HIGH     | Restrict to specific paths | 2h     |

Cross-Component Findings:
- Finding #3 affects both agent (client) and microservice (server)
- Shared secret in Finding #1 also referenced in terraform outputs
```

**Write:**
- Single folder: `security_review_<app_name>_<YYYY-MM-DD>.txt`
- Multi-folder: `security_review_<app_name>_multi_<YYYY-MM-DD>.txt`

## Analysis Rules

### Architecture Analysis Rules
- Reference actual file names, classes, and configuration keys
- **Multi-folder:** Always prefix file paths with component label (e.g., `agent/src/main.py`)
- Use technical, precise tone for senior architects
- Use `[NOT IDENTIFIED IN CODEBASE]` for unavailable data — NEVER guess
- Format: Plain text, UTF-8; wrap for readability, but prefer completeness over aggressive trimming in generated outputs
- Use `=` for major separators, `-` for minor
- Tables: ASCII with `|` separators

### Security Analysis Rules
- **Reference exact file:line in EVERY finding**
- **Multi-folder:** Tag every finding with component (in tables and descriptions)
- Use severity: `CRITICAL | HIGH | MEDIUM | LOW | INFO`
- Missing controls = findings (e.g., no rate limiting = HIGH)
- **Multi-folder:** Cross-component issues get `[CROSS]` tag + list both components
- Use `[NOT IDENTIFIED IN CODEBASE]` for missing data — NEVER guess
- Format: Plain text, UTF-8, ASCII tables; wrap for readability, but prefer completeness over aggressive trimming in generated outputs
- Technical tone for AppSec engineers

### Multi-Folder Specific Rules
- **Component identification**: Every file path must map to a component
- **Cross-component detection**: Look for:
  - Imports: `from other_component import X` or `require('other-repo/...')`
  - API calls: HTTP/gRPC endpoints pointing to other component's services
  - Shared configs: Same env vars or config keys in multiple components
  - Data dependencies: Component A writes data that Component B reads
- **Severity escalation**: Issues affecting multiple components may be elevated:
  - Shared hardcoded secret = CRITICAL (vs HIGH if single component)
  - Unencrypted inter-component traffic = HIGH (vs MEDIUM for external)
- **Integration mapping**: Document all inter-component communication patterns
- **Unified vs separated**: Present ONE coherent system architecture, not separate docs

### Severity Guidelines
- **CRITICAL**: Immediate exploitation risk, data breach, compliance violation
- **HIGH**: Significant weakness, privilege escalation, sensitive data exposure
- **MEDIUM**: Config weakness, missing control, moderate risk
- **LOW**: Improvement, hardening, best practice
- **INFO**: Observation, note, future consideration

## Quality Validation

### Before Writing Files

**Verify both outputs have:**

These output-length targets apply only to files generated by this combined-analysis skill. Treat them as preferred ranges, not hard caps; do not remove useful evidence or analysis solely to force the file under a limit.

Architecture Summary:
- [ ] All 10 sections present
- [ ] Component table with 5+ entries
- [ ] Architecture diagram (Mermaid or ASCII)
- [ ] Dependency library table
- [ ] No `[NOT IDENTIFIED]` in critical fields
- [ ] File is preferably 6,000-20,000 chars

Security Review:
- [ ] All 14 sections present
- [ ] Executive summary with posture rating
- [ ] At least 3 findings documented
- [ ] Every finding has file:line reference
- [ ] Consolidated findings table
- [ ] Remediation roadmap by severity
- [ ] File is preferably 8,000-30,000 chars

## After Completion

### Single Folder Completion Message

```
✅ Combined Analysis Complete!

📄 Generated Files:
   ├─ architecture_summary_<app>_<date>.txt (X KB)
   └─ security_review_<app>_<date>.txt (Y KB)

📊 Analysis Summary:
   ├─ Files scanned: 142 (skipped 1,105)
   ├─ Components identified: 24
   ├─ Security findings: 27 (2 CRITICAL, 5 HIGH, 8 MEDIUM, 12 LOW)
   └─ Time saved: ~50% vs separate analyses

� Optional Deep Security Scan Available:

Would you like to enhance this security review with detailed vulnerability scanning?

Deep scan adds:
• Specific CVE identification with remediation versions
• OWASP Top 10 vulnerability detection (injection, XSS, deserialization)
• Infrastructure misconfiguration analysis (IAM wildcards, exposed resources)
• Supply chain risk assessment (outdated packages, license compliance)
• Hardcoded secrets detection (API keys, credentials)

⏱️  Time: +10-15 minutes
📋 Recommended for: Production deployments, high-risk apps, compliance needs

Would you like to run the deep security scan now?
[Yes - Recommended for production] [No - Skip to report generation]
```

**After user responds:**
- **If Yes**: Read and execute `skills/sdr-deep-security-scan.SKILL.md`
- **If No**: Continue with next step message below

**If user skips deep scan or after deep scan completes:**
```
�🚀 Next Step:
   Run "Generate SDR report for <app>" to create final HTML report.

💡 Tip: Both files are now ready as inputs for Step 3 (Report Generation).
```

### Multi-Folder Completion Message

```
✅ Multi-Component Combined Analysis Complete!

📄 Generated Files:
   ├─ architecture_summary_<app>_multi_<date>.txt (X KB)
   └─ security_review_<app>_multi_<date>.txt (Y KB)
   
   Location: <first-component-path> or <workspace-root>

📊 Analysis Summary:
   
   Components Analyzed: 3
   ├─ agent: 36 files scanned
   ├─ microservice: 37 files scanned
   └─ terraform: 44 files scanned
   
   Total: 117 files scanned (353 skipped)
   
   Cross-Component Integrations: 7
   ├─ agent → microservice: 4 API calls
   ├─ terraform → agent: 2 provisioned resources
   └─ terraform → microservice: 3 provisioned resources
   
   Security Findings: 34 total
   ├─ agent: 11 findings (1 CRITICAL, 2 HIGH, 3 MEDIUM, 5 LOW)
   ├─ microservice: 19 findings (1 CRITICAL, 3 HIGH, 5 MEDIUM, 10 LOW)
   ├─ terraform: 5 findings (1 HIGH, 2 MEDIUM, 2 LOW)
   └─ Cross-component: 1 finding (1 HIGH)
   
   Time saved: ~60% vs analyzing components separately

� Optional Deep Security Scan Available:

Would you like to enhance this security review with detailed vulnerability scanning across all components?

Deep scan adds:
• Specific CVE identification with remediation versions
• OWASP Top 10 vulnerability detection (injection, XSS, deserialization)
• Infrastructure misconfiguration analysis (IAM wildcards, exposed resources)
• Supply chain risk assessment (outdated packages, license compliance)
• Hardcoded secrets detection (API keys, credentials)
• Cross-component vulnerability analysis (shared secrets, common misconfigs)

⏱️  Time: +15-20 minutes (scans all 3 components)
📋 Recommended for: Production deployments, high-risk apps, compliance needs

Would you like to run the deep security scan now?
[Yes - Recommended for production] [No - Skip to report generation]
```

**After user responds:**
- **If Yes**: Read and execute `skills/sdr-deep-security-scan.SKILL.md`
- **If No**: Continue with next step message below

**If user skips deep scan or after deep scan completes:**
```
�🚀 Next Step:
   Run "Generate SDR report for <app>" to create final HTML report.

💡 Tip: Both output files include component tags for easy filtering.
```

## Efficiency Benefits

### Single Folder: Combined vs Separate Steps

**Compared to running architecture-analysis + security-review separately:**

| Metric | Separate Steps | Combined Analysis | Improvement |
|--------|---------------|-------------------|-------------|
| Files scanned | 2x (once per step) | 1x | **50% fewer** |
| Time to complete | 15-20 min | 8-12 min | **40-50% faster** |
| Token usage | ~100% (baseline) | ~60% | **40% savings** |
| File reads | 2x | 1x | **50% fewer** |
| Context switching | 2 separate sessions | 1 session | **Simpler** |

### Multi-Folder: Combined vs Sequential Single-Folder Analyses

**Compared to running combined analysis on each folder separately, then manually merging:**

| Metric | 3 Separate Analyses | Multi-Folder Combined | Improvement |
|--------|---------------------|----------------------|-------------|
| Analysis runs | 3 runs | 1 run | **67% fewer** |
| Time to complete | 24-36 min (3 x 8-12) | 12-18 min | **50% faster** |
| Output files | 6 files (2 per folder) | 2 unified files | **67% fewer** |
| Cross-component detection | Manual/missing | Automatic | **Complete** |
| Manual merging effort | 30-60 min | 0 min | **100% saved** |
| Integration mapping | Incomplete | Comprehensive | **Better quality** |
| Shared issue detection | Missed | Automatic | **Higher security** |

## When to Use Separate Skills Instead

Use individual `architecture-analysis.SKILL.md` or `security-review-analysis.SKILL.md` when:
- Only need one output (e.g., architecture review only)
- Want to review architecture before security analysis
- Debugging specific analysis issues
- Need to regenerate one output without re-scanning

## Usage Examples

### Example 1: Standard Single Folder Analysis
```
"Run combined SDR analysis on c:\apps\my-banking-app"
```

### Example 2: Single Folder With App Name Specified
```
"Run combined SDR analysis on c:\apps\sblc-copilot for app name SBLC Copilot"
```

### Example 3: Single Folder With Metadata
```
"Run combined analysis on c:\apps\aero, SEAL ID 111975, app name AERO"
```

### Example 4: Multi-Folder Analysis (Generic Labels)
```
"Run combined SDR analysis on:
- agent: C:\repos\smart-query-agent
- microservice: C:\repos\document-processor
- terraform: C:\repos\infrastructure-tf"
```

### Example 5: Multi-Folder Analysis (Microservices)
```
"Run combined SDR analysis on:
- api-gateway: /workspaces/api-gw
- auth-service: /workspaces/auth-svc
- payment-service: /workspaces/payment-svc
- shared-lib: /workspaces/common-utils"
```

### Example 6: Multi-Folder Analysis (Layers)
```
"Run combined SDR analysis on:
- frontend: C:\projects\myapp\ui
- backend: C:\projects\myapp\api
- database: C:\projects\myapp\migrations"
```

### Example 7: Multi-Folder Analysis (Infrastructure + Code)
```
"Run combined SDR analysis on:
- application: /repos/trading-app
- helm-charts: /repos/trading-helm
- terraform: /repos/trading-infra
- ci-cd: /repos/trading-pipelines"
```

### Example 8: Multi-Folder With App Name
```
"Run combined SDR analysis for Smart Query System on:
- agent: C:\repos\smart-query-agent
- microservice: C:\repos\document-processor
- terraform: C:\repos\infrastructure-tf"
```

## Notes
- Both output files maintain same quality and format as separate analyses
- All findings will include exact file:line references
- Architecture and security insights cross-reference same codebase scan
- Can proceed directly to Step 3 (SDR Report Generation) after completion
- If one output file fails validation, both files are regenerated

---

## Post-Analysis Workflow Integration

**After successful analysis completion, prompt user for next step:**

Use `vscode_askQuestions` tool:

**Question: "Analysis complete! What would you like to do next?"**
- Header: "SDR Analysis Complete - Next Steps"
- Question: "Your analysis files have been generated successfully. What would you like to do next?"
- Message: "You can publish directly to Confluence, generate an HTML report for offline review, archive in the knowledge base, or just keep the analysis files."
- Options:
  - "Publish to Confluence" (recommended: true) → Launch `sdr-confluence-publisher.SKILL.md`
    - Description: "Generate draft for review and publish to CIBSECARCH Confluence page"
  - "Generate HTML report" → Launch `sdr-report-generator.SKILL.md`
    - Description: "Create offline HTML report for manual upload to Confluence"
  - "Store in knowledge base" → Launch `sdr-knowledge-base.SKILL.md`
    - Description: "Archive this SDR for future queries and pattern analysis"
  - "Nothing - just analysis files" → Exit
    - Description: "Keep analysis files only, I'll handle the rest manually"
- Allow freeform input: false

**Based on user selection:**

**If "Publish to Confluence":**
```
User selected: Publish to Confluence
Launching sdr-confluence-publisher skill...

[Read and execute sdr-confluence-publisher.SKILL.md]
```

**If "Generate HTML report":**
```
User selected: Generate HTML report
Launching sdr-report-generator skill...

[Read and execute sdr-report-generator.SKILL.md]
```

**If "Store in knowledge base":**
```
User selected: Store in knowledge base
Launching sdr-knowledge-base skill...

[Read and execute sdr-knowledge-base.SKILL.md]
```

**If "Nothing - just analysis files":**
```
✓ Analysis complete!

Output files:
- architecture_summary_<app>_<date>.txt
- security_review_<app>_<date>.txt

Next steps (when ready):
- Publish to Confluence: "Publish the <app> SDR to Confluence"
- Generate HTML report: "Generate the SDR report for <app>"
- Archive in knowledge base: "Store the completed <app> SDR in the knowledge base"
```

**Benefits of workflow integration:**
- Seamless transition from analysis to publishing
- No need to remember next command
- Reduces workflow friction
- Guides users through complete SDR process
- Single conversation completes entire SDR from start to finish
