---
description: Convert PDF, DOCX, PPTX, MSG, PNG, and JPEG artifacts to plain text for SDR analysis. Supports single or multiple folders. Run this BEFORE running sdr-combined-analysis if the codebase(s) have artifacts/ folders.
---

# Artifacts Conversion Skill

Convert mixed-format artifacts (PDF, DOCX, PPTX, MSG, PNG, JPEG) to plain text before running SDR analysis.

Supports **single folder** or **multiple folders** with component labels.

## When to Use

**Run this FIRST if:**
- The codebase has an `artifacts/` folder with non-text files
- Multiple codebases have `artifacts/` folders (multi-folder analysis)
- You see PDFs, DOCX, PPTX, MSG, PNG, or JPEG files in the codebase(s)
- Architecture/design documentation is in binary formats

**Skip this if:**
- No `artifacts/` folder exists in any codebase
- All documentation is already in `.txt` or `.md` format
- You've already run conversion and files haven't changed

## Input Formats

### Single Folder (Traditional)
```
"Convert artifacts for <codebase-path>"
```

### Multiple Folders (Multi-Component System)
```
"Convert artifacts for:
- <component-label>: <path-1>
- <component-label>: <path-2>
- <component-label>: <path-3>"
```

**Example:**
```
"Convert artifacts for:
- agent: C:\repos\smart-query-agent
- microservice: C:\repos\document-processor
- terraform: C:\repos\infrastructure-tf"
```

## What It Does

1. **Detects** artifacts folder(s) in target codebase(s)
2. **Validates** Python dependencies are installed
3. **Runs** conversion script to extract text from documents, presentations, emails, and OCR images (per folder)
4. **Confirms** successful conversion (per folder)
5. **Reports** aggregate results across all folders

## ⚠️ Important Limitations

**Diagram Understanding:**
- **OCR extracts text labels only** - Cannot understand diagram structure, relationships, or flow
- **No visual comprehension** - Boxes, arrows, connections, and layout are lost
- **Architecture diagrams** - Only text inside shapes is extracted, not the architecture itself
- **Flowcharts** - Flow direction and relationships are NOT preserved

**What IS captured:** Text labels, component names, annotations in diagrams
**What is LOST:** Visual structure, relationships, data flow, component connections

**Recommendation for critical diagrams:**
- Provide verbal description of architecture
- Use diagram-as-code formats (Mermaid, PlantUML, Draw.io XML)
- Or attach images directly in Copilot chat and ask for description (uses vision model)

## Workflow

### Step 1: Check for Artifacts

#### Single Folder
When user requests SDR analysis, first check:
```
Does <codebase-path>/artifacts/ exist?
```

**If NO:** Skip this skill, proceed to sdr-combined-analysis  
**If YES:** Continue to Step 2

#### Multiple Folders
When user requests multi-folder SDR analysis, check each folder:
```
For each component:
  Does <component-path>/artifacts/ exist?
```

**Results tracking:**
```
Components with artifacts:
- agent: YES (C:\repos\smart-query-agent\artifacts) → 8 files
- microservice: NO (no artifacts folder)
- terraform: YES (C:\repos\infrastructure-tf\artifacts) → 3 files

Total: 2 components need conversion
```

**If NO components have artifacts:** Skip this skill, proceed to sdr-combined-analysis  
**If ANY component has artifacts:** Continue to Step 2

### Step 2: Check Dependencies

Run dependency check from SDR workspace:
```bash
python artifacts-to-text.py --check-deps
```

**Expected Output:**
```
✓ pdfplumber
✓ pdf2image
✓ python-docx
✓ python-pptx
✓ extract-msg
✓ easyocr
✓ pillow
✓ All dependencies available
```

**If dependencies missing:**
```bash
cd <sdr-workspace>
pip install -r requirements.txt
```

**Note:** `<sdr-workspace>` is the path where you cloned the SDR repository (e.g., `C:\projects\SDR` on Windows or `/home/user/SDR` on Linux).

### Step 3: Run Conversion

#### Single Folder Conversion

Execute conversion from SDR workspace:
```bash
python artifacts-to-text.py <codebase-path>/artifacts --output <codebase-path>/artifacts_converted
```

**Example:**
```bash
python artifacts-to-text.py C:\repos\my-app\artifacts --output C:\repos\my-app\artifacts_converted
```

#### Multi-Folder Conversion

Run conversion **for each component with artifacts** (sequentially):

