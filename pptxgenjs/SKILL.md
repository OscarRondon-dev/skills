# PptxGenJS Expert Skill

Expert guidance for creating and manipulating PowerPoint presentations using **PptxGenJS** in JavaScript/TypeScript. Works in Node, React, Angular, Vite, Electron, and browsers.

## 1. Installation

```bash
npm install pptxgenjs
```

### CDN (Browser)
```html
<script src="https://cdn.jsdelivr.net/gh/gitbrent/pptxgenjs/dist/pptxgen.bundle.js"></script>
```

### Import
```js
// ES6 / TypeScript
import pptxgen from "pptxgenjs";

// CommonJS
const pptxgen = require("pptxgenjs");

// Browser (script tag)
let pres = new PptxGenJS();
```

## 2. Quick Start

```js
let pres = new pptxgen();                       // 1. Create presentation
let slide = pres.addSlide();                     // 2. Add slide
slide.addText("Hello World!", { x: 1, y: 1 });  // 3. Add content
pres.writeFile({ fileName: "output.pptx" });     // 4. Save
```

## 3. Presentation Setup & Options

### Creating
```js
import pptxgen from "pptxgenjs";
let pres = new pptxgen();
```

### Slide Layout (Size)
```js
pres.layout = "LAYOUT_WIDE";  // 13.3 x 7.5 in
// Options: LAYOUT_16x9 (default, 10x5.625), LAYOUT_16x10 (10x6.25),
//          LAYOUT_4x3 (10x7.5), LAYOUT_WIDE (13.3x7.5)

// Custom layout
pres.defineLayout({ name: "A3", width: 16.5, height: 11.7 });
pres.layout = "A3";

console.log(pres.presLayout); // { width: 10, height: 5.625 }
```

### Metadata
```js
pres.title = "My Presentation";
pres.author = "John Doe";
pres.subject = "Annual Report";
pres.company = "Acme Corp";
pres.revision = "3";
```

## 4. Slides

### Adding Slides
```js
let slide1 = pres.addSlide();
let slide2 = pres.addSlide({ masterName: "MASTER_SLIDE" });  // With master
```

### Slide Methods
```js
slide.addText(text, options);
slide.addTable(rows, options);
slide.addShape(shapeType, options);
slide.addChart(chartType, data, options);
slide.addImage(options);
slide.addMedia(options);
slide.addNotes("Speaker notes here");
```

### Slide Properties
```js
slide.background = { color: "F1F1F1" };
slide.background = { color: "FF3399", transparency: 50 };
slide.background = { data: "image/png;base64,..." };
slide.background = { path: "https://example.com/bg.jpg" };
slide.color = "363636";  // Default text color for this slide
slide.hidden = true;     // Hide slide
slide.slideNumber = { x: 1.0, y: "90%", fontFace: "Arial", fontSize: 12, color: "999999" };
```

### Speaker Notes
```js
slide.addNotes("This is the key insight slide.\nRemember to emphasize ROI.");
```

### Sections
```js
pres.addSection({ title: "Introduction" });
pres.addSection({ title: "Results", order: 2 });
let slide = pres.addSlide({ sectionTitle: "Introduction" });
```

## 5. Text

### Basic Text
```js
slide.addText("Hello World!", { x: 1, y: 1, w: 5, h: 1, fontSize: 24, color: "0088CC" });
```

### Position (Inches or Percent)
```js
{ x: 1.0, y: 1.0, w: 5.0, h: 2.0 }       // Inches (default)
{ x: "50%", y: "50%", w: "90%", h: "30%" } // Percent of slide
```

