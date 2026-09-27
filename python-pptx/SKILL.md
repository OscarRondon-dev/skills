# python-pptx Expert Skill

Expert guidance for creating and manipulating PowerPoint presentations using **python-pptx v1.0.2** in Python. Covers the complete API: slides, shapes, text, tables, charts, images, effects, and XML-level features.

## 1. Installation
```bash
pip install "python-pptx>=1.0.2"
```

## 2. Presentation Setup
```python
from pptx import Presentation
from pptx.util import Inches, Pt, Emu, Cm

prs = Presentation()
prs.slide_width = Inches(13.333)   # 16:9 widescreen
prs.slide_height = Inches(7.5)

slide = prs.slides.add_slide(prs.slide_layouts[6])  # blank layout

# Slide size presets:
# Widescreen 16:9   → 13.333 x 7.5 in
# Standard 4:3      → 10.0 x 7.5 in

prs.save('output.pptx')
```

**Layouts:** `slide_layouts[0..11]` vary by template. Layout `[6]` is reliably blank. Always use blank for full custom control.

## 3. Slide Background
```python
def set_bg(slide, color):
    fill = slide.background.fill
    fill.solid()
    fill.fore_color.rgb = color

# Theme color (instead of RGB)
fill.fore_color.theme_color = MSO_THEME_COLOR.ACCENT_1
```

## 4. Colors & Fills

### RGB Colors
```python
from pptx.dml.color import RGBColor
navy = RGBColor(0x00, 0x1E, 0x40)
```

### Fill Types
```python
from pptx.enum.dml import MSO_FILL, MSO_PATTERN

shape.fill.solid()                             # Solid color
shape.fill.fore_color.rgb = RGBColor(0xFF, 0, 0)

shape.fill.gradient()                          # Gradient
shape.fill.gradient_angle = 90                 # degrees
stops = shape.fill.gradient_stops
stops[0].position = 0.0; stops[0].color.rgb = color1
stops[1].position = 1.0; stops[1].color.rgb = color2

shape.fill.background()                        # Transparent

shape.fill.patterned()                         # Pattern (MSO_PATTERN)
shape.fill.pattern = MSO_PATTERN.WAVE
shape.fill.fore_color.rgb = RGBColor(0, 0, 0)  # foreground
shape.fill.back_color.rgb = RGBColor(255,255,255)  # background

# Picture fill (fill shape with image)
# Requires XML manipulation — use shape.fill.picture()
```

### Theme Colors
```python
from pptx.enum.dml import MSO_THEME_COLOR
shape.fill.solid()
shape.fill.fore_color.theme_color = MSO_THEME_COLOR.ACCENT_1
# Also: DARK_1, LIGHT_1, DARK_2, LIGHT_2, ACCENT_1..6,
#        BACKGROUND_1, BACKGROUND_2, TEXT_1, TEXT_2,
#        HYPERLINK, FOLLOWED_HYPERLINK
```

## 5. Shapes

### Basic Shapes
```python
from pptx.enum.shapes import MSO_SHAPE

slide.shapes.add_shape(MSO_SHAPE.RECTANGLE, Inches(0), Inches(0),
                        Inches(4), Inches(3))

# Common MSO_SHAPE values:
RECTANGLE, ROUNDED_RECTANGLE, OVAL, LINE, ISOSCELES_TRIANGLE,
PARALLELOGRAM, TRAPEZOID, DIAMOND, PENTAGON, HEXAGON, OCTAGON,
CUBE, BEVEL, DONUT, PIE, ARC, BRACKET, BRACE,
LEFT_ARROW, RIGHT_ARROW, UP_ARROW, DOWN_ARROW, LEFT_RIGHT_ARROW,
UP_DOWN_ARROW, CHEVRON, PENTAGON, STAR_5_POINT, STAR_6_POINT,
STAR_7_POINT, RIBBON, RIBBON_2, CLOSE, CLOUD, SUN, MOON,
LIGHTNING_BOLT, HEART, CROSS, PLAQUE, CAN, RIGHT_BRACKET,
LEFT_BRACKET, RIGHT_BRACE, LEFT_BRACE,
CORNER, FOLDED_CORNER, WAVE,
FLOWCHART_PROCESS, FLOWCHART_DECISION, FLOWCHART_TERMINATOR,
CALLOUT_1, CALLOUT_2, CALLOUT_3,
ACTION_BUTTON_CUSTOM, ACTION_BUTTON_BACK_OR_PREVIOUS,
ACTION_BUTTON_NEXT, ACTION_BUTTON_HOME, ACTION_BUTTON_INFORMATION,
SMILEY_FACE, DECAGON, DODECAGON, CHART_PLUS, CHART_STAR,
NOTCHED_RIGHT_ARROW, STRIPED_RIGHT_ARROW, SWOOSH_ARROW
```

