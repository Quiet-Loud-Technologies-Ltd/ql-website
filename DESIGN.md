# Quiet Loud Website — Design System

> Last updated: June 2025  
> Reference: [wibify.agency/en](https://wibify.agency/en) — adapted to purple brand, soft radii, no serif

---

## Stack

- **Single file:** `index.html` (~3,500 lines) — all HTML, CSS, JS in one file
- **Dev server:** `npx serve -p 3400 .`
- **No build step** — edit directly, reload browser
- **JS libraries:** Lenis v1.1.20 (smooth scroll, CDN)
- **Fonts:** Google Fonts — Plus Jakarta Sans + Space Mono

---

## Colour tokens

```css
--bg:       #0f0e0c   /* canvas */
--bg2:      #161512   /* elevated surfaces */
--bg3:      #1c1a17   /* cards, inputs */
--bg4:      #231f1a   /* deep inset */
--ink:      #f4f2ec   /* primary text */
--ink-soft: #b8b4a8
--ink-faint:#6b6760
--line:     #2a2722   /* borders */
--line2:    #332f29   /* stronger borders */
--accent:        #7c3aed   /* purple — NEVER change */
--accent-dim:    #5b2ab0
--accent-bright: #9a5cff
--accent-glow:        rgba(124,58,237,.18)
--accent-glow-strong: rgba(124,58,237,.32)
```

---

## Typography

| Role | Font | Weight | Notes |
|---|---|---|---|
| Headings | Plus Jakarta Sans | 500–800 | letter-spacing -.02 to -.03em |
| Body | Plus Jakarta Sans | 400–500 | line-height 1.6 |
| Mono labels | Space Mono | 400–700 | ALL CAPS, .10–.16em tracking, 10–12px |

Eyebrow pattern: `font-family: var(--ff-mono); font-size: 11px; letter-spacing: .16em; text-transform: uppercase; color: var(--ink-faint);`

---

## Motion

```css
--ease:        cubic-bezier(.16, 1, .3, 1)     /* expo-out settle */
--ease-spring: cubic-bezier(.34, 1.56, .64, 1) /* gentle overshoot */
```

- **Lenis** smooth scroll: `lerp: 0.1`. Paused when bento/notebook overlay is open.
- **Scroll-reveal:** `.reveal` class (opacity 0, translateY 26px) → `.in` via IntersectionObserver. Re-triggered on every page switch.
- Stagger: 28ms per element, max 210ms.
- `prefers-reduced-motion`: all reveals disabled.

---

## Navigation

### Floating pill (always visible)
```
[quietloud]  |  [Book a discovery call →]  [≡]
```
Fixed, centered, `top: 18px`. Glassmorphic dark fill + backdrop-blur.

### Bento overlay (hamburger)
Full-screen grid on open. 4 columns desktop, 2 tablet, 1 mobile.
Staggered tile entrance (transition-delay 40ms per tile).
Closes on: Close button · Esc key · backdrop click.

**Tiles:**
| Tile | Class | Size |
|---|---|---|
| Services | `bt-feature` | 2×2 |
| Contact CTA | `bt-cta` + `bt-tall` | 1×2, purple gradient |
| Products, Tools, How we work, Case studies, About, Home | default | 1×1 |

---

## Primary CTA button — Shiny CTA technique

Exact wibify.agency pattern, adapted to purple.

### How it works
1. `@property --gradient-angle` — CSS-animatable angle, spins 0→360deg
2. `@property --gradient-angle-offset` — 0deg default, 95deg on hover (shifts arc position)
3. `@property --cta-arc` — 10% default, 22% on hover (arc width)
4. Background = **two layers**:
   - `linear-gradient(#1c1a17, #1c1a17) padding-box` — dark fill
   - `conic-gradient(from angle, rgba(255,255,255,.18), purple arc%, ...) border-box` — spinning border
5. `border: 2px solid transparent` — gives the conic room to show
6. `::before` — dot-grid texture, masked to follow spinning arc
7. **Hover** — `inset box-shadow` floods interior from top-left with purple light

### Rules
- **No outer glow** (no `filter: drop-shadow`, no outer `box-shadow`)
- The grey `rgba(255,255,255,.18)` base makes the full border ring visible at rest
- Interior fill on hover is purely inset shadows — sits behind text naturally

```css
/* hover state */
box-shadow:
  inset 0 0 0 1px var(--bg4),
  inset 50px 24px 80px rgba(124,58,237,.55),
  inset 20px 8px 40px rgba(154,92,255,.3);
```

---

## Card hover system

All `.card`, `.svc-card`, `.product-card`, `.case-card`, `.nuq-cap`, `.story-card`:

```css
/* ::after radial glow bloom */
background: radial-gradient(130% 130% at 0% 0%, var(--accent-glow), transparent 55%);
opacity: 0 → 1 on hover

/* hover state */
border-color: var(--accent);
transform: translateY(-4px);
box-shadow: 0 18px 48px -20px rgba(0,0,0,.75), 0 0 0 1px var(--accent-glow);
```

> Keep `position: relative; z-index: 1` on all card children to stay above the `::after` layer.

---

## SPA Router

```js
showPage(id)   // switch active page, trigger scroll-reveal
goPage(id)     // close bento menu then showPage
openMenu()     // open bento, pause Lenis
closeMenu()    // close bento, resume Lenis
```

Pages: `home` · `services` · `howwework` · `products` · `tools` · `casestudies` · `about` · `contact`

---

## Wibify patterns not yet built (backlog)

- **Big-type project list** — Case Studies: giant row names, right-aligned mono tags + year, hover reveal
- **Scroll-pinned process** — How We Work: sticky steps, cross-fading visuals (Strategy → Design → Build → Launch)
- **Ghost watermark numbers** — `01`–`04` on service cards, mono tech chips
- **Verify our claims block** — external reference links as trust signal
