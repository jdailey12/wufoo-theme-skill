---
name: wufoo-theme-builder
description: Build, convert, or audit feature-complete custom CSS themes for Wufoo forms. Use whenever the user mentions Wufoo themes, Wufoo custom CSS, Wufoo Theme Kit, custom.css, Theme Builder Advanced CSS URL, styling a Wufoo form, matching a screenshot or design brief in Wufoo, auditing existing Wufoo CSS, or converting an existing website/app/native form style into Wufoo. Especially use for Astro, React, Next.js, Svelte, Vue, Tailwind, CSS Modules, SCSS, design-token files, or component-library forms that need their styling translated to standalone Wufoo selectors.
---

# Wufoo Theme Builder

Build a complete custom CSS theme for a Wufoo form. The visual source can be a reference image, screenshot, written brief, existing Wufoo CSS, or a native form in an app/framework project. The output is a `custom.css` ready to upload via **Wufoo Theme Builder -> Advanced**, plus a self-contained `preview.html` that demos every field type so the user can sanity-check before going live.

The hard part of theming Wufoo is not visual taste. It is translating the source design into Wufoo's stable selector surface without losing field types, states, CSS Layout Keywords, multi-page chrome, or production-specific overrides from Wufoo's own `index.css`.

## When to use

- User shares a form mockup/screenshot and asks for a Wufoo theme
- User references "Wufoo Theme Kit", `custom.css`, or `example.css`
- User wants to upload a stylesheet via Theme Builder → Advanced
- User says "style my Wufoo form like ___"
- User asks to refresh or audit an existing Wufoo theme
- User is in an Astro, React, Next.js, Svelte, Vue, Tailwind, CSS Modules, SCSS, or design-token project and wants its native form styling converted to Wufoo
- User asks to make Wufoo match a website contact form, lead form, checkout form, RSVP form, or app form already present in code

## Output

Drop two files into the working directory (or into `WufooThemeKit/css/` and `WufooThemeKit/` if that structure exists):

1. **`custom.css`** — the production theme. Single file. Imports fonts, defines tokens, covers every Wufoo selector category, ends with print + autofill rules.
2. **`preview.html`** — a self-contained demo that links `custom.css` and renders every field type, state, and layout keyword on a representative background. The user opens this in a browser to verify before uploading.

Don't create extra docs or planning files unless the user asks.

---

## Workflow

### 1. Identify the source

Choose one path:

- **Screenshot/brief**: study the image or written direction directly.
- **Existing app/native form**: inspect project files before asking questions. Search for form components, route/page files, adjacent stylesheets, token/config files, and UI-library wrappers. Read `references/code-transfer.md`.
- **Existing Wufoo CSS**: audit the CSS against `references/selectors.md`, the template coverage, and the production specificity notes below.

If something critical is missing, ask one focused question. Do not interview. For app-code transfer, only ask after you have inspected likely files.

### 2. Analyze the design

Walk through `references/design-analysis.md` to extract these tokens from the source:

- **Palette** (3–6 colors): bg, ink, ink-muted, line, line-focus, accent/CTA
- **Typography**: 1–2 font families (Google Fonts), weights used, label treatment (uppercase + tracked? sentence case?), input size, label size
- **Input style**: borderless underline / boxed / filled / pill — pick one and apply consistently
- **Button style**: full-width / inline, dark-on-light / light-on-dark, rounded / square, tracked / not
- **Spacing rhythm**: row gap between fields (default 32px), column gap (default 24px)
- **Embed scenario**: by default the theme leaves `body` and `#container` **transparent** so it works on any background. Only set a body bg if the user explicitly asks the theme to enforce one.

For existing app/native forms, resolve styles to final values, not just class names:

- Tailwind utilities -> concrete CSS values from `tailwind.config.*`, CSS variables, and defaults
- Astro scoped styles -> actual selectors/properties, ignoring Astro's generated scope attributes
- CSS Modules/SCSS -> compiled design intent, including variables and mixins
- Component libraries -> rendered input/button states and theme tokens, not library class names
- Browser-computed styles -> use when source styles are spread across runtime CSS or hard to resolve statically