### Shape Properties
```python
shape.fill.solid()
shape.fill.fore_color.rgb = color
shape.line.color.rgb = RGBColor(0, 0, 0)    # Border color
shape.line.width = Pt(2)                     # Border width
shape.line.dash_style = MSO_LINE.DASH        # Border dash style
# MSO_LINE: SOLID, DASH, DASH_DOT, DASH_DOT_DOT, LONG_DASH,
#           LONG_DASH_DOT, ROUND_DOT, SQUARE_DOT

shape.rotation = 45.0   # degrees

shape.shadow.inherit = False   # Remove shadow (breaks inheritance)
```

### Connectors
```python
from pptx.enum.shapes import MSO_CONNECTOR_TYPE
connector = slide.shapes.add_connector(
    MSO_CONNECTOR_TYPE.STRAIGHT,
    Inches(1), Inches(2), Inches(4), Inches(2))
connector.line.color.rgb = BLUE
connector.line.width = Pt(2)
# Also: STRAIGHT, ELBOW, CURVE, RIGHT_ARROW
```

### Group Shapes
```python
group = slide.shapes.add_group_shape()
rect = group.shapes.add_shape(
    MSO_SHAPE.RECTANGLE, Inches(0), Inches(0),
    Inches(2), Inches(1))
rect.fill.solid(); rect.fill.fore_color.rgb = NAVY
# group.left = Inches(3)  # Move entire group
```

### Freeform Shapes
```python
builder = slide.shapes.build_freeform(Inches(1), Inches(1))
builder.add_line_segments([
    (Inches(2), Inches(1)),
    (Inches(2), Inches(2)),
], close=True)
freeform = builder.convert_to_shape()
```

### Action Buttons (click/hover)
```python
from pptx.enum.action import PP_ACTION
shape.click.action = PP_ACTION.FIRST_SLIDE
shape.click.target_slide = prs.slides[0]
# Also: PREVIOUS_SLIDE, NEXT_SLIDE, LAST_SLIDE, LAST_SLIDE_VIEWED,
#        END_SHOW, NAMED_SLIDE, HYPERLINK, PLAY, OPEN_FILE,
#        RUN_MACRO, RUN_PROGRAM, OLE_VERB

# Hover action (mouse over)
shape.hover.action = PP_ACTION.NEXT_SLIDE
```

## 6. Text

### Text Box with Autofit
```python
from pptx.enum.text import PP_ALIGN, MSO_ANCHOR, MSO_AUTO_SIZE

def add_textbox(slide, left, top, width, height, text,
                size=18, bold=False, color=WHITE, align=PP_ALIGN.LEFT,
                font='Calibri', autofit=True):
    txBox = slide.shapes.add_textbox(
        Inches(left), Inches(top), Inches(width), Inches(height))
    tf = txBox.text_frame
    tf.word_wrap = True
    if autofit:
        tf.auto_size = MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE
    p = tf.paragraphs[0]
    p.text = text
    p.font.size = Pt(size)
    p.font.bold = bold
    p.font.color.rgb = color
    p.font.name = font
    p.alignment = align
    return txBox
```

### Autofit Options
```python
from pptx.enum.text import MSO_AUTO_SIZE
tf.auto_size = MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE    # Shrink text (DEFAULT)
tf.auto_size = MSO_AUTO_SIZE.SHAPE_TO_FIT_TEXT    # Expand shape
tf.auto_size = MSO_AUTO_SIZE.NONE                 # No autofit
tf.auto_size = None                                # Inherit from theme
```

### Text Frame Margins
```python
tf.margin_left = Inches(0.1)
tf.margin_right = Inches(0.1)
tf.margin_top = Inches(0.05)
tf.margin_bottom = Inches(0.05)
```

### Run-level Formatting (mixed styles in same paragraph)
```python
def add_run_textbox(slide, left, top, width, height, runs,
                     align=PP_ALIGN.LEFT, autofit=True):
    """runs: [{text, size?, bold?, color?, font?}]"""
    txBox = slide.shapes.add_textbox(
        Inches(left), Inches(top), Inches(width), Inches(height))
    tf = txBox.text_frame
    tf.word_wrap = True
    if autofit:
        tf.auto_size = MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE
    p = tf.paragraphs[0]; p.alignment = align
    for rd in runs:
        run = p.add_run()
        run.text = rd.get('text', '')
        run.font.size = Pt(rd.get('size', 14))
        run.font.bold = rd.get('bold', False)
        run.font.color.rgb = rd.get('color', WHITE)
        run.font.name = rd.get('font', 'Calibri')
    return txBox

# Example: "Total: 1,728 tests"
# add_run_textbox(slide, 1, 1, 5, 0.5, [
#     {'text': 'Total: ', 'size': 16, 'bold': True, 'color': DARK},
#     {'text': '1,728 tests', 'size': 16, 'bold': True, 'color': BLUE},
# ])
```

