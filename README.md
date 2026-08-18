# Secure Design Review (SDR) Automation System

This repository provides a modular suite of GitHub Copilot Skills engineered to automate and streamline the end-to-end Secure Design Review (SDR) lifecycle for enterprise applications. It integrates artifact text extraction, single-pass architecture and security analysis, deep vulnerability scanning (CVEs, OWASP Top 10, and IaC misconfigurations), and Confluence-ready report generation to accelerate threat modeling and security compliance across development teams.

---

## ⚡ QUICK START

### 1. Install Dependencies (One-Time)
```bash
pip install -r requirements.txt
```

Verify installation:
```bash
python artifacts-to-text.py --check-deps
```

### 2. Convert Artifacts (If Needed)
If your codebase has an `artifacts/` folder with PDFs, DOCX, PPTX, MSG, or images:
```
Convert artifacts for <path-to-codebase>
```

This runs the artifacts-conversion skill to extract text from PDFs, DOCX, PPTX, MSG, TXT, and OCR images. Output goes to `<codebase>/artifacts_converted/`.

**Skip this step if:** No `artifacts/` folder or all docs already in text format.

### 3. Run SDR Analysis

**Option A - Combined Analysis (Recommended - 50% Faster):**
```
Run combined SDR analysis on <repo-path>
```

Produces both files in a single scan:
- `architecture_summary_<app>_<date>.txt`
- `security_review_<app>_<date>.txt`

**After analysis, you'll be prompted:**
1. **"Run optional deep security scan?"** (recommended for production deployments)
   - **Yes** → Enhances security review with detailed CVE scanning, OWASP Top 10 detection, and IaC misconfigurations (+10-15 min)
   - **No** → Skip to next step with high-level findings
2. **"What would you like to do next?"**
   - Generate HTML report
   - Store in knowledge base
   - Nothing - just analysis files

**Option B - Standalone Analysis (Advanced - Only if you need just one):**
```
Run architecture analysis on <repo-path>
```
Or:
```
Run security review on <repo-path>
```

Standalone skills are in `skills/` - use only when you specifically need architecture OR security, not both.

### 4. Publish or Generate Report

**Option A - HTML Report (Traditional - For Offline Review):**
```
Generate the SDR report for <app-name>
```

You'll be prompted for metadata (SEAL IDs, JIRA story, etc.)

Output: `SDR_<app>_<date>.html` (ready for manual Confluence upload)

### 5. Archive (Optional)
```
Store the completed <app-name> SDR in the knowledge base
```

Archives SDR for future queries and pattern analysis.

---

## Overview

The SDR automation system includes six core Copilot skills:

| Skill | Purpose | Output |
|-------|---------|--------|
| **Artifacts Conversion** | Extract text from PDFs, DOCX, PPTX, MSG, TXT, and images | `artifacts_converted/` folder |
| **Combined SDR Analysis** | Fast single-pass codebase analysis (RECOMMENDED) | Both .txt files |
| **Deep Security Scan** | OPTIONAL: Detailed CVE/OWASP/IaC vulnerability scanning (after combined analysis) | Enhanced security_review with deep findings |
| **SDR Report Generator** | Create Confluence HTML from .txt files (traditional workflow) | `SDR_<app>_<date>.html` |
| **SDR Knowledge Base** | Store and query past SDRs | Organized knowledge base |

**Optional Skills** (in `skills/`):
- Architecture Analysis (architecture only, no security)
- Security Review (security only, no architecture)

Use optional skills only when you specifically need one analysis without the other (~5% of use cases).

**Workflow Recommendations:**
- **Traditional:** Combined Analysis → Report Generator → Manual Upload (25-35 minutes total)
- **Safest:** Combined Analysis → Confluence Publisher with draft review (includes validation + rollback)

**Deep Security Scan adds:**
- Specific CVE identification with remediation versions
- OWASP Top 10 vulnerability detection (injection, XSS, deserialization)
- Infrastructure misconfiguration analysis (IAM wildcards, exposed resources)
- Supply chain risk assessment (outdated packages, license compliance)
- Hardcoded secrets detection (API keys, credentials)
- +10-15 minutes scan time but comprehensive vulnerability coverage

