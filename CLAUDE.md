# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A self-contained **telc Deutsch B1 study cheat sheet**, split across **three sibling HTML pages** that share the same look and behaviour:

- **`index.html`** — telc B1 Prüfung & Redemittel (exam overview, Brief schreiben, Meinung/Sprechen, Lust haben, Was für…, Konjunktiv II, Vorschläge, Bestätigen & Reagieren).
- **`verben.html`** — Verben & Zeiten + Satzbau & Nebensätze (all tenses, modal/separable verbs, imperative, passive, word order, negation, connectors, subordinate clauses).
- **`faelle.html`** — Fälle & Präpositionen + Extras (articles/cases, n-Deklination, Genitiv, prepositions, verb+preposition, reflexives, comparative, adjective endings, frequency, direction).

Each page is a **complete standalone file**: embedded `<style>` at the top, content (`<nav>` + `<main>`) in the middle, and vanilla-JS `<script>` at the bottom. There are **no dependencies, no build step, no framework, and no tests**. Keeping each page self-contained (works offline, opens by double-click, prints cleanly) is a deliberate constraint — do not add external CSS/JS/images, bundlers, or package managers.

**The `<style>` and `<script>` blocks are shared by copy — byte-identical across all three pages.** If you change one, apply the *exact* same change to the other two and verify (e.g. `for f in index verben faelle; do sed -n '/<style>/,/<\/style>/p' $f.html | md5 -q; done | sort -u` should print one hash; same for `<script>`).

Content is bilingual (German primary, English glosses) and derived from a source PDF; it is not official telc material.

## Commands

There is nothing to build/lint/test. Workflow is: edit the relevant page, view in a browser, push.

```bash
# Local preview (some browser extensions/automation reject file:// URLs, so prefer a server)
python3 -m http.server 8765
# then open http://localhost:8765/index.html  (or /verben.html, /faelle.html)

# Deploy: GitHub Pages serves from `main` root. A push IS the deploy (rebuilds in ~1 min).
git add -A && git commit -m "…" && git push
```

Live site: https://hanihashemi.github.io/deutsch-cheat-sheet-b1/ (`.nojekyll` disables Jekyll so files serve as-is).

## Architecture & conventions

**Three-page layout.** The pages are linked by a **page switcher** (`nav.pageswitch` under the `<header>`: three `<a>` links, the current page carrying `aria-current="page"`). All three share the same header/footer/search/theme chrome and an identical `<style>`/`<script>`; only the `<nav>` groups and `<main>` cards differ. Which topic lives on which page is fixed by its group (see the list above) — put a new card on the page that matches its group.

**Cross-page links.** In-card cross-references use a bare fragment (`href="#sec-x"`) when the target card is **on the same page**, and a file-qualified link (`href="faelle.html#sec-x"`) when it's **on another page**. Nav `.chip` links are always same-page fragments. If you move a card between pages, re-point every link that now crosses a page boundary.

**Section/card model.** Each topic is an independent `<section class="card" id="sec-...">` in `<main>`, with a `.card-head` (`.card-ico` + `.card-title`; the `<span class="en">` inside the title is the English subtitle) and a `.card-body`. Tables go in `.table-wrap` (adds horizontal scroll) around `table.sheet` with `thead`/`tbody`; cell classes are `.de` (German term), `.en` (English), `.ex` (example sentence). Adjective-ending and case-declension matrices use `table.grid` instead.

**Navigation must mirror DOM order.** Nav is `.chip` links (`href="#sec-..."`) grouped in `.navgroup` blocks. A section's position in that page's `<main>` must match its chip's position in that page's nav, because the scrollspy active-highlight tracks scroll order. The legend card (`#sec-legend`, the "Farbcode der Fälle" box) carries `data-noindex` to exclude it from search/scrollspy and is duplicated at the top of `<main>` on all three pages.

**Search & scrollspy auto-discover cards** — no JS edits needed when you add a section (search/scrollspy operate per page). Search (`runSearch`) decides section visibility from the whole section's text, then filters individual `tbody tr` rows only when the match is inside a row. So: put searchable prose/visuals anywhere inside a `.card`; put tabular data in `tbody` rows to get row-level filtering.

**Theming.** Light-only "Ozean" palette (cyan/petrol): all colors are CSS custom properties defined in `:root`; there is **no dark mode** (deliberately removed — don't re-add a toggle). New UI must use `var(--...)` tokens — including SVG diagrams and the page switcher — so a future palette change stays a one-block edit (replicated across the three pages).

**Case color code (keep consistent everywhere).** Inline highlights use `.k` plus one of `.nom` (blue), `.akk` (red), `.dat` (green), `.gen` (amber); pill variants are `.tag.akku` / `.tag.dativ`. Convention: green = Dativ, red = Akkusativ, blue = Nominativ, amber = Genitiv.

**Visualizations are original and inline** — hand-drawn SVG (`<svg class="fig">` with `.box`/`.ball`/arrow classes) or pure-CSS components (`.prep-grid`, `.exam`/`.exam-panel`, `.letter`, `.satzbau`, `.flow`, frequency bars). No raster or external images (offline- and copyright-safe). Style SVG via CSS classes + theme vars, not hardcoded fills.

## When adding or editing a topic

1. **Pick the page** that matches the topic's group: telc/Redemittel → `index.html`; Verben & Zeiten or Satzbau & Nebensätze → `verben.html`; Fälle & Präpositionen or Extras → `faelle.html`.
2. Add the `<section class="card" id="sec-x">` in the DOM position that matches where its chip sits in that page's nav, and add the `.chip` to the correct `.navgroup`.
3. Reuse the existing classes above; no JavaScript changes are required for it to be searchable/navigable. Link to same-page cards with `#sec-x`, to other-page cards with `page.html#sec-x`.
4. If you touch the shared `<style>` or `<script>`, replicate the change byte-for-byte into all three pages.
5. Verify by loading the page in a browser (via the local server), test the search, click the page switcher, and confirm no console errors.
