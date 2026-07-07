# Artifact-to-Text Converter

Converts mixed-format artifacts (PDF, DOCX, PNG, JPEG, TXT) into readable plain-text files for SDR analysis.

## What It Does

- **PDF extraction**: Extracts text from PDFs via `pdfplumber`
- **DOCX extraction**: Extracts text, tables, and paragraphs from Word documents
- **Image OCR**: Converts PNG/JPEG images (standalone or embedded in documents) to text via EasyOCR
- **TXT passthrough**: Copies plain-text files as-is
- **Manifest generation**: Creates a JSON summary of what was converted and any errors

## Setup

### 1. Install Dependencies

```bash
pip install pdfplumber pdf2image python-docx pillow easyocr
```

### 2. Verify Installation

```bash
python artifacts-to-text.py --check-deps
```

Expected output:
```
✓ pdfplumber
✓ pdf2image
✓ python-docx
✓ easyocr
✓ pillow
✓ All dependencies available
```

## Usage

### Basic Usage

```bash
python artifacts-to-text.py <artifacts_folder> --output <output_folder>
```

### Example

```bash
# Convert all artifacts in ./artifacts/ to ./artifacts_converted/
python artifacts-to-text.py ./artifacts --output ./artifacts_converted

# Or use default output folder (artifacts_converted)
python artifacts-to-text.py ./artifacts
```

### Flags

- `--check-deps`: Verify all dependencies are installed
- `--quiet`: Suppress verbose output
- `--output <folder>`: Specify output directory (default: `artifacts_converted`)

## Output

For each input file, the script creates:
- `<filename>_extracted.txt` — All extracted/OCR'd text in UTF-8 format

Plus:
- `CONVERSION_MANIFEST.json` — Summary of conversion status, coverage, and errors

### Example Output Structure

```
artifacts_converted/
├── design-doc_extracted.txt       (from design-doc.pdf)
├── architecture_extracted.txt     (from architecture.docx)
├── diagram1_extracted.txt         (from diagram1.png, OCR'd)
├── CONVERSION_MANIFEST.json       (conversion summary)
```

## Integration with SDR Skills

Use this script as a **preflight step** before running architecture or security analysis:

### Manual workflow:
```bash
# 1. Convert artifacts to text
python artifacts-to-text.py ./my-repo/artifacts --output ./my-repo/artifacts_converted

# 2. Run architecture analysis
# The skill will now have access to all converted .txt files

# 3. Run security review
```

### Automated in Skill (TBD)
Skills can invoke this script during their preflight phase to ensure all artifacts are readable before proceeding.

## Supported File Types

| Format | Support | Tool |
|--------|---------|------|
| `.pdf` | ✓ Full | pdfplumber + EasyOCR for images |
| `.docx` | ✓ Full | python-docx + EasyOCR for embedded images |
| `.txt` | ✓ Full | Direct read |
| `.png` | ✓ Full | EasyOCR |
| `.jpeg` / `.jpg` | ✓ Full | EasyOCR |

## Limitations & Notes

- **Scanned PDFs**: Require OCR; may produce lower-quality text than native PDFs
- **Complex DOCX layouts**: Tables and nested elements work; formatting is simplified to plain text
- **Images**: OCR quality depends on image resolution and language (currently English only)
- **Large files**: PDF text extraction from very large files (~1000+ pages) may take time
- **OCR performance**: First run downloads language model (~100MB); subsequent runs are faster

## Troubleshooting

### Missing Dependencies
```
ERROR: ModuleNotFoundError: No module named 'pdfplumber'
```
**Solution**: Run `pip install pdfplumber pdf2image python-docx pillow easyocr`

### OCR Not Working
```
WARNING: EasyOCR not installed. Image OCR will be skipped.
```
**Solution**: Run `pip install easyocr` (this is the largest download ~100MB due to language model)

### Encoding Errors
If you see Unicode errors, ensure your terminal supports UTF-8:
```bash
export PYTHONIOENCODING=utf-8
python artifacts-to-text.py ./artifacts
```

## Output Example

For a PDF with mixed text and images, output might look like:

```
--- PAGE 1 ---
Architecture Overview

This document describes the system architecture...

--- PAGE 2 ---
[IMAGE FOUND ON PAGE 2 - See converted images]

[TABLE]
Component | Purpose | Language
backend | API server | Python
frontend | Web UI | React
database | Data storage | PostgreSQL
[END TABLE]
```

And a corresponding PNG image would be converted to a file like `diagram_extracted.txt` with OCR'd text from that image.

## Questions?

- Check `CONVERSION_MANIFEST.json` for detailed per-file status
- Run with `--quiet` flag if you want to suppress verbose output
- Errors are collected and reported in the manifest