## Repository Structure

```
SDR/
├── .github/
│   └── copilot-instructions.md         # Workspace-level Copilot configuration
├── skills/                              # Copilot skills (main automation)
│   ├── artifacts-conversion.SKILL.md   # Convert PDFs, DOCX, PPTX, MSG, TXT, and images to text
│   ├── sdr-combined-analysis.SKILL.md  # Combined analysis (recommended)
│   ├── sdr-confluence-publisher.SKILL.md # Direct Confluence publish (recommended)
│   ├── sdr-report-generator.SKILL.md   # HTML report generation (traditional)
│   ├── sdr-knowledge-base.SKILL.md     # Knowledge base management
│   ├── common/
│   │   └── artifact-handling.md        # Shared artifact handling procedures
│   └── optional/                       # Advanced/standalone skills
│       ├── README.md                   # When to use optional skills
│       ├── architecture-analysis.SKILL.md
│       └── security-review-analysis.SKILL.md
├── reference/                           # Supporting documentation
│   ├── confluence-patterns.md          # Reference examples from actual reports
│   ├── sdr-canonical-template.html     # Canonical report shell used for all reports
│   ├── sdr-application-details-static.html # Locked fuller Application Details table
│   ├── sdr-reviewed-areas-static.html  # Locked reviewed areas matrix
│   ├── sdr-report-field-mappings.md    # Data source mappings
│   ├── REPORT_QUALITY_IMPROVEMENTS.md  # Quality analysis and guidelines
│   └── archive/                        # Historical reports and examples
├── knowledge-base/                      # SDR knowledge base (grows over time)
│   ├── completed/                      # Finalized SDRs organized by date
│   ├── insights/                       # Extracted learnings and patterns
│   └── indexes/                        # Quick lookup indexes
├── examples/                            # Sample repos and test outputs
│   ├── sample-repos/                   # Test codebases
│   └── manual-outputs/                 # Example SDR outputs
├── archive/                             # Historical implementation docs
├── artifacts-to-text.py                 # Artifact converter script
├── requirements.txt                     # Python dependencies
└── README.md                            # This file
```

---

## Detailed Workflows

### Recommended Workflow (Fastest - 5-10 minutes)

**Step 0: Convert Artifacts (If Needed)**
```
Convert artifacts for c:\path\to\your-app
```
Only if `artifacts/` folder exists with PDFs, DOCX, PPTX, MSG, TXT, or images.

**Step 1: Run Combined Analysis**
```
Run combined SDR analysis on c:\path\to\your-app
```

**What it does:**
1. Scans codebase once
2. Extracts both architecture and security data
3. Generates both output files
4. **Prompts:** "What would you like to do next?"

**Outputs:**
- `architecture_summary_<app>_<date>.txt`
- `security_review_<app>_<date>.txt`

**Advantages:**
- 50% faster than separate steps
- ~40% less token usage
- No redundant file scanning
- Single pass ensures consistency

### Traditional Workflow (For Offline Review - 25-35 minutes)

**Step 0: Convert Artifacts (If Needed)**
Same as above

**Step 1: Run Combined Analysis**
Same as above

**Step 2: Select "Generate HTML report"**

Agent launches report generator:
```
Generate the SDR report for <app-name>
```
Output: `SDR_<app>_<date>.html`

**Step 3: Manual Review & Upload**
1. Open the HTML in a browser
2. Review for accuracy
3. Make any necessary edits
4. Upload to Confluence manually
5. Fix any formatting issues

**Total time:** 25-35 minutes

**When to use:** Need offline review, other spaces, custom template mods

---

### Alternative: Standalone Analysis (Advanced)

Use only when you need architecture OR security independently.

**Architecture Only:**
```
Run architecture analysis on c:\path\to\your-app
```
Output: `architecture_summary_<app>_<date>.txt`