```bash
# Component 1
python artifacts-to-text.py <component-1-path>/artifacts --output <component-1-path>/artifacts_converted

# Component 2 (if has artifacts)
python artifacts-to-text.py <component-2-path>/artifacts --output <component-2-path>/artifacts_converted

# Component 3 (if has artifacts)
python artifacts-to-text.py <component-3-path>/artifacts --output <component-3-path>/artifacts_converted
```

**Example:**
```bash
# agent component
python artifacts-to-text.py C:\repos\smart-query-agent\artifacts --output C:\repos\smart-query-agent\artifacts_converted

# terraform component (microservice has no artifacts, skip)
python artifacts-to-text.py C:\repos\infrastructure-tf\artifacts --output C:\repos\infrastructure-tf\artifacts_converted
```

**Progress reporting during multi-folder conversion:**
```
🔄 Converting artifacts for multiple components...

Component: agent
   └─ Converting 8 files from C:\repos\smart-query-agent\artifacts...
   ✓ Completed: 8/8 files (2 PDFs, 2 DOCX, 1 PPTX, 3 images)

Component: microservice
   └─ No artifacts folder, skipping

Component: terraform  
   └─ Converting 3 files from C:\repos\infrastructure-tf\artifacts...
   ✓ Completed: 3/3 files (1 PDF, 2 images)

📊 Total: 11 files converted across 2 components
```

### Step 4: Validate Output

#### Single Folder Validation

Check for successful conversion:
```
1. Verify artifacts_converted/ folder was created
2. Check for CONVERSION_MANIFEST.json
3. Review manifest for:
   - Files successfully converted
   - Any conversion errors
   - Coverage statistics
```

**Expected Structure:**
```
artifacts_converted/
├── design-doc_extracted.txt
├── architecture_extracted.txt
├── deck_extracted.txt
├── meeting-summary_extracted.txt
├── diagram1_extracted.txt
├── diagram2_extracted.txt
└── CONVERSION_MANIFEST.json
```

#### Multi-Folder Validation

Check each component's conversion output:
```
For each component with artifacts:
  1. Verify <component-path>/artifacts_converted/ folder created
  2. Check for CONVERSION_MANIFEST.json
  3. Aggregate success/error counts
```

**Expected Structure (Multi-Folder):**
```
C:\repos\smart-query-agent\
├── artifacts/
│   ├── design.pdf
│   ├── diagram1.png
│   └── ...
└── artifacts_converted/
    ├── design_extracted.txt
    ├── diagram1_extracted.txt
    └── CONVERSION_MANIFEST.json

C:\repos\infrastructure-tf\
├── artifacts/
│   └── network-diagram.pdf
└── artifacts_converted/
    ├── network-diagram_extracted.txt
    └── CONVERSION_MANIFEST.json
```

### Step 5: Report Results

#### Single Folder Report

Inform user:
```
✅ Converted N files from artifacts/
   - M PDFs extracted
   - X DOCX files extracted
   - Y PPTX files extracted
   - Z MSG files extracted
   - W images OCR'd
   
Output: <codebase-path>/artifacts_converted/

Ready to proceed with SDR analysis.
```

**If errors occurred:**
```
⚠️ Conversion completed with warnings:
   - <file1>: <error details>
   - <file2>: <error details>
   
Readable files: N
Unreadable files: M

Proceed with analysis? [Y/N]
```

#### Multi-Folder Report

Inform user with aggregate results:
```
✅ Multi-Component Artifact Conversion Complete!

📊 Conversion Summary:

Component: agent (C:\repos\smart-query-agent)
   ✓ Converted 8 files
   ├─ 2 PDFs extracted
   ├─ 2 DOCX files extracted
   ├─ 1 PPTX file extracted
   └─ 3 images OCR'd
   Output: artifacts_converted/

Component: microservice
   - No artifacts folder (skipped)

Component: terraform (C:\repos\infrastructure-tf)
   ✓ Converted 3 files
   ├─ 1 PDF extracted
   ├─ 1 MSG file extracted
   └─ 2 images OCR'd
   Output: artifacts_converted/

📈 Totals:
   ├─ Components processed: 2/3
   ├─ Files converted: 11
   ├─ PDFs: 3
   ├─ DOCX: 2
   ├─ PPTX: 1
   ├─ MSG: 1
   └─ Images: 5

✅ Ready to proceed with multi-folder SDR analysis.
```

**If errors occurred in any component:**
```
⚠️ Multi-component conversion completed with warnings:

Component: agent
   ✓ 7/8 files successful
   ✗ diagram3.png: OCR failed (no text detected)

Component: terraform
   ✓ 3/3 files successful

📊 Overall:
   Readable files: 10
   Unreadable files: 1
   
Proceed with analysis using available data? [Y/N]
```

