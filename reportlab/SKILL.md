---
name: reportlab
description: Comprehensive expert skill for ReportLab PDF library. Covers low-level pdfgen (canvas, shapes, text), fonts (TrueType, CID, Type 1), Platypus engine (templates, frames, flowables), rich Paragraphs (XML markup), advanced Tables, Charts, Barcodes, and logic-driven document generation (TOC, Index, DocAssign).
---

# ReportLab PDF Library Skill

Expert guidance for generating PDFs using Python's ReportLab library. This skill covers everything from pixel-perfect canvas drawing to high-level automated document layout.

## 1. Low-Level Graphics (`pdfgen`)
The `canvas` is the base for all PDF generation. Coordinate system: `(0,0)` is bottom-left, X goes right, Y goes up.

### Basic Canvas setup
```python
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import letter, A4

c = canvas.Canvas("hello.pdf", pagesize=letter)
width, height = letter # 612, 792 points

c.drawString(100, 750, "Hello World")
c.showPage() # Saves current page, moves to next
c.save() # Finalizes and saves the file
```

### Shapes & Lines
```python
c.line(x1, y1, x2, y2)
c.rect(x, y, width, height, stroke=1, fill=0)
c.circle(x_cen, y_cen, r, stroke=1, fill=0)
c.ellipse(x1, y1, x2, y2, stroke=1, fill=0)
c.roundRect(x, y, width, height, radius, stroke=1, fill=0)
c.bezier(x1, y1, x2, y2, x3, y3, x4, y4)
```

### State Control (Colors, Fonts, Transforms)
```python
c.setFillColorRGB(r, g, b) # 0.0 to 1.0
c.setStrokeColorCMYK(c, m, y, k)
c.setFont("Helvetica-Bold", 12)
c.setLineWidth(2)
c.setDash([2, 2], 0) # [dash, gap], phase

# Transitions
c.translate(dx, dy)
c.rotate(degrees)
c.scale(x_factor, y_factor)

c.saveState() # Pushes current state to stack
# ... changes ...
c.restoreState() # Pops state from stack
```

## 2. Fonts & Encodings
ReportLab uses UTF-8/Unicode by default. 

### Standard Fonts (Built-in)
- Helvetica, Times-Roman, Courier (plus -Bold, -Oblique variants)
- Symbol, ZapfDingbats

### TrueType Font Registration
```python
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont

pdfmetrics.registerFont(TTFont('Vera', 'Vera.ttf'))
c.setFont('Vera', 32)
```

### Asian Fonts (CID)
```python
from reportlab.pdfbase.cidfonts import UnicodeCIDFont
pdfmetrics.registerFont(UnicodeCIDFont('HeiseiMin-W3')) # Japanese
```

## 3. Platypus (Document Layout)
Platypus separates layout (Templates/Frames) from content (Flowables).

### Standard Template Structure
```python
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.lib.units import inch

styles = getSampleStyleSheet()
doc = SimpleDocTemplate("output.pdf", pagesize=letter)
story = []

# Add Flowables
story.append(Paragraph("Chapter 1", styles['Heading1']))
story.append(Spacer(1, 0.2*inch))
story.append(Paragraph("This is body text.", styles['Normal']))
story.append(PageBreak())

doc.build(story)
```

### Frames & PageTemplates (Advanced Layout)
```python
from reportlab.platypus import PageTemplate, Frame, BaseDocTemplate

def onPage(canvas, doc):
    canvas.saveState()
    canvas.drawString(inch, 0.5*inch, f"Page {doc.page}")
    canvas.restoreState()

frame = Frame(inch, inch, 6.5*inch, 9*inch, id='F1')
template = PageTemplate(id='main', frames=[frame], onPage=onPage)
doc = BaseDocTemplate("complex.pdf", pageTemplates=[template])
```

## 4. Paragraph XML Markup
The `<Paragraph>` object supports HTML-like tags for styling substrings.

### Common Tags
- `<b>Bold</b>`, `<i>Italic</i>`, `<u>Underline</u>`
- `<strong>Strong</strong>`, `<strike>Strike</strike>`
- `<font face="Times-Roman" size="14" color="red">Custom Font</font>`
- `<sup>Superscript</sup>`, `<sub>Subscript</sub>`
- `<greek>alpha</greek>` (α)
- `<link href="http://example.com" color="blue">Link</link>`
- `<br/>` Line break
- `<img src="path/to/image.jpg" width="20" height="20" valign="middle"/>`

### Numbering & Bullets
```python
# Auto-incrementing counters
p1 = Paragraph('<seq id="S1"/>. First item', styles['Normal'])
p2 = Paragraph('<seq id="S1"/>. Second item', styles['Normal'])

# Explicit Bullets
p = Paragraph("List item", styles['Normal'], bulletText="•")
```

