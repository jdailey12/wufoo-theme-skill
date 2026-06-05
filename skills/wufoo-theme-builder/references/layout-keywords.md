# Wufoo CSS Layout Keywords

CSS Layout Keywords are class names the **form builder** applies to fields when the user wants them to sit side-by-side. They're added via Field Settings → **Add CSS Layout Keywords** in Wufoo's UI. Keywords are **case-sensitive**.

This is Wufoo's public API for column layouts — every theme should support them so the user can rearrange columns from the form builder without touching CSS.

## The keywords

### 2-column

```
leftHalf · rightHalf
```

Field 1 gets `leftHalf`, field 2 gets `rightHalf`. Two fields, side by side.

### 3-column

```
leftThird · middleThird · rightThird
```

Three sequential fields, each gets one keyword.

### 4-column

```
leftFourth · middleFourth · middleFourth · rightFourth
```

`middleFourth` is used **twice** — once on field 2 and once on field 3. So a 4-column row uses three distinct keywords: `leftFourth`, `middleFourth` (×2), `rightFourth`.

### 5-column

```
leftFifth · middleFifth · middleFifth · middleFifth · rightFifth
```

`middleFifth` is used **three times** (fields 2, 3, 4). So a 5-column row uses three distinct keywords: `leftFifth`, `middleFifth` (×3), `rightFifth`.

## Other Wufoo CSS keywords (non-layout)

These don't control columns but are part of the same keyword system. Cover them in the theme:

| Keyword | What it does |
|---|---|
| `noLabel` | Hides the field's label entirely. Useful for compact layouts. |
| `inline` | On a radio/checkbox group: flow choices horizontally instead of stacked. |
| `scrollText` | On a section break: turns the section into a scrolling box (Terms of Service style). |
| `scrollable` | Legacy/custom alias some older drafts use. Support it, but document `scrollText` as the official keyword. |
| `hide` | Hides the entire public-facing field row. Keep required hidden fields optional or prefilled. |
| `cloak` | Hides the inputs in a field but leaves the label visible. |
| `hideSecondary` | On page breaks: hides the previous-page control. |
| `hideCents` | On price fields: hides cents. |
| `hideSeconds` | On time fields: hides seconds. |
| `hideAMPM` | On time fields: hides the AM/PM dropdown. |
| `hideAddr2` | On address fields: hides the second street address line. |
| `altInstruct` | Moves instruction/help text under the field. |
| `nospin` | On a number input: removes the up/down spinner buttons. |

## Implementation pattern

Use flex on `<ul>` and let the keywords override `flex-basis`:

```css
.wufoo ul {
  display: flex;
  flex-wrap: wrap;
  column-gap: var(--wf-col-gap);
  row-gap: var(--wf-gap);
}

.wufoo li {
  flex: 1 1 100%;  /* default: full width */
  min-width: 0;
}

@media (min-width: 480px) {
  .wufoo li.leftHalf,
  .wufoo li.rightHalf {
    flex: 0 1 calc((100% - var(--wf-col-gap)) / 2);
    max-width: calc((100% - var(--wf-col-gap)) / 2);
  }
}

@media (min-width: 640px) {
  .wufoo li.leftThird,
  .wufoo li.middleThird,
  .wufoo li.rightThird {
    flex: 0 1 calc((100% - (var(--wf-col-gap) * 2)) / 3);
    max-width: calc((100% - (var(--wf-col-gap) * 2)) / 3);
  }
  /* same pattern for fourth + fifth */
}
```

The `max-width` is needed because `flex-basis: calc(...)` alone can grow if siblings shrink. Setting `max-width` to the same calc keeps columns at exact fractions.

Put vertical rhythm on `row-gap`, section padding/margins, or wrapper-level spacing. Avoid sibling-only spacing rules such as `.section + li { margin-top: ... }`; when the next row contains `leftHalf` / `rightHalf` or thirds, only the first column gets the extra margin and the row no longer shares a top edge.

## Breakpoints

Recommended breakpoints (built into the template):

| Layout | Activates from |
|---|---|
| Halves | 480px |
| Thirds, Fourths, Fifths | 640px |

Below those widths, fields fall back to full-width stacking. This keeps mobile readable — five columns on a 375px phone is unreadable.

## Common pitfalls

**Case sensitivity.** `leftHalf`, not `lefthalf` or `LeftHalf`. The Wufoo article on this is explicit: keywords are case-sensitive.

**Sequential fields.** The keywords only work if the fields are adjacent in the form builder. `leftHalf` followed by an unrelated field followed by `rightHalf` won't sit side by side — they need to be next to each other.

**Hidden `<li>`s and grid math.** If you ever use `display: none` on certain `<li>` types (like sections), they're removed from flex/grid placement. That's fine for flex-wrap (the next `<li>` flows up); but if you scope rules to "the first two `<li>`s" via `:nth-child`, the count includes hidden ones. Avoid `:nth-child` for layout — use the keyword classes.

**`hide` is field-level.** Do not treat `.hide` as an accessibility helper on every element. In Wufoo, `li.hide` means the field should disappear from the public form. Use label-specific selectors such as `label.hide` if you need a screen-reader-only label rule.

**Fields wider than their column.** If an input has `min-width` set higher than the column allows, it'll overflow. Always pair the keyword classes with `min-width: 0` on the `<li>` and `max-width: 100%` on the inputs so they shrink to fit.

**`address` and other complex fields with `leftHalf`.** Putting a 6-input address into a `leftHalf` column makes the address sub-grid cramped. Wufoo's article on this calls it out: "you probably won't be able to get five [address fields] next to each other." For complex fields, default to full-width.

**Visual alignment required.** After generating the preview, inspect the rendered rows. Confirm `leftHalf` and `rightHalf` share the same top edge, and `leftThird` / `middleThird` / `rightThird` align as one row. Repeat at mobile width to confirm all keyword fields stack cleanly.

## Reference

Original Wufoo article: *How to Arrange Form Fields to Sit Next to Each Other in Wufoo* by Chris Coyier. The keywords above match exactly what's documented there.
