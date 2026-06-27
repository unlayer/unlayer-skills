---
name: unlayer-elements
description: Build emails, pages, and documents in code with @unlayer/react-elements — React components that render to email-safe (table) HTML, responsive web HTML, or print/PDF. Use when generating templates programmatically without the visual editor.
---

# Build with Unlayer Elements (code-first)

## Overview

`@unlayer/react-elements` is a set of React components for building emails, pages, and documents **in code** — no visual editor. Write JSX once and render it to:

- **email** — table-based HTML (Outlook/Gmail/Yahoo safe)
- **web** — responsive div/flexbox HTML
- **document** — print / PDF-optimized HTML

The output is a faithful reproduction of what the Unlayer editor produces, so designs round-trip into the editor as JSON. Full SSR support (Next.js, Remix, `renderToString`).

### When to use this skill vs the editor

| You want to… | Use |
|---|---|
| Generate/assemble templates **in code** (AI generation, programmatic emails, design systems) | **this skill** (`@unlayer/react-elements`) |
| Embed a **drag-and-drop visual editor** in your app | `unlayer-integration` |
| Export HTML/PDF from a saved design / Cloud API | `unlayer-export` |

## Install

```bash
npm view @unlayer/react-elements version   # verify the published version first
npm install @unlayer/react-elements
```

> This skill targets `@unlayer/react-elements` **0.1.11+** (`npm view @unlayer/react-elements version` to check) and uses only forms verified against it. The natural forms below — string/number sizes, object or string `fontFamily`, factored-out style objects — all type-check on 0.1.11+; on older versions, upgrade.

## Mental model — the structure is strict

```
<Email> | <Page> | <Document>        ← root wrapper, sets the render mode
  <Row layout={ColumnLayouts.X}>     ← layout container; column count MUST match the layout
    <Column>                         ← only Columns go directly in a Row
      <Heading /> <Paragraph />      ← items only inside Columns (no nesting)
      <Button /> <Image /> ...
```

- `<Email>` = email mode, `<Page>` = web, `<Document>` = print. They thread the mode to all children.
- A `<Row>` takes `layout={ColumnLayouts.TwoEqual}` (or `cells={[1,1]}`) and must contain exactly the matching number of `<Column>` children.
- Items (`Heading`, `Paragraph`, `Button`, `Image`, `Divider`, `Social`, `Menu`, `Table`, `Video`, `Html`) go **inside** Columns, never directly in a Row.

## Render

```tsx
import { renderToHtml, renderToJson } from '@unlayer/react-elements';

const html = renderToHtml(<Email>…</Email>);   // final HTML string (no hydration markers)
const json = renderToJson(<Email>…</Email>);   // Unlayer design JSON (import into the editor)
```

> Pass the **root element** (`<Email>`/`<Page>`/`<Document>`) directly. If you wrap your template in your own component, **call it** — `renderToJson(MyEmail())` — don't pass `<MyEmail />` (the renderers read the root element type, so a wrapper component isn't recognized).

## Complete example

```tsx
import {
  Email, Row, Column, ColumnLayouts,
  Heading, Paragraph, Button,
} from '@unlayer/react-elements';

const sans = { label: 'Sans', value: "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Arial, sans-serif" };

export function Welcome() {
  return (
    <Email backgroundColor="#F6F9FC" contentWidth="600px" fontFamily={sans} previewText="You're all set — get started inside.">
      <Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="32px 40px 8px 40px">
        <Column>
          <Heading headingType="h4" fontSize="12px" fontWeight={700} color="#635BFF" letterSpacing="0.08em">
            WELCOME
          </Heading>
          <Heading headingType="h1" fontSize="30px" fontWeight={700} color="#1A1F36" lineHeight="120%">
            You're all set.
          </Heading>
          <Paragraph html="Everything's ready — here's how to get started." fontSize="16px" color="#697386" lineHeight="155%" />
        </Column>
      </Row>
      <Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="16px 40px 36px 40px">
        <Column>
          <Button href="https://example.com" backgroundColor="#635BFF" color="#FFFFFF"
            fontSize="16px" fontWeight={600} padding="14px 28px" borderRadius="8px" textAlign="center">
            Get started
          </Button>
        </Column>
      </Row>
    </Email>
  );
}
```

## Critical conventions — use canonical shapes

These shapes work in every version. Non-canonical forms (a string `fontFamily`, a bare-number `fontSize`) are not reliable.