## Conversion Details

### Supported Formats

| Format | Tool | What's Extracted |
|--------|------|------------------|
| **PDF** | pdfplumber | Text, tables, metadata |
| **DOCX** | python-docx | Text, paragraphs, tables |
| **PPTX** | python-pptx | Slide text, tables, speaker notes |
| **MSG** | extract-msg | Email headers, body, attachment names |
| **PNG/JPEG** | EasyOCR | Text via OCR (English) |
| **TXT** | Direct copy | Passthrough |

### Output Format

Each input file produces:
- `<filename>_extracted.txt` - All extracted text in UTF-8

Plus:
- `CONVERSION_MANIFEST.json` - Conversion summary

### Handling Errors

**Common Issues:**

**1. Missing Dependencies**
```
ERROR: No module named 'pdfplumber'
```
**Fix:** `pip install -r requirements.txt`

**2. Corrupted Files**
```
ERROR: Cannot read <file>: Invalid format
```
**Action:** Note in manifest, skip file, continue with others

**3. Unsupported Outlook/PowerPoint Variant**
```
ERROR: Cannot read <file>: Unsupported MSG/PPTX variant
```
**Action:** Note in manifest, skip file, continue with others

**4. Permission Errors**
```
ERROR: Access denied to <file>
```
**Action:** Check file permissions, retry with elevated access

**5. OCR Failures**
```
WARNING: OCR failed for <image>: No text detected
```
**Action:** Note in manifest, image may be diagram/screenshot without text

## Integration with Analysis Skills

### Single Folder Integration

After successful conversion, the sdr-combined-analysis skill will:
1. Check for `artifacts_converted/` folder
2. Include converted files in analysis
3. Reference both original artifacts/ and artifacts_converted/ in findings

### Multi-Folder Integration

After successful multi-folder conversion, the sdr-combined-analysis skill will:
1. Check for `artifacts_converted/` folder in EACH component path
2. Include all converted files from all components in analysis
3. Tag converted artifacts by component (e.g., "agent/artifacts_converted/design.txt")
4. Reference both original artifacts/ and artifacts_converted/ in findings with component labels

## Best Practices

1. **Run Once Per Codebase** - Conversion output is cached, don't re-run unless files change
2. **Review Manifest** - Check CONVERSION_MANIFEST.json for coverage before analysis
3. **Handle Partial Success** - Even if some files fail, proceed with available data
4. **Large Files Warning** - PDFs >50MB may take several minutes to process
5. **Image Quality** - Better quality images = better OCR results

## Example Invocations

### Basic Conversion
```
User: "Run SDR analysis on c:\repos\my-app"

Agent Response:
"I'll check for artifacts that need conversion first.

Checking c:\repos\my-app\artifacts... Found 8 files (3 PDFs, 2 DOCX, 3 PNGs).

Running conversion..."

[Executes conversion command]

"✅ Conversion complete. Converted 8 files to c:\repos\my-app\artifacts_converted\
   
Ready to proceed with combined SDR analysis."
```

### With Dependency Issues
```
User: "Convert artifacts for c:\repos\my-app"

Agent Response:
"Checking dependencies...
⚠️ Missing: pdfplumber, python-pptx, extract-msg, easyocr

Installing dependencies: pip install -r requirements.txt
[Runs installation]

Dependencies installed. Running conversion..."
```

### Partial Success
```
Agent Response:
"✅ Conversion completed with warnings:
   - design.pdf: Extracted successfully (12 pages)
   - architecture.docx: Extracted successfully
   - operating-model.pptx: Extracted successfully (14 slides)
   - approval-thread.msg: Extracted successfully
   - diagram1.png: OCR successful (1,240 chars)
   - diagram2.png: OCR successful (856 chars)
   - encrypted.pdf: ❌ FAILED (Password protected)
   
Readable: 6/7 files (86% coverage)

Proceed with SDR analysis using available files? [Y/N]"
```

---

## Integration Notes

This skill is referenced by:
- `sdr-combined-analysis.SKILL.md` - Checks for artifacts before analysis
- Main README - Step 2 in Quick Start workflow
- `.github/copilot-instructions.md` - Artifact handling policy

## Maintenance

- **Dependencies listed in:** `/requirements.txt`
- **Conversion script:** `/artifacts-to-text.py`
- **Full documentation:** `/ARTIFACTS_CONVERTER_README.md`