# Type B — Print-First Document Reference

For A4 HKO brochures and print worksheets.

---

## Page Shell

```html
<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Title]</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@300;400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
  <style>
    :root {
      --sit-color: [situation color];
      --sit-light: [10% tint of sit-color];
      --ink: #1a1a1a;
      --ink-soft: #555555;
      --ink-faint: #999999;
      --paper: #FDFCF9;
      --rule: #E0DDD6;
      --field-bg: #F5F4F0;
      --font-head: 'DM Serif Display', Georgia, serif;
      --font-body: 'DM Sans', Helvetica, sans-serif;
      --font-mono: 'DM Mono', monospace;
      --page-w: 210mm;
      --page-h: 297mm;
      --mg: 11mm;
    }
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { font-size: 9.5pt; }
    body { font-family: var(--font-body); color: var(--ink); background: #ccc; line-height: 1.5; }

    .page {
      width: var(--page-w); min-height: var(--page-h);
      background: var(--paper); margin: 8mm auto; padding: var(--mg);
      padding-top: calc(var(--mg) + 2mm);
      position: relative; display: flex; flex-direction: column; gap: 4mm;
      page-break-after: always;
    }
    .page::before {
      content: ''; position: absolute; top: 0; left: 0; right: 0; height: 4px;
      background: var(--sit-color);
    }
    .page-tag {
      position: absolute; top: 5.5mm; right: var(--mg);
      font-family: var(--font-mono); font-size: 6.5pt; color: var(--ink-faint); letter-spacing: 0.05em;
    }
    .page-footer {
      margin-top: auto; padding-top: 2mm; border-top: 1px solid var(--rule);
      display: flex; justify-content: space-between; align-items: center;
    }
    .page-footer span { font-family: var(--font-mono); font-size: 6pt; color: var(--ink-faint); letter-spacing: 0.05em; }

    @media print {
      body { background: white; }
      .page { margin: 0; box-shadow: none; }
      @page { size: A4 portrait; margin: 0; }
    }
    @media screen {
      body { padding: 8mm 0 20mm; }
      .page { box-shadow: 0 4px 24px rgba(0,0,0,0.14); border-radius: 2px; }
    }
  </style>
</head>
<body>
  <div class="page">
    <span class="page-tag">SEITE 1 / 4</span>
    <!-- page content -->
    <div class="page-footer">
      <span>ABU REFORM 2030 — HKO LERNSITUATION</span>
      <span>[TOPIC] — SITUATION [X]</span>
      <span>S. 1 / 4</span>
    </div>
  </div>
  <!-- more .page divs -->
</body>
</html>
```

---

## Typography Scale

```css
h1 { font-family: var(--font-head); font-size: 22pt; color: var(--sit-color); line-height: 1.1; letter-spacing: -0.02em; }
h2 { font-family: var(--font-head); font-size: 12pt; color: var(--ink); letter-spacing: -0.01em; }
h3 { font-family: var(--font-body); font-size: 7.5pt; font-weight: 600; text-transform: uppercase; letter-spacing: 0.08em; color: var(--sit-color); }
p { font-size: 8.5pt; line-height: 1.55; color: var(--ink-soft); }
```

---

## Section Header

```html
<div class="section-head">
  <div class="s-icon">1</div>
  <h2>Leitfragen</h2>
  <span style="margin-left:auto; font-family:var(--font-mono); font-size:7pt; color:var(--ink-faint);">Woche 1 | Lehrmittel S. XX</span>
</div>
```
```css
.section-head { display: flex; align-items: center; gap: 3mm; padding-bottom: 1.5mm; border-bottom: 1.5px solid var(--sit-color); margin-bottom: 3mm; }
.s-icon { width: 18px; height: 18px; background: var(--sit-color); color: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 8.5pt; font-weight: 600; flex-shrink: 0; font-family: var(--font-mono); }
```

---

## Chips / Labels

```html
<span class="chip">1.5 Ökologie</span>
<span class="chip chip-outline">Situation A</span>
```
```css
.chip { display: inline-block; font-family: var(--font-mono); font-size: 6.5pt; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase; background: var(--sit-color); color: white; padding: 1.5px 6px; border-radius: 2px; }
.chip-outline { background: transparent; color: var(--sit-color); border: 1px solid var(--sit-color); }
```

---

## Leitfragen (Guided questions with answer fields)

```html
<div class="lq">
  <div class="lq-n">1</div>
  <div>
    <div class="lq-level">K2 — Verstehen | <span class="lq-ref">Lehrmittel S. 39</span></div>
    <div class="lq-text">Erkläre den Unterschied zwischen...</div>
  </div>
  <div class="lq-field"></div>
</div>
```
```css
.lq { display: grid; grid-template-columns: 22px 1fr; gap: 0 3mm; margin-bottom: 3.5mm; }
.lq-n { width: 22px; height: 22px; background: var(--sit-color); color: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-family: var(--font-mono); font-size: 8pt; font-weight: 500; flex-shrink: 0; margin-top: 0.5mm; }
.lq-level { font-family: var(--font-mono); font-size: 6pt; color: var(--ink-faint); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 0.5mm; }
.lq-ref { color: var(--sit-color); }
.lq-text { font-size: 8.5pt; color: var(--ink); font-weight: 500; margin-bottom: 1mm; line-height: 1.4; }
.lq-field { grid-column: 2; background: var(--field-bg); border: 1px solid var(--rule); border-radius: 2px; min-height: 13mm; padding: 2mm; margin-top: 1mm; }
```

---

## Situation Box

```html
<div class="sit-box">
  <div class="sit-box-head">
    <span class="sit-letter">A</span>
    <span class="sit-title">«Titel der Situation»</span>
    <span class="sit-pages">Lehrmittel S. XX–XX</span>
  </div>
  <div class="sit-body">
    <p>[Situation narrative]</p>
    <p class="sit-q">→ Kernfrage der Situation?</p>
  </div>
</div>
```
```css
.sit-box { border: 2px solid var(--sit-color); border-radius: 4px; overflow: hidden; }
.sit-box-head { background: var(--sit-color); color: white; padding: 2.5mm 4mm; display: flex; align-items: center; gap: 3mm; }
.sit-letter { font-family: var(--font-head); font-size: 20pt; line-height: 1; opacity: 0.65; }
.sit-title { font-family: var(--font-head); font-size: 12pt; line-height: 1.2; }
.sit-pages { margin-left: auto; font-family: var(--font-mono); font-size: 6.5pt; opacity: 0.8; white-space: nowrap; }
.sit-body { padding: 4mm; }
.sit-body p { font-size: 9pt; color: var(--ink); line-height: 1.6; }
.sit-q { margin-top: 3mm; padding-top: 3mm; border-top: 1px dashed var(--rule); font-size: 8.5pt; font-style: italic; color: var(--sit-color); font-weight: 500; }
```

---

## Mindmap Zone

```html
<div class="mm-zone">
  <div class="mm-label">MINDMAP HIER ZEICHNEN — FOTOGRAFIEREN — EINREICHEN</div>
</div>
```
```css
.mm-zone { border: 1.5px solid var(--rule); border-radius: 4px; flex: 1; min-height: 44mm; position: relative; }
.mm-label { position: absolute; top: 2.5mm; left: 3mm; font-family: var(--font-mono); font-size: 6.5pt; color: var(--ink-faint); text-transform: uppercase; letter-spacing: 0.08em; }
```

---

## Handlungsprodukt Box

```html
<div class="hp-box">
  <div class="hp-head">
    <h3>Format: [Format description]</h3>
    <span style="margin-left:auto; font-size:7.5pt; color:var(--sit-color); font-weight:500;">[Scope]</span>
  </div>
  <div class="hp-body">
    <p>[Instructions]</p>
    <div class="hp-steps">
      <div class="hp-step">
        <div class="hp-step-n">1</div>
        <div>
          <div class="hp-step-label">Step label</div>
          <div class="hp-step-hint">Hint text</div>
        </div>
      </div>
    </div>
  </div>
</div>
<div class="hp-write">
  <div class="hp-write-label">HIER SCHREIBEN / SKIZZIEREN</div>
  <div class="hp-write-note">Weitere Blätter können als Anhang abgegeben werden</div>
</div>
```
```css
.hp-box { border: 2px solid var(--sit-color); border-radius: 4px; overflow: hidden; }
.hp-head { background: var(--sit-light); padding: 2mm 4mm; border-bottom: 1.5px solid var(--sit-color); display: flex; align-items: center; gap: 3mm; }
.hp-body { padding: 3mm 4mm; }
.hp-steps { display: flex; flex-direction: column; gap: 1.5mm; }
.hp-step { display: grid; grid-template-columns: 20px 1fr; gap: 0 2.5mm; }
.hp-step-n { width: 20px; height: 20px; background: var(--sit-color); color: white; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-family: var(--font-mono); font-size: 7.5pt; font-weight: 500; flex-shrink: 0; }
.hp-step-label { font-size: 7.5pt; font-weight: 600; color: var(--ink); }
.hp-step-hint { font-size: 7pt; color: var(--ink-faint); line-height: 1.35; }
.hp-write { border: 1.5px dashed var(--sit-color); border-radius: 4px; flex: 1; min-height: 70mm; position: relative; background: white; margin-top: 3mm; }
.hp-write-label { position: absolute; top: 2.5mm; left: 3mm; font-family: var(--font-mono); font-size: 6.5pt; color: var(--sit-color); text-transform: uppercase; letter-spacing: 0.08em; }
.hp-write-note { position: absolute; bottom: 2.5mm; right: 3mm; font-family: var(--font-mono); font-size: 6pt; color: var(--ink-faint); font-style: italic; }
```

---

## Reflexion Questions

```html
<div class="rx-item">
  <div class="rx-q"><span class="rx-n">R1</span>Was hat mich überrascht?</div>
  <div class="rx-field"></div>
</div>
```
```css
.rx-item { margin-bottom: 3.5mm; }
.rx-q { font-size: 8.5pt; font-weight: 600; color: var(--ink); margin-bottom: 1.5mm; display: flex; gap: 2mm; }
.rx-n { font-family: var(--font-mono); color: var(--sit-color); font-size: 8pt; flex-shrink: 0; }
.rx-field { background: var(--field-bg); border: 1px solid var(--rule); border-radius: 2px; min-height: 15mm; padding: 2mm; }
```

---

## QR Codes for External Content

**Rule: Any time the teacher provides a URL, embed URL, or SRF/video link, generate a QR code for it in the printed document.** QR codes let students scan and open videos or links directly from paper without typing long URLs.

### Required: Add qrcode.js to `<head>`

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
```

### CSS for QR box

```css
.qr-box { width: 22mm; height: 22mm; background: white; border: 1px solid var(--rule); border-radius: 2px; display: flex; align-items: center; justify-content: center; overflow: hidden; }
.qr-box canvas, .qr-box img { width: 100% !important; height: 100% !important; }
.qr-label { font-family: var(--font-mono); font-size: 5.5pt; color: var(--ink-faint); text-transform: uppercase; letter-spacing: 0.08em; text-align: center; margin-top: 1mm; }
```

### Standalone QR — inline in text or beside a resource

```html
<div style="display:flex; flex-direction:column; align-items:center; gap:1mm; flex-shrink:0;">
  <div class="qr-box" id="qr-video1"></div>
  <div class="qr-label">Video ansehen</div>
</div>
```

### Video Card with embedded QR (recommended for media resources)

Use this pattern when the teacher provides a video/link alongside a description:

```html
<div style="border:1.5px solid var(--rule); border-radius:4px; overflow:hidden;">
  <div style="background:var(--sit-light); border-bottom:1.5px solid var(--sit-color); padding:2mm 4mm; display:flex; align-items:center; justify-content:space-between;">
    <span style="font-size:8pt; font-weight:600; color:var(--sit-color);">📺 [Video title]</span>
    <span style="font-family:var(--font-mono); font-size:6pt; color:var(--ink-faint);">ca. X Min.</span>
  </div>
  <div style="padding:3mm 4mm; display:flex; gap:4mm; align-items:flex-start;">
    <div style="flex:1;">
      <p>[Short description of the video/resource content]</p>
    </div>
    <div style="flex-shrink:0; display:flex; flex-direction:column; align-items:center; gap:1mm;">
      <div class="qr-box" id="qr-video1"></div>
      <div class="qr-label">Video aufrufen<br>via QR-Code</div>
    </div>
  </div>
</div>
```

### JS: Generate all QR codes on DOMContentLoaded

Always generate QR codes in a single `DOMContentLoaded` listener at the bottom of `<body>`. Use the `--sit-color` hex value as `colorDark`:

```html
<script>
document.addEventListener('DOMContentLoaded', function() {
  new QRCode(document.getElementById('qr-video1'), {
    text: 'https://[full-url-here]',
    width: 84,
    height: 84,
    colorDark: '[sit-color hex, e.g. #2471A3]',
    colorLight: '#ffffff',
    correctLevel: QRCode.CorrectLevel.M
  });
  // Add more QRCode() calls for each URL
});
</script>
```

### URL extraction rules

| Input type | Use as QR target |
|---|---|
| SRF embed `urn:srf:video:XXXX` | `https://www.srf.ch/play/tv/redirect/detail/XXXX` |
| Full `https://` URL | Use as-is |
| YouTube embed `youtube.com/embed/ID` | `https://www.youtube.com/watch?v=ID` |
| Any other embed URL | Extract the canonical URL and use that |

Each URL gets its own unique element `id` (e.g. `qr-video1`, `qr-video2`, `qr-article`).

---

## Assessment Table (Bewertungstabelle)

```html
<table class="bw-table">
  <thead>
    <tr><th>Produkt</th><th>Abgabe</th><th>Gewicht</th><th>Bewertungsart</th></tr>
  </thead>
  <tbody>
    <tr><td>Leitfragen</td><td>Woche 1</td><td class="w">20%</td><td>Inhaltliche Korrektheit</td></tr>
    <tr class="total"><td colspan="2">Gesamtnote</td><td class="w">100%</td><td></td></tr>
  </tbody>
</table>
```
```css
.bw-table { width: 100%; border-collapse: collapse; font-size: 7.5pt; }
.bw-table th { background: var(--sit-color); color: white; font-weight: 600; font-size: 7pt; text-transform: uppercase; letter-spacing: 0.06em; padding: 2mm 3mm; text-align: left; }
.bw-table td { padding: 1.8mm 3mm; border-bottom: 1px solid var(--rule); vertical-align: middle; color: var(--ink-soft); }
.bw-table tr:nth-child(even) td { background: var(--field-bg); }
.bw-table .w { font-family: var(--font-mono); font-weight: 500; color: var(--sit-color); }
.bw-table .total td { font-weight: 600; color: var(--ink); border-top: 2px solid var(--sit-color); border-bottom: none; }
```
