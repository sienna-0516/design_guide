# BARO AI Design System — Developer Handoff

> **Bring AI Right ON.** Brand + UI system for BARO AI (liquid-cooled AI infrastructure / POSEIDON).
> This package is what a developer needs to consume the system in a real codebase.

These are **design references and ready-made tokens/components** — not a finished
app. Recreate the screens in your target environment (React, Vue, Svelte, native…)
using your established patterns; the CSS tokens and the prebuilt React components
here are drop-in if your stack is React + plain CSS.

---

## 1. What's in this package

```
colors_and_type.css      ← single source of truth: tokens (color/type/space/radii/
                           shadow/motion) + @font-face + light/dark theme tokens
_ds_bundle.js            ← compiled React components, attach to window.BAROAIDesignSystem_236701
_adherence.oxlintrc.json ← lint rules that flag off-system colors/values (optional)
types/                   ← *.d.ts type signatures for each component
fonts/                   ← Poppins (display), Inter (body/UI), Freesentation (한글)
assets/logo/             ← logo lockups + spark marks (SVG/PNG)
assets/icons/            ← core filled icon set (42px grid)
assets/icons-line/       ← outlined "line" icon set (admin/product UI, currentColor)
assets/illustrations/    ← empty-state vectors
```

---

## 2. Install

### Fonts + tokens (always)
Copy `colors_and_type.css` and `fonts/` into your project, keeping `fonts/`
next to the CSS (the `@font-face` `src` paths are relative: `./fonts/…`). Then:

```html
<link rel="stylesheet" href="/path/to/colors_and_type.css">
```

or in a bundler:

```js
import "./colors_and_type.css";
```

Everything downstream (tokens, components) assumes this stylesheet is loaded.

### Prebuilt React components (optional — React stacks only)
The components are UMD-style and self-register on a global namespace. Load React 18
first, then the bundle:

```html
<script src="https://unpkg.com/react@18.3.1/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.production.min.js"></script>
<script src="/path/to/_ds_bundle.js"></script>
<script>
  const { Nav, Hero, Features, Stats, SpecBand, CTA, Footer } =
    window.BAROAIDesignSystem_236701;
</script>
```

If you'd rather rebuild the components natively, treat the `.jsx` source +
the `types/*.d.ts` as the spec and re-implement using tokens below.

---

## 3. Theming — light / dark

The system is **dark-first**; light mode is a token override. Set one attribute
on the root element and the whole UI flips:

```html
<html data-theme="light">   <!-- omit, or data-theme="dark", for dark mode -->
```

```js
// toggle + persist
const t = localStorage.getItem("theme") || "dark";
document.documentElement.setAttribute("data-theme", t);
```

**Semantic theme tokens** (these flip between modes — always use these for
surfaces/text/borders rather than raw hex):

| Token | Dark | Light |
| --- | --- | --- |
| `--surface-page` | `#131313` | `#F9F9F9` |
| `--surface-deep` | `#0A0A0A` | `#EDEDEA` |
| `--surface-band` | `#0E0E0E` | `#F0F0EE` |
| `--surface-raised` | `#1B1B1B` | `#FFFFFF` |
| `--text-1` | `#F9F9F9` | `#131313` |
| `--text-2` | `rgba(249,249,249,.66)` | `rgba(19,19,19,.66)` |
| `--text-3` | `rgba(249,249,249,.50)` | `rgba(19,19,19,.52)` |
| `--text-4` | `rgba(249,249,249,.40)` | `rgba(19,19,19,.42)` |
| `--hairline` | `rgba(255,255,255,.08)` | `rgba(19,19,19,.12)` |
| `--hairline-soft` | `rgba(255,255,255,.06)` | `rgba(19,19,19,.08)` |
| `--outline-strong` | `rgba(255,255,255,.40)` | `rgba(19,19,19,.32)` |
| `--nav-bg` | `rgba(19,19,19,.55)` | `rgba(249,249,249,.72)` |
| `--card-shadow` | `0 4px 16px rgba(0,0,0,.24)` | `0 4px 16px rgba(19,19,19,.08)` |

**Brand yellow `#FFDE2A` is constant in both themes.** The CTA yellow band stays
yellow in both modes (brand accent moment).

### Logo per theme (important brand rule)
The logo follows the **surface**, not the page:

- **Dark surface →** all-yellow lockup (`assets/logo/baro-logo-yellow.svg`)
- **Light surface →** black text + yellow-filled star (`assets/logo/baro-logo.svg`)
- Monochrome white (over photos): `assets/logo/baro-logo-white.svg`

Never use a yellow-outline star with white text, and never re-stack/re-space the
spark and wordmark — use the lockup SVGs as-is. For tight spaces use the spark
alone: `spark-yellow.png` (dark) / `spark-brand.png` (black+yellow, on light).

To swap the logo image with the theme, drive it from a CSS variable defined per
page (url resolves relative to the file that declares it):

```css
:root              { --logo-lockup: url(assets/logo/baro-logo-yellow.svg); }
[data-theme=light] { --logo-lockup: url(assets/logo/baro-logo.svg); }
.logo { background: var(--logo-lockup) center/contain no-repeat; }
```

### Logo placement, clearspace & alignment
- **Alignment anchor = the "A".** When centering the lockup (slide covers, hero,
  any centered placement), it must center on the **A** of BARO and the marked
  height line — **not** the bounding box. The provided lockup SVGs already bake
  this in: their canvas is `296 × 176` with the A's optical center at the canvas
  center, so just center the SVG normally (`center/contain`, `margin:auto`,
  fl/grid centering) and it lands correctly. Don't re-crop or trim the SVG
  viewBox — that destroys the anchor and the logo will sit low/left of true center.