### Text Properties
```js
{
  x: 1, y: 1, w: 5, h: 1,
  align: "center",           // "left" | "center" | "right"
  valign: "middle",          // "top" | "middle" | "bottom"
  fontFace: "Arial",
  fontSize: 24,
  color: "0088CC",
  bold: true,
  italic: true,
  underline: true,
  strike: "sngStrike",       // "sngStrike" | "dblStrike"
  subscript: false,
  superscript: false,
  charSpacing: 12,            // Character spacing (points)
  lineSpacing: 28,            // Line spacing (points)
  lineSpacingMultiple: 1.5,   // Line spacing multiplier
  paraSpaceBefore: 12,        // Paragraph spacing before (points)
  paraSpaceAfter: 24,         // Paragraph spacing after (points)
  margin: 0.5,                // Padding/inset (inches)
  inset: 1.25,                // Alternative to margin
  indentLevel: 1,             // Bullet indent level
  rotate: 45,                 // Rotation degrees
  autoFit: true,              // Fit text to shape
  fit: "shrink",              // "shrink" | "resize" | "none"
  wrap: true,                 // Text wrapping
  rtlMode: false,             // Right-to-left mode
  lang: "en-US",              // Language
  isTextBox: true,            // PPT "Textbox" mode
  shape: pres.ShapeType.rect, // Use a shape as text container
  fill: { color: "F1F1F1" },
  line: { color: "0088CC", width: 2 },
  rectRadius: 0.3             // Rounded rectangle radius
}
```

### Shadow on Text
```js
shadow: { type: "outer", color: "696969", blur: 3, offset: 10, angle: 45 }
// type: "outer" | "inner"
// blur: 1-256, offset: 1-256, angle: 0-359, opacity: 0-1
```

### Glow on Text
```js
glow: { size: 10, opacity: 0.75, color: "0088CC" }
```

### Highlight
```js
highlight: "FFFF00"  // Hex color
```

### Outline on Text
```js
outline: { size: 1.5, color: "696969" }
```

### Hyperlink
```js
hyperlink: { url: "https://github.com", tooltip: "Visit GitHub" }
hyperlink: { slide: 2, tooltip: "Go to Slide 2" }  // Internal slide link
```

### Word-Level Formatting (Array of text objects)
```js
slide.addText([
  { text: "word-level\nformatting", options: { fontSize: 36, fontFace: "Courier", color: "99ABCC", align: "right", breakLine: true } },
  { text: "...in the same textbox", options: { fontSize: 48, fontFace: "Arial", color: "FFFF00", align: "center" } },
], { x: 0.5, y: 4.1, w: 8.5, h: 2.0, margin: 0.1, fill: { color: "232323" } });
```

### Bullets
```js
// Simple bullet
slide.addText("Bulleted item", { x: 1, y: 1, bullet: true });

// Bullet types
bullet: true                              // Default circle
bullet: { type: "number" }                // 1. 2. 3.
bullet: { type: "alphaLcPeriod" }         // a. b. c.
bullet: { type: "alphaUcPeriod" }         // A. B. C.
bullet: { type: "romanLcParenBoth" }      // (i) (ii) (iii)
bullet: { type: "romanUcParenBoth" }      // (I) (II) (III)
bullet: { code: "2605" }                  // Unicode star
bullet: { code: "25BA" }                  // Unicode triangle
bullet: { code: "2713" }                  // Checkmark

// Multi-line with line breaks
slide.addText("Line 1\nLine 2\nLine 3", { bullet: true });

// Per-line bullets via array
slide.addText([
  { text: "Star bullet", options: { bullet: { code: "2605" }, color: "CC0000" } },
  { text: "Triangle bullet", options: { bullet: { code: "25BA" }, color: "00CD00" } },
  { text: "No bullet", options: { fontSize: 12 } },
], { x: 1, y: 1 });
```

### Line Breaks
```js
// Use \n for line breaks in strings
// Use breakLine: true for array text objects
// Use softBreakBefore: true for shift-enter style breaks
```

### Tab Stops
```js
// Supported via XML — see demo modules
```

## 6. Tables

