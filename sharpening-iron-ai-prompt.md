# Sharpening Iron — AI Agent Recreation Prompt

## Project Overview

**Title:** Sharpening Iron — A letter from Mark to Rob
**Format:** Single-page broadsheet newspaper / editorial print experience
**Theme:** Seven checkpoints of a mutual growth covenant between two friends (Mark and Rob), using mountain climbing and iron-forge metaphors
**Tone:** Bold, brutalist, typographic, sincere, masculine, motivational
**Core metaphor:** "Iron sharpens iron" — mutual accountability and growth

---

## Design System

### Color Palette
| Token | Hex | Usage |
|-------|-----|-------|
| `--y` (Yellow) | `#FFD400` | Accent color, highlights, hero backgrounds, interactive elements, sun/forge fire |
| `--k` (Black) | `#0A0A0A` | Primary dark, backgrounds, text on light surfaces, ink for lettering |
| `--p` (Paper) | `#F2EEE3` | Off-white paper/cream, primary light background, body text on dark surfaces |

### Typography
- **Display/Headlines:** Anton (embedded via base64 WOFF2) — Impact, "Arial Narrow" fallbacks; all-caps, condensed, heavy weight, tight letter spacing for newspaper masthead feel
- **Body/UI:** Space Mono (embedded via base64 WOFF2, 400 + 700 weights) — ui-monospace, Menlo, Consolas fallbacks; monospace for editorial/news ticker feel, labels, captions, buttons
- No system sans-serif — strictly display + monospace for that print/broadsheet character

### Layout Variables
- `--rule: clamp(5px, .7vw, 10px)` — Thick border rule weight (newspaper column rules)
- `--pad: clamp(16px, 3vw, 48px)` — Responsive page padding
- `--meter: 38px` — Fixed height of the top progress/navigation bar

### Visual Language
- **Brutalist geometry:** Hard rectangles, sharp 90° angles, thick borders, no border-radius anywhere
- **Paper cut aesthetic:** Flat color blocks, layered silhouettes, no gradients (except fog/lighting in WebGL)
- **Stamps:** Rotated rubber-stamp elements (EXCLUSIVE, CHECKPOINT, BREAKING: GROWTH) with thick borders, slight rotation (-7° to -8°)
- **Box shadows:** Hard offset shadows (`6px 6px 0`) on buttons, no blur for print/block-print feel
- **Voxel/3D pixel typography:** Hero scenes use Three.js instanced box meshes to render brutalist 3D block letters
- **Braided ropes:** SVG two-strand alternating black/yellow/white striped ropes that animate and tighten to knots
- **Meter navigation:** Fixed top progress bar with 7 checkpoint ticks, percentage readout, yellow fill bar

---

## Page Structure & Sections

The page scrolls vertically with this exact structure:

### 1. Fixed Altitude Meter (always visible)
- Fixed to top, z-index 40
- Three parts: left label ("CHECKPOINT XX/07"), middle 7 tick buttons (one per checkpoint, with labels visible on desktop), right percentage (XX%)
- Yellow fill bar animates across as you scroll (uses `mix-blend-mode: difference`)
- Clicking any tick smoothly scrolls to that checkpoint
- On mobile (<560px), the left label is hidden

### 2. Masthead (flat, no WebGL)
- Cream paper background (`--p`), black text
- Two-line giant Anton headline: "SHARPENING" on line 1, "IRON" on line 2 with a braided rope SVG slot filling the remaining space
- GSAP intro animation: letters flip up from below with 3D rotation, rope slot reveals left-to-right, black bar slides in, "EXCLUSIVE" stamp bounces in with elastic ease
- Black sub-bar: "A LETTER FROM MARK TO ROB — VOL. 1 — NO. 7 CHECKPOINTS"
- Rotated yellow "EXCLUSIVE" stamp in top-right, position aligned to the rope slot
- Masthead has a thick bottom border rule