### 3. Copy the template, fill the tokens

Copy `assets/theme-template.css` to the user's working directory as `custom.css`. The template already covers the important Wufoo surfaces. Your job is:

1. Replace the `:root` token block with the palette + font choices from step 2
2. Adjust the `--wf-input-size`, `--wf-label-size`, gap, and radius variables to match the design
3. Update the main input rules if the design uses **boxed/filled/pill/floating-label** inputs instead of the default underline (see `references/input-styles.md`)
4. Update the button rules if the design uses a different button shape (rounded, outlined, inline, etc.)
5. Leave unrelated coverage blocks alone unless the design demands a deviation

When converting app code, translate visual intent into Wufoo selectors. Do not copy app-only class names, scoped attributes, Tailwind utility strings, React component names, or framework-specific selectors into the production Wufoo theme unless the user has added matching custom CSS keywords in Wufoo. Wufoo will render its own markup.

The template covers:

- Reset + transparent page background
- Form base, header, sections, dividers
- Labels, sub-labels, required indicators, `noLabel`, `hide`, and `cloak` keyword behavior
- All field types (text, email, tel, url, number, search, password, date/datetime/month/week/time, textarea, select, multi-select, file, range, color, radio, checkbox)
- States: focus, disabled, readonly, `:invalid` neutralization
- Inline span groups (Name, Date, Time, Phone, Currency)
- `.complex` address layout with `.full`/`.left`/`.right`
- Choice fields with `inline` keyword variant
- Likert/rating tables, captcha
- Instructions / help text
- Error states (`li.error`, `p.errMsg`, top-of-form `#errMsg`)
- Confirmation page (`#confirm`)
- Buttons: submit, prev/next, reset, print, disabled
- Multi-page progress bar + page counter
- CSS Layout Keywords (`leftHalf`/`rightHalf`, `leftThird`–`rightThird`, `leftFourth`–`rightFourth`, `leftFifth`–`rightFifth`)
- Non-layout keywords where practical (`scrollText`, `scrollable` alias, `altInstruct`, `hideSecondary`, `hideCents`, `hideSeconds`, `hideAMPM`, `hideAddr2`, `nospin`)
- Autofill override (transparent on dark backgrounds)
- Print stylesheet

Do not delete coverage blocks you think the user "won't use" — Wufoo forms grow, and a missing selector becomes a visible bug the moment they add a Likert field. Coverage is the value.

If the source design uses floating labels or labels visually inside the input, read `references/input-styles.md` before writing CSS. Floating-label themes need explicit filled-but-blurred QA because label/value overlap is easy to miss.

### 4. Generate the preview

Copy `assets/preview-template.html` to the user's working directory as `preview.html`. The template is already wired to `css/custom.css` — adjust the `<link>` href if your custom.css lives elsewhere.

Update the inline `<style>` block at the top to set the preview's body background to match the user's intended embed surface (default: dark forest green `#14241B`; for a light-context theme, use a complementary light tone). This `<style>` block is **demo-only** — it does not get included in `custom.css`.

The preview renders two demos stacked:
1. The exact reference form (matched to the screenshot)
2. A comprehensive demo: every field type, every state, every column layout

If the reference shows a specific form (e.g., contact, RSVP, application), tailor demo 1 to match the labels and structure shown. If converting from an app/native form, demo 1 should mirror that source form's field order, labels, placeholders, helper text, validation copy, and CTA text. Demo 2 stays generic so the user can spot any field type that breaks.

### 5. Render and QA before handoff

Do not hand off a theme based only on reading CSS. Open `preview.html` with whatever visual tool is available: browser automation, in-app browser, screenshot/image inspection, DOM inspector, or manual browser inspection. If no automation exists, still open or inspect the preview manually and describe what you checked.

