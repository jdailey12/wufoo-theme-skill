# Wufoo Selector Reference

Complete catalog of selectors a Wufoo theme should cover. Wufoo's HTML structure is stable and documented in the Theme Kit's `index.html` — these are the same hooks every Wufoo form renders.

## Critical: Wufoo's `index.css` loads first

Before reading the selector list below, know this: **on a deployed Wufoo form, Wufoo's own `index.css` loads before your custom CSS.** Several of its rules win on specificity. Properties known to need `!important` to override:

- `font-size`, `font-family`, `font-weight`, `line-height`, `color` on inputs/textarea/select (otherwise inputs render at inconsistent sizes — usually the select picks up your tokens but text inputs don't)
- All four borders + padding + height on `select.select` (default has heavy all-around border)
- `width`, `display`, `padding` on `.btTxt` / `input[type=submit]` (default is `width: auto`)
- `display`, color on `.req` (default is visible red asterisk; `display: none` alone won't hide it)

The Theme Kit's `index.html` does **not** include `index.css`, so themes look perfect in the local preview but break in production unless you've baked the `!important` overrides in. The included `theme-template.css` already has them in the right places — leave them in.

## Page chrome

| Selector | What it is |
|---|---|
| `html`, `body#public` | Outer page. Reset to `background: transparent` so the theme works on any embed surface. |
| `#container` | Wraps the entire form. Constrain `max-width`, center it, set padding here. |
| `#logo`, `#logo a` | Wufoo branding. Hide unless the user has a paid plan and wants their own logo. |

## Form base

| Selector | Notes |
|---|---|
| `.wufoo` | The `<form>`. Set base font + ink color here. |
| `.wufoo ul`, `.wufoo ol` | The list of fields. Use `display: flex; flex-wrap: wrap` with `column-gap` + `row-gap` so CSS Layout Keywords can place fields in columns. |
| `.wufoo li` | A field row. Default `flex: 1 1 100%` (full width); CSS Layout Keywords override the basis. |
| `.wufoo fieldset`, `.wufoo legend` | Wrappers around radio/checkbox groups. Strip default browser styling. |

## Header / sections

| Selector | Notes |
|---|---|
| `.wufoo header.info` | Form title block (above the field list). |
| `.wufoo header.info h2` | Form title. |
| `.wufoo header.info div` | Form description. |
| `.wufoo li.section` | A section divider (Wufoo "Section Break" field type). |
| `.wufoo li.section.first` | The first section (no top spacing in Wufoo's default). |
| `.wufoo .section h3` | Section heading. |
| `.wufoo .section div` | Section description. |
| `.wufoo li.section.scrollText .section` | Wufoo's official `scrollText` keyword (Terms of Service block). Set `max-height` + `overflow-y: auto`. |
| `.wufoo li.section.scrollable .section` | Legacy/custom alias for older drafts. Support it, but tell users to apply `scrollText` in Wufoo. |

## Labels

| Selector | Notes |
|---|---|
| `.wufoo label.desc` | Primary field label. |
| `.wufoo legend.desc` | Same role as `label.desc` but for radio/checkbox groups inside fieldsets. **Must share styling with `label.desc`.** |
| `.wufoo li span > label`, `.wufoo li.complex span > label` | Sub-labels: "First/Last" under Name, "MM/DD/YYYY" under Date, etc. Smaller, muted, often uppercase tracked. |
| `.wufoo label.choice` | The label next to a radio or checkbox. Always sentence case. |
| `.wufoo .req`, `.wufoo span.req` | The required-field asterisk. Default-hide unless the design specifically wants one. |
| `.wufoo label.hide`, `.wufoo legend.hide`, `.wufoo span.hide` | Visually-hidden label/helper hooks. Use the sr-only clip pattern. |
| `.wufoo li.noLabel > label.desc` | Wufoo's `noLabel` keyword. Hides the label entirely. |
| `.wufoo li.hide` | Wufoo's official `hide` keyword. Hide the entire field row from the public form. |
| `.wufoo li.cloak` | Hide a field's inputs while leaving its label visible. |
| `.wufoo li.hideCents`, `.wufoo li.hideSeconds`, `.wufoo li.hideAMPM`, `.wufoo li.hideAddr2` | Hide specific sub-parts of price, time, and address fields where Wufoo exposes the matching inner hook. |
| `.wufoo li.altInstruct .instruct` | Keep instructions below the field. |

## Inputs

Wufoo uses both class hooks and HTML5 type attributes. Cover both for maximum compatibility:

| Selector | Used for |
|---|---|
| `.wufoo input.text`, `.wufoo input[type="text"]` | Single-line text |
| `.wufoo input[type="email"]` | Email |
| `.wufoo input[type="tel"]` | Phone |
| `.wufoo input[type="url"]` | Website |
| `.wufoo input[type="number"]` | Number, currency, quantity |
| `.wufoo input[type="search"]` | Search |
| `.wufoo input[type="password"]` | Password |
| `.wufoo input[type="date"]`, `[type="datetime-local"]`, `[type="month"]`, `[type="week"]`, `[type="time"]` | Native date/time pickers |
| `.wufoo textarea.textarea` | Multi-line text |
| `.wufoo select.select` | Single-select dropdown |
| `.wufoo select[multiple]` | Multi-select listbox |
| `.wufoo input.file`, `.wufoo input[type="file"]` | File upload (also style `::file-selector-button`) |
| `.wufoo input[type="range"]` | Slider |
| `.wufoo input[type="color"]` | Color picker |
| `.wufoo input.radio`, `.wufoo input[type="radio"]` | Radio button (use `accent-color` for the dot) |
| `.wufoo input.checkbox`, `.wufoo input[type="checkbox"]` | Checkbox (use `accent-color` for the check) |

### Width helpers (override these)

Wufoo applies `.small`, `.medium`, `.large` to inputs to control char-width. These look ugly in modern themes — override all three to `width: 100%` and rely on CSS Layout Keywords for column control.

```css
.wufoo input.text.small,
.wufoo input.text.medium,
.wufoo input.text.large { width: 100%; }
```

### Placeholder

```css
.wufoo input::placeholder,
.wufoo textarea::placeholder { color: var(--wf-ink-muted); }
```

Also style `::-webkit-input-placeholder` and `::-moz-placeholder` for older browsers.

### Number spinner removal

```css
.wufoo input[type="number"], .wufoo input.nospin {
  -moz-appearance: textfield;
  appearance: textfield;
}
.wufoo input[type="number"]::-webkit-outer-spin-button,
.wufoo input[type="number"]::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
```

### Native validation neutralization

Chrome's red `:invalid` ring overrides custom error styling. Always include:

```css
.wufoo input:invalid, .wufoo textarea:invalid, .wufoo select:invalid {
  box-shadow: none;
  outline: none;
}
```

### Select dropdown options

Native `<option>` elements ignore most styling but at least set readable colors:

```css
.wufoo select.select option { color: <fg>; background: <bg>; }
```

The dropdown popup will still render in the OS native style. That's a browser limitation, not a theme bug.

## States

| Selector | Notes |
|---|---|
| `:focus` on inputs/textareas/selects | The active field. Strengthen the bottom border or add a subtle glow. |
| `.wufoo .focused` | Wufoo's class on the parent `<li>` of the active field. Most themes can leave this as `background: transparent`. |
| `:disabled`, `[readonly]` | Reduce opacity, `cursor: not-allowed`. |
| `.wufoo li.error` | The `<li>` of a field that failed validation. Color the label + input border with `--wf-error`. |
| `.wufoo p.errMsg`, `.wufoo .errMsg` | The error message below an errored field. |
| `.wufoo #errMsg`, `.wufoo .notice`, `.wufoo .info.error` | Top-of-form error notice (when the whole form failed validation). |

## Inline span groups

Some fields render as multiple `<input>`s inside `<span>` siblings inside an `<li>`:

- **Name**: Title / First / Last / Suffix
- **Date**: MM / DD / YYYY
- **Time**: HH / MM + AM/PM select
- **Phone**: ### / ### / ####
- **Currency**: `$` / dollars input / `.` / cents input

Style the spans as `display: inline-block; vertical-align: top` and let the input's `size` attribute set its width:

```css
.wufoo li > span input.text { width: auto; }
```

## Complex fields

`li.complex` wraps fields with internal grid layout (Address, File). Inside `li.complex > div`:

| Selector | Notes |
|---|---|
| `.wufoo .complex span.full` | Spans the full row width. |
| `.wufoo .complex span.left` | Left half. |
| `.wufoo .complex span.right` | Right half. |

These are Wufoo's address-block hooks — set `display: flex; flex-wrap: wrap` on the parent `<div>` and let `.full` use `flex: 1 1 100%`, `.left`/`.right` use `flex: 1 1 calc(50% - <gap>/2)`.

## Choice fields

| Selector | Notes |
|---|---|
| `.wufoo .col` | Wraps the radio/checkbox spans. Default to vertical stack. |
| `.wufoo .col span` | Each row: input + label. Use `display: inline-flex; align-items: center; gap: 10px`. |
| `.wufoo li.inline .col` | The `inline` keyword — flip to horizontal flow with `flex-direction: row; flex-wrap: wrap`. |

## Likert / Rating

| Selector | Notes |
|---|---|
| `.wufoo table.likert`, `.wufoo .likert table` | The Likert grid. Borderless, padded cells, first column emphasized. |
| `.wufoo .rating` | Star/scale rating widget. |

## Captcha

| Selector | Notes |
|---|---|
| `.wufoo .captcha` | Captcha block. |
| `.wufoo .captcha img` | The captcha image. |
| `.wufoo .captcha input.text` | The "type the text" input. |

## Buttons

| Selector | Notes |
|---|---|
| `.wufoo li.buttons` | The buttons row at the form's bottom. Use flex so multiple buttons split. |
| `.wufoo .btTxt.submit`, `.wufoo input[type="submit"].btTxt`, `.wufoo button[type="submit"]` | The primary submit. |
| `.wufoo #prevPage`, `.wufoo .btTxt.prev`, `.wufoo .btTxt.btprev` | Multi-page form: previous-page button. Style as secondary. |
| `.wufoo #nextPage`, `.wufoo .btTxt.next`, `.wufoo .btTxt.btnext` | Multi-page form: next-page button. Style as primary. |
| `.wufoo input[type="reset"].btTxt`, `.wufoo button[type="reset"]` | Reset. Style as secondary. |
| `.wufoo .btTxt.print`, `.wufoo #printForm` | Print. Style as secondary. |
| `:hover`, `:focus-visible`, `:active`, `:disabled` | Cover all four. |

## Multi-page form chrome

| Selector | Notes |
|---|---|
| `.wufoo .pageOf`, `.wufoo #pageOf` | "Page 1 of 3" indicator. |
| `.wufoo .progress` | Progress bar track. |
| `.wufoo .progress .progressBar`, `.wufoo .progress .bar` | Progress bar fill. |

## Confirmation page

When a form submits successfully, Wufoo shows a confirmation block:

| Selector | Notes |
|---|---|
| `#confirm`, `.confirm` | The container. |
| `#confirm h1`, `.confirm h1`, `#confirm h2` | The "Thanks!" headline. |
| `#confirm p`, `.confirm p` | The body text. |

## File upload progress

| Selector | Notes |
|---|---|
| `.wufoo .fileupload-progress` | The "Uploading…" status block. |
| `.wufoo .fileupload-progress .bar` | The progress bar. |
| `.wufoo .fileupload-list` | The list of queued/uploaded files. |

## Instructions

| Selector | Notes |
|---|---|
| `.wufoo .instruct` | The instruction container that appears below an input. |
| `.wufoo .instruct small` | The instruction text. |

## Misc

| Selector | Notes |
|---|---|
| `.wufoo img.icon` | Date-picker icon. Use `filter` to recolor for dark themes, or `opacity: 0.6`. |
| `.wufoo a` | Inline links inside the form (rare, but used in scrollable Terms of Service). |

## Autofill

Chrome's yellow autofill bg leaks through dark themes. Override:

```css
.wufoo input:-webkit-autofill,
.wufoo textarea:-webkit-autofill,
.wufoo select:-webkit-autofill {
  -webkit-text-fill-color: var(--wf-ink);
  caret-color: var(--wf-ink);
  -webkit-box-shadow: 0 0 0 1000px transparent inset;
  transition: background-color 9999s ease-in-out 0s;
}
```

## Print

Always include a print stylesheet — Wufoo forms are commonly printed for paper records:

```css
@media print {
  html, body#public, #container { background: #FFFFFF !important; color: #000 !important; }
  .wufoo, .wufoo label.desc, .wufoo legend.desc { color: #000 !important; }
  .wufoo input.text, .wufoo textarea.textarea, .wufoo select.select {
    color: #000 !important;
    border-bottom-color: #000 !important;
  }
  .wufoo li.buttons, #logo { display: none !important; }
}
```
