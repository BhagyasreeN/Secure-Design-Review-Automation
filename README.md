# Secure-Design-Review-Automation

Automated security design review tool powered by Copilot Skills to identify vulnerabilities and validate secure architecture patterns. This system streamlines the security review process by combining codebase analysis, vulnerability scanning, and automated report generation into a single unified workflow.

## Features

- **Automated Security Vulnerability Detection** – CVE identification, OWASP Top 10 detection, hardcoded secrets scanning
- **Architecture Analysis** – Design pattern validation and architectural consistency review
- **Artifact Processing** – Automatic text extraction from PDFs, DOCX, PPTX, MSG files and images
- **Combined Analysis Mode** – 50% faster single-pass codebase scanning for both architecture and security
- **Deep Security Scanning** – Optional comprehensive vulnerability assessment with remediation guidance
- **Report Generation** – Confluence-ready HTML reports or knowledge base storage
- **Infrastructure Analysis** – IAM misconfiguration detection, supply chain risk assessment

## Getting Started

### Prerequisites

- Python 3.7+
- pip (Python package manager)
- Target codebase available locally or via workspace

### Installation

1. **Clone or add repository to workspace:**
   ```bash
   git clone https://github.com/BhagyasreeN/Secure-Design-Review-Automation.git
   cd Secure-Design-Review-Automation
   ```

2. **Install Dependencies (One-Time):**
   ```bash
   pip install -r requirements.txt
   ```

3. **Verify Installation:**
   ```bash
   python artifacts-to-text.py --check-deps
   ```

### Quick Start

#### Recommended Workflow (5-10 minutes)

**Step 1: Convert Artifacts (If Needed)**
```bash
# If your repo has an artifacts/ folder with PDFs, DOCX, PPTX, MSG, or images:
# Run the artifacts conversion skill to extract text
```
Skip this step if your codebase is already in text format.

**Step 2: Run Combined Analysis**
```
Run combined SDR analysis on [path-to-your-app]
```

This produces both files in a single scan:
- `architecture_summary__.txt`
- `security_review__.txt`

**Step 3: Generate Report (Optional)**
```
Generate the SDR report for [path-to-your-app]
```

Output: `SDR__.html` (ready for review or manual Confluence upload)

**Step 4: Store in Knowledge Base (Optional)**
```
Store the completed SDR in the knowledge base
```

Archives SDR for future queries and pattern analysis.

## How It Works

The SDR automation system includes six core Copilot Skills:

| Skill | Purpose | Output |
|-------|---------|--------|
| **Artifacts Conversion** | Extract text from PDFs, DOCX, PPTX, MSG, images | `artifacts_converted/` folder |
| **Combined SDR Analysis** | Fast single-pass codebase analysis (RECOMMENDED) | Both `.txt` files |
| **Deep Security Scan** | Optional detailed vulnerability scanning | Enhanced `security_review__.txt` |
| **SDR Report Generator** | Create Confluence-ready HTML from `.txt` files | `SDR__.html` |
| **SDR Knowledge Base** | Store and query past SDRs | Organized knowledge base |
| **Artifacts to Text** | Python utility for binary file conversion | Extracted text files |

### Analysis Capabilities

**Combined Analysis includes:**
- Architectural pattern validation
- Security vulnerability detection
- Code quality assessment
- Dependency analysis

**Deep Security Scan adds (+10-15 min):**
- Specific CVE identification with remediation versions
- OWASP Top 10 vulnerability detection (injection, XSS, deserialization)
- Infrastructure misconfiguration analysis (IAM wildcards, exposed resources)
- Supply chain risk assessment (outdated packages, license compliance)
- Hardcoded secrets detection (API keys, credentials)

## Project Structure

```
SDR/
├── .github/
│   └── copilot-instructions.md          # Workspace-level Copilot configuration
├── skills/                              # Copilot skills (main automation)
│   ├── artifacts-conversion.SKILL.md
│   ├── sdr-combined-analysis.SKILL.md
│   ├── sdr-report-generator.SKILL.md
│   ├── sdr-confluence-publisher.SKILL.md
│   └── sdr-knowledge-base.SKILL.md
├── common/
│   ├── artifact-handling.md
│   └── optional/                        # Advanced/standalone skills
│       ├── architecture-analysis.SKILL.md
│       └── security-review-analysis.SKILL.md
├── reference/                           # Supporting documentation
│   ├── confluence-patterns.md
│   ├── sdr-canonical-template.html
│   ├── sdr-report-field-mappings.md
│   └── REPORT_QUALITY_IMPROVEMENTS.md
├── archive/
│   ├── knowledge-base/                  # Historical SDRs and insights
│   └── indexes/                         # Quick lookup indexes
├── examples/
│   ├── sample-repos/                    # Test codebases
│   └── manual-outputs/                  # Example SDR outputs
├── artifacts-to-text.py                 # Artifact converter script
├── requirements.txt                     # Python dependencies
└── README.md                            # This file
```

## Usage Examples

### Basic Analysis
```
Run combined SDR analysis on ./my-application
```

### With Deep Security Scan
```
Run combined SDR analysis on ./my-application
# When prompted: Select "Yes" for deep security scan
```

### Generate HTML Report
```
Generate the SDR report for ./my-application
# Provide metadata when prompted:
# - SEAL ID
# - JIRA story
# - Application owner
# Output: SDR_[timestamp].html
```

### Archive for Future Reference
```
Store the completed SDR in the knowledge base
```

## Best Practices

### Before Starting
- Add target repo to workspace for optimal results
- Convert artifacts first if repo has binary files
- Gather metadata (SEAL IDs, JIRA story, application owner details)
- Review any existing architecture documentation

### During Analysis
- Run skills in recommended order
- Review outputs for accuracy before proceeding
- Validate file references and line numbers in findings
- Add context and clarifications as needed

### After Completion
- **Human review is mandatory** – Always review generated reports
- Validate findings with development team
- Make necessary edits for accuracy
- Store finalized SDRs in knowledge base for future reference

## Troubleshooting

| Issue | Solution |
|-------|----------|
| SKILL Not Found | Ensure `.SKILL.md` files are in workspace and properly named |
| Cannot Access Target Repository | Add target repo to workspace (File → Add Folder to Workspace) |
| Incomplete Analysis | Provide more context or point to specific code areas missed |
| Report Formatting Issues | Use Confluence HTML editor's source mode to paste structure |
| Artifact Conversion Fails | Run `python artifacts-to-text.py --check-deps` and reinstall requirements |

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

## Sharing with Your Team

### Via Git Repository
```bash
cd Secure-Design-Review-Automation
git init
git add .
git commit -m "SDR automation skills"
git remote add origin [your-repo-url]
git push -u origin main
```

### Team Setup
Each team member needs to install dependencies once:
```bash
pip install -r requirements.txt
```

## Maintenance & Updates

- **Quarterly:** Review and update skills based on new security patterns and feedback
- **Quarterly:** Update knowledge base insights
- **Semi-annually:** Validate indexes and clean up
- **Annually:** Archive old SDRs (>2 years)

## License

[Add your license information here]

## Support & Contact

For issues, questions, or feedback:
- Open an issue on GitHub
- Review the [troubleshooting section](#troubleshooting)
- Check existing documentation in the `reference/` directory

---

**Quick Links:**
- [Copilot Instructions](.github/copilot-instructions.md)
- [Report Field Mappings](reference/sdr-report-field-mappings.md)
- [Quality Guidelines](reference/REPORT_QUALITY_IMPROVEMENTS.md)