### Basic Table
```js
let rows = [
  ["Header 1", "Header 2", "Header 3"],
  ["Cell A1", "Cell B1", "Cell C1"],
  ["Cell A2", "Cell B2", "Cell C2"],
];
slide.addTable(rows, { x: 0.5, y: 1.0, w: 9.0 });
```

### Table Options
```js
{
  x: 0.5, y: 1.0, w: 9.0, h: 3.0,
  colW: [2.0, 3.0, 4.0],           // Column widths (uniform number or array)
  rowH: [1.0, 0.5, 0.5],           // Row heights (uniform number or array)
  align: "left",                     // Cell alignment
  valign: "middle",                  // Vertical alignment
  fontFace: "Arial",
  fontSize: 12,
  color: "363636",
  bold: false,
  italic: false,
  underline: false,
  margin: 0.1,                       // Cell padding (number or array [t,r,b,l])
  fill: { color: "F1F1F1" },        // Cell background
  border: { type: "solid", pt: "1", color: "999999" },
  border: { type: "none" },          // Borderless table
  // Per-side borders: array [top, right, bottom, left]
  border: [
    { pt: "2", color: "0088CC" },   // Top
    { pt: "1", color: "999999" },   // Right
    { pt: "2", color: "0088CC" },   // Bottom
    { pt: "1", color: "999999" },   // Left
  ],
  colspan: 2,                        // Column span (cell-level)
  rowspan: 2,                        // Row span (cell-level)
  autoPage: true,                    // Auto-create slides on overflow
  autoPageCharWeight: 0,             // -1.0 to 1.0
  autoPageLineWeight: 0,             // -1.0 to 1.0
  autoPageRepeatHeader: true,        // Repeat header on new pages
  autoPageHeaderRows: 1,             // Rows to repeat as header
  newSlideStartY: 0.5,              // Y position on overflow slides
}
```

### Cell-Level Formatting (override per cell)
```js
let rows = [
  [
    { text: "Red Cell", options: { color: "FF0000", bold: true } },
    { text: "Green Cell", options: { color: "00FF00", fill: { color: "000000" } } },
    { text: "Blue Cell", options: { color: "0000FF", align: "right" } },
  ],
];
slide.addTable(rows, { x: 0.5, y: 1, w: 9, color: "363636" });
```

### Word-Level Formatting Inside Cells
```js
let textObjs = [
  { text: "Red ", options: { color: "FF0000" } },
  { text: "Green ", options: { color: "00FF00" } },
  { text: "Blue", options: { color: "0000FF" } },
];
let rows = [
  [
    { text: "Cell 1", options: { fontFace: "Arial" } },
    { text: textObjs, options: { fill: { color: "232323" } } },
  ],
];
slide.addTable(rows, { x: 0.5, y: 3.5, w: 9 });
```

## 7. Shapes (200+ shape types)

### Basic Shapes
```js
// Without text
slide.addShape(pres.ShapeType.rect, { fill: { color: "FF0000" } });
slide.addShape(pres.ShapeType.ellipse, { fill: { color: "0088CC" } });
slide.addShape(pres.ShapeType.line, { line: { color: "FF0000", width: 1 } });

// With text (via addText with shape)
slide.addText("Rectangle", {
  shape: pres.ShapeType.rect,
  fill: { color: "FF0000" },
  x: 1, y: 1, w: 3, h: 2,
  color: "FFFFFF",
  fontSize: 18,
});
```

