# Sharpening Iron

**Seven checkpoints. One covenant. A letter from Mark to Rob, set as a broadsheet.**

> A single-file interactive HTML experience — no build step, no dependencies beyond CDN-hosted Three.js and GSAP. Open `index.html` in any modern browser.

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![Three.js](https://img.shields.io/badge/Three.js-000000?logo=three.js&logoColor=white)
![GSAP](https://img.shields.io/badge/GSAP-88CE02?logo=greensock&logoColor=white)

---

## What it is

*Sharpening Iron* is a scroll-driven narrative built as one self-contained HTML file. It presents a personal letter from Mark to Rob across **seven checkpoints**, mixing flat editorial design, a continuous rope-and-type horizontal pull, and three full Three.js WebGL scenes (chapters 01, 05, 07).

### Tone arc

| Block | Type | Tone |
|-------|------|------|
| **01 — The Shared Horizon** | 3D hero | Sacred, slow |
| **02 — The Mirror** | Flat scroll | Dry |
| **03 — The Exchange of Value** | Flat scroll | Dry |
| **04 — The Better ROI** | Flat scroll | Dry |
| **05 — The Mutual Challenge** | 3D hero | Warm, playful |
| **06 — The Confidence of the Climb** | Static still | Still |
| **07 — The Covenant** | 3D hero | Loud |

### 3D scenes (Three.js)

- **01 · Summit** — Layered paper-cut ridges in fog, a yellow sunrise line, and the headline "SAME MOUNTAIN" printed across the peaks.
- **05 · Climb** — Voxel headline "HOLD THE LINE" as a cliff. Two faceless folded-paper figures climb; the camera swaps who anchors and who leads as you scroll.
- **07 · Forge** — Two iron bars heat up and strike three times. Each hit shakes the camera, flashes the page yellow, and lands a letter of "IRON ON IRON."

### Flat sections (chapters 02–04)

One continuous **300vw horizontal track** pulled by scroll. A braided climbing rope runs down the left gutter with seven pitstop rings. Each panel has its own type treatment:
- **02** — Mirrored headline resolving into "A COMPASS."
- **03** — "GIVE →" and "← TAKE" sliding past a tightening knot.
- **04** — A compounding counter ticking up to **37.8×**.

---

## Tech stack

| Dependency | Version | Purpose |
|------------|---------|---------|
| Three.js | r128 | WebGL 3D scenes (CDN) |
| GSAP | 3.12.5 | Intro timeline + microinteractions (CDN) |
| Anton | Google Fonts | Display / masthead |
| Space Mono | Google Fonts | Body / UI |

No npm. No bundler. No build step.

---

## Getting started

```bash
# Clone or download the repo
git clone https://github.com/<your-username>/sharpening-iron.git
cd sharpening-iron

# Serve locally (any static server works)
python3 -m http.server 8080
# or
npx serve .
```

Then open `http://localhost:8080` in Chrome, Firefox, Safari, or Edge.

### Quick preview without cloning

If you just want to see it, open the raw `index.html` from the repo in a browser, or use a code-playground link if published.

---

## File structure

```
sharpening-iron/
├── index.html          # Everything: HTML, CSS, JS, WebGL
├── README.md           # This file
└── vercel.json         # Vercel deployment config
```

---

## Accessibility & safety nets

- **Reduced motion:** If the user has `prefers-reduced-motion: reduce`, all scrub animations are removed, the horizontal pull collapses to a vertical stack, and the page renders at its resolved state.
- **No WebGL fallback:** If Three.js fails to initialise, the three hero chapters become flat colour modules (cream, yellow, black) with their typography intact.
- **Keyboard:** All interactive chips are `<button>` elements with `aria-label`s. The meter ticks are focusable and announce the target checkpoint.
- **Screen readers:** Canvas is `aria-hidden`. Checkpoint labels are announced via `aria-live="polite"` toast.

---

## Deployment

### GitHub Pages

1. Push the repo to GitHub.
2. Go to **Settings → Pages**.
3. Set **Source** to `Deploy from a branch` → `main` → `/root`.
4. Save. GitHub will serve `index.html` at `https://<user>.github.io/sharpening-iron/`.

### Vercel

```bash
# Install Vercel CLI if you haven't
npm i -g vercel

# Deploy from the project root
cd sharpening-iron
vercel --prod
```

Or connect the GitHub repo to Vercel via the dashboard for automatic deploys on push.

---

## Customising the letter

The seven checkpoints live as HTML `<section>` elements inside `<main>`. Each has a `data-short` attribute used in the meter:

```html
<section class="chapter c1" id="p1" data-short="HORIZON" data-scene="summit">
  ...
</section>
```

To change the copy, edit the headline (`<h2>`), quote (`.quote`), snippet (`.snippet`), and hashtag (`data-tag`) in place. The emoji cell is the last child of each `.cap`.

To add a checkpoint:
1. Add a new `<section>` or `<article class="panel ...">`.
2. Add a corresponding `<button class="tk">` in the meter JS or rebuild the tick array.
3. Update the `items` and `shorts` arrays in the page script if you change the order.

---

## Design notes

- **Colour palette:** Paper `#F2EEE3`, Ink `#0A0A0A`, Yellow `#FFD400`.
- **Type scale:** Fluid `clamp()` sizing. Anton for display; Space Mono for UI.
- **Motion:** Intro is GSAP. Scroll scrubbing is a custom `requestAnimationFrame` loop with exponential smoothing (`1 - exp(-dt * 7)`). Dash-offset on the rope is updated per frame.
- **Performance:** Pixel ratio is capped at 2. Three.js scenes use `frustumCulled = false` only where needed (instanced meshes). `will-change` is used sparingly on elements that actually move.

---

## Known caveats

- **Google Fonts CDN:** Requires internet on first load. After that the browser caches them.
- **godly.website** now redirects to recent.design.
- **Rope & checkpoint placement** may need nudging on real devices — the values are in `buildRopes()` and the `panel` grid.

---

## License

MIT

---

## BYOK Models

A companion page lists free, self-replenishing LLM API providers and models from the [awesome-free-byok-models](https://github.com/velo4705/awesome-free-byok-models) list.

- **Page:** `byok.html` — filterable table of 55 providers and their free models.
- **Data:** `providers.json` — parsed from the upstream README.

Open `byok.html` alongside the main broadsheet, or deploy it as part of the same GitHub Pages site.