Use this state checklist in the rendered preview:

- Empty field
- Focused field
- Typed value, then blurred
- Filled-but-blurred/prefilled field
- Select field
- Textarea
- Date/time/native inputs
- File/range/color inputs
- Readonly, disabled, and prefilled fields
- Error state
- Mobile stacked layout
- Multi-column layout rows

For floating-label themes, verify that labels stay floated after a value is entered and the field loses focus. Add `placeholder=" "` or real placeholder text to the preview fields that rely on `:placeholder-shown`. Mention in handoff that live Wufoo fields using floating labels also need placeholders or equivalent state hooks where the CSS depends on them.

Treat selects, date/time inputs, files, ranges, and color inputs as special cases. Do not blindly apply text-input floating-label behavior to them; their labels usually need to stay static or become visually hidden on purpose.

For layout keywords, visually confirm row alignment: `leftHalf` and `rightHalf` fields share the same top edge, and `leftThird` / `middleThird` / `rightThird` fields align as one row. Repeat on mobile to confirm the same fields stack cleanly.

Final polish checklist:

- No label/value overlap
- Input, select, and textarea heights feel consistent
- Field border weights match
- CTA width, font, and alignment match the reference
- Section spacing is even
- Multi-column rows align along the top edge
- Mobile stacking reads naturally
- Readonly/error/disabled contrast is readable

### 6. Hand off

Tell the user:

1. The two files are ready (`custom.css` + `preview.html`)
2. To preview: open `preview.html` in a browser
3. To deploy: upload `custom.css` to a public URL (their server, S3, etc.), then in Wufoo go to **Theme Builder → select theme → Advanced → CSS URL** and paste the URL. Wufoo's Theme Builder docs are at https://help.wufoo.com (search "custom CSS keywords")
4. Summarize the visual QA you performed and any issue you fixed or could not fully verify.
5. **Important:** the local preview doesn't load Wufoo's `index.css`, so it won't catch every issue. After deploying, ask the user to send back a screenshot of the live form. Spot-check the items in the "Verifying the theme on a deployed Wufoo form" section below.

Mention any deviations you made from the reference and why (e.g., "I made labels uppercase + tracked even though the screenshot is sentence-case, because the visual rhythm of the form benefits from it — flip `text-transform: none` on `label.desc` if you'd rather match the screenshot exactly").

For app-code transfers, also mention what was not portable into Wufoo exactly: JavaScript-only widget behavior, animation timing, custom combobox/dropdown popups, icon-in-input layouts, masked-input logic, or component-library internals. Preserve the visual style, then call out the behavior gap plainly.

---

## Reference docs

Load these only when needed:

- **`references/selectors.md`** — Complete Wufoo selector hierarchy with examples. Read when you're unsure whether a selector exists or what class names a field type uses.
- **`references/layout-keywords.md`** — CSS Layout Keywords (`leftHalf`, `leftThird`, etc.) — how Wufoo applies them, breakpoint behavior, common pitfalls.
- **`references/design-analysis.md`** — How to extract design tokens from a reference image. Read at step 2.
- **`references/input-styles.md`** — The four common input style families (borderless underline, boxed, filled, pill) and the CSS for each. Read at step 3 if the design isn't a borderless underline.
- **`references/code-transfer.md`** — How to translate existing app/native form styling to standalone Wufoo CSS. Read whenever the source is project code, a framework component, Tailwind utilities, CSS Modules, SCSS, design tokens, or a component library.

## Assets

- **`assets/theme-template.css`** — The full theme template. Copy → fill tokens → ship.
- **`assets/preview-template.html`** — The two-demo preview HTML. Copy → adjust body bg → ship.

---

## Design principles

A few things I learned the hard way that should shape every theme this skill produces:

**Transparent page background by default.** Setting `body { background: <color> }` couples the theme to one embed scenario. Most Wufoo forms get embedded on the user's website via iframe or HTML embed, and the user's site already has a background. A transparent theme works on any of them. The preview can fake a background for demo purposes; the production CSS shouldn't.