### Shape Types
```js
pres.ShapeType.rect
pres.ShapeType.ellipse
pres.ShapeType.line
pres.ShapeType.roundRect        // ROUNDED_RECTANGLE
pres.ShapeType.triangle
pres.ShapeType.pentagon
pres.ShapeType.hexagon
pres.ShapeType.octagon
pres.ShapeType.diamond
pres.ShapeType.parallelogram
pres.ShapeType.trapezoid
pres.ShapeType.chevron
pres.ShapeType.leftArrow
pres.ShapeType.rightArrow
pres.ShapeType.upArrow
pres.ShapeType.downArrow
pres.ShapeType.leftRightArrow
pres.ShapeType.upDownArrow
pres.ShapeType.star5           // 5-point star
pres.ShapeType.star6
pres.ShapeType.star7
pres.ShapeType.sun
pres.ShapeType.moon
pres.ShapeType.cloud
pres.ShapeType.heart
pres.ShapeType.lightning
pres.ShapeType.smileyFace
pres.ShapeType.bevel
pres.ShapeType.cube
pres.ShapeType.donut
pres.ShapeType.pie
pres.ShapeType.arc
pres.ShapeType.ribbon
pres.ShapeType.foldedCorner
pres.ShapeType.wave
pres.ShapeType.flowChartProcess
pres.ShapeType.flowChartDecision
pres.ShapeType.flowChartTerminator
pres.ShapeType.bracket
pres.ShapeType.brace
pres.ShapeType.actionButtonCustom
pres.ShapeType.actionButtonNext
pres.ShapeType.actionButtonBackPrevious
pres.ShapeType.callout1
pres.ShapeType.callout2
// ... 200+ total (see ShapeType enum)
```

### Shape Properties
```js
{
  x: 1, y: 1, w: 3, h: 2,
  fill: { color: "0088CC", transparency: 20 },
  line: { color: "000000", width: 2, dashType: "solid" | "dash" | "dot" | "lgDash" },
  flipH: true,
  flipV: false,
  rotate: 45,
  rectRadius: 0.5,         // For rounded rectangles (0-1)
  shadow: { type: "outer", color: "000000", blur: 3, offset: 5, angle: 45, opacity: 0.5 },
  shapeName: "My Shape",
  hyperlink: { url: "https://github.com" },
  align: "center",
}
```

## 8. Charts

### Chart Types
```js
pres.ChartType.area
pres.ChartType.bar
pres.ChartType.bar3d
pres.ChartType.bubble
pres.ChartType.bubble3d
pres.ChartType.doughnut
pres.ChartType.line
pres.ChartType.pie
pres.ChartType.radar
pres.ChartType.scatter
```

### Basic Chart
```js
let dataChart = [
  {
    name: "Actual Sales",
    labels: ["Jan", "Feb", "Mar"],
    values: [1500, 4600, 5156],
  },
  {
    name: "Projected Sales",
    labels: ["Jan", "Feb", "Mar"],
    values: [1000, 2600, 3456],
  },
];
slide.addChart(pres.ChartType.bar, dataChart, { x: 1, y: 1, w: 8, h: 4 });
```