### Rich Text (multiple paragraphs with styles)
```python
def add_rich_textbox(slide, left, top, width, height, paragraphs, autofit=True):
    """paragraphs: [{text, size?, bold?, color?, align?, font?, space_after?}]"""
    txBox = slide.shapes.add_textbox(
        Inches(left), Inches(top), Inches(width), Inches(height))
    tf = txBox.text_frame
    tf.word_wrap = True
    if autofit:
        tf.auto_size = MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE
    for i, pd in enumerate(paragraphs):
        p = tf.paragraphs[0] if i == 0 else tf.add_paragraph()
        p.text = pd.get('text', '')
        p.font.size = Pt(pd.get('size', 14))
        p.font.bold = pd.get('bold', False)
        p.font.color.rgb = pd.get('color', DARK)
        p.font.name = pd.get('font', 'Calibri')
        p.alignment = pd.get('align', PP_ALIGN.LEFT)
        p.space_after = Pt(pd.get('space_after', 6))
    return txBox
```

### Font Properties (per run or paragraph)
```python
run.font.name = 'Calibri'                  # Font family
run.font.size = Pt(14)                     # Font size
run.font.bold = True                       # Bold
run.font.italic = True                     # Italic
run.font.underline = True                  # Underline (bool)
run.font.underline = MSO_UNDERLINE.DOUBLE_LINE  # Underline (style)
run.font.color.rgb = RGBColor(0xFF, 0, 0)  # Color
run.font.size = Pt(12)                     # Font size

# MSO_UNDERLINE: NONE, SINGLE_LINE, DOUBLE_LINE, SINGLE_ACCOUNTING,
#                DOUBLE_ACCOUNTING, WORDS, DASH, DASH_DOT,
#                DASH_DOT_DOT, DOTTED, HEAVY, THICK, WAVE, WAVY_DOUBLE,
#                WAVY_HEAVY, DASH_LONG, DOTTED_HEAVY, DASH_HEAVY,
#                DASH_DOT_HEAVY, DASH_DOT_DOT_HEAVY,
#                DASH_LONG_HEAVY, DOT_DOT_DASH, DOT_DOT_DOT_DASH,
#                WAVY_DASH, MIXED
```

### Alignment
```python
from pptx.enum.text import PP_ALIGN, MSO_ANCHOR

p.alignment = PP_ALIGN.LEFT   # LEFT, CENTER, RIGHT, JUSTIFY, DISTRIBUTE

txBox.text_frame.vertical_anchor = MSO_ANCHOR.MIDDLE
# MSO_ANCHOR: TOP, MIDDLE, BOTTOM
```

### Native Bullets (via XML)
```python
def add_bullet_textbox(slide, left, top, width, height, items,
                       size=14, color=DARK, font='Calibri', bullet_char='2022',
                       autofit=True):
    """items = [texto1, texto2, ...], bullet_char = hex Unicode code"""
    from pptx.oxml.ns import qn
    txBox = slide.shapes.add_textbox(
        Inches(left), Inches(top), Inches(width), Inches(height))
    tf = txBox.text_frame; tf.word_wrap = True
    if autofit: tf.auto_size = MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE
    for i, text in enumerate(items):
        p = tf.paragraphs[0] if i == 0 else tf.add_paragraph()
        p.text = text; p.font.size = Pt(size)
        p.font.color.rgb = color; p.font.name = font
        pPr = p._p.get_or_add_pPr()
        for old in pPr.findall(qn('a:buChar')): pPr.remove(old)
        bc = pPr.makeelement(qn('a:buChar'),
            {'char': chr(int(bullet_char, 16))})
        pPr.append(bc)
    return txBox

# Common bullet_char values: '2022' = •, '2023' = ‣, '25CF' = ●,
# '25CB' = ○, '25A0' = ■, '2713' = ✓, '2794' = ➔
```

### Hyperlinks
```python
def add_hyperlink(slide, left, top, width, height, text, url,
                  size=14, color=BLUE, font='Calibri'):
    txBox = slide.shapes.add_textbox(
        Inches(left), Inches(top), Inches(width), Inches(height))
    run = txBox.text_frame.paragraphs[0].add_run()
    run.text = text; run.font.size = Pt(size)
    run.font.color.rgb = color; run.font.name = font
    run.hyperlink.address = url
    run.hyperlink.tooltip = url  # Optional tooltip
    return txBox

# Internal hyperlink (to another slide)
run.hyperlink.address = None
run.hyperlink.target_slide = prs.slides[2]  # Jump to slide 2
```

## 7. Images