**Inputs are 100% width by default.** Wufoo's `.field.text.small`/`.medium`/`.large` size classes set char-widths that look fine in Wufoo's stock theme but produce inconsistent ladder shapes in modern themes. Override them all to `width: 100%` and rely on **CSS Layout Keywords** (`leftHalf` etc.) for column control. This is the single biggest improvement you can make over Wufoo's defaults.

**Coverage > brevity.** A short theme that misses error states, multi-page chrome, or file upload progress will embarrass the user the first time their form errors out. Keep the template's coverage intact.

**Visual behavior > selector coverage.** A theme is not done because the selectors exist. It is done when the rendered preview proves the important states work: empty, focused, filled, blurred, error, disabled, readonly, mobile, and multi-column.

**Use CSS custom properties for the palette.** Every theme has the same skeleton — only the tokens change. Defining 8–12 properties at `:root` and referencing them throughout makes the theme tweakable in seconds and obvious to read.

**Don't force layouts the form builder didn't ask for.** Earlier versions of this skill forced first-two-`<li>` elements into a 2-column row. That broke the moment someone made a single-column form or a form where the name fields weren't first. Respect Wufoo's CSS Layout Keywords instead — that's their public API for column layouts, and it's exactly what users in the form builder will use.

**Keep row rhythm on rows, not sibling hacks.** Avoid spacing rules such as `.section + li { margin-top: ... }` when the next row may contain `leftHalf` / `rightHalf` or third-width fields. Put spacing on the section block, `row-gap`, or wrapper-level rhythm so columns keep the same top edge.

**Port style, not markup.** In app-code transfers, the source form's DOM is evidence, not the target. Wufoo will render different HTML. Convert tokens, visual treatments, spacing, and state behavior into the Wufoo selector set.

**Honor the screenshot — but flag deviations.** If the screenshot's button is dark-on-cream and the user wants to embed on dark, you'll need to invert. That's a real change; tell the user about it.

---

## Wufoo specificity reality

The single biggest gotcha when shipping a Wufoo theme is **Wufoo's `index.css` loads before your custom CSS**, and several of its selectors win on specificity. The kit's `index.html` doesn't include `index.css` so themes look correct in the local preview but break the moment the form goes live. This is why the template uses `!important` in specific places — they're not stylistic choices, they're necessary overrides.

**Specific properties that need `!important` to beat Wufoo's defaults:**

| Property | Why |
|---|---|
| `font-family`, `font-size`, `font-weight`, `line-height`, `color` on inputs/textarea/select | Wufoo sets these per-field-type. Without `!important`, only the select picks up the theme's `--wf-input-size` (because the select rule already uses `!important` for borders) — text inputs and textareas silently render at Wufoo's default sizes, mismatching. |
| `border-top`, `border-left`, `border-right`, `border-bottom`, `padding` on `select.select` | Wufoo's default gives selects a heavier all-around border than text inputs. Single-side override (e.g. `border-bottom: 1px solid X`) without zeroing the other three doesn't work. |
| `width`, `display`, `padding` on `.btTxt`, `input[type="submit"]` | Wufoo sets `.btTxt { width: auto; }`. Without `!important` on `width: 100%`, your submit button renders auto-width on the left of the row regardless of flex parent. |
| `display` on `.req` | If you're trying to hide the required asterisk, plain `display: none` won't beat Wufoo's specificity. Either use `!important` or (better) restyle `.req` as a subtle visible marker. |

**Rule of thumb:** if a property looked fine locally but broke on Wufoo, the fix is `!important` on that property. Don't sprinkle `!important` everywhere — only on the specific properties Wufoo's defaults are setting.

## Common pitfalls