### 3. Checkpoint 01: The Shared Horizon (WebGL Hero, 340vh tall)
- **Scene:** Layered paper-cut mountain silhouettes (7 layers) in black on cream, receding into fog, yellow sun rising behind peaks with a thin yellow horizon line
- **Progress animation:** Starts high looking at paper sky, eases down to reveal the summit view; sun rises; yellow horizon line draws in
- **Text overlay:**
  - Top-left header box (black on yellow outline): big outline "01" next to "The Shared Horizon"
  - Rotated "CHECKPOINT" stamp top-right
  - Bottom caption bar (yellow): pull quote "Different terrain, same mountain." + letter snippet + copyable `#SameMountain` tag button + mountain emoji (🏔️) in paper cell to the right
- **Voxel text:** Floating "SAME MOUNTAIN" (or stacked "SAME / MOUNTAIN" on mobile) appears in the distance in black
- The WebGL canvas is fixed and shows through this sticky section

### 4. Checkpoints 02-04: Horizontal Pull (flat, 340vh tall)
Sticky section where scrolling down pulls three full-width panels horizontally left:
- **SVG braided rope:** Two black strands with alternating yellow/white dashes run along the bottom across all 3 panels, animating as you scroll, tightening to a knot in the middle
- **Panel 02 (black background, yellow text):** "The Mirror, Not the Magnifying Glass"
  - Mirror art: "Not a gap." with a rule line and reflected text below that resolves from "Not a gap." to "A compass." as you scroll
  - Quote: "Not a gap. A compass."
  - Snippet: "I didn't see someone who needs fixing. I saw someone operating at a frequency most people never find."
  - Copy tag: `#CalibrateHere`
  - Mirror emoji (🪞)
- **Panel 03 (yellow background, black text):** "The Exchange of Value"
  - Flow art: Two outlined words "Give →" and "← Take" slide past each other, crossing in the middle
  - Quote: "Resource flowing both ways."
  - Snippet: "You called it "just being a good person." I call it leadership. Now I want to put actual weight on your rope."
  - Copy tag: `#WeightOnTheRope`
  - Handshake emoji (🤝)
- **Panel 04 (black background, yellow text):** "The Better ROI"
  - Compounding counter: Counts up from 1.0× to 37.8× (representing 1% daily growth over 365 days)
  - Label: "1% A DAY. 365 DAYS."
  - "BREAKING: GROWTH" stamp pops in scaled up, shrinks to normal
  - Quote: "Invest in what compounds."
  - Snippet: "These aren't side projects; they're the foundations. Don't get distracted by base camp comforts."
  - Copy tag: `#Compounding`
  - Chart up emoji (📈)
- On mobile (<760px), panels stack vertically with number+emoji top row, content below
- Panel numbers (02/03/04) parallax slightly left/right with scroll

### 5. Checkpoint 05: The Mutual Challenge (WebGL Hero, 340vh tall)
- **Scene:** Yellow background, voxel cliff face spelling "HOLD THE LINE" in black blocks; two paper-cut faceless climbing figures connected by a quadratic bezier rope
- **Progress animation:** First figure (Mark?) climbs up the cliff; second figure starts below, catches up as they swap roles (belay → climb); rope physics between them; they meet at the top and cheer
- **Camera:** Frames both climbers, zooms when close, pulls back as the rope stretches
- **Text overlay:**
  - Top-left header: outline "05" next to "The Mutual Challenge"
  - Bottom caption bar (black): pull quote "Depth over speed. Visibility over humility." + letter snippet + copyable `#HoldTheLine` tag + crossed swords emoji (⚔️)
- On mobile, the letter snippet is hidden by default (toggleable via SNIPPETS button)

### 6. Checkpoint 06: The Confidence of the Climb (silent/static, 100vh tall)
- **Cream paper background — NO ANIMATIONS, NO TRANSITIONS** (intentionally quiet, still moment)
- Static content:
  - Number "06"
  - Heading: "The Confidence of the Climb"
  - Bold quote: "You have the map. I have the compass."
  - Body text: "Neither of us has this figured out. But together? We see the route more clearly."
  - Copy tag `#MapAndCompass` + compass emoji (🧭)
