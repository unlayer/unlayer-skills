# Unlayer Elements — Component Reference

All components import from `@unlayer/react-elements`. Use the prop shapes below — verified against `@unlayer/react-elements` `0.1.11+`.

## Root wrappers — `Email` / `Page` / `Document`

Set the render mode (`Email` = email/tables, `Page` = web/flexbox, `Document` = print/PDF) and thread it to all children.

| Prop | Type | Notes |
|---|---|---|
| `backgroundColor` | string | `"#F7F8F9"` |
| `contentWidth` | string | `"600px"` (an email also adds a small gutter each side) |
| `contentAlign` | string | `"center"` |
| `fontFamily` | `{ label, value }` | **object**, e.g. `{ label: "Arial", value: "arial,helvetica,sans-serif" }` |
| `textColor` | string | base text color |
| `previewText` | string | inbox preview text shown in the rendered email HTML |
| `linkStyle` | object | `{ linkColor, linkHoverColor, linkUnderline, linkHoverUnderline }` |

## Layout — `Row` / `Column`

`Row`
| Prop | Type | Notes |
|---|---|---|
| `layout` | `ColumnLayout` | `ColumnLayouts.OneColumn` … `FiveEqual`, `TwoWideNarrow`, etc. |
| `cells` | `number[]` | alternative to `layout`, e.g. `[2, 1]` = 67% / 33% |
| `backgroundColor` | string | |
| `padding` | string | `"20px 40px"`, `"0px"` (always include `px`) |
| `noStackMobile` | boolean | keep columns side-by-side on mobile |

`Column`
| Prop | Type | Notes |
|---|---|---|
| `padding` | string | inner padding |
| `backgroundColor` | string | |
| `borderRadius` | string | `"8px"` |
| `border` | object | `{ borderBottomWidth, borderBottomStyle, borderBottomColor, … }` — per-side; great for hairline dividers |

### ColumnLayouts

`OneColumn` `[1]` · `TwoEqual` `[1,1]` · `TwoWideNarrow` `[2,1]` · `TwoNarrowWide` `[1,2]` · `ThreeEqual` `[1,1,1]` · `ThreeNarrowWideNarrow` `[1,2,1]` · `FourEqual` · `FiveEqual`. The number of `<Column>` children **must** match.

## Content items

### `Heading`
| Prop | Type | Notes |
|---|---|---|
| `headingType` / `level` | `"h1"`–`"h6"` | `level` is an alias |
| `text` / children | string | heading text — **plain string only** |
| `fontSize` | string | `"22px"` |
| `fontWeight` | number | `400`, `700` |
| `color` | string | |
| `lineHeight` | string | `"110%"`, `"1.3"` |
| `letterSpacing` | string | `"0.06em"`, `"0.5px"` |
| `textAlign` | `"left"`/`"center"`/`"right"` | |
| `fontFamily` | `{ label, value }` | object |

Use Heading for **any prominent number/amount**, never Paragraph.

### `Paragraph`
| Prop | Type | Notes |
|---|---|---|
| `html` | string | rich text with `<b> <i> <u> <a> <code>` etc. — use this for any formatting |
| `text` / children | string | plain text (no tags) |
| `fontSize` | string | `"14px"` |
| `color` | string | |
| `lineHeight` | string | `"140%"` |
| `textAlign` / `fontFamily` | | |

### `Button`
| Prop | Type | Notes |
|---|---|---|
| `href` | string \| `{ name, values:{ href, target } }` | plain string auto-wrapped |
| `backgroundColor` / `color` | string | |
| `hoverBackgroundColor` / `hoverColor` | string | |
| `fontSize` (string) / `fontWeight` (number) / `fontFamily` (object) | | |
| `padding` | string | `"14px 28px"` |
| `borderRadius` | string | `"8px"`, `"500px"` (pill) |
| `textAlign` | | |

By default a button is content-sized. For a **fixed / full-width** button, pass the top-level `width` prop: `width="100%"` (or `"200px"`).

### `Image`
| Prop | Type | Notes |
|---|---|---|
| `src` | string \| `{ url, width?, height?, autoWidth?, maxWidth? }` | `width`/`height` = **natural** size |
| `alt` / `altText` | string | |
| `textAlign` | | |
| `action` | `{ name:"web", values:{ href, target } }` | wrap image in a link |

Default is **responsive** (fills the column, capped at natural size). For a **fixed display size**, set it on the src object: `src={{ url, autoWidth: false, maxWidth: "50%" }}` (a percent of the container). Hero images cap at ~500px wide.

### `Divider`
`borderTopWidth` `"1px"` · `borderTopColor` `"#BBBBBB"` · `borderTopStyle` `"solid"` · `textAlign`.

### `Social`
`icons={[{ name: "Facebook", url: "https://…" }]}` · `iconType` `"circle"`/`"rounded"`/`"squared"` · `iconSize` `32` · `spacing` `10` · `align`.

### `Menu`
`items={[{ text: "Home", href: "/" }]}` · `layout` `"horizontal"`/`"vertical"` · `separator` `"|"` · `align`.

### `Table`
For **real tabular data only**: `headers={["A","B"]}` · `data={[["1","2"]]}` · `columns` · `rows` · `enableHeader`. ⚠️ The shorthand renders a bordered, unpadded grid — do **not** use it for "details" or "line items"; use label/value Rows instead (see `patterns.md`).

### `Video`
`videoUrl="https://youtube.com/watch?v=…"` (auto-parsed) or `video={{ type, videoId, thumbnail }}`.

### `Html`
`html="<p>Custom HTML</p>"` — raw passthrough.

## Rendering

```tsx
import { renderToHtml, renderToJson } from '@unlayer/react-elements';
const html = renderToHtml(<Email>…</Email>); // HTML string
const json = renderToJson(<Email>…</Email>); // Unlayer design JSON
```

## Common gotchas

- `fontFamily` must be the `{ label, value }` object. `fontWeight` is a number; `fontSize`/`padding`/`lineHeight`/`letterSpacing` are strings with units.
- Column count must match the Row layout, or the row renders empty.
- `Image` `width`/`height` are the natural size; for a fixed display set `autoWidth:false` + a percent `maxWidth` on the src object.
- `<Table headers/data>` = ugly spreadsheet — avoid for details; use label/value Rows.
- Heading/Paragraph children must be a plain string — use `Paragraph html` for inline formatting.
