# Design Analysis: Reading a Reference Image

When the user gives you a reference image (or a written brief), extract these tokens before touching CSS. The goal is to get from "this screenshot" to a filled-in `:root` block in 5–10 minutes.

## What to extract

### 1. Embed context (ask if unclear)

Where will this form live?

- **On a dark site** → labels need to be light, submit button is usually light-on-dark
- **On a light site** → labels are dark, submit button is dark-on-light
- **Standalone (Wufoo's hosted page)** → matches the screenshot literally
- **Unknown / "wherever I drop it"** → keep `body` transparent, pick neutral palette that contrasts on common backgrounds

If the reference shows a specific bg (e.g., cream `#F6F1E8`), ask: *"Should the theme enforce this background, or stay transparent so it inherits from the embed?"* Default to transparent.

### 2. Palette (8 colors)

Pull these from the image, eyedropper-style:

| Token | Sample from |
|---|---|
| `--wf-ink` | Heading / strongest label color |
| `--wf-ink-soft` | Body copy if different from labels |
| `--wf-ink-muted` | Placeholder text, sub-labels |
| `--wf-line` | Input bottom border / dividers (idle) |
| `--wf-line-focus` | Input bottom border on focus (often = `--wf-ink`) |
| `--wf-cta-bg` | Submit button background |
| `--wf-cta-fg` | Submit button text |
| `--wf-cta-bg-hover` | Slightly lighter/darker than `--wf-cta-bg` |

Plus `--wf-error` (warm coral or red that reads on the chosen palette) and `--wf-success` (muted green) — pick complementary values if not in the reference.

For dark embeds, `--wf-line` is usually a translucent version of `--wf-ink`: `rgba(<ink-rgb>, 0.22)`. Same for `--wf-ink-muted` at `0.55`.

### 3. Typography

Identify 1–2 font families. Common patterns:

| Vibe | Likely font |
|---|---|
| Geometric, modern, bold uppercase labels | **Manrope**, Inter, DM Sans, Outfit |
| Editorial / luxury / serif placeholder | **Newsreader**, Playfair Display, Cormorant |
| Industrial / brutalist | **JetBrains Mono**, Space Mono, IBM Plex Mono |
| Friendly / startup | **Inter**, Plus Jakarta Sans, Nunito |
| Classic / corporate | **Source Sans 3**, IBM Plex Sans |

Pick from Google Fonts (free, fast, easy `@import`). Use **one font** unless the design clearly pairs two (e.g., serif placeholder + sans labels). Single-font themes feel cleaner and load faster.

Note the **weights** used:
- 300/400 for body/input text
- 600/700/800 for labels and buttons
- Display weight (often 800/900) for the form title

### 4. Label treatment

Three common patterns:

| Treatment | CSS |
|---|---|
| **Uppercase + tracked** (boutique, luxury) | `text-transform: uppercase; letter-spacing: 0.18em; font-weight: 700-800` |
| **Sentence case, bold** (startup, friendly) | `text-transform: none; font-weight: 600; letter-spacing: normal` |
| **Small caps** (editorial) | `font-variant: small-caps; letter-spacing: 0.05em` |

The screenshot tells you which.

### 5. Input style

Pick **one** family and apply consistently. See `input-styles.md` for CSS for each:

| Style | When |
|---|---|
| **Borderless underline** (default in template) | Modern, editorial, luxury, minimalist |
| **Boxed** (1px border, slight radius) | Corporate, classic forms, dashboards |
| **Filled** (background fill, no border) | Friendly, app-like, mobile-feel |
| **Pill** (high border-radius) | Playful, consumer, lifestyle |

### 6. Button style

| Style | Visual cue |
|---|---|
| **Full-width block** | Spans the form width — most boutique forms |
| **Inline auto-width** | Sits at left/right with `padding: 14px 32px` |
| **Outlined / secondary** | Used for prev/reset; transparent bg + border |
| **Rounded vs square** | Match `border-radius` to input style |

Note button height (commonly 48/56/64px), text treatment (uppercase + tracked vs sentence case), and corner radius.

### 7. Spacing rhythm

Eyeball the spacing between fields:

- Tight: 16–20px row gap
- Default: 28–32px row gap (template default)
- Generous: 40–48px row gap (luxury, editorial)

Column gap usually mirrors row gap or is slightly tighter.

## Summary template

After analyzing, fill in this template before writing CSS. Keeps your tokens organized:

```
Theme: <name>
Embed context: <dark | light | transparent>
Reference body bg (for preview only): <hex>

Palette
  ink:        <hex>
  ink-soft:   <hex/rgba>
  ink-muted:  <hex/rgba>
  line:       <hex/rgba>
  line-focus: <hex>
  cta-bg:     <hex>
  cta-fg:     <hex>
  cta-hover:  <hex>
  error:      <hex>

Fonts
  primary: <family> @ <weights>
  display: <family or "same as primary"> @ <weights>

Treatments
  Labels:  <uppercase tracked | sentence case | small caps>
  Inputs:  <underline | boxed | filled | pill>
  Buttons: <full-width | inline> · <square | rounded N> · <uppercase tracked | sentence case>

Spacing
  --wf-gap:        <px>
  --wf-col-gap:    <px>
  --wf-input-size: <px>
  --wf-label-size: <px>
  --wf-radius:     <px>
```

Write this into the comment header at the top of the generated `custom.css` so the user can see at a glance how the theme was configured. It also makes future tweaks much easier — they (or you) can update tokens at `:root` and trust the rest of the theme to pick them up.
