---
name: unlayer-export
description: Use when exporting content from the Unlayer editor — HTML, PDF, image, plain text export, saving and loading design JSON, auto-save patterns, server-side Cloud API export.
---

# Export Content

## Overview

Unlayer supports multiple export formats. Some are client-side (free), others use the Cloud API (paid).

## Which Export Method?

| Method | Output | Paid? | Use When |
|--------|--------|-------|----------|
| `saveDesign` | Design JSON | No | Save for later editing |
| `exportHtml` | HTML + design JSON | No | Email sending, web publishing |
| `exportPlainText` | Plain text + design | No | SMS, accessibility fallback |
| `exportImage` | PNG URL + design | Yes | Thumbnails, previews, social sharing |
| `exportPdf` | PDF URL + design | Yes | Print-ready documents |
| `exportZip` | ZIP URL + design | Yes | Offline download packages |

> **Critical:** Always save the **design JSON** alongside any export. All export methods return `data.design` — save it so users can edit later.

---

## Save & Load Designs

```javascript
// SAVE — store design JSON in your database
unlayer.saveDesign(async (design) => {
  await fetch('/api/templates', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ design }),  // design is a JSON object
  });
});

// LOAD — restore a saved design (must wait for editor:ready)
unlayer.addEventListener('editor:ready', async () => {
  const response = await fetch('/api/templates/123');
  const saved = await response.json();
  unlayer.loadDesign(saved.design);   // Pass the saved JSON object
});

// LOAD BLANK
unlayer.loadBlank({ backgroundColor: '#ffffff', contentWidth: '600px' });

// LOAD AN UNLAYER TEMPLATE
unlayer.loadTemplate(templateId);     // ID from Unlayer dashboard
```

---

## Export HTML

```javascript
unlayer.exportHtml((data) => {
  const { html, design, chunks } = data;

  // html     — Full HTML document (string)
  // design   — Design JSON (always save this!)
  // chunks   — { css, js, body, fonts, tags }
  //   body   — Just the content inside <body> (no wrapper)
  //   css    — Extracted CSS styles
  //   fonts  — Web fonts used in the design

  // Save both to your backend
  await fetch('/api/templates', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ design, html }),
  });
}, {
  // All options are optional
  cleanup: true,          // Remove editor markup (default: true)
  minify: false,          // Minify HTML output
  inlineStyles: false,    // Move CSS inline (for email clients)
  mergeTags: {},          // Replace merge tags with real values
  title: 'My Email',     // Set HTML <title>
});
```

**Using chunks** — when you need just the body content (no `<!DOCTYPE>` wrapper):

```javascript
unlayer.exportHtml((data) => {
  const { body, css, fonts } = data.chunks;
  const myHtml = `<style>${css}</style>${fonts}${body}`;
});
```

---

## Export Plain Text

```javascript
unlayer.exportPlainText((data) => {
  const { text, design } = data;
  // Use as email plain-text fallback
}, {
  ignorePreheader: false,
  ignoreLinks: false,
  ignoreImages: false,
  mergeTags: {},
});
```

---

## Export Image (Paid — Cloud API)

Generates a PNG screenshot of the design. The returned URL is **temporary (valid ~24 hours)** — download and re-host it if you need it permanently.

**Client-side:**

```javascript
unlayer.exportImage((data) => {
  if (data.error) { console.error(data.error); return; }

  // data.url — temporary PNG URL (expires ~24 hours)
  // Download and save to your storage:
  const imageBlob = await fetch(data.url).then(r => r.blob());
  await uploadToYourStorage(imageBlob, 'thumbnail.png');
}, {
  fullPage: false,           // true = entire page, false = viewport
  width: 600,                // Output width in pixels
  height: undefined,         // Auto-calculated if not set
  deviceScaleFactor: 2,      // 2 = retina quality
  mergeTags: {},
});
```

**Server-side via Cloud API** (get API key from Dashboard > Project > Settings > API Keys):

```javascript
const response = await fetch('https://api.unlayer.com/v2/export/image', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: 'Basic ' + Buffer.from('YOUR_API_KEY:').toString('base64'),
  },
  body: JSON.stringify({
    displayMode: 'email',
    design: designJSON,       // The saved design JSON object
    mergeTags: {},
  }),
});

const data = await response.json();
// data.url — temporary URL, download immediately
```

