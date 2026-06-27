# Unlayer Elements — Section Recipes

Ready-to-use building blocks for emails that look genuinely premium, not "agent-generated." Pick a real brand palette; put white content rows on a light-gray `Email backgroundColor` for a card feel; keep 40–48px horizontal padding with intentional vertical rhythm. All shapes here are verified against `@unlayer/react-elements` `0.1.11+`.

All Rows must be **direct children of the root** — when you factor a section into a helper, the helper must `return` a `<Row>` and be called inline (`{section(...)}`), never used as `<section/>`.

## Brand header (logo)

```tsx
<Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="28px 40px 12px 40px">
  <Column>
    {/* a real logo image, or a styled wordmark for reliability */}
    <Heading headingType="h2" fontSize="22px" fontWeight={700} color={BRAND} textAlign="left">brandname</Heading>
  </Column>
</Row>
```

## Hero: eyebrow + headline + subhead + CTA

```tsx
<Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="20px 40px 4px 40px">
  <Column>
    <Heading headingType="h4" fontSize="12px" fontWeight={700} color={BRAND} letterSpacing="0.08em">NEW</Heading>
    <Heading headingType="h1" fontSize="32px" fontWeight={700} color={INK} lineHeight="118%">Your headline here</Heading>
    <Paragraph html="A short, warm one-liner that sets up the value." fontSize="16px" color={MUTED} lineHeight="155%" />
  </Column>
</Row>
<Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="20px 40px 32px 40px">
  <Column>
    {/* full-width CTA: pass the top-level width prop */}
    <Button width="100%" href="#" backgroundColor={BRAND} color="#FFFFFF"
      fontSize="16px" fontWeight={700} padding="15px 28px" borderRadius="10px" textAlign="center">Get started</Button>
  </Column>
</Row>
```

## Details / line-items — label/value rows (NOT a Table)

The single most important recipe. `Table` shorthand renders an ugly bordered spreadsheet; build clean rows with a hairline drawn by the Column's `border` object.

```tsx
const HAIRLINE = { borderBottomWidth: '1px', borderBottomStyle: 'solid', borderBottomColor: '#EBEBEB' };
function detailRow(label: string, value: string, last = false) {
  const cell = { padding: '14px 0', border: last ? undefined : HAIRLINE };
  return (
    <Row layout={ColumnLayouts.TwoEqual} backgroundColor="#FFFFFF" padding="0 40px">
      <Column {...cell}><Paragraph html={label} fontSize="14px" color={MUTED} lineHeight="140%" /></Column>
      <Column {...cell}><Paragraph html={`<b>${value}</b>`} fontSize="14px" color={INK} textAlign="right" lineHeight="140%" /></Column>
    </Row>
  );
}

// usage:
{detailRow("Order", "#2705-0042")}
{detailRow("Date", "Jul 1, 2026")}
{detailRow("Total", "$65.50", true)}   // last row: no divider, often bolder/bigger
```

Add a section label above it:

```tsx
<Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="16px 40px 2px 40px">
  <Column><Heading headingType="h4" fontSize="12px" fontWeight={700} color={MUTED} letterSpacing="0.06em">SUMMARY</Heading></Column>
</Row>
```

## Feature grid (2×2)

```tsx
<Row layout={ColumnLayouts.TwoEqual} backgroundColor="#FFFFFF" padding="24px 40px 8px 40px">
  <Column>
    <Heading headingType="h3" fontSize="17px" fontWeight={700} color={INK}>Feature one</Heading>
    <Paragraph html="One clear sentence about it." fontSize="14px" color={MUTED} lineHeight="155%" />
  </Column>
  <Column>
    <Heading headingType="h3" fontSize="17px" fontWeight={700} color={INK}>Feature two</Heading>
    <Paragraph html="One clear sentence about it." fontSize="14px" color={MUTED} lineHeight="155%" />
  </Column>
</Row>
```

## Stat / metric row (3 numbers)

```tsx
<Row layout={ColumnLayouts.ThreeEqual} backgroundColor="#FFFFFF" padding="0 40px">
  <Column>
    <Heading headingType="h2" fontSize="28px" fontWeight={700} color={INK} textAlign="center">1.2M</Heading>
    <Paragraph html="API calls" fontSize="12px" color={MUTED} textAlign="center" />
  </Column>
  {/* repeat */}
</Row>
```

Numbers are **always `<Heading>`** — a Paragraph's margin scales with font size and balloons the layout.

## A fixed-size / centered image

```tsx
<Image src={{ url: 'https://…/logo.png', autoWidth: false, maxWidth: '50%' }} altText="Logo" textAlign="center" />
```

`width`/`height` on the src object are the **natural** size and stay responsive; `autoWidth:false` + a percent `maxWidth` pins the display size. (Hero images cap at ~500px wide.)

## Section divider

```tsx
<Row layout={ColumnLayouts.OneColumn} backgroundColor="#FFFFFF" padding="0 40px">
  <Column><Divider borderTopWidth="1px" borderTopColor="#EBEBEB" borderTopStyle="solid" /></Column>
</Row>
```

## Footer (links / social / legal)

```tsx
<Row layout={ColumnLayouts.OneColumn} padding="20px 40px 40px 40px">
  <Column>
    <Menu items={[{ text: "Docs", href: "#" }, { text: "Unsubscribe", href: "#" }]} layout="horizontal" separator="·" align="center" />
    <Paragraph html="Company · City, State" fontSize="12px" color={MUTED} textAlign="center" />
  </Column>
</Row>
```

## Palette starters

| Brand vibe | Background | Accent | Ink | Muted | Hairline |
|---|---|---|---|---|---|
| Light SaaS | `#F6F9FC` | `#635BFF` | `#1A1F36` | `#697386` | `#E3E8EE` |
| Warm consumer | `#F7F7F7` | `#FF385C` | `#222222` | `#717171` | `#EBEBEB` |
| Dark / music | `#121212` | `#1ED760` | `#FFFFFF` | `#B3B3B3` | `#2A2A2A` |

## Do / Don't

- ✅ Label/value Rows with a Column `border` hairline. ❌ `<Table headers/data>` for details.
- ✅ `<Heading>` for amounts/stats. ❌ `<Paragraph>` for big numbers.
- ✅ `Paragraph html="… <b>x</b> <a href>…"` for inline formatting. ❌ JSX children with tags (won't render).
- ✅ Fixed image via `src={{ url, autoWidth:false, maxWidth:"50%" }}`. ❌ a px `width` expecting a fixed box (it's the natural size; default is responsive).
- ✅ object `fontFamily={{ label, value }}`, number `fontWeight`, string `fontSize`. ❌ string `fontFamily` / bare-number sizes.
- ✅ eyebrow + bold headline + generous padding. ❌ flat text on a single background with cramped spacing.
