---
name: unlayer-elements
description: Write emails, web pages, documents, PDFs, and images in code with Hyper Elements (@hyper-elements/react), the successor to Unlayer Elements (@unlayer/react-elements), and move templates off the old package with its codemod. Use when writing or generating templates with React components, when a project imports @unlayer/react-elements, or when someone mentions Unlayer Elements or Hyper Elements.
---

# Build templates in code with Hyper Elements

Unlayer Elements (`@unlayer/react-elements`) is now Hyper Elements (`@hyper-elements/react`), with new components. Write new templates with the new package. `ColumnLayouts`, `renderToHtml()`, `renderToJson()`, and styles as props belong to the old package and aren't in the new one.

## Get the full guide

Hyper Elements has its own skill, kept in step with the package:

```bash
npx skills add hyper-elements/hyper-elements
```

Without it, read `node_modules/@hyper-elements/react/GUIDE.md` once the package is installed (every prop and style is in `REFERENCE.md` beside it), or https://hyperelements.dev/llms.txt.

## A template

```bash
npm install @hyper-elements/react
```

```tsx
import { Button, Column, Email, Heading, Text } from "@hyper-elements/react";

export default function Welcome({ name = "Ada" }: { name?: string }) {
  return (
    <Email previewText="Welcome to Acme" style={{ backgroundColor: "#f4f4f5", gap: 16 }}>
      <Column style={{ backgroundColor: "#ffffff", padding: 32, gap: 16 }}>
        <Heading level={1} style={{ fontSize: 24 }}>Welcome, {name}</Heading>
        <Text>Your workspace is ready.</Text>
        <Button href="https://example.com/start">Open your workspace</Button>
      </Column>
    </Email>
  );
}
```

A template has one root: `Email`, `Page`, `Document`, or `Canvas`. Styles go in `style={{ }}`, and space between blocks is the parent's `gap`.

```tsx
import { render } from "@hyper-elements/react";
import { toDesign } from "@hyper-elements/react/builder";
import Welcome from "./welcome";

const { html, text } = await render(<Welcome name="Alex" />);
const { design } = toDesign(<Welcome name="Alex" />); // open in the Unlayer visual editor with loadDesign()
```

## Move existing templates

A project that imports `Body`, `ColumnLayouts`, or `renderToHtml()` from `@unlayer/react-elements` is on the old package. The codemod rewrites its templates and keeps props, loops, and helpers:

```bash
npx @hyper-elements/migrate src/emails --from unlayer-elements          # lists what it would change
npx @hyper-elements/migrate src/emails --from unlayer-elements --write  # replaces them (only files git can restore)
```

Then install `@hyper-elements/react`, and remove `@unlayer/react-elements` once nothing imports it. What the codemod can't move is listed: rewrite that part by hand. Guide: https://hyperelements.dev/docs/migrate/unlayer-elements

React Email templates move with the same tool, which checks each one against its original: `npx @hyper-elements/migrate src/emails`. Guide: https://hyperelements.dev/docs/migrate/react-email