---

## Export PDF / ZIP (Paid — Cloud API)

```javascript
// PDF
unlayer.exportPdf((data) => {
  if (data.error) { console.error(data.error); return; }
  // data.url — temporary PDF URL
}, { mergeTags: {} });

// ZIP
unlayer.exportZip((data) => {
  if (data.error) { console.error(data.error); return; }
  // data.url — temporary ZIP URL
}, { mergeTags: {} });
```

---

## Auto-Save Pattern

### Design only (fast, free):

```javascript
let saveTimeout;

unlayer.addEventListener('design:updated', () => {
  clearTimeout(saveTimeout);
  saveTimeout = setTimeout(() => {
    unlayer.saveDesign(async (design) => {
      await fetch('/api/templates/123', {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ design }),
      });
    });
  }, 1000); // 1-second debounce
});
```

### Design + HTML (common):

```javascript
let saveTimeout;

unlayer.addEventListener('design:updated', () => {
  clearTimeout(saveTimeout);
  saveTimeout = setTimeout(() => {
    unlayer.exportHtml(async (data) => {
      await fetch('/api/templates/123', {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ design: data.design, html: data.html }),
      });
    });
  }, 1000);
});
```

### Design + HTML + Thumbnail (full):

```javascript
let saveTimeout;

unlayer.addEventListener('design:updated', () => {
  clearTimeout(saveTimeout);
  saveTimeout = setTimeout(() => {
    // Save design + HTML immediately
    unlayer.exportHtml(async (data) => {
      await fetch('/api/templates/123', {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ design: data.design, html: data.html }),
      });
    });

    // Generate thumbnail (slower, paid — debounce longer or do on manual save)
    unlayer.exportImage(async (data) => {
      if (data.error) return;
      // Download temporary URL and re-host
      const blob = await fetch(data.url).then(r => r.blob());
      const formData = new FormData();
      formData.append('thumbnail', blob, 'thumbnail.png');
      await fetch('/api/templates/123/thumbnail', { method: 'PUT', body: formData });
    }, { width: 300, deviceScaleFactor: 1 });
  }, 3000); // Longer debounce for image generation
});
```

---

## Design JSON Quick Reference

The design JSON has this structure (see [references/design-json.md](references/design-json.md) for full TypeScript types):

```
JSONTemplate
├── counters          — Internal counters
├── schemaVersion     — Currently 16
└── body
    ├── rows[]        — Each row contains columns
    │   ├── cells[]   — Column ratios: [1,1] = 50/50
    │   └── columns[]
    │       └── contents[]  — Content items (text, image, button...)
    ├── headers[]     — Same as rows (with headersAndFooters feature)
    ├── footers[]     — Same as rows
    └── values        — Body-level styles (backgroundColor, contentWidth, fontFamily)
```

Content types: `text`, `heading`, `button`, `image`, `divider`, `social`, `html`, `video`, `menu`, `timer`.

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Only saving HTML, not design JSON | **Always save both** — all export methods return `data.design` |
| Calling export before `editor:ready` | Wait for the event first |
| Storing `exportImage` URL permanently | URL expires ~24 hours — download and re-host immediately |
| Not debouncing auto-save | `design:updated` fires on every keystroke — debounce 1-3 seconds |
| Ignoring `chunks` in exportHtml | Use `chunks.body` when you need just content without `<!DOCTYPE>` wrapper |
| Missing API key for image/PDF/ZIP | Cloud API key required — get from Dashboard > Project > Settings > API Keys |

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `exportImage` returns error | Check API key, check Cloud API plan, verify design isn't empty |
| Exported HTML looks different from editor | Use `cleanup: true` (default), check custom CSS |
| `design:updated` fires too often | Always debounce — it fires on every property change |
| Loaded design shows blank | Check `schemaVersion` compatibility, validate JSON structure |

## Resources

- [Export HTML](https://docs.unlayer.com/builder/export-html)
- [Export Image](https://docs.unlayer.com/builder/export-image)
- [Export PDF](https://docs.unlayer.com/builder/export-pdf)
- [Export Plain Text](https://docs.unlayer.com/builder/export-plain-text)
- [Save & Load Designs](https://docs.unlayer.com/builder/load-and-save-designs)
- [Cloud API](https://docs.unlayer.com/api)