## 5. Tables & TableStyles
Tables use a coordinate system `(column, row)`. `(0,0)` is top-left, `(-1,-1)` is bottom-right.

### Creating a Table
```python
from reportlab.platypus import Table, TableStyle
from reportlab.lib import colors

data = [
    ['Header 1', 'Header 2'],
    ['Row 1, Col 1', 'Row 1, Col 2'],
    ['Row 2, Col 1', 'Row 2, Col 2']
]

t = Table(data, colWidths=[2*inch, 2*inch])
t.setStyle(TableStyle([
    ('BACKGROUND', (0,0), (-1,0), colors.grey),
    ('TEXTCOLOR', (0,0), (-1,0), colors.whitesmoke),
    ('ALIGN', (0,0), (-1,-1), 'CENTER'),
    ('FONTNAME', (0,0), (-1,0), 'Helvetica-Bold'),
    ('BOTTOMPADDING', (0,0), (-1,0), 12),
    ('GRID', (0,0), (-1,-1), 0.5, colors.black),
    ('SPAN', (0,1), (0,2)), # Spans Col 0, Rows 1 to 2
]))
```

## 6. Programming Flowables (Chapter 8)
Allows logic and variable assignment within the flowable list.
- `DocAssign(var, expr)`: `DocAssign('i', 3)`
- `DocExec(stmt)`: `DocExec('i-=1')`
- `DocPara(expr, format, style)`: `DocPara('i', format='Value is %(__expr__)d', style=n)`
- `DocIf(cond, thenBlock, elseBlock)`: `DocIf('i>3', [P1], [P2])`
- `DocWhile(cond, whileBlock)`: Loop until condition is false.

## 7. Table of Contents & Index (Chapter 9)
Requires `multiBuild` for multiple passes.

### Table of Contents
```python
from reportlab.platypus.tableofcontents import TableOfContents
toc = TableOfContents()
toc.levelStyles = [styles['Heading1'], styles['Heading2']]
story.append(toc)
# In DocTemplate.afterFlowable:
# self.notify('TOCEntry', (level, text, self.page, destinationKey))
```

### Simple Index
```python
from reportlab.platypus import SimpleIndex
index = SimpleIndex()
story.append(Paragraph('Term <index item="Term"/>', styles['Normal']))
story.append(index)
# Use doc.build(story, canvasmaker=index.getCanvasMaker())
```

## 8. Graphics, Charts & Barcodes (Chapter 11 & Appendix)

### Shapes and Drawings
```python
from reportlab.graphics.shapes import Drawing, Rect, String
from reportlab.graphics import renderPDF

d = Drawing(100, 100)
d.add(Rect(10, 10, 80, 80, fillColor=colors.blue))
renderPDF.drawToFile(d, 'shapes.pdf')
```

### Charts (Pie, Bar, Line)
```python
from reportlab.graphics.charts.piecharts import Pie
from reportlab.graphics.charts.barcharts import VerticalBarChart

pc = Pie()
pc.x, pc.y, pc.data = 50, 50, [10, 20, 30]
pc.labels = ['A', 'B', 'C']

bc = VerticalBarChart()
bc.x, bc.y, bc.data = 50, 50, [(10, 20, 30)]
```

### Barcodes
```python
from reportlab.graphics.barcode import code128, qr
barcode = code128.Code128("DATA123", barWidth=0.5, barHeight=20)
# QR Codes
q = qr.QrCodeWidget('http://www.reportlab.com')
```

## 9. Interactive Features & Security (Chapter 4)
- **Encryption**: `c = canvas.Canvas(..., encrypt="password")` or `StandardEncryption(userPass, ownerPass, canPrint=0)`.
- **Forms (AcroForm)**: `c.acroform.checkbox(name='CB1', x=100, y=700)`, `textfield`, `radio`, `listbox`, `choice`.
- **Outlines**: `c.addOutlineEntry("Chapter 1", "ch1", level=0)`
- **Bookmarks**: `c.bookmarkPage("ch1")`, `c.linkAbsolute("Go to start", "ch1", rect=(x1,y1,x2,y2))`

## 10. Writing Your Own Flowables (Chapter 10)
Subclass `Flowable` and implement `wrap(availWidth, availHeight)` and `draw()`.

```python
from reportlab.platypus.flowables import Flowable

class MyCircle(Flowable):
    def __init__(self, radius=10):
        Flowable.__init__(self)
        self.radius = radius
    def wrap(self, availWidth, availHeight):
        return (2*self.radius, 2*self.radius)
    def draw(self):
        self.canv.circle(self.radius, self.radius, self.radius)
```