**Security Only:**
```
Run security review on c:\path\to\your-app
```
Output: `security_review_<app>_<date>.txt`

**Note:** Standalone skills located in `skills/` folder.

---

## Skill Details

### Artifacts Conversion (`artifacts-conversion.SKILL.md`)

**Best for:** Converting binary artifacts before SDR analysis

**Features:**
- Detects artifacts/ folder automatically
- Validates Python dependencies
- Extracts text from PDFs, DOCX, PPTX, and MSG files
- Passes through TXT artifacts unchanged
- OCRs images (PNG, JPEG) for text content
- Creates artifacts_converted/ folder with text files
- Handles conversion errors gracefully

**Usage:**
```
Convert artifacts for <codebase-path>
```

**Output:** `<codebase>/artifacts_converted/` folder with `*_extracted.txt` files

---

### Combined SDR Analysis (`sdr-combined-analysis.SKILL.md`)

**Best for:** Complete SDR workflow - fastest option

**Features:**
- Single-pass codebase scanning
- Dual-purpose analysis (architecture + security)
- Smart file filtering (skips node_modules, build artifacts, etc.)
- Processes converted artifacts automatically
- 50% faster than separate skills
- **NEW:** Post-analysis workflow integration (prompts for next step)

**Outputs:** Both architecture and security .txt files

**NEW Workflow Integration:**
After analysis, prompts:
1. **"Run optional deep security scan?"** (recommended for production deployments)
   - Enhances security review with detailed vulnerability scanning
   - Adds 10-15 minutes but provides comprehensive CVE/OWASP/IaC coverage
2. **"What would you like to do next?"**
   - Generate HTML report → Launches sdr-report-generator
   - Store in knowledge base → Launches sdr-knowledge-base
   - Nothing - just analysis files → Exit

---

### Deep Security Scan (`sdr-deep-security-scan.SKILL.md`) 🔬 OPTIONAL

**Best for:** Comprehensive vulnerability assessment for production deployments

**Features:**
- 🔍 **Code-level vulnerability detection:** SQL injection, XSS, command injection, path traversal, insecure deserialization, hardcoded secrets, weak crypto using `code-security` skill
- 🛡️ **CVE identification:** Specific CVE IDs with CVSS scores, current vs fixed versions, and upgrade paths using `dependency-security` skill
- ☁️ **Infrastructure misconfiguration scanning:** IAM wildcards, public resources, missing encryption, container hardening issues using `infra-security` skill
- 📋 **OWASP Top 10 mapping:** Every finding mapped to relevant OWASP/CWE categories
- 🔗 **Supply chain risk assessment:** Outdated packages, license compliance issues
- 🎯 **Multi-component analysis:** Tags findings by component, detects cross-component vulnerabilities

**When to use:**
- ✅ Production deployments requiring comprehensive security assessment
- ✅ High-risk or external-facing applications
- ✅ Applications handling sensitive data (PII, financial, health)
- ✅ Compliance requirements (SOX, PCI-DSS, HIPAA)
- ✅ AI/ML applications with LLM or agent components
- ✅ Previous security incidents or concerns

**When to skip:**
- ⏭️ Early architecture reviews (not deployment-ready)
- ⏭️ Low-risk internal tools with no sensitive data
- ⏭️ Time-constrained rapid assessments
- ⏭️ Read-only reference architectures

**Usage:**
Automatically prompted after `sdr-combined-analysis` completes. Can also run manually:
```
Run deep security scan on <app-name>
```

**What it does:**
1. Reads existing `security_review_<app>_<date>.txt` from combined analysis
2. Loads and executes three security skills:
   - `code-security`: Scans source files for vulnerabilities
   - `dependency-security`: Analyzes dependencies for CVEs
   - `infra-security`: Reviews IaC for misconfigurations
