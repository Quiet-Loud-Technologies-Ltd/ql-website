# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Dev server

```bash
npx serve -p 3400 .
```

The preview server is pre-configured in `.claude/launch.json` — use `preview_start("ql-website")` to launch it. There is **no build step**. Edit `index.html` and reload the browser.

---

## Architecture

Everything — HTML, CSS, and JS — lives in a **single file: `index.html`** (~4 000 lines). There is no framework, bundler, or package.json.

### File layout (top to bottom)

| Region | Lines (approx) | Contents |
|---|---|---|
| CSS Block 1 | ~67–2 100 | Reset, design tokens, all component styles |
| CSS Block 2 (visual upgrade) | ~2 185–2 290 | Canvas, aurora, portal mockup, card spotlight, bento redesign |
| `</style>` / `<body>` | 2 275 | — |
| Tool notebook overlay | 2 350–2 465 | `#toolNotebook` — full-screen AI tool UI |
| Floating nav | ~2 466 | `<nav>` → `.nav-pill` |
| Bento overlay | ~2 420–2 535 | `#bentoMenu` — full-screen navigation grid |
| Page sections | 2 536–3 395 | `#pg-home`, `#pg-services`, `#pg-howwework`, `#pg-products`, `#pg-tools`, `#pg-casestudies`, `#pg-about`, `#pg-contact` |
| Script block 1 (router + tools) | ~3 400–3 800 | SPA router, bento logic, `TOOLS` data object, notebook open/close |
| CDN Lenis | ~3 800 | `unpkg.com/lenis@1.1.20` |
| Script block 2 (motion) | ~3 803–end | Canvas animation, portal trigger, count-up, spotlight, magnetic buttons |

### SPA router

Pages are `<section class="page" id="pg-{id}">`. Only one has `class="active"` at a time.

```js
showPage(id)   // swap active page + re-run scroll-reveal
goPage(id)     // closeMenu() then showPage()
openMenu()     // show bento overlay, pause Lenis
closeMenu()    // hide bento overlay, resume Lenis
```

Hash-based routing: `#home`, `#services`, `#howwework`, `#products`, `#tools`, `#casestudies`, `#about`, `#contact`.

### Tool notebook

`TOOLS` object at ~line 3 491 — each key is a tool ID with `name`, `family`, `starters`, `greeting`, `respond(msg)`, `result(msg)`. `openNotebook(toolId)` wires up the chat UI and renders the result panel. All tools belong to the **Nuqleus** family (Cirql tools have been removed).

---

## Design system

### Colour tokens (never change `--accent`)

```css
--bg: #0f0e0c  --bg2: #161512  --bg3: #1c1a17  --bg4: #231f1a
--ink: #f4f2ec  --ink-soft: #b8b4a8  --ink-faint: #6b6760
--line: #2a2722  --line2: #332f29
--accent: #7c3aed  --accent-dim: #5b2ab0  --accent-bright: #9a5cff
--accent-glow: rgba(124,58,237,.18)  --accent-glow-strong: rgba(124,58,237,.32)
```

### Typography

- **Headings:** Plus Jakarta Sans 500–800, `letter-spacing: -.02em` to `-.03em`
- **Body:** Plus Jakarta Sans 400–500
- **Mono labels / eyebrows:** Space Mono, 10–12px, ALL CAPS, `.10–.16em` tracking, `color: var(--ink-faint)`

### Motion

```css
--ease:        cubic-bezier(.16, 1, .3, 1)     /* expo-out */
--ease-spring: cubic-bezier(.34, 1.56, .64, 1) /* gentle overshoot */
```

Scroll-reveal: add `.reveal` class → `.in` toggled by `IntersectionObserver` via `setupReveal()`. Stagger: 28 ms per element, capped at 210 ms. Always gate with `prefers-reduced-motion`.

---

## Key patterns

### Primary CTA button (shiny spinning border)

Uses `@property --gradient-angle` to animate a conic-gradient border ring. Background is two layers: `linear-gradient(…) padding-box` (dark fill) + `conic-gradient(…) border-box` (spinning arc). Hover floods interior via **inset** `box-shadow` only — no outer glow, no `filter: drop-shadow`.

### Card hover system

Cards (`.card`, `.svc-card`, `.product-card`, `.case-card`, `.nuq-cap`, `.bento-tile`) get a JS-injected `<i class="card-glow">` child. The glow tracks pointer position via CSS custom properties `--mx`/`--my`. All card children need `position: relative; z-index: 1` to stay above the `::after` glow layer.

### Hero visual layer

- **Canvas** (`#heroNet`): node-network, cursor-reactive, DPR-aware, pauses on hidden tab. Uses `clip-path: inset(0)` to contain within hero bounds — **do not add `overflow: clip` to `.hero`**, it will clip the aurora and `::before` glow that bleed upward.
- **Aurora** (`.hero-aurora`): three blurred blobs with `inset: -20%` intentionally extending above the hero for atmospheric top-of-page glow.
- **Portal mockup** (`#heroPortal`): gets class `.go` via IntersectionObserver to trigger bar-fill and chart-grow CSS animations.

### Bento menu tiles

7 tiles in a 4-column grid. Feature tile (Services) spans `2×2`. CTA tile spans `1×2`. Key modifier classes: `.bt-feature`, `.bt-products`, `.bt-cta`, `.bt-tall`, `.bt-secondary`. Each tile gets a `<i class="card-glow">` injected by the spotlight JS.

---

## File editing rules

**Always use HTML entities for non-ASCII characters** in any code you write or edit (`&rarr;` not `→`, `&mdash;` not `—`, `&middot;` not `·`). If you use PowerShell to read/write this file, **always specify `-Encoding UTF8`** on `Get-Content` and use `[System.IO.File]::WriteAllLines(path, lines, [System.Text.UTF8Encoding]::new($false))` for writes. Failure to do so causes multi-round UTF-8/cp1252 mojibake across the entire file.

---

## Backlog (not yet built)

- **Big-type case study list** — giant row names, right-aligned mono tags + year, hover reveal
- **Scroll-pinned How We Work** — sticky steps, cross-fading visuals (Strategy → Design → Build → Launch)
- **Ghost watermark numbers** — `01`–`04` on service cards
- **Verify our claims block** — external reference links as trust signal