### All Chart Options
```js
{
  // Position/Size
  x: 1, y: 1, w: 8, h: 4,

  // General
  showTitle: true,
  title: "Sales by Region",
  titleAlign: "center",          // "left" | "center" | "right"
  titleColor: "000000",
  titleFontFace: "Arial",
  titleFontSize: 18,
  titleRotate: 0,
  titlePos: { x: 0, y: 10 },

  // Legend
  showLegend: true,
  legendPos: "b",                // "b" | "tr" | "l" | "r" | "t"
  legendFontFace: "Arial",
  legendFontSize: 10,
  legendColor: "000000",

  // Data labels
  showLabel: true,
  showValue: true,
  showPercent: true,
  showSerName: false,
  showLeaderLines: false,
  dataLabelColor: "000000",
  dataLabelFontFace: "Arial",
  dataLabelFontSize: 11,
  dataLabelFontBold: true,
  dataLabelPosition: "bestFit",  // "bestFit" | "b" | "ctr" | "inBase" | "inEnd" | "l" | "outEnd" | "r" | "t"
  dataLabelFormatCode: "#,##0",
  dataLabelBkgrdColors: false,
  showDataTable: false,
  showDataTableKeys: true,
  dataTableFontSize: 13,

  // Colors
  chartColors: ["0088CC", "FFCC00", "33AA33", "CC3333", "9933CC"],
  chartColorsOpacity: 100,        // 1-100
  invertedColors: ["0088CC"],     // Colors for negative values

  // Chart area
  chartArea: { fill: { color: "0088CC" }, border: { pt: "1", color: "f1f1f1" }, roundedCorners: true },
  plotArea: { fill: { color: "FFFFFF" }, border: { pt: "1", color: "f1f1f1" } },
  layout: { x: 0, y: 0, w: 1, h: 1 },  // Positioning within chart area (0-1)

  // Bar-specific
  barDir: "col",                 // "col" | "bar" (horizontal)
  barGrouping: "clustered",      // "clustered" | "stacked" | "percentStacked"
  barGapWidthPct: 150,           // 0-500
  barGapDepthPct: 150,           // 3D
  barOverlapPct: 0,              // -100 to 100
  bar3DShape: "box",             // "box" | "cylinder" | "coneToMax" | "pyramid" | "pyramidToMax"

  // Line-specific
  lineSize: 2,                   // Line thickness (0 = no line)
  lineDash: "solid",             // "solid" | "dash" | "dashDot" | "lgDash" | etc.
  lineCap: "round",              // "flat" | "round" | "square"
  lineSmooth: false,
  lineDataSymbol: "circle",      // "circle" | "dash" | "diamond" | "dot" | "none" | "square" | "triangle"
  lineDataSymbolSize: 6,
  dataNoEffects: false,

  // Pie/Doughnut-specific
  holeSize: 50,                  // Doughnut hole percent

  // Radar
  radarStyle: "standard",        // "standard" | "marker" | "filled"

  // Scatter
  dataLabelFormatScatter: "custom",  // "custom" | "customXY" | "XY"

  // 3D
  v3DRotX: -45, v3DRotY: 180, v3DPerspective: 18, v3DRAngAx: true,

  // Cat Axis
  catAxisHidden: false,
  catAxisTitle: "Categories",
  showCatAxisTitle: false,
  catAxisTitleColor: "000000",
  catAxisTitleFontFace: "Arial",
  catAxisTitleFontSize: 12,
  catAxisTitleRotate: 0,
  catAxisLabelColor: "000000",
  catAxisLabelFontFace: "Arial",
  catAxisLabelFontSize: 10,
  catAxisLabelFontBold: false,
  catAxisLabelPos: "nextTo",     // "low" | "high" | "nextTo"
  catAxisLabelRotate: 0,
  catAxisLabelFrequency: 1,
  catAxisLineShow: true,
  catAxisLineColor: "000000",
  catAxisLineSize: 1,
  catAxisLineStyle: "solid",
  catAxisMajorTickMark: "cross", // "none" | "inside" | "outside" | "cross"
  catAxisMinorTickMark: "none",
  catAxisMaxVal: null,
  catAxisMinVal: null,
  catAxisMajorUnit: null,
  catAxisMinorUnit: null,
  catAxisOrientation: "minMax",  // "minMax" | "maxMin"
  catGridLine: { size: 1, color: "E0E0E0", style: "solid" },

  // Val Axis
  valAxisHidden: false,
  valAxisTitle: "Values",
  showValAxisTitle: false,
  valAxisTitleColor: "000000",
  valAxisTitleFontFace: "Arial",
  valAxisTitleFontSize: 12,
  valAxisTitleRotate: 0,
  valAxisLabelColor: "000000",
  valAxisLabelFontFace: "Arial",
  valAxisLabelFontSize: 10,
  valAxisLabelFontBold: false,
  valAxisLabelFormatCode: "General",
  valAxisLineShow: true,
  valAxisLineColor: "000000",
  valAxisLineSize: 1,
  valAxisLineStyle: "solid",
  valAxisMajorTickMark: "cross",
  valAxisMinorTickMark: "none",
  valAxisMaxVal: null,
  valAxisMinVal: null,
  valAxisMajorUnit: null,
  valAxisMinorUnit: null,
  valAxisOrientation: "minMax",
  valAxisCrossesAt: null,
  valAxisDisplayUnit: null,      // "thousands" | "millions" | "billions" | etc.
  valAxisLogScaleBase: null,
  valGridLine: { size: 1, color: "E0E0E0", style: "solid" },

  // Series Axis (3D)
  serAxisTitle: "Series Axis",
  serAxisTitleColor: "000000",

  // Data element shadow
  shadow: { type: "outer", color: "000000", blur: 3, offset: 2, angle: 90, opacity: 0.35 },

  // Combo chart
  secondaryCatAxis: false,
  secondaryValAxis: false,
}
```