3. Appends three new sections to security review:
   - **Section 15: Code Vulnerabilities (Deep Scan)** - OWASP Top 10 with code excerpts
   - **Section 16: Dependency Vulnerabilities (CVE Details)** - Specific CVEs with remediation versions
   - **Section 17: Infrastructure Misconfigurations (IaC Deep Scan)** - CIS benchmark violations
4. Updates Executive Summary with new finding counts
5. Merges all findings into Consolidated Findings & Roadmap table

**Outputs:**
- Enhanced `security_review_<app>_<date>.txt` (sections 15-17 appended)
- Detailed findings with:
  - Unique IDs (CODE-xxx, DEP-xxx, INFRA-xxx)
  - Exact file:line references
  - Code excerpts showing vulnerable code
  - Risk explanations with business impact
  - Concrete fix recommendations with working code
  - Effort estimates for remediation

**Time:** +10-15 minutes (single folder), +15-20 minutes (multi-folder)

**Example findings added:**
```
CODE-001: SQL Injection in user_controller.py:142 [HIGH]
DEP-002: flask 1.1.2 → CVE-2023-30861 [CRITICAL] - Upgrade to 2.3.0
INFRA-003: IAM Wildcard Permissions in iam.tf:45 [CRITICAL]
```

**Integration:**
- Report generator automatically includes deep scan sections if present
- Knowledge base stores deep scan results for pattern analysis

---

### SDR Report Generator (`sdr-report-generator.SKILL.md`)

**Purpose:** Convert analysis files into Confluence-ready HTML (traditional workflow)

**Features:**
- JIRA integration for Request Overview
- Template-first Confluence-compatible HTML generation
- Single-component and multi-component reports use the same canonical shell
- Locked fuller Application Details table sourced from `reference/sdr-application-details-static.html`
- Locked reviewed areas matrix sourced from `reference/sdr-reviewed-areas-static.html`
- "Top Findings" section organized by security domain
- Reviewed Areas only fills `Current State` and `recommendations`
- Missing evidence is stated explicitly in generated cells
- Quality validation before output

**Requirements:**
- Architecture summary .txt file
- Security review .txt file
- Manual inputs (TRC, AO, dates, etc.)

**Current report shape:**
- Application Details uses the fuller SBLC-style field set
- Reviewed Areas uses the richer multi-row donor structure for both single and multi-component reviews
- Design review comments is the final section
- Appendix is not part of the current generated report contract

**Output:** `SDR_<app>_<date>.html`

**When to use:**
- Need offline HTML file
- Publishing to other spaces
- Want to email report as attachment
- Require custom template modifications

**Time:** ~8-12 minutes + manual upload/formatting (~10-20 min)

---

### SDR Knowledge Base (`sdr-knowledge-base.SKILL.md`)

**Purpose:** Organizational learning and pattern recognition

**Capabilities:**
- Store completed SDRs with metadata
- Query past reviews by technology, LoB, finding type
- Identify common patterns and trends
- Compare similar applications
- Generate insights from historical data

**Usage examples:**
```
Store the completed my-app SDR in the knowledge base
```

```
Query the knowledge base for all SDRs with MCP servers
```

```
Find common AI/ML security findings from past reviews
```

---

### Optional: Architecture Analysis (`architecture-analysis.SKILL.md`)

**Best for:** Architecture documentation only, or when reviewing architecture before security

**Use only when:** You specifically need architecture without security review (~5% of cases)

**Features:**
- Comprehensive tech stack identification
- Infrastructure and deployment analysis (GAIA/GAP/GKP/AWS)
- AI/ML elements detection (models, LLMs, MCP servers, agents)
- Generates Mermaid architecture diagrams
- Integration and dependency mapping

**Output:** `architecture_summary_<app>_<date>.txt`

---

### Optional: Security Review (`security-review-analysis.SKILL.md`)

**Best for:** Security assessment only, or follow-up after architecture review

**Use only when:** You specifically need security without architecture documentation (~5% of cases)

**Features:**
- Evidence-based findings with file paths and line numbers
- OWASP Top 10 coverage
- AI/LLM security assessment (prompt injection, data leakage)
- MCP server security analysis
- Supply chain vulnerability detection
- Severity ratings: CRITICAL / HIGH / MEDIUM / LOW / INFO