```python
# From file
slide.shapes.add_picture('logo.png', Inches(0.5), Inches(0.3),
                          Inches(1.5), Inches(0.6))

# From BytesIO
from io import BytesIO
with open('screenshot.png', 'rb') as f:
    img_data = BytesIO(f.read())
slide.shapes.add_picture(img_data, Inches(0.8), Inches(1.6),
                          Inches(5.5), Inches(4.0))

# Supported: PNG, JPG, JPEG, GIF, BMP, TIFF, EMF, WMF
```

## 8. Tables

### Basic Table
```python
def add_table(slide, left, top, width, height, data, header_color=BLUE):
    """data = [[col1, col2, ...], ...] — first row is header"""
    rows, cols = len(data), len(data[0])
    ts = slide.shapes.add_table(rows, cols, Inches(left), Inches(top),
                                 Inches(width), Inches(height))
    tbl = ts.table
    for i, row_data in enumerate(data):
        for j, val in enumerate(row_data):
            cell = tbl.cell(i, j); cell.text = str(val)
            for p in cell.text_frame.paragraphs:
                p.font.size = Pt(11); p.font.name = 'Calibri'
                p.font.color.rgb = WHITE if i == 0 else DARK
                p.alignment = PP_ALIGN.CENTER if i == 0 else PP_ALIGN.LEFT
            cell.fill.solid()
            if i == 0: cell.fill.fore_color.rgb = header_color
            elif i % 2 == 0: cell.fill.fore_color.rgb = ZEBRA
            else: cell.fill.fore_color.rgb = WHITE
    return ts
```

### Table Properties
```python
tbl.first_col = True      # Distinct first column formatting
tbl.first_row = True      # Distinct first row formatting
tbl.last_col = False      # Distinct last column
tbl.horz_banding = True   # Alternating row colors

# Column widths
tbl.columns[0].width = Inches(1.5)
tbl.columns[1].width = Inches(3.0)
```

### Cell Properties
```python
cell.fill.solid()
cell.fill.fore_color.rgb = RGBColor(0xFF, 0, 0)
cell.margin_left = Inches(0.1)
cell.margin_right = Inches(0.1)
cell.margin_top = Inches(0.05)
cell.margin_bottom = Inches(0.05)

# Vertical alignment
cell.vertical_anchor = MSO_ANCHOR.MIDDLE
```

### Merge Cells
```python
# Merge cells in range
cell_tl = tbl.cell(0, 0)  # Top-left
cell_br = tbl.cell(0, 2)  # Bottom-right
cell_tl.merge(cell_br)    # Merges across 3 columns in row 0

cell.split()              # Unmerge
```

## 9. Charts

### Chart Data Setup
```python
from pptx.chart.data import CategoryChartData
from pptx.enum.chart import XL_CHART_TYPE, XL_LEGEND_POSITION
from pptx.enum.chart import XL_LABEL_POSITION, XL_MARKER_STYLE
from pptx.enum.chart import XL_TICK_MARK, XL_TICK_LABEL_POSITION

chart_data = CategoryChartData()
chart_data.categories = ['Cat A', 'Cat B', 'Cat C']
chart_data.add_series('Series 1', (30, 45, 22))
chart_data.add_series('Series 2', (15, 30, 40))
```

