# Input Style Variants

The default theme template uses a **borderless underline** input style. If the reference image uses a different family, replace section 8 of `theme-template.css` with one of the variants below. All four play nicely with the template's `:root` tokens — change just the input rules and the rest of the theme stays consistent.

## 1. Borderless underline (template default)

Modern, editorial, luxury. The look used in the boutique form screenshot that originally drove this skill.

```css
.wufoo input.text,
.wufoo input[type="text"],
.wufoo input[type="email"],
/* ... rest of the input selectors ... */
.wufoo textarea.textarea,
.wufoo select.select {
  width: 100%;
  background: transparent;
  border: 0;
  border-bottom: 1px solid var(--wf-line);
  border-radius: 0;
  padding: 0 0 14px 0;
  font-size: var(--wf-input-size);
  color: var(--wf-ink);
}

:focus { border-bottom-color: var(--wf-line-focus); }
```

## 2. Boxed

Corporate, dashboard, classic. 1px border all around, small radius.

```css
.wufoo input.text,
.wufoo input[type="text"],
/* ... etc ... */
.wufoo textarea.textarea,
.wufoo select.select {
  width: 100%;
  background: transparent;
  border: 1px solid var(--wf-line);
  border-radius: var(--wf-radius); /* set to 4px in :root */
  padding: 12px 14px;
  font-size: 16px;
  color: var(--wf-ink);
}

:focus {
  border-color: var(--wf-line-focus);
  box-shadow: 0 0 0 3px rgba(<ink-rgb>, 0.12);
}
```

Set `--wf-radius: 4px` and `--wf-input-size: 16px` in `:root` for this style.

## 3. Filled

Friendly, app-like, mobile-feel. Subtle background fill, no border, slight radius.

```css
.wufoo input.text,
.wufoo input[type="text"],
/* ... etc ... */
.wufoo textarea.textarea,
.wufoo select.select {
  width: 100%;
  background: rgba(<ink-rgb>, 0.06);
  border: 0;
  border-radius: var(--wf-radius); /* 6–8px */
  padding: 14px 16px;
  font-size: 16px;
  color: var(--wf-ink);
}

:focus { background: rgba(<ink-rgb>, 0.10); }
```

Add a subtle bottom underline on focus if you want stronger feedback:
```css
:focus { box-shadow: inset 0 -2px 0 0 var(--wf-line-focus); }
```

## 4. Pill

Playful, consumer, lifestyle. High border-radius. Works best with shorter inputs and inline-auto-width buttons.

```css
.wufoo input.text,
.wufoo input[type="text"],
/* ... etc ... */
.wufoo textarea.textarea,
.wufoo select.select {
  width: 100%;
  background: transparent;
  border: 1px solid var(--wf-line);
  border-radius: 999px; /* full pill */
  padding: 12px 20px;
  font-size: 15px;
  color: var(--wf-ink);
}

.wufoo textarea.textarea {
  border-radius: 18px; /* textareas can't go full pill */
}

:focus { border-color: var(--wf-line-focus); }
```

For pill style, also round the buttons:

```css
.wufoo .btTxt.submit { border-radius: 999px; }
```

---

## Style decisions that follow input style

| Input style | Recommended button shape | Recommended label treatment |
|---|---|---|
| Underline | Full-width square or 2px radius | Uppercase tracked |
| Boxed | Full-width 4px radius | Sentence case bold or uppercase tracked |
| Filled | Full-width 6–8px radius | Sentence case bold |
| Pill | Pill-shaped (999px radius) | Sentence case bold |

These are conventions, not rules — flip them if the reference design says so.

## Multi-input field considerations

The inline-span groups (Name, Date, Time, Phone, Currency) need extra care for boxed/filled/pill styles since each `<input>` becomes its own visual chip:

- **Boxed/filled/pill** with sub-labels under each input usually looks crowded. Consider hiding the sub-labels (`.wufoo li > span > label { display: none }`) and relying on `placeholder` instead.
- For the **Name** field, this is especially noticeable — Title/First/Last/Suffix as four separate boxed inputs is busy. Adding `gap: 8px` between spans helps.

## Address (`.complex`) considerations

The address block uses 6 inputs in a `.full`/`.left`/`.right` grid. Boxed and filled styles look great here — the grid reads cleanly. Underline style works but the narrow city/state fields can feel sparse; bump `min-width` on `.left`/`.right` to `240px` if needed.