**Output:** `security_review_<app>_<date>.txt`

---

## Quality Features

The SDR system includes several quality improvements based on analysis of actual Confluence reports:

✅ **Standardized Structure** - Reports follow one canonical shell for both single and multi-component reviews
✅ **Static Templates** - Application Details and Reviewed Areas come from locked reference templates
✅ **Top Findings Section** - Dynamically organized by security domain (not severity)
✅ **Content Guidelines** - 8K-18K character target, 3-10 key findings
✅ **Evidence-Based** - All findings reference specific files and line numbers
✅ **Confluence-Ready** - HTML output matches Confluence formatting patterns

See [reference/REPORT_QUALITY_IMPROVEMENTS.md](reference/REPORT_QUALITY_IMPROVEMENTS.md) for detailed analysis.

---

## Best Practices

### Before Starting

1. **Add target repo to workspace** - For best results: `File → Add Folder to Workspace`
2. **Convert artifacts first** - If the repo has PDFs, DOCX, PPTX, MSG, TXT, or images in an `artifacts/` folder
3. **Gather metadata** - SEAL ID, JIRA story, application owner details
4. **Review documentation** - Collect design docs and architecture diagrams

### During Analysis

1. Run skills in recommended order
2. Review outputs for accuracy before proceeding to next step
3. Validate file references and line numbers in findings
4. Add context and clarifications as needed

### After Completion

1. **Human review is mandatory** - Always review generated reports
2. Validate findings with development team
3. Make necessary edits for accuracy
4. Store finalized SDRs in knowledge base for future reference

---

## Sharing with Your Team

### Git Repository (Recommended)

```bash
cd SDR
git init
git add .
git commit -m "SDR automation skills"
git remote add origin <your-internal-repo-url>
git push -u origin main
```

Team members clone the repository and skills become available immediately.

### Direct Folder Copy

1. Copy entire `SDR/` folder to shared location
2. Team members copy to their workspace
3. Open workspace and skills auto-load

### Team Setup

Each team member needs to install dependencies once:
```bash
pip install -r requirements.txt
```

Then they can immediately run SDR commands.

---

## Troubleshooting

### Skill Not Found
**Fix:** Ensure `.SKILL.md` files are in workspace and properly named with `.SKILL.md` extension

### Cannot Access Target Repository
**Fix:** Add target repo to workspace (`File → Add Folder to Workspace`) or use absolute path

### Incomplete Analysis
**Fix:** Provide more context or point to specific code areas that were missed

### Report Formatting Issues
**Fix:** Use Confluence HTML editor's source mode to paste and validate structure

### Artifact Conversion Fails
**Fix:**
- Verify: `python artifacts-to-text.py --check-deps`
- Reinstall: `pip install -r requirements.txt`
- Check file permissions

---

## Additional Resources

- [ARTIFACTS_CONVERTER_README.md](ARTIFACTS_CONVERTER_README.md) - Full artifact converter documentation
- [reference/REPORT_QUALITY_IMPROVEMENTS.md](reference/REPORT_QUALITY_IMPROVEMENTS.md) - Quality analysis and guidelines
- [reference/sdr-report-field-mappings.md](reference/sdr-report-field-mappings.md) - Data source mappings
- [reference/confluence-patterns.md](reference/confluence-patterns.md) - Confluence formatting examples
- [archive/](archive/) - Historical implementation documentation

---

## Maintenance & Updates

### Regular Maintenance
- **Quarterly:** Review and update skills based on new security patterns and feedback
- **Quarterly:** Update knowledge base insights
- **Semi-annually:** Validate indexes and clean up
- **Annually:** Archive old SDRs (>2 years)

### Customization

Skills can be customized by editing the `.SKILL.md` files:
- Modify output format and section ordering
- Add/remove analysis areas
- Adjust severity definitions
- Add new finding categories
- Integrate with internal tools and APIs

---
