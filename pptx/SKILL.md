---
name: pptx
description: Presentation creation, editing, and analysis. Use when working with PowerPoint presentations (.pptx files) for creating, modifying, or analyzing slides.
license: Apache-2.0
source: anthropics/skills
---

# PPTX Processing Guide

## Reading Content

```bash
# Extract text
python -m markitdown presentation.pptx
```

### Key File Structures
- `ppt/presentation.xml` - Main presentation metadata
- `ppt/slides/slide{N}.xml` - Individual slides
- `ppt/notesSlides/` - Speaker notes
- `ppt/media/` - Images and media

## Creating Presentations with Python

```python
from pptx import Presentation
from pptx.util import Inches, Pt

prs = Presentation()

# Add slide
slide_layout = prs.slide_layouts[1]  # Title and Content
slide = prs.slides.add_slide(slide_layout)

# Set title
title = slide.shapes.title
title.text = "My Presentation"

# Add content
body = slide.placeholders[1]
tf = body.text_frame
tf.text = "First bullet point"

p = tf.add_paragraph()
p.text = "Second bullet point"
p.level = 1

prs.save('output.pptx')
```

## Creating with PptxGenJS (Node.js)

```javascript
import pptxgen from 'pptxgenjs';

const pptx = new pptxgen();

const slide = pptx.addSlide();
slide.addText('Hello World', {
  x: 1,
  y: 1,
  w: '80%',
  fontSize: 36,
  bold: true,
});

slide.addImage({
  path: 'image.png',
  x: 1,
  y: 2,
  w: 4,
  h: 3,
});

await pptx.writeFile({ fileName: 'output.pptx' });
```

## Design Principles

- **Typography**: Use web-safe fonts (Arial, Georgia, Verdana)
- **Color**: Choose 3-5 colors that work together
- **Layout**: Use two-column layouts for charts/tables
- **Contrast**: Ensure text is readable on backgrounds

## Dependencies

```bash
pip install python-pptx markitdown
npm install pptxgenjs
```
