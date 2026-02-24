# Design JSON Schema

## Top-Level Structure

```typescript
interface JSONTemplate {
  counters: Record<string, number>;   // e.g., { u_row: 3, u_column: 4, u_content_text: 5 }
  schemaVersion: number;
  body: {
    id: string;                       // e.g., "_BZCs8S2YW"
    rows: Row[];
    headers: Row[];                   // Only with headersAndFooters feature
    footers: Row[];                   // Only with headersAndFooters feature
    values: BodyValues;
  };
}
```

## Body Values

```typescript
interface BodyValues {
  backgroundColor: string;
  contentWidth: string;             // '600px'
  fontFamily: { label: string; value: string };
  textColor: string;
  linkStyle: {
    inherit: boolean;
    linkColor: string;
    linkHoverColor: string;
    linkUnderline: boolean;
    linkHoverUnderline: boolean;
  };
  // Popup mode only:
  popupPosition: string;            // 'center'
  popupWidth: string;               // '600px'
  popupHeight: string;              // 'auto'
  borderRadius: string;             // '10px'
  contentAlign: string;             // 'center'
  contentVerticalAlign: string;     // 'center'
}
```

## Row Structure

```typescript
interface Row {
  id: string;
  cells: number[];                  // Column ratios: [1,1] = 50/50, [1,2] = 33/66
  columns: Column[];
  values: {
    displayCondition: object | null;
    columns: boolean;               // false = locked columns
    backgroundColor: string;
    columnsBackgroundColor: string;
    backgroundImage: {
      url: string;
      fullWidth: boolean;
      repeat: boolean;
      center: boolean;
      cover: boolean;
    };
    padding: string;                // "0px" or "10px 20px 10px 20px"
    _meta: { htmlID: string; htmlClassNames: string };
  };
}
```

## Column Structure

```typescript
interface Column {
  id: string;
  contents: ContentItem[];
  values: {
    _meta: { htmlID: string; htmlClassNames: string };
    border: object;
    padding: string;
    backgroundColor: string;
  };
}
```

## Content Item Structure

These are the shared properties common to all content items. Each tool type (text, button, image, etc.) adds its own specific properties to `values` — refer to the full JSON schema for tool-specific fields.

```typescript
interface ContentItem {
  id: string;
  type: string;                     // See content types below
  values: {
    // --- Shared properties (all tools) ---
    containerPadding: string;
    anchor: string;
    textAlign: string;              // 'left' | 'center' | 'right'
    lineHeight: string;             // '140%'
    linkStyle: {
      inherit: boolean;
      linkColor: string;
      linkHoverColor: string;
      linkUnderline: boolean;
      linkHoverUnderline: boolean;
    };
    hideDesktop: boolean;
    displayCondition: object | null;
    _meta: { htmlID: string; htmlClassNames: string };
    selectable: boolean;
    draggable: boolean;
    duplicatable: boolean;
    deletable: boolean;
    hideable: boolean;
    // --- Tool-specific properties vary per type ---
    // e.g., text/heading: { text: string }
    // e.g., image: { src: { url, width, height }, alt, action }
    // e.g., button: { text, href, buttonColors, ... }
  };
}
```

## Content Types

`text` | `heading` | `button` | `image` | `divider` | `social` | `html` | `video` | `menu` | `timer` | `table` | `carousel` | `paragraph`

## Validation Constants

| Constant | Valid Values |
|----------|-------------|
| Display modes | `'email'` \| `'web'` \| `'popup'` \| `'document'` |
| Devices | `'desktop'` \| `'mobile'` \| `'tablet'` |
| Text direction | `'ltr'` \| `'rtl'` \| `null` |
| Alignments | `'left'` \| `'center'` \| `'right'` \| `'justify'` |
| Audit severity | `'ERROR'` \| `'WARNING'` |
| Padding format | `'10px'` or `'10px 20px'` or `'10px 20px 30px 40px'` (always px) |

## Real Design Example

```json
{
  "counters": { "u_row": 1, "u_column": 1, "u_content_text": 1 },
  "schemaVersion": 16,
  "body": {
    "id": "_BZCs8S2YW",
    "rows": [{
      "id": "LB2ltnM2OZ",
      "cells": [1],
      "columns": [{
        "id": "HI7oaTElxq",
        "contents": [{
          "id": "PKtuJs3uBF",
          "type": "text",
          "values": {
            "containerPadding": "10px",
            "anchor": "",
            "textAlign": "left",
            "lineHeight": "140%",
            "linkStyle": {
              "inherit": true,
              "linkColor": "#0000ee",
              "linkHoverColor": "#0000ee",
              "linkUnderline": true,
              "linkHoverUnderline": true
            },
            "hideDesktop": false,
            "displayCondition": null,
            "_meta": { "htmlID": "u_content_text_1", "htmlClassNames": "u_content_text" },
            "selectable": true,
            "draggable": true,
            "duplicatable": true,
            "deletable": true,
            "hideable": true,
            "text": "<p>Hello World</p>"
          }
        }],
        "values": {
          "_meta": { "htmlID": "u_column_1", "htmlClassNames": "u_column" },
          "border": {},
          "padding": "0px",
          "backgroundColor": ""
        }
      }],
      "values": {
        "displayCondition": null,
        "columns": false,
        "backgroundColor": "",
        "columnsBackgroundColor": "",
        "backgroundImage": { "url": "", "fullWidth": true, "repeat": false, "center": true, "cover": false },
        "padding": "0px"
      }
    }],
    "headers": [],
    "footers": [],
    "values": {
      "backgroundColor": "#ffffff",
      "contentWidth": "600px",
      "fontFamily": { "label": "Arial", "value": "arial,helvetica,sans-serif" }
    }
  }
}
```