### All Chart Types (56 total)
```python
# 2D Area
XL_CHART_TYPE.AREA, AREA_STACKED, AREA_STACKED_100

# 3D Area
XL_CHART_TYPE.THREE_D_AREA, THREE_D_AREA_STACKED, THREE_D_AREA_STACKED_100

# Bar (horizontal)
XL_CHART_TYPE.BAR_CLUSTERED, BAR_STACKED, BAR_STACKED_100

# 3D Bar
XL_CHART_TYPE.THREE_D_BAR_CLUSTERED, THREE_D_BAR_STACKED,
THREE_D_BAR_STACKED_100

# Column (vertical)
XL_CHART_TYPE.COLUMN_CLUSTERED, COLUMN_STACKED, COLUMN_STACKED_100

# 3D Column
XL_CHART_TYPE.THREE_D_COLUMN, THREE_D_COLUMN_CLUSTERED,
THREE_D_COLUMN_STACKED, THREE_D_COLUMN_STACKED_100

# Line
XL_CHART_TYPE.LINE, LINE_STACKED, LINE_STACKED_100,
LINE_MARKERS, LINE_MARKERS_STACKED, LINE_MARKERS_STACKED_100

# 3D Line
XL_CHART_TYPE.THREE_D_LINE

# Pie
XL_CHART_TYPE.PIE, PIE_EXPLODED, PIE_OF_PIE, BAR_OF_PIE

# 3D Pie
XL_CHART_TYPE.THREE_D_PIE, THREE_D_PIE_EXPLODED

# Doughnut
XL_CHART_TYPE.DOUGHNUT, DOUGHNUT_EXPLODED

# Radar
XL_CHART_TYPE.RADAR, RADAR_FILLED, RADAR_MARKERS

# XY Scatter
XL_CHART_TYPE.XY_SCATTER, XY_SCATTER_LINES,
XY_SCATTER_LINES_NO_MARKERS, XY_SCATTER_SMOOTH,
XY_SCATTER_SMOOTH_NO_MARKERS

# Bubble
XL_CHART_TYPE.BUBBLE, BUBBLE_THREE_D_EFFECT

# Stock
XL_CHART_TYPE.STOCK_HLC, STOCK_OHLC, STOCK_VHLC, STOCK_VOHLC

# Surface
XL_CHART_TYPE.SURFACE, SURFACE_TOP_VIEW,
SURFACE_TOP_VIEW_WIREFRAME, SURFACE_WIREFRAME

# 3D Cone
XL_CHART_TYPE.CONE_COL, CONE_COL_CLUSTERED, CONE_COL_STACKED,
CONE_COL_STACKED_100, CONE_BAR_CLUSTERED, CONE_BAR_STACKED,
CONE_BAR_STACKED_100

# 3D Cylinder
XL_CHART_TYPE.CYLINDER_COL, CYLINDER_COL_CLUSTERED,
CYLINDER_COL_STACKED, CYLINDER_COL_STACKED_100,
CYLINDER_BAR_CLUSTERED, CYLINDER_BAR_STACKED,
CYLINDER_BAR_STACKED_100

# 3D Pyramid
XL_CHART_TYPE.PYRAMID_COL, PYRAMID_COL_CLUSTERED,
PYRAMID_COL_STACKED, PYRAMID_COL_STACKED_100,
PYRAMID_BAR_CLUSTERED, PYRAMID_BAR_STACKED,
PYRAMID_BAR_STACKED_100
```

### Column/Bar Chart
```python
chart_frame = slide.shapes.add_chart(
    XL_CHART_TYPE.COLUMN_CLUSTERED,
    Inches(0.8), Inches(1.6), Inches(5.5), Inches(4.5), chart_data)
chart = chart_frame.chart

# Legend
chart.has_legend = True
chart.legend.position = XL_LEGEND_POSITION.BOTTOM  # BOTTOM, TOP, LEFT, RIGHT, CORNER
chart.legend.include_in_layout = False
chart.legend.font.size = Pt(10)
chart.legend.horz_offset = -0.1   # Fine-tune x position (-1.0 to 1.0)

# Series formatting
chart.series[0].format.fill.solid()
chart.series[0].format.fill.fore_color.rgb = BLUE
chart.series[1].format.fill.solid()
chart.series[1].format.fill.fore_color.rgb = ORANGE

# Bar plot properties
plot = chart.plots[0]
plot.gap_width = 100                  # Gap between bars (% of bar width)
plot.overlap = -10                    # Overlap between series (-100 to 100)
```

### Pie Chart
```python
pie_data = CategoryChartData()
pie_data.categories = ['A', 'B', 'C']
pie_data.add_series('Data', (65, 20, 15))

pie_frame = slide.shapes.add_chart(
    XL_CHART_TYPE.PIE, Inches(0.8), Inches(1.6),
    Inches(4.5), Inches(3.5), pie_data)
pie = pie_frame.chart
pie.has_legend = True
pie.legend.position = XL_LEGEND_POSITION.BOTTOM

plot = pie.plots[0]
plot.has_data_labels = True
plot.data_labels.show_percentage = True
plot.data_labels.show_category_name = False  # Use legend instead
plot.data_labels.show_value = False
plot.data_labels.show_series_name = False
plot.data_labels.show_legend_key = False
plot.data_labels.font.size = Pt(12)
plot.data_labels.font.bold = True
plot.data_labels.number_format = '0.0'       # Number format string
plot.data_labels.number_format_is_linked = False

# Can set: show_category_name, show_value, show_percentage,
#          show_series_name, show_legend_key

# Data label position
plot.data_labels.position = XL_LABEL_POSITION.CENTER
# Positions: CENTER, INSIDE_END, INSIDE_BASE, OUTSIDE_END, BEST_FIT,
#            LEFT, RIGHT, ABOVE, BELOW, MIXED

# Color individual slices
for i, color in enumerate([GREEN, ORANGE, DGRAY]):
    plot.series[0].points[i].format.fill.solid()
    plot.series[0].points[i].format.fill.fore_color.rgb = color

# Custom data label per point
dLbl = plot.series[0].points[0].data_label
dLbl.has_text_frame = True  # Add custom text
dLbl.text_frame.text = "Custom label"
dLbl.position = XL_LABEL_POSITION.OUTSIDE_END
```