- **fontFamily is an object**: `{ label, value }`, e.g. `{ label: 'Arial', value: 'arial, sans-serif' }`. Define it once and reuse.
- **fontWeight is a number** (`700`). **fontSize / padding / lineHeight / letterSpacing are strings** with units: `fontSize="28px"`, `padding="20px 40px"` (and `"0px"`, never `"0"`), `lineHeight="150%"` (or unitless `"1.5"`), `letterSpacing="0.08em"`.
- **Big numbers / amounts must be `<Heading>`, never `<Paragraph>`** — a paragraph's margin scales with font size and balloons the layout.
- **Column count must match the Row layout** (`TwoEqual` → exactly 2 `<Column>`), or the row renders empty.
- **Inline formatting goes in `<Paragraph html="…">`** with real tags (`<b>`, `<a href>`). Heading/Paragraph **children must be a plain string** — passing JSX with tags as children does not render correctly. Use `html` for anything formatted.
- **`previewText`** on the root shows inbox preview text in the rendered email HTML.

## Image sizing — match Unlayer's model

`src` is a URL string **or** `{ url, width?, height? }`, where **`width`/`height` are the image's NATURAL size**, not a display width.

- **Default is responsive** — the image fills its column, capped at its natural size. `<Image src="…" />` or `<Image src={{ url, width: 400, height: 260 }} />` both render responsively. Provide the natural `width`/`height` when you know them.
- **For a fixed display size, set it on the src object as `autoWidth:false` + `maxWidth` (a percent of the container):**
  ```tsx
  <Image src={{ url: 'https://…', autoWidth: false, maxWidth: '50%' }} altText="…" />
  ```
- **Heads-up:** large / hero images currently render at up to ~500px wide and won't go fully edge-to-edge — center them on the background.

## Making it actually look good

Agent-generated emails often look cheap. To make them genuinely nice:

- **Never use `<Table headers={…} data={…}>` for a details list or line items.** The shorthand renders a bordered, zero-padding spreadsheet. Build **label/value rows** instead — a muted label left, a bold value right, separated by a hairline using the Column's `border` object:

  ```tsx
  const HAIRLINE = { borderBottomWidth: '1px', borderBottomStyle: 'solid', borderBottomColor: '#EBEBEB' };
  function detailRow(label: string, value: string, last = false) {
    const cell = { padding: '14px 0', border: last ? undefined : HAIRLINE };
    return (
      <Row layout={ColumnLayouts.TwoEqual} backgroundColor="#FFFFFF" padding="0 40px">
        <Column {...cell}><Paragraph html={label} fontSize="14px" color="#717171" /></Column>
        <Column {...cell}><Paragraph html={`<b>${value}</b>`} fontSize="14px" color="#222222" textAlign="right" /></Column>
      </Row>
    );
  }
  // call it inline: {detailRow('Total', '$1,248.00', true)} — NOT <detailRow/>;
  // a Row must be a direct child of the root, so a section helper must return a <Row>.
  ```

- Use the brand's **real palette**; put white content rows on a light-gray `Email backgroundColor` for a card feel.
- Add an **eyebrow**: a small uppercase `<Heading fontSize="12px" fontWeight={700} letterSpacing="0.08em">` in an accent color above the headline.
- Generous, consistent **40–48px horizontal padding** and intentional vertical rhythm. Solid brand CTA with `borderRadius` ~8–10px.
- **Full-width button** (when you want one) — pass the top-level `width` prop (`"100%"`, or `"200px"` for a fixed width):
  ```tsx
  <Button width="100%" href="…" backgroundColor="…" color="#FFFFFF"
    fontSize="16px" fontWeight={700} padding="14px 28px" borderRadius="8px" textAlign="center">Get started</Button>
  ```

## Component quick reference

`Email`/`Page`/`Document` (`backgroundColor`, `contentWidth`, `fontFamily`, `textColor`, `previewText`) · `Row` (`layout`/`cells`, `backgroundColor`, `padding`) · `Column` (`padding`, `backgroundColor`, `borderRadius`, `border`) · `Heading` (`headingType` h1–h6 / `level`, `text`/string children, `fontSize`, `fontWeight`, `color`, `lineHeight`, `letterSpacing`, `textAlign`, `fontFamily`) · `Paragraph` (`html` or string children/`text`) · `Button` (`href`, `backgroundColor`, `color`, `padding`, `borderRadius`, `textAlign`; `size` via `values` for a fixed width) · `Image` (`src` string or object, `altText`, `action`) · `Divider` (`borderTop*`) · `Social` (`icons={[{name,url}]}`) · `Menu` (`items={[{text,href}]}`) · `Table` (`headers`/`data` — for real tabular data only) · `Video` (`videoUrl`) · `Html` (`html`).

See `references/component-reference.md` for full prop tables and `references/patterns.md` for ready-to-use section recipes.

## Out of scope

This skill is the **code-first** library. For the drag-and-drop **visual editor** SDK, see `unlayer-integration`; for export/Cloud API, see `unlayer-export`.