### Combo Charts
```js
slide.addChart(
  [
    { type: pres.ChartType.bar, data: dataBar, options: { barGrouping: "clustered" } },
    { type: pres.ChartType.line, data: dataLine, options: { lineSize: 3, secondaryValAxis: true } },
  ],
  { catAxes: [{}, {}], valAxes: [{}, { valAxisHidden: true }] }
);
```

## 9. Images

```js
// URL path
slide.addImage({ path: "https://example.com/image.jpg", x: 1, y: 1, w: 4, h: 3 });

// Local path
slide.addImage({ path: "images/logo.png", x: 0.5, y: 0.3, w: 1.5, h: 0.6 });

// Base64 data
slide.addImage({ data: "image/png;base64,iVBORw0KGgo...", x: 1, y: 1, w: 4, h: 3 });

// Image options
{
  x: 1, y: 1, w: 4, h: 3,
  path: "image.jpg",           // URL or local path
  data: "image/png;base64,...", // Base64 (alternative to path)
  flipH: false,
  flipV: false,
  rotate: 45,
  rounding: true,               // Circle crop
  transparency: 50,             // 0-100
  sizing: { type: "crop" | "contain" | "cover", w: 2, h: 2, x: 0, y: 0 },
  shadow: { type: "outer", color: "000000", blur: 3, offset: 5, angle: 45, opacity: 0.5 },
  hyperlink: { url: "https://github.com" },
  placeholder: "title" | "body",
  altText: "Description of image",
}
```

## 10. Media (Video/Audio/YouTube)

```js
// Video from path
slide.addMedia({ type: "video", path: "https://example.com/video.mp4", x: 1, y: 1, w: 6, h: 4 });

// Video with base64
slide.addMedia({ type: "video", data: "video/mp4;base64,...", x: 1, y: 1 });

// Audio
slide.addMedia({ type: "audio", path: "../media/sample.mp3", x: 1, y: 1 });

// YouTube (Microsoft 365 only)
slide.addMedia({ type: "online", link: "https://www.youtube.com/embed/Dph6ynRVyUc", x: 1, y: 1 });

// Media options
{
  type: "video" | "audio" | "online",
  path: "video.mp4",
  data: "video/mp4;base64,...",
  link: "https://youtube.com/embed/...",
  cover: "base64-cover-image",     // Poster frame
  extn: "mp4",                     // Extension for paths without it
  x: 1, y: 1, w: 6, h: 4,
}
```

## 11. HTML-to-PowerPoint (Table to Slides)

```js
// Convert HTML table to PPTX slides
pres.tableToSlides("tableElementID");
pres.tableToSlides("tableElementID", {
  master: "MASTER_SLIDE",
  x: 0.5, y: 0.5, w: 11, h: 5.5,
  addHeaderToEach: true,
  addImage: { path: "logo.png", x: 10, y: 0.5, w: 1.2, h: 0.75 },
  addShape: { shape: pres.ShapeType.rect, options: { x: 0, y: 0, w: 1, h: 1, fill: { color: "0088CC" } } },
  addText: { text: "Title", options: { x: 1, y: 0.5, color: "0088CC" } },
  addTable: { rows: [["A","B"]], options: { x: 0, y: 0, w: 5 } },
});
```

## 12. Slide Masters and Placeholders

### Define a Master
```js
pres.defineSlideMaster({
  title: "MASTER_SLIDE",
  background: { color: "FFFFFF" },
  margin: [0.5, 0.5, 0.5, 0.5],  // [top, right, bottom, left] in inches
  objects: [
    { line: { x: 3.5, y: 1.0, w: 6.0, line: { color: "0088CC", width: 5 } } },
    { rect: { x: 0, y: 5.3, w: "100%", h: 0.75, fill: { color: "F1F1F1" } } },
    { text: { text: "Status Report", options: { x: 3.0, y: 5.3, w: 5.5, h: 0.75 } } },
    { image: { x: 11.3, y: 6.4, w: 1.67, h: 0.75, path: "images/logo.png" } },
    // Placeholder
    {
      placeholder: {
        options: { name: "body", type: "body", x: 0.6, y: 1.5, w: 12, h: 5.25 },
        text: "(custom placeholder text!)",
      },
    },
  ],
  slideNumber: { x: 0.3, y: "90%" },
});
```

### Use a Master
```js
let slide = pres.addSlide({ masterName: "MASTER_SLIDE" });
slide.addText("Body Placeholder here!", { placeholder: "body" });
```

## 13. Saving

### writeFile (download or save to disk)
```js
pres.writeFile();                                          // Default: "Presentation.pptx"
pres.writeFile({ fileName: "My Presentation.pptx" });
pres.writeFile({ fileName: "My Presentation.pptx", compression: true });  // Smaller file
pres.writeFile().then(fileName => console.log("Saved: " + fileName));
```

### write (other formats)
```js
pres.write({ outputType: "base64" }).then(data => console.log(data.substring(0, 100)));
pres.write({ outputType: "arraybuffer" }).then(buffer => { /* upload to cloud */ });
pres.write({ outputType: "nodebuffer" }).then(buffer => { fs.writeFileSync("out.pptx", buffer); });
pres.write({ outputType: "blob" }).then(blob => { /* browser download */ });
// Options: "arraybuffer", "base64", "binarystring", "blob", "nodebuffer", "uint8array"
```

### stream (Node only)
```js
pres.stream().then(data => {
  res.writeHead(200, { "Content-disposition": "attachment;filename=out.pptx" });
  res.end(Buffer.from(data, "binary"));
});
```

## 14. Complete Template

```js
import pptxgen from "pptxgenjs";

let pres = new pptxgen();
pres.layout = "LAYOUT_WIDE";
pres.author = "Developer";
pres.title = "My Presentation";
pres.theme = { headFontFace: "Arial", bodyFontFace: "Calibri" };

// Define master
pres.defineSlideMaster({
  title: "MASTER_SLIDE",
  background: { color: "FFFFFF" },
  objects: [
    { rect: { x: 0, y: 0, w: "100%", h: 1.2, fill: { color: "001E40" } } },
  ],
});

// Slide 1
let slide = pres.addSlide({ masterName: "MASTER_SLIDE" });
slide.addText("Hello World", { x: 1, y: 1.5, w: 10, h: 1, fontSize: 44, bold: true, color: "001E40" });

pres.writeFile({ fileName: "demo.pptx" });
```

## 15. Key Differences from python-pptx

| Feature | python-pptx | PptxGenJS |
|---------|-------------|-----------|
| Dimensions | Inches(x) | number (inches) or 'n%' |
| Auto-paging tables | Manual | Built-in `autoPage: true` |
| HTML-to-PPTX | Not available | `tableToSlides()` |
| Base64 images | Manual encoding | `data:` property |
| Word formatting | Run objects | Array of `{text, options}` |
| Colors | `RGBColor()` | Hex strings `"0088CC"` |
| Fills | FillFormat object | `{ color: "0088CC" }` |
| Masters | Complex XML | `defineSlideMaster()` |
| Export formats | Stream only | base64, blob, buffer, stream |
| Async | No | Yes (Promises) |
| Browser support | No | Yes |
