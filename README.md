QUICK START
Install Dependencies (One-Time)
pip install -r requirements.txt

Verify Installation:
python artifacts-to-text.py --check-deps

Convert Artifacts (If Needed)
If your codebase has an artifacts/ folder with PDFs, DOCX, PPTX, MSG, or images:

Convert artifacts for

This runs the artifacts-conversion skill to extract text from PDFs, DOCX, PPTX, MSG, TXT, and OCR images. Output goes to /artifacts_converted/.

Skip this step if: No artifacts/ folder or all docs already in text format.

Run SDR Analysis

Option A – Combined Analysis (Recommended – 50% Faster):
Run combined SDR analysis on

Produces both files in a single scan:

architecture_summary__.txt

security_review__.txt

After analysis, you'll be prompted:

Run optional deep security scan? (recommended for production deployments)

Yes -> Enhances security review with detailed CVE scanning, OWASP Top 10 detection, and IaC misconfigurations (+10-15 min)

No -> Skip to next step with high-level findings

What would you like to do next?

Generate HTML report

Store in knowledge base

Nothing – just analysis files

Option B – Standalone Analysis (Advanced – Only if you need just one):
Run architecture analysis on 
Or:
Run security review on

Standalone skills are in skills/ – use only when you specifically need architecture OR security, not both.

Publish or Generate Report

Option A – HTML Report (Traditional – For Offline Review):
Generate the SDR report for

You'll be prompted for metadata (SEAL IDs, JIRA story, etc.)

Output: SDR__.html (ready for manual Confluence upload)

Archive (Optional)
Store the completed  SDR in the knowledge base

Archives SDR for future queries and pattern analysis.

OVERVIEW
The SDR automation system includes six core Copilot skills:

Skill                 | Purpose                                                    | Output
Artifacts Conversion  | Extract text from PDFs, DOCX, PPTX, MSG, TXT, and images   | artifacts_converted/ folder
Combined SDR Analysis | Fast single-pass codebase analysis (RECOMMENDED)           | Both .txt files
Deep Security Scan    | Optional Detailed vulnerability scanning                   | Enhanced security_review
SDR Report Generator  | Create Confluence HTML from .txt files                     | SDR__.html
SDR Knowledge Base    | Store and query past SDRs                                  | Organized knowledge base

Optional Skills (in skills/):

Architecture Analysis (architecture only, no security)

Security Review (security only, no architecture)

Use optional skills only when you specifically need one analysis without the other (<5% of use cases).

Workflow Recommendations:

Traditional: Combined Analysis + Report Generator + Manual upload (25-35 minutes total)

Safest: Combined Analysis + Confluence Publisher with draft review (includes validation + rollback)

Deep Security Scan adds:

Specific CVE identification with remediation versions

OWASP Top 10 vulnerability detection (injection, XSS, deserialization)

Infrastructure misconfiguration analysis (IAM wildcards, exposed resources)

Supply chain risk assessment (outdated packages, license compliance)

Hardcoded secrets detection (API keys, credentials)

+10-15 minutes scan time but comprehensive vulnerability coverage

REPOSITORY STRUCTURE
SDR/
|-- .github/
|   |-- copilot-instructions.md      # Workspace-level Copilot configuration
|-- skills/                          # Copilot skills (main automation)
|   |-- artifacts-conversion.SKILL.md # Convert artifacts to text
|   |-- sdr-combined-analysis.SKILL.md# Combined analysis (recommended)
|   |-- sdr-confluence-publisher.SKILL.md # Direct Confluence publish
|   |-- sdr-report-generator.SKILL.md # HTML report generation (traditional)
|   |-- sdr-knowledge-base.SKILL.md  # Knowledge base management
|-- common/
|   |-- artifact-handling.md         # Shared artifact handling procedures
|   |-- optional/                    # Advanced/standalone skills
|       |-- README.md                # When to use optional skills
|       |-- architecture-analysis.SKILL.md
|       |-- security-review-analysis.SKILL.md
|-- reference/                       # Supporting documentation
|   |-- confluence-patterns.md       # Reference examples from actual reports
|   |-- sdr-canonical-template.html  # Canonical report shell used for all reports
|   |-- sdr-application-details-static.html # Locked architecture Application Details table
|   |-- sdr-reviewed-areas-static.html # Locked donor-aligned Reviewed Areas matrix
|   |-- sdr-report-field-mappings.md # Data source mappings
|   |-- REPORT_QUALITY_IMPROVEMENTS.md # Quality analysis and guidelines
|-- archive/                         # Historical reports and examples
|   |-- knowledge-base/              # SDR knowledge base (grows over time)
|   |   |-- completed/               # Finalized SDRs organized by date
|   |   |-- insights/                # Extracted learnings and patterns
|   |-- indexes/                     # Quick lookup indexes
|-- examples/                        # Sample repos and test outputs
|   |-- sample-repos/                # Test codebases
|   |-- manual-outputs/              # Example SDR outputs
|-- artifacts-to-text.py             # Artifact converter script
|-- requirements.txt                 # Python dependencies
|-- README.txt                       # This file

