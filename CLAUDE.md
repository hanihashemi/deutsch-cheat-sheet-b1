# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page, self-contained **telc Deutsch B1 study cheat sheet**. The entire app is one `index.html` (~1600 lines): embedded `<style>` at the top, content (`<nav>` + `<main>`) in the middle, and vanilla-JS `<script>` at the bottom. There are **no dependencies, no build step, no framework, and no tests**. Keeping it self-contained (works offline, opens by double-click, prints cleanly) is a deliberate constraint — do not add external CSS/JS/images, bundlers, or package managers.

Content is bilingual (German primary, English glosses) and derived from a source PDF; it is not official telc material.

## Commands

There is nothing to build/lint/test. Workflow is: edit `index.html`, view in a browser, push.

```bash
# Local preview (some browser extensions/automation reject file:// URLs, so prefer a server)
python3 -m http.server 8765
# then open http://localhost:8765/index.html

# Deploy: GitHub Pages serves from `main` root. A push IS the deploy (rebuilds in ~1 min).
git add -A && git commit -m "…" && git push
```

Live site: https://hanihashemi.github.io/deutsch-cheat-sheet-b1/ (`.nojekyll` disables Jekyll so files serve as-is).

## Architecture & conventions

**Section/card model.** Each topic is an independent `<section class="card" id="sec-...">` in `<main>`, with a `.card-head` (`.card-ico` + `.card-title`; the `<span class="en">` inside the title is the English subtitle) and a `.card-body`. Tables go in `.table-wrap` (adds horizontal scroll) around `table.sheet` with `thead`/`tbody`; cell classes are `.de` (German term), `.en` (English), `.ex` (example sentence). Adjective-ending matrices use `table.grid` instead.

**Navigation must mirror DOM order.** Nav is `.chip` links (`href="#sec-..."`) grouped in `.navgroup` blocks. The section's position in `<main>` must match the chip's position in the nav, because the scrollspy active-highlight tracks scroll order. The legend card carries `data-noindex` to exclude it from both search and scrollspy.

**Search & scrollspy auto-discover cards** — no JS edits needed when you add a section. Search (`runSearch`) decides section visibility from the whole section's text, then filters individual `tbody tr` rows only when the match is inside a row. So: put searchable prose/visuals anywhere inside a `.card`; put tabular data in `tbody` rows to get row-level filtering.

**Theming.** Light-only "Ozean" palette (cyan/petrol): all colors are CSS custom properties defined in `:root`; there is **no dark mode** (deliberately removed — don't re-add a toggle). New UI must use `var(--...)` tokens — including SVG diagrams — so a future palette change stays a one-block edit.

**Case color code (keep consistent everywhere).** Inline highlights use `.k` plus one of `.nom` (blue), `.akk` (red), `.dat` (green), `.gen` (amber); pill variants are `.tag.akku` / `.tag.dativ`. Convention: green = Dativ, red = Akkusativ, blue = Nominativ, amber = Genitiv.

**Visualizations are original and inline** — hand-drawn SVG (`<svg class="fig">` with `.box`/`.ball`/arrow classes) or pure-CSS components (`.prep-grid`, `.exam`/`.exam-panel`, `.letter`, `.satzbau`, `.flow`, frequency bars). No raster or external images (offline- and copyright-safe). Style SVG via CSS classes + theme vars, not hardcoded fills.

## When adding or editing a topic

1. Add the `<section class="card" id="sec-x">` in the DOM position that matches where its chip sits in the nav, and add the `.chip` to the correct `.navgroup`.
2. Reuse the existing classes above; no JavaScript changes are required for it to be searchable/navigable.
3. Verify by loading it in a browser (via the local server), test the search, and confirm no console errors.
