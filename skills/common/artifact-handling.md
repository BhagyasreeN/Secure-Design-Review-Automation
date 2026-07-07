# Artifact Handling - Common SDR Procedures

## Artifact Coverage Requirement

- Treat repository + `artifacts/` directory as in-scope input
- Include: `.pdf`, `.docx`, `.pptx`, `.msg`, `.txt`, `.png`, `.jpeg`, `.jpg`
- **ALWAYS read architecture and design documents** (MANDATORY):
  - ARCHITECTURE.md, DESIGN.md, README.md
  - docs/architecture/**, docs/design/**
  - Architecture Decision Records (ADR)
  - System diagrams and design documents
  - Any files in artifacts_converted/ folder

## Preflight: Convert Mixed-Format Artifacts

**If `artifacts/` directory exists:**
```bash
python artifacts-to-text.py <repo_root>/artifacts --output <repo_root>/artifacts_converted
```

See [ARTIFACTS_CONVERTER_README.md](../../ARTIFACTS_CONVERTER_README.md) for setup.

This tool:
- Extracts text from PDFs, DOCX, PPTX, and MSG
- Passes through TXT artifacts unchanged
- OCRs all images (standalone + embedded)

**If conversion tool unavailable:**
1. Inventory all in-scope files
2. Attempt to read each file
3. Classify: `READABLE` | `PARTIALLY_READABLE` | `UNREADABLE`
4. If any `UNREADABLE` or `PARTIALLY_READABLE`: **STOP** and notify user
5. List affected files, types, and limitations
6. Continue only after user acknowledgment