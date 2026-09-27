---
name: pdfmake
description: Comprehensive expert skill for pdfmake v0.3.x. Covers ALL documentation topics: DDO, Fonts (Custom/Standard), Styling, Columns (Snaking), Tables, Lists, Sections, Images, SVGs, QR, TOC, Outlines, Watermarks, Encryption, and PDF/A.
---

# pdfmake (v0.3.x)

pdfmake is a declarative PDF generation library. You define the document structure in a **Document Definition Object (DDO)**.

## Core Concepts
- **Declarative**: No manual coordinates.
- **Units**: Points (pt) (1/72 inch).
- **v0.3.x Features**: Snaking columns, enhanced security policies, PDF/A support, and improved performance.

## Methods (Server/Client)
- `pdfmake.createPdf(docDefinition, options)`
- `.write(filename)`: Returns Promise (Server).
- `.getBuffer()`, `.getBase64()`, `.getStream()`, `.getDataUrl()`: Returns Promises.
- `.download(filename)`, `.open()`, `.print()`: (Client-side helpers).

## Fonts

### Standard 14 Fonts (ANSI only)
`Courier`, `Helvetica`, `Times`, `Symbol`, `ZapfDingbats`.
```javascript
pdfmake.addFonts({
  Helvetica: { normal: 'Helvetica', bold: 'Helvetica-Bold', italics: 'Helvetica-Oblique', bolditalics: 'Helvetica-BoldOblique' }
});
```

### Custom Fonts
- **Server**: Provide paths to `.ttf` files via `pdfmake.addFonts()`.
- **Client (VFS)**: Use `vfs_fonts.js` (Virtual File System).
- **Client (URL)**: Access fonts via URL (requires `setUrlAccessPolicy`).

## Styling
- **Inline**: `{ text: '...', fontSize: 15, bold: true }`
- **Dictionary**: Define in `styles: { ... }` and use `style: 'styleName'`.
- **Inheritance**: Use `extends: 'otherStyle'` (v0.3.3+).
- **Default Style**: Define `defaultStyle: { font: 'Roboto', fontSize: 10 }`.
- **Properties**: `color`, `background`, `alignment`, `margin`, `decoration`, `lineHeight`, `characterSpacing`, `wordBreak` ('normal', 'break-all').

## Layout Components

### Tables
```javascript
{
  table: {
    headerRows: 1,
    widths: ['*', 'auto', 100, '20%'],
    body: [
      ['Col 1', 'Col 2', 'Col 3', 'Col 4'],
      [{ text: 'Spanned', colSpan: 2 }, '', 'Val', { rowSpan: 2, text: 'RowSpan' }],
      ['Val', 'Val', 'Val', '']
    ]
  },
  layout: 'lightHorizontalLines' // 'noBorders', 'headerLineOnly', or custom layout
}
```

### Columns & Snaking Columns
- **Standard**: `columns: [{ width: '*', text: '...' }, { width: 'auto', text: '...' }]`
- **Snaking (v0.3.5+)**: Newspaper-style flow.
  ```javascript
  { columns: [{ text: longText, width: '*' }, { text: '', width: '*' }], snakingColumns: true }
  ```

### Lists
- **Unordered**: `ul: ['Item 1', 'Item 2']`
- **Ordered**: `ol: ['Item 1', 'Item 2']`
- **Styling**: `markerColor`, `listType` (for `ol`: 'decimal', 'lower-roman', etc).

### Stack & Sections
- **Stack**: `stack: ['Para 1', 'Para 2']` - Group items to apply common styles/margins.
- **Sections**: Divide document with unique settings (inherit from previous).
  ```javascript
  { section: ['Content'], pageSize: 'A4', pageOrientation: 'landscape' }
  ```

## rich media & interactivity
- **images/svgs**: `{ image: 'path/base64/url', width: 100 }` or `{ svg: '<svg>...</svg>' }`.
- **qr codes**: `{ qr: 'data', foreground: 'blue', eccLevel: 'H' }`.
- **links**: `link: 'url'`, `linkToPage: 2`, `linkToDestination: 'id'`.
- **toc**: `{ toc: { title: { text: 'INDEX' } } }` (items need `tocitem: true`).
- **outlines (bookmarks)**: `{ text: 'header', outline: true, outlineexpanded: true }`.

## vector graphics (canvas)
use the `canvas` key for basic shapes.
```javascript
{
  canvas: [
    { type: 'rect', x: 0, y: 0, w: 100, h: 50, color: 'blue', linecolor: 'black', linewidth: 2 },
    { type: 'line', x1: 0, y1: 0, x2: 100, y2: 50, linewidth: 3 },
    { type: 'polyline', points: [{ x: 10, y: 10 }, { x: 50, y: 10 }, { x: 10, y: 50 }], close: true, color: 'red' },
    { type: 'ellipse', x: 150, y: 100, r1: 40, r2: 20, color: 'green' }
  ]
}
```

## page settings, breaks & logic

### background & watermark
```javascript
{
  background: (currentPage, pageSize) => {
    return { text: `confidential - page ${currentPage}`, opacity: 0.1 };
  },
  watermark: { text: 'draft', opacity: 0.3, angle: 45 }
}
```

### page break logic (orphan control)
use `pagebreakbefore` to dynamically decide where to break.
```javascript
{
  pagebreakbefore: (currentNode, followingNodesOnPage, nodesOnNextPage, previousNodesOnPage) => {
    // break if it's a header and it's the last thing on the page
    return currentNode.headlineLevel === 1 && followingNodesOnPage.length === 0;
  }
}
```

## metadata & properties
```javascript
{
  info: {
    title: 'document title',
    author: 'agent',
    subject: 'subject',
    keywords: 'key1, key2',
    creationdate: new Date(),
    moddate: new Date()
  },
  language: 'es-ES'
}
```

## icons
use custom icon fonts (e.g., fontello).
1. define font: `Fontello: { normal: 'fontello.ttf' }`.
2. use character: `{ text: '', font: 'Fontello' }`.

## best practices
1. use **styles** instead of inline properties for maintainability.
2. use **star (*) widths** for responsive tables.
3. use **{ stack: [...] }** to apply styles or margins to a group of elements.
4. always provide **empty placeholders** for `colspan`/`rowspan`.
5. define **access policies** for production environments to prevent ssrf.

