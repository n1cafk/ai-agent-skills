---
name: docx
description: Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. Use when working with Word documents (.docx files).
license: Apache-2.0
source: anthropics/skills
---

# DOCX Processing Guide

## Reading Content

### Text Extraction with Pandoc
```bash
# Convert to markdown with tracked changes
pandoc --track-changes=all document.docx -o output.md
```

### Raw XML Access
```bash
# Unpack .docx file (it's a ZIP archive)
unzip document.docx -d unpacked/
```

Key files:
- `word/document.xml` - Main document contents
- `word/comments.xml` - Comments
- `word/media/` - Embedded images

## Creating Documents with docx-js

```typescript
import { Document, Paragraph, TextRun, Packer } from 'docx';
import * as fs from 'fs';

const doc = new Document({
  sections: [{
    children: [
      new Paragraph({
        children: [
          new TextRun({ text: "Hello World", bold: true }),
          new TextRun({ text: " - Normal text" }),
        ],
      }),
    ],
  }],
});

const buffer = await Packer.toBuffer(doc);
fs.writeFileSync("output.docx", buffer);
```

## Editing with Python

```python
from docx import Document

doc = Document('input.docx')

# Access paragraphs
for para in doc.paragraphs:
    print(para.text)

# Modify content
doc.paragraphs[0].text = "New text"

# Add paragraph
doc.add_paragraph("New paragraph")

doc.save('output.docx')
```

## Tracked Changes

Tracked changes use these XML elements:
- `<w:ins>` - Insertions
- `<w:del>` - Deletions

## Dependencies

```bash
pip install python-docx
npm install docx
```