DETAILED WORKFLOWS
Recommended Workflow (Fastest – 5-10 minutes)

Step 0: Convert Artifacts (If Needed)
Convert artifacts for C:\path\to\your-app
(Only if artifacts/ folder exists with PDFs, DOCX, PPTX, MSG, TXT, or images.)

Step 1: Run Combined Analysis
Run combined SDR analysis on C:\path\to\your-app

What it does:

Scans codebase once

Extracts both architecture and security data

Generates both output files

Prompts: "What would you like to do next?"

Outputs:

architecture_summary__.txt

security_review__.txt

Advantages:

50% faster than separate steps

~40% less token usage

No redundant file scanning

Single pass ensures consistency

Traditional Workflow (For Offline Review – 25-35 minutes)

Step 0: Convert Artifacts (If Needed)
Same as above

Step 1: Run Combined Analysis
Same as above

Step 2: Select "Generate HTML report"
Agent launches report generator:
Generate the SDR report for 
Output: SDR__.html

Step 3: Manual Review & Upload

Open the HTML in a browser

Review for accuracy

Make any necessary edits

Upload to Confluence manually

Fix any formatting issues

SKILL DETAILS
Artifacts Conversion (artifacts-conversion.SKILL.md)
Best for: Converting binary artifacts before SDR analysis

Combined SDR Analysis (sdr-combined-analysis.SKILL.md)
Best for: Complete SDR workflow – fastest option

Deep Security Scan (sdr-deep-security-scan.SKILL.md) - OPTIONAL
Best for: Comprehensive vulnerability assessment for production deployments

SDR Report Generator (sdr-report-generator.SKILL.md)
Purpose: Convert analysis files into Confluence-ready HTML (traditional workflow)

SDR Knowledge Base (sdr-knowledge-base.SKILL.md)
Purpose: Organizational learning and pattern recognition

BEST PRACTICES
Before Starting

"Add target repo to workspace" – For best results: File -> Add Folder to Workspace

"Convert artifacts first" – If the repo has binary files in an artifacts/ folder

"Gather metadata" – SEAL ID, JIRA story, application owner details

"Review documentation" – Collect design docs and architecture diagrams

During Analysis

Run skills in recommended order

Review outputs for accuracy before proceeding to next step

Validate file references and line numbers in findings

Add context and clarifications as needed

After Completion

"Human review is mandatory" – Always review generated reports

Validate findings with development team

Make necessary edits for accuracy

Store finalized SDRs in knowledge base for future reference

SHARING WITH YOUR TEAM
Git Repository (Recommended)
cd SDR
git init
git add .
git commit -m "SDR automation skills"
git remote add origin 
git push -u origin main

Team Setup
Each team member needs to install dependencies once:
pip install -r requirements.txt

TROUBLESHOOTING
SKILL Not Found: Ensure .SKILL.md files are in workspace and properly named.

Cannot Access Target Repository: Add target repo to workspace (File -> Add Folder to Workspace).

Incomplete Analysis: Provide more context or point to specific code areas missed.

Report Formatting Issues: Use Confluence HTML editor's source mode to paste structure.

Artifact Conversion Fails: Run python artifacts-to-text.py --check-deps and reinstall requirements.

MAINTENANCE & UPDATES
Quarterly: Review and update skills based on new security patterns and feedback

Quarterly: Update knowledge base insights

Semi-annually: Validate indexes and clean up

Annually: Archive old SDRs (>2 years)

Last Updated: June 4, 2026
Maintained By: Bhagyasree Nimm
