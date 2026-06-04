# Wufoo Theme Builder Skill

[![skills.sh](https://skills.sh/b/jdailey12/wufoo-theme-skill)](https://skills.sh/jdailey12/wufoo-theme-skill)

An agent skill for building polished, production-ready Wufoo custom CSS themes.

It can create a complete Wufoo `custom.css` from a screenshot, design brief, existing Wufoo CSS file, or the styling of a native form in an app project. It is especially useful when you have an Astro, React, Next.js, Svelte, Vue, Tailwind, CSS Modules, SCSS, or design-token-based form and want Wufoo to match it.

## Install

```bash
npx skills add https://github.com/jdailey12/wufoo-theme-skill --skill wufoo-theme-builder
```

For Codex global install:

```bash
npx skills add https://github.com/jdailey12/wufoo-theme-skill --skill wufoo-theme-builder -g -a codex -y
```

## What It Produces

- `custom.css`: a standalone Wufoo stylesheet for Theme Builder -> Advanced -> CSS URL.
- `preview.html`: a local preview covering the reference form, all major Wufoo field types, states, and CSS Layout Keywords.

## Skill Location

The skill lives at:

```text
skills/wufoo-theme-builder/SKILL.md
```

Skills.sh and the `skills` CLI discover this layout automatically.