### Line Chart
```python
line_frame = slide.shapes.add_chart(
    XL_CHART_TYPE.LINE_MARKERS, Inches(0.8), Inches(1.6),
    Inches(5.5), Inches(4.5), chart_data)
line = line_frame.chart
line.has_legend = True
line.legend.position = XL_LEGEND_POSITION.BOTTOM

# Series fill & line
line.series[0].format.line.color.rgb = BLUE
line.series[0].format.line.width = Pt(2.5)

# Markers on line charts
line.series[0].marker.style = XL_MARKER_STYLE.CIRCLE
line.series[0].marker.size = 9
# Marker styles: CIRCLE, DIAMOND, SQUARE, TRIANGLE, STAR, PLUS, X,
#                DASH, DOT, PICTURE, AUTOMATIC, NONE

line.series[0].marker.format.fill.solid()
line.series[0].marker.format.fill.fore_color.rgb = BLUE
line.series[0].marker.format.line.color.rgb = WHITE
```

### Axis Customization
```python
# Value axis (usually Y)
value_axis = chart.value_axis
value_axis.minimum_scale = 0          # Min value
value_axis.maximum_scale = 100        # Max value
value_axis.major_unit = 20            # Tick spacing
value_axis.minor_unit = 5             # Minor tick spacing
value_axis.has_major_gridlines = True
value_axis.has_minor_gridlines = False
value_axis.visible = True

# Axis crossing
value_axis.crosses = XL_AXIS_CROSSES.MINIMUM
# Also: AUTOMATIC, MAXIMUM, CUSTOM
value_axis.crosses_at = 0.0           # Specific crossing point

# Tick marks
value_axis.major_tick_mark = XL_TICK_MARK.OUTSIDE  # CROSS, INSIDE, OUTSIDE, NONE
value_axis.minor_tick_mark = XL_TICK_MARK.NONE

# Reverse order (bars from top)
value_axis.reverse_order = False

# Category axis (usually X)
category_axis = chart.category_axis
category_axis.tick_label_position = XL_TICK_LABEL_POSITION.LOW
# Positions: HIGH, LOW, NEXT_TO_AXIS, NONE
category_axis.tick_labels.font.size = Pt(10)
category_axis.tick_labels.number_format = 'General'
category_axis.tick_labels.offset = 100  # 0-1000, % of default spacing
category_axis.visible = True

# Axis titles
category_axis.has_title = True
category_axis.axis_title.text_frame.text = 'Categories'
category_axis.axis_title.text_frame.paragraphs[0].font.size = Pt(12)
category_axis.axis_title.text_frame.paragraphs[0].font.bold = True

value_axis.has_title = True
value_axis.axis_title.text_frame.text = 'Values'

# Axis line/fill formatting
category_axis.format.line.color.rgb = DGRAY
category_axis.format.line.width = Pt(1)

# Major gridlines formatting
value_axis.major_gridlines.format.line.color.rgb = RGBColor(0xE0, 0xE0, 0xE0)
value_axis.major_gridlines.format.line.dash_style = MSO_LINE.DASH
```

### Chart ChartFormat (fill + line for any chart element)
```python
# Available on: series, points, axes, gridlines, plot area, legend
series.format.fill.solid()
series.format.fill.fore_color.rgb = BLUE
series.format.line.color.rgb = WHITE
series.format.line.width = Pt(1)

# Chart title
chart.has_title = True
chart.chart_title.text_frame.text = 'My Chart'
chart.chart_title.text_frame.paragraphs[0].font.size = Pt(16)
chart.chart_title.text_frame.paragraphs[0].font.bold = True
```

### Multiple Plots (overlay)
```python
chart_data = CategoryChartData()
chart_data.categories = ['A', 'B', 'C']
chart_data.add_series('Bars', (30, 45, 22))
chart_data.add_series('Line', (15, 30, 40))

chart_frame = slide.shapes.add_chart(
    XL_CHART_TYPE.COLUMN_CLUSTERED,
    Inches(0.8), Inches(1.6), Inches(5.5), Inches(4.5), chart_data)
chart = chart_frame.chart
# Add a second plot of a different type
plot2 = chart.plots.add()
plot2.type = XL_CHART_TYPE.LINE_MARKERS
# (advanced — requires series re-assignment)
```

## 10. Effects

### Shadow (XML level)
```python
def add_shadow(shape, blur='60000', dist='25000', direction='2700000',
                alpha='20000', color='000000'):
    """Apply shadow to a shape via XML"""
    spPr = shape._element.spPr
    for e in spPr.findall(qn('a:effectLst')): spPr.remove(e)
    el = spPr.makeelement(qn('a:effectLst'), {})
    sh = el.makeelement(qn('a:outerShdw'), {
        'blurRad': blur, 'dist': dist, 'dir': direction,
        'algn': 'tl', 'rotWithShape': '1'})
    c = sh.makeelement(qn('a:srgbClr'), {'val': color})
    a = c.makeelement(qn('a:alpha'), {'val': alpha})
    c.append(a); sh.append(c); el.append(sh); spPr.append(el)
    return shape

# Shadow parameters:
# blurRad: 0-2147483647 (EMU), ~60000 = 6pt
# dist: 0-2147483647 (EMU), ~25000 = 2.5pt
# dir: 0-3600000 (60000ths of degree), 2700000 = 270° = down
# alpha: 0-100000 (1000ths of percent), 20000 = 20%
```