- A thick yellow horizontal rule runs across at ~66% viewport height
- A static SVG compass rose sits on the right; the needle (after 2s delay on intersection) settles from 150° to 38° with GSAP back ease — **this is the ONLY animation in this section**
- Buttons have NO hover/active effects in this section (no lift, no shadow change)
- On mobile, compass is smaller and positioned lower

### 7. Checkpoint 07: The Covenant (WebGL Hero, 460vh tall — tallest section)
- **Scene:** Black background, two dark iron bars that heat up (turn yellow) as you progress; they strike together THREE times (at 26%, 50%, 74% scroll through section)
- **Each strike:** Hit-stop (60ms freeze), camera shake, expanding ring shockwave, yellow/white sparks burst outward, and one word of "IRON ON IRON" voxel text assembles from scattered pieces
  - Strike 1: "IRON" lands
  - Strike 2: "ON" lands
  - Strike 3: "IRON" lands (full phrase complete)
- Scroll warping: Scroll distance is stretched around each strike so even fast scrolling lands every impact
- **Text overlay:**
  - Top-left header (yellow on black outline): outline "07" next to "The Covenant"
  - Rotated "CHECKPOINT" stamp top-right
  - Bottom caption bar (yellow): pull quote "Iron on iron." + letter snippet + copyable `#IronOnIron` tag + anchor emoji (⚓)
- Screen flash is intentionally disabled (set to 0 in code) — only camera shake and rings/sparks
- On mobile, the letter snippet is hidden by default

### 8. Footer
- Cream paper background
- Large Anton headline: "Bring your full self to this ascent. I'm bringing mine."
- Row with:
  - Generated SVG barcode (random black bars on white, procedural)
  - Signature: "—Mark" in Anton
  - "BACK TO BASE CAMP ↑" button (black on yellow, yellow on black hover)
- Bottom ticker tape: Infinite scrolling Anton text "See you at the next checkpoint — Mark ▲" repeated, black background, yellow text, animates left continuously

### 9. Mobile Snippet Toggle Button
- Only visible on screens <860px
- Fixed position top-right below meter
- Toggles whether the letter snippet text is shown on the current hero chapter (01, 05, 07)
- Shows toast if you try to use it outside a hero section

### 10. Toast Notifications
- Fixed bottom-center, black with yellow border, monospace bold text
- Slides up when hashtags are copied, says "COPIED #HashTag"
- Auto-dismisses after 1.7 seconds

---

## Interactions & Microinteractions

### Hashtag Copy Buttons
- All `.tag` elements: cream background, thick black border, hard shadow `6px 6px 0 black`
- Hover: Lifts up (-3px,-3px), shadow grows to 9px, background inverts (ink color)
- Active: Presses down (4px,4px), shadow disappears to 0
- Click: Copies hashtag text to clipboard, shows toast, elastic bounce animation (GSAP)
- In section 06 (calm), hover/active effects are disabled entirely

### Emoji Cell Hover (desktop/fine pointer only)
- Emoji in the right-side cells scale up with elastic ease on enter
- On pointer move: emoji tilts in 3D (rotationY/rotationX based on cursor position, perspective 520px)
- Smooth return to center on leave with elastic easing

### Numbers (02/03/04 panel headers, hero outline numbers)
- Outline text (stroke only, no fill) by default
- On hover: fills in with solid color (2-step animation, no easing)

### Pull Quotes
- Ink color on bg color by default
- On hover: Inverts to cream on black (paper)

### Back to Top Button
- Solid black with yellow text, thick black border
- Hover: Inverts to yellow with black text
- Click: Smooth scrolls to top

### Reduced Motion Support
- Respects `prefers-reduced-motion: reduce`
- All WebGL sections become static 100vh blocks showing final state
- Horizontal run becomes vertical stacked panels with no transform
- Ticker animation stops
- All transitions/animations removed
- Ticking scroll event instead of rAF loop

### WebGL Fallback
- If Three.js fails or WebGL unavailable, `.nogl` class added
- Hero chapters become flat colored blocks (cream, yellow, black respectively) with static headers/captions
- No canvas, no 3D

---

## Technical Implementation Notes

