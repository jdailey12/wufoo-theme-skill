# Converting App Form Styles to Wufoo

Use this when the user wants a Wufoo form to match an existing native form in a website or app. The goal is to transfer the visual system, not the source markup.

## First pass: find the source form

Search the project before asking questions:

- Forms and routes: `form`, `contact`, `lead`, `signup`, `rsvp`, `application`, `checkout`
- Components: `.astro`, `.tsx`, `.jsx`, `.svelte`, `.vue`, `.html`
- Styles: `.css`, `.scss`, `.sass`, `.module.css`, `.module.scss`
- Tokens/config: `tailwind.config.*`, `theme.*`, `tokens.*`, `variables.*`, `globals.css`, `app.css`

Read the form component and the closest styles first. Then trace imports outward only as needed.

## Extract the resolved design

Capture final values, not class names:

```
Source form: <path/component>
Field model: <labels, types, helper/error text, CTA>
Embed context: <light/dark/transparent/unknown>

Palette
  bg/surface:
  ink:
  ink-muted:
  line:
  focus:
  cta-bg:
  cta-fg:
  error:

Typography
  family:
  label:
  input:
  button:

Controls
  input style: <underline | boxed | filled | pill>
  radius:
  padding:
  height/min-height:
  focus state:
  disabled state:
  error state:
  button style:

Layout
  max width:
  row gap:
  column gap:
  columns:
```

If styles are spread across runtime CSS or component-library themes, run the app when practical and inspect computed styles in a browser. Use computed styles as the source of truth for font size, line height, colors, border, radius, padding, and focus/error states.

## Framework notes

### Astro

- Check the `.astro` component for scoped `<style>` blocks and imported global CSS.
- Ignore generated `data-astro-*` scope attributes; they will not exist in Wufoo.
- Preserve the intent of scoped selectors by mapping them to Wufoo selectors such as `label.desc`, `input.text`, `textarea.textarea`, `select.select`, `.btTxt.submit`, and `li.error`.

### React / Next.js

- Trace component props to understand field labels, placeholders, helper text, and validation copy.
- For CSS Modules, follow the imported module file and convert hashed/local classes to visual rules.
- For styled-components, Emotion, stitches, vanilla-extract, or theme objects, resolve theme tokens before writing Wufoo CSS.
- Do not copy component names, generated class names, or `data-*` state hooks unless the user will add matching custom CSS keywords to Wufoo.

### Svelte / Vue

- Read component-scoped styles and global styles.
- Ignore framework-generated scoping attributes.
- Convert state classes and validation blocks into Wufoo's `li.error`, `p.errMsg`, `.errMsg`, `#errMsg`, `.focused`, `:focus`, `:disabled`, and `[readonly]` surfaces.

### Tailwind

- Resolve utilities into concrete values. Check `tailwind.config.*` for colors, fonts, spacing, radius, shadows, and plugins.
- Map common utilities:
  - `rounded-*` -> `--wf-radius`
  - `space-y-*`, `gap-*`, `grid gap-*` -> `--wf-gap` / `--wf-col-gap`
  - `text-*`, `font-*`, `tracking-*`, `uppercase` -> label/input/button typography
  - `border-*`, `ring-*`, `focus:*` -> input idle/focus/error rules
  - `bg-*`, `text-*` on buttons -> CTA tokens
- Do not paste Tailwind class strings into `custom.css`. Wufoo does not ship Tailwind.

### SCSS / CSS variables / design tokens

- Resolve variables and mixins into the final token values used by the form.
- Preserve useful CSS custom properties in `:root` only when they are local to the generated Wufoo theme.
- If the app uses semantic tokens (`--color-primary`, `--form-border`), map them into `--wf-*` tokens rather than keeping the app naming unless clarity benefits.

### Component libraries

- Libraries like shadcn/ui, Radix, MUI, Chakra, Bootstrap, and custom design systems often encode behavior and styling separately.
- Transfer visual values and interaction states. Do not promise custom dropdown popups, floating labels, JS masks, combobox behavior, or animated validation if Wufoo cannot render the same behavior.
- Native `<select>` option popups remain browser-controlled; make them readable, then call out the limitation.

## Wufoo mapping

Use this mapping when converting app form parts:

| Source part | Wufoo target |
|---|---|
| Form wrapper | `#container`, `.wufoo`, `.wufoo ul` |
| Form title/description | `header.info h2`, `header.info div` |
| Field row | `.wufoo li` |
| Label | `label.desc`, `legend.desc` |
| Helper text | `.instruct`, `.instruct small` |
| Error row/message | `li.error`, `p.errMsg`, `.errMsg`, `#errMsg` |
| Text input | `input.text`, relevant `input[type=*]` selectors |
| Textarea | `textarea.textarea` |
| Select | `select.select` |
| Radio/checkbox | `input.radio`, `input.checkbox`, `label.choice`, `.col` |
| Submit button | `.btTxt.submit`, `input[type="submit"].btTxt` |
| Multi-column rows | Wufoo CSS Layout Keywords, not nth-child selectors |

## What not to port

Do not port:

- Framework-only selectors (`.module_hash`, `.astro-*`, generated component-library classes)
- App layout wrappers that will not exist around the embedded Wufoo form
- JavaScript-only behavior, masks, popovers, comboboxes, or animated widgets
- Overly specific selectors tied to the source DOM order

Do port:

- Palette, typography, control shape, spacing, state styles, CTA treatment, helper/error styling, and enough field structure in `preview.html` to compare against the source form.