### Glow Effect (XML)
```python
def add_glow(shape, color='0054CA', radius='50000', alpha='40000'):
    spPr = shape._element.spPr
    for e in spPr.findall(qn('a:effectLst')): spPr.remove(e)
    el = spPr.makeelement(qn('a:effectLst'), {})
    glow = el.makeelement(qn('a:glow'), {'rad': radius})
    c = glow.makeelement(qn('a:srgbClr'), {'val': color})
    a = c.makeelement(qn('a:alpha'), {'val': alpha})
    c.append(a); glow.append(c); el.append(glow); spPr.append(el)
```

### Reflection Effect (XML)
```python
def add_reflection(shape, blur='2000', dist='4000', start_alpha='100000',
                    end_alpha='0', fade_dir='5400000', rot='0'):
    spPr = shape._element.spPr
    for e in spPr.findall(qn('a:effectLst')): spPr.remove(e)
    el = spPr.makeelement(qn('a:effectLst'), {})
    ref = el.makeelement(qn('a:reflection'), {})
    ref.set('blurRad', blur); ref.set('dist', dist)
    ref.set('stA', start_alpha); ref.set('endA', end_alpha)
    ref.set('dir', fade_dir); ref.set('rotWithShape', rot)
    # Add color
    c = ref.makeelement(qn('a:srgbClr'), {'val': '000000'})
    ref.append(c); el.append(ref); spPr.append(el)
```

### 3D Effects (bevel, extrusion — XML)
```python
def add_3d(shape, top_w='80000', top_h='80000', bottom_w='80000',
            bottom_h='80000', extrusion_h='25400', metal='0'):
    """Add 3D bevel and extrusion. EMU values."""
    spPr = shape._element.spPr
    # Remove existing 3D
    for e in spPr.findall(qn('a:scene3d')): spPr.remove(e)
    for e in spPr.findall(qn('a:sp3d')): spPr.remove(e)
    # Add 3D scene
    s3d = spPr.makeelement(qn('a:scene3d'), {})
    cam = s3d.makeelement(qn('a:camera'), {'prst': 'orthographicFront'})
    s3d.append(cam); spPr.append(s3d)
    # Add shape 3D properties
    sp3d = spPr.makeelement(qn('a:sp3d'), {})
    if extrusion_h:
        sp3d.set('extrusionH', extrusion_h)
    if top_w or bottom_w:
        bevel_t = sp3d.makeelement(qn('a:bevelT'), {'w': top_w, 'h': top_h})
        bevel_b = sp3d.makeelement(qn('a:bevelB'), {'w': bottom_w, 'h': bottom_h})
        sp3d.append(bevel_t); sp3d.append(bevel_b)
    spPr.append(sp3d)
```

## 11. Speaker Notes
```python
def add_notes(slide, text):
    slide.notes_slide.notes_text_frame.text = text

# Rich notes via TextFrame
notes = slide.notes_slide.notes_text_frame
notes.text = "Presenter notes here"
notes.paragraphs[0].font.size = Pt(12)
```

## 12. OLE Objects (embed files)

```python
# Embed Excel, PDF, etc.
ole = slide.shapes.add_ole_object(
    'data.xlsx',                              # File path or BytesIO
    'Excel.Sheet.12',                         # ProgID
    Inches(1), Inches(1),                     # Position
    Inches(4), Inches(3))                     # Size
```

## 13. Video (Movie)

```python
# Add video to slide
movie = slide.shapes.add_movie(
    'video.mp4', Inches(1), Inches(1),
    Inches(6), Inches(4),
    poster_frame_image='poster.jpg')  # Optional poster frame
```

## 14. Slide Transitions (via XML)

```python
def set_transition(slide, transition_type='fade', duration=500):
    """Apply slide transition. Types: fade, push, wipe, cut, split, etc."""
    transition = slide._element.sldShow.get_or_add_transition()
    transition.set('dur', str(duration))  # Duration in ms
    # Remove existing effect
    for child in list(transition):
        transition.remove(child)
    # Add transition type
    from pptx.oxml.ns import qn
    effect = transition.makeelement(
        qn(f'p:{transition_type}'), {})
    transition.append(effect)
```

## 15. Slide Layouts & Masters