### Embedded Assets
- **Both fonts are embedded as base64 WOFF2 in @font-face rules** — NO external font requests, self-contained
- Three.js r128 loaded from cdnjs.cloudflare.com
- GSAP 3.12.5 loaded from cdnjs.cloudflare.com

### Architecture
- Single self-contained HTML file (all CSS in `<style>`, all JS in `<script>` at end of body)
- Zero dependencies beyond the two CDN scripts
- Vanilla JavaScript, no frameworks
- IIFE wrapper (`(function(){ 'use strict'; ... })()`) to avoid global scope pollution

### WebGL Architecture
- Single shared full-screen fixed canvas, `visibility: hidden` by default, only visible when a hero section is in view
- Three scenes, one camera each — switched based on which section is on screen
- View offset used to reserve bottom space for caption overlaps (camera doesn't render behind the text)
- `setViewOffset` critical for framing: shifts the camera viewport up so content sits above the caption bar
- Portrait/landscape detection rebuilds the forge scene with different cell size/text layout
- Voxel text system: Renders text to 2D canvas, samples pixels into a grid, uses InstancedMesh with box geometry + per-face vertex colors (front = accent color, sides = paper color for 3D depth)
- Rope in climb scene uses quadratic bezier between two anchor points with instanced striped boxes, sags realistically based on distance

### Scrolling Physics
- All animations driven by scroll position (not time), with exponential smoothing (`ps += (raw - ps) * (1 - exp(-dt*7))`) to avoid jank
- Horizontal pull: track translates by `-runPs * 2 * vw` (maps 0-1 vertical scroll to 0-200vw horizontal movement, since the track is 300vw for 3 panels)
- Hero chapters use `position: sticky; top: 0` with tall wrapper (340vh/460vh), canvas renders inside sticky 100svh stage
- Clip-path reveals for headers/captions — they wipe in from left as progress increases

### Accessibility
- All interactive elements are real `<button>` elements
- `aria-label` on all icon buttons and emoji cells
- `aria-hidden="true"` on decorative elements (outlined numbers, SVGs, stamps)
- `aria-live="polite"` on toast
- `aria-current="true"` on active meter tick
- `role="img"` + aria-label on SVG illustrations (compass, barcode)
- Focus visible: 4px solid currentColor outline, 3px offset
- Semantic HTML: `<main>`, `<header>`, `<nav>`, `<section>`, `<article>`, `<footer>`
- `-webkit-text-size-adjust: 100%` to prevent iOS text boosting

### Responsive Breakpoints
| Breakpoint | Behavior |
|------------|----------|
| < 560px | Meter hides left label |
| < 760px | Flat panels stack vertically (number+emoji top, content bottom); calm section compass repositions |
| < 860px | Hero captions stack single-column; snippet text smaller; SNIPPETS button appears; checkpoint 02-04 snippets smaller; desktop-only emoji tilt disabled |
| < 980px | Meter tick labels hidden (only numbers show) |
| `w/h < 0.85` | Portrait mode triggers different camera framing and voxel text layout |

---

## Content Reference: All Seven Checkpoints

| # | Title | Core Metaphor | Quote | Hashtag | Emoji | Key Visual |
|---|-------|---------------|-------|---------|-------|------------|
| 01 | The Shared Horizon | Mountain summit, shared journey | "Different terrain, same mountain." | #SameMountain | 🏔️ | Layered mountains, sunrise |
| 02 | The Mirror, Not the Magnifying Glass | Reflection, seeing potential not flaws | "Not a gap. A compass." | #CalibrateHere | 🪞 | Mirror text resolving |
| 03 | The Exchange of Value | Mutual exchange, give and take | "Resource flowing both ways." | #WeightOnTheRope | 🤝 | Words sliding past each other |
| 04 | The Better ROI | Compounding growth, 1% daily | "Invest in what compounds." | #Compounding | 📈 | Counter going 1× → 37.8× |
| 05 | The Mutual Challenge | Climbing partners, accountability pact | "Depth over speed. Visibility over humility." | #HoldTheLine | ⚔️ | Two figures climbing rope, role swap |
| 06 | The Confidence of the Climb | Map + compass, shared direction | "You have the map. I have the compass." | #MapAndCompass | 🧭 | Static compass, needle settling |
| 07 | The Covenant | Iron forging, hammer strikes | "Iron on iron." | #IronOnIron | ⚓ | Two bars striking, IRON ON IRON assembles |

---

## Key Animation Details

### Masthead Intro Timeline (GSAP)
1. Letters flip up from below with rotateX(-90°) → 0, staggered 0.05s, power4.out, 1.05s
2. Rope slot clip-path wipes from right to left revealing it, 1.1s, power3.inOut, starts -0.8s relative
3. Black bar same clip-path reveal, 0.7s, starts -0.7s
4. Stamp scales from 2.8x with rotation -26° → 1x at -7°, bounces in with power4.in, 0.3s
5. Stamp x-shifts from -5px → 0 with elastic.out(2, .3), 0.5s

### Forge Strike Mechanics
- Strikes at progress positions [0.26, 0.5, 0.74]
- Each strike: kick = 1 (decays with exp(-dt*6)), 60ms hit-stop (animation freezes), sparks burst in parabolic arcs (gravity -4.5), expanding ring (scales 1→22, opacity fades), voxel word assembles from scattered random positions/rotations/scales
- Scroll warping: Nonlinear remapping so each strike has more scroll distance around it (Gaussian-influenced cumulative distribution)
- Bars heat from dark grey (0x1c1c1c) to yellow using color lerp, gap narrows then separates as bars fly apart at end
- Camera shake: small per-frame offsets using sin/cos at high frequencies (53, 61, 29, 37 Hz) multiplied by kick value

### Braid/Rope Dash Animation
- The alternating colored dashes on the braided rope animate by updating stroke-dashoffset continuously based on run progress, creating a flowing/striping effect

### Needle in Section 06
- Starts at 150° rotation
- After IntersectionObserver fires at 50% visibility and 2s delay, animates to 38° over 1.5s with back.out(1.3) ease
- This is the ONLY animation in the otherwise completely static section

---

## Implementation Guidelines for Recreation

1. **Self-contained single HTML file** — no external assets except Three.js and GSAP from CDNs
2. **Base64 embed the fonts** — do not link to Google Fonts
3. **Use CSS custom properties** for the 3-color palette and spacing values
4. **Sticky + tall wrapper pattern** for scroll-driven scenes (not scroll-jacking)
5. **clip-path for reveals** — not opacity transforms for the wipe-in feel
6. **Hard shadows, no border-radius** everywhere
7. **Instanced meshes** for voxel letters and rope segments — do not create thousands of individual meshes
8. **Smooth scroll progress** with lerp/exponential smoothing — do not use raw scrollY directly for animations
9. **svh units** for 100% viewport height to handle mobile browser chrome
10. **Camera view offset** for framing scenes above caption overlaps — critical for composition
11. **Per-face vertex colors** on voxel boxes to get the two-tone (front = color, sides = paper) 3D printed look
12. **mix-blend-mode: difference** for the meter fill bar so it inverts over any tick button it crosses
13. **Hit-stop** at each forge strike — freeze rendering for ~60ms for impact feel
14. **Reduced motion** is not an afterthought — provide a fully usable flat experience
15. **Section 06 is sacred** — zero transitions/animations except the one needle settle
16. **Rotated stamps** always at -7° or -8°, slight rotation for rubber-stamp authenticity

---

## Vibe & Emotional Arc

The page should feel like:
- **Opening:** Bold, warm, triumphant (dawn breaking over mountains)
- **Middle (02-04):** Energetic, propulsive, building momentum (horizontal pull)
- **05:** Determined, gritty, challenging (climb)
- **06:** Quiet, still, confident — the breath before the final commitment
- **07:** Powerful, visceral, climactic — hammer strikes, metal meeting metal, the covenant being forged
- **Closing:** Warm resolution, signature, forward momentum (ticker tape), invitation to return to the start

The emotional core is male friendship, mutual accountability, and the covenant to show up fully for each other's growth — rendered through the metaphor of climbing and blacksmithing, with all the typographic conviction of a vintage newspaper extra.