- **Clearspace:** keep a margin of **at least the spark's width** clear on all
  four sides of the lockup. Nothing (text, UI, image edge) inside that zone.
- **Minimum size:** lockup **≥ 120px wide** (or **≥ 56px tall**); spark-alone
  **≥ 16px**. Below that, use the spark mark, not the full lockup.
- **Left-aligned contexts** (nav, footer brand column): align the lockup's left
  edge to the column grid; use `background-position: left center`.
- **Fixed relationship:** the spark sits slightly upper-left of the wordmark —
  this is fixed in the artwork. Never re-stack the spark directly above the
  wordmark, re-space the two, rotate, recolor, or add effects.

```css
/* clearspace + min-size example */
.logo {
  --spark-w: 0.22em;                 /* spark ≈ 22% of lockup width */
  min-width: 120px;
  padding: var(--spark-w);           /* clearspace on all sides */
  background: var(--logo-lockup) center/contain no-repeat content-box;
}
```

---

## 4. Component API (React)

All on `window.BAROAIDesignSystem_236701`. Each is a full-width marketing section.

| Component | Props | Notes |
| --- | --- | --- |
| `Nav` | `active?: string` (default `"Home"`) | Top nav; spark mark + links + yellow CTA. |
| `Hero` | — | Full-bleed photo hero (stays dark in both themes by design). |
| `Features` | — | Three-up feature cards. |
| `Stats` | — | Four big-number data strip. |
| `SpecBand` | — | POSEIDON spec headline + facts table. |
| `CTA` | — | Yellow CTA band (constant yellow in both themes). |
| `Footer` | — | Four-column footer incl. Korean address block. |

Compose:

```jsx
<><Nav active="Home" /><Hero /><Features /><Stats /><SpecBand /><CTA /><Footer /></>
```

A composed, themeable reference page (with the light/dark toggle) lives in the
live project at `ui_kits/website/index.html`.

---

## 5. Design tokens (the full scale)

**Brand:** `--baro-yellow #FFDE2A` · `--baro-black #131313` · `--baro-white #F9F9F9`
Yellow should occupy **10–30%** of any composition — an accent, not a flood.

**Yellow variants:** `#FFA800 #FFC200 #FFD243 #FFE65E #FFEE92 #FFF7B1`
**Greyscale (warm-biased):** `#2B2B2B #393939 #4D4D4D #656565 #B2B2B2 #D9D9D9 #F0F0F0`
**Semantic:** positive `#22C678` · negative `#C60909` / `#EE8484` · blue `#2E92FF` / `#267AD6` · purple `#A23DF2`
**Gradients (only these two):** aqua `#3EFFB9→#2AF1F1` · dawn `#64A7E6→#DCE2C5`

**Type families** — language picks the face automatically (stacks list Latin
first, Freesentation next):
- Display (EN): **Poppins** (`--font-display`) — Gilroy substitute. H1 48 / H2 32 / H3 24 / H4 20 / H5 18, all −1% tracking.
- Body/UI (EN): **Inter** (`--font-sans`).
- 한글: **Freesentation** (`--font-kr`) — always, never Inter for Korean.
- Mono: `--font-mono` (technical values only).

**Body scale (base = 14px):** L 16 (lead only) · **M 14 (default body)** · S 13 · Xs 12 (caption only).

**Spacing — 8px grid:** `4 8 12 16 24 32 40 48 64 80 96 120` (`--sp-1`…`--sp-12`).
**Radii:** sm 6 · md 10 · lg 20 · pill 999 (`--radius-*`).
**Shadows:** `--shadow-btn` · `--shadow-card` · `--shadow-hover` · `--shadow-glow-y` (yellow glow).
**Motion:** standard `cubic-bezier(.2,.6,.2,1)` · spring `cubic-bezier(.34,1.3,.64,1)`; durations `--dur-1..4` = 120/200/320/500ms.

> All values exist as CSS custom properties in `colors_and_type.css` — prefer
> `var(--token)` over hardcoded hex so theming and lint keep working.

---

## 6. Icons & assets

- **Core icons** `assets/icons/` — filled silhouettes, 42px viewBox, single color.
- **Line icons** `assets/icons-line/` — outlined, ~2.6px stroke, `stroke="currentColor"`
  so they inherit text color (set `color:` to theme them). 30 admin/product icons:
  alert-circle, check, cloud-upload, eye, file-*, folder-plus, info, layout-grid,
  link, lock, network, octagon-x, panel-left, pencil, setting, shield, user(+check/plus),
  x-circle, users, etc.
- **Empty states** `assets/illustrations/` — `empty-no-data.svg`, `empty-unreadable.svg`.
- **Color rule:** `#F9F9F9` on dark · `#131313` on light · `#FFDE2A` only when the
  icon is a primary brand accent.
- **Never:** emoji, unicode-glyph icons, third-party icon packs mixed in, multicolor fills.

---

## 7. Do / Don't (quick reference)

- ✅ Link `colors_and_type.css` once; build everything from `var(--token)`.
- ✅ Flip themes with `data-theme` on the root; use semantic surface/text tokens.
- ✅ Korean text → Freesentation (apply `.kr` or `--font-kr`).
- ✅ Keep yellow at 10–30% coverage; focus ring is `2px solid --baro-yellow`.
- ❌ Don't recolor/rotate/re-space the logo or spark.
- ❌ Don't invent gradients or colors outside the tokens.
- ❌ No emoji, no warm marketing stock, no illustrated people icons.

---

*Generated from the live BARO AI Design System project. The interactive reference
(every token + component as a card, with the light/dark toggle) lives in that
project's Design System tab.*