- **Wufoo CSS Layout Keywords are case-sensitive.** `leftHalf`, not `lefthalf` or `LeftHalf`. Match exactly in both the CSS and any documentation you give the user.
- **Container padding for embed.** The template defaults `#container { padding: 0 }` because most users embed inside their own wrapper that already provides spacing. If the form is going on Wufoo's standalone hosted page (not embedded), bump padding back to `48px 24px`. Confirm with the user which deployment they're targeting.
- **Required asterisks default to visible-but-subtle, not hidden.** Reasons: (1) Wufoo's CSS specificity makes `display: none` unreliable without `!important`, (2) hiding required indicators is bad UX on production forms. Template restyles `.req` as a small muted asterisk that reads as "required" without clashing. To hide entirely, change `display: inline` to `display: none` (keep the `!important`).
- **The reCAPTCHA notice**. Wufoo automatically appends a "This site is protected by reCAPTCHA Enterprise..." block below the submit button on production forms. By default it's small black text with bright blue links — completely off-theme. The template's footer/reCAPTCHA block handles it. The kit's `index.html` doesn't include this block, so you won't see it in local preview — verify after deploy.
- **Floating label overlap**. If labels move into inputs, the filled-but-blurred state is mandatory QA. The label must remain floated after a value is typed and focus leaves the field. This usually requires a placeholder (`placeholder=" "`) or a real placeholder so CSS can use `:placeholder-shown`.
- **Number input spinners**: kill them on **all** number inputs (`-webkit-appearance: none` + `-moz-appearance: textfield`), not just ones with the `.nospin` class. The default browser spinners look terrible in modern themes.
- **`:invalid` styling**: Chrome's default red ring on `:invalid` overrides your error styling unless you explicitly neutralize it (`box-shadow: none; outline: none;` on `:invalid`).
- **Autofill on dark backgrounds**: Chrome's yellow autofill background leaks through unless you set `-webkit-box-shadow: 0 0 0 1000px transparent inset` and override `-webkit-text-fill-color`.
- **Select option dropdowns** are browser-native and ignore most styling. Set `option { color: <fg>; background: <bg>; }` to at least make them readable, but don't promise the user the dropdown will match the rest of the theme — it won't on every browser.
- **`li.section` with `display: none`** is removed from the grid flow, so `:nth-child` selectors will skip them. If you ever scope a layout rule to "the first two `<li>`s", remember sections + buttons count too.
- **Submit button wrapped in a `<div>` or `<p>`.** Wufoo sometimes wraps the submit input in an inline-block container. The template includes `.wufoo li.buttons > div, .wufoo li.buttons > p { width: 100% }` so the button can stretch — leave that rule in.
- **Custom fonts via `@import`**: works but blocks render. For production, the user should add a `<link rel="preconnect">` to `fonts.googleapis.com` and load via `<link>` instead. Mention this in handoff.
- **Framework class leakage**: Tailwind classes, CSS Module hashes, Astro scope attributes, and UI-library internals will not exist in Wufoo. If they appear in `custom.css`, the conversion likely went wrong.

## Verifying the theme on a deployed Wufoo form

The local `preview.html` is a useful sanity check but is **not** representative of production — it doesn't load Wufoo's `index.css` and doesn't include the reCAPTCHA notice. After uploading the CSS to Wufoo, ask the user to send back a screenshot of the deployed form. Look specifically at:

1. **Input text size consistency** — text input, textarea, and select should all be the same size. If the select looks bigger, your `font-size` rules are missing `!important`.
2. **Submit button width** — should span the full row. If it's auto-width on the left, your button rules need `!important` on `width: 100%`.
3. **Select underline weight** — should match the other inputs. If the select has a heavier border, your select rule needs the four-side border zeroing with `!important`.
4. **Required asterisks** — should match the theme palette (muted, not red). If you see red `*`, your `.req` rule needs `!important`.
5. **reCAPTCHA notice** — should pick up the theme's font + ink color, with theme-colored links instead of blue.

If any of those are off in production but right in local preview, the fix is almost always adding `!important` to the relevant property.