```python
# Access layouts
for layout in prs.slide_layouts:
    print(layout.name)  # 'Title Slide', 'Blank', etc.

# Use specific layout by index
slide = prs.slides.add_slide(prs.slide_layouts[0])  # Title slide

# Access placeholders
for ph in slide.placeholders:
    print(ph.placeholder_format.idx, ph.name)

# Fill placeholder content
title_ph = slide.placeholders[0]  # Title
title_ph.text = "Slide Title"
subtitle_ph = slide.placeholders[1]  # Subtitle
subtitle_ph.text = "Subtitle text"
```

## 16. Headers & Footers (slide-level)

```python
# Slide numbers
slide.header_footer.slide_number = True  # Doesn't always work — use XML

# Date/time
slide.header_footer.date_time = datetime.now()

# Footer text
slide.header_footer.footer_text = "Confidential"
```

## 17. Presentation Sections (organize slides in thumbnail pane)

```python
from pptx.oxml.ns import qn

def add_section(prs, name, start_slide_index):
    """Create a section grouping slides from start_slide_index"""
    sldIdLst = prs.presentation.sldIdLst
    section = prs.presentation._element.makeelement(
        qn('p:section'), {'name': name})
    sldIdEls = list(sldIdLst)
    ids_in_section = sldIdEls[start_slide_index:]
    for sldId in ids_in_section:
        sldIdLst.remove(sldId)
    for sldId in ids_in_section:
        section.append(sldId)
    sldIdLst.append(section)
```

## 18. Freeform Builder

```python
# Build custom shapes with line segments
builder = slide.shapes.build_freeform(Inches(1), Inches(1))
builder.add_line_segments([
    (Inches(1), Inches(2)),
    (Inches(2), Inches(2)),
    (Inches(3), Inches(1.5)),
], close=True)
freeform = builder.convert_to_shape(
    origin_x=Inches(0), origin_y=Inches(0))
freeform.fill.solid()
freeform.fill.fore_color.rgb = BLUE
```

## 19. Manipulate Existing PPTX

```python
# Open and modify
prs = Presentation('existing.pptx')
for slide in prs.slides:
    for shape in slide.shapes:
        if shape.has_text_frame:
            for p in shape.text_frame.paragraphs:
                if 'old' in p.text:
                    p.text = p.text.replace('old', 'new')
prs.save('modified.pptx')
```

## 20. Clone Slides

```python
# python-pptx does NOT support cloning slides natively.
# To duplicate: save, unzip, copy XML, modify relationships.
# Or use: pptx-template or python-pptx's internal XML copy.
```

## 21. Best Practices & Common Pitfalls

### Layout Rules (slide = 13.333 x 7.5 in)
- Always calculate `left + width <= 13.333` and `top + height <= 7.5`
- Leave min 0.5" bottom margin
- Don't overlap content columns (left vs right)
- Use `MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE` to prevent text overflow

### Chart Rules
- Don't mix vastly different scales in COLUMN_CLUSTERED (e.g., 22k vs 18)
- Pie charts: use `show_percentage=True`, `show_category_name=False`, `show_value=False`, rely on legend
- Keep at least 0.4" margin between chart area and header

### Text Rules
- Short text for fixed-width boxes; use abbreviations if needed
- All runs go in the SAME paragraph for run-level formatting (not separate paragraphs)
- Use `tf.auto_size = MSO_AUTO_SIZE.TEXT_TO_FIT_SHAPE` on all textboxes

### File Locks
- Close PPT in PowerPoint before re-generating (PermissionError)
- Use unique filenames or delete-lock-release pattern

### V1 → V2 Migration (unrelated to python-pptx — this is for Pydantic)

## 22. Full Working Template

```python
from pptx import Presentation
from pptx.util import Inches, Pt
from pptx.dml.color import RGBColor
from pptx.enum.text import PP_ALIGN, MSO_ANCHOR, MSO_AUTO_SIZE
from pptx.enum.shapes import MSO_SHAPE, MSO_CONNECTOR_TYPE
from pptx.enum.chart import XL_CHART_TYPE, XL_LEGEND_POSITION, XL_LABEL_POSITION
from pptx.chart.data import CategoryChartData
from pptx.oxml.ns import qn

prs = Presentation()
prs.slide_width = Inches(13.333)
prs.slide_height = Inches(7.5)

# Colors
NAVY = RGBColor(0x00, 0x1E, 0x40)
BLUE = RGBColor(0x00, 0x54, 0xCA)
WHITE = RGBColor(0xFF, 0xFF, 0xFF)

# Helper functions here (add_textbox, add_rect, etc.)

slide = prs.slides.add_slide(prs.slide_layouts[6])
# Build content...

prs.save('output.pptx')
```

## 23. References
- Official docs: https://python-pptx.readthedocs.io/en/latest/
- API: Presentation, Slide, SlideShapes, TextFrame, Font, FillFormat, LineFormat
- All code examples above work with python-pptx v1.0.2
