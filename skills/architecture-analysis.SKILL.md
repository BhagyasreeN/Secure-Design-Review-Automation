---
description: Analyze code repository to produce a comprehensive Architecture Summary .txt file for JPMorganChase applications. Use this skill for Step 1 of a Secure Design Review.
---

# Architecture Analysis Skill

Analyze codebase and produce **Architecture Summary** `.txt` file. Run as **Step 1** before Security Review.

## When to Use
- Step 1 of SDR workflow
- Document architecture for senior architects
- Assess: Java/Python, microservices, AI/ML agents, MCP, AWS ECS/EKS/GAIA/GAP/GKP

## Inputs
- Complete code repository access
- Design docs and diagrams (if available)
- App name and metadata (SEAL ID, LoB)

### Artifact Coverage
See [common/artifact-handling.md](common/artifact-handling.md) for preflight procedures.

## File Scanning Strategy

See [common/file-scanning.md](common/file-scanning.md) for skip patterns, priority file list, and scanning order.

## Output
**File:** `architecture_summary_<app_name>_<YYYY-MM-DD>.txt`  
**Location:** Codebase root (NOT in SDR folder)

## Sections to Generate

1. **Header** - App name, date, repo, audience
2. **Application Overview** - Name, purpose, tech stack
3. **Architecture & Components**
   - Pattern, modules/services table, protocols, APIs, data layer
4. **Infrastructure & Deployment**
   - Platforms (GAIA/GAP/GKP/AWS), containers, CI/CD, observability
   - Resource table
5. **AI/ML Elements**
   - Models, LLMs, agents, MCP, RAG, MLOps
   - Element table: `Element | Type | Framework | Location | Notes`
6. **Integrations & Dependencies** - JPMC platforms, brokers, external feeds
   - Library table: `Library | Version | Purpose | License`
7. **Architecture Diagram** - Mermaid flowchart (wrap in `===== MERMAID DIAGRAM START/END =====`)
8. **Footer** - End marker, validation disclaimer

> **Boundary rule:** Security findings, control gaps, and architectural recommendations belong
> exclusively in the Security Review output file. Do not include a Security & Compliance section
> or an Observations & Recommendations table in the architecture summary.

## Analysis Rules

- Scan priority files per File Scanning Strategy above (skip node_modules, build outputs, etc.)
- Include evidence from non-code artifacts (`.pdf`, `.docx`, `.txt`, `.png`, `.jpeg`, `.jpg`) when readable
- Reference actual file names, classes, and configuration keys
- Use technical, precise tone for senior architects
- Use `[NOT IDENTIFIED IN CODEBASE]` for unavailable data — NEVER guess
- Format: Plain text, UTF-8, max 120 chars/line
- Use `=` for major separators, `-` for minor
- Tables: ASCII with `|` separators

## Progress Reporting (MANDATORY)

Emit a 📁 discovery report (total / skipped / to-analyze counts) at the start, then a 🔍 progress update every 20-25 files (file counts by category, running tally of components/services/integrations discovered, ETA). Keep each update under 10 lines.

## After Completion

Once this file is created, tell the user:
> "Architecture Summary complete. Run **Step 2** — Security Review — using `skills/security-review-analysis.SKILL.md` to continue the SDR."
Rules
- Scan: source, config, IaC, CI/CD, Dockerfiles, API specs, docs, artifacts
- Reference actual file names, classes, config keys
- Use `[NOT IDENTIFIED IN CODEBASE]` for unavailable data — NEVER guess
- Format: Plain text, UTF-8, max 120 chars/line, ASCII tables with `|`
- Technical tone for senior architects

## After Completion
Tell user: "Architecture Summary complete. Run **Step 2** — Security Review