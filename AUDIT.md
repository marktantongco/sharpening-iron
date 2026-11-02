# Sharpening Iron — Deep Audit & Best-Practice Research

## Executive summary

*Sharpening Iron* already exhibits several traits of award-winning editorial web experiences: a strong three-act motion arc, intentional palette, scroll-scrubbed 3D, and thoughtful accessibility fallbacks. Against the current Awwwards/FWA/CSSDA corpus, its main gaps are **performance instrumentation**, **reduced-motion completeness**, **font loading discipline**, **mobile refinement**, and **narrative pacing polish**. The biggest single upgrade would be replacing the hand-rolled scroll loop with GSAP ScrollTrigger (or Lenis) and adding a proper content-driven loader.

---

## 1. How it measures up

### 1.1 Motion design & scroll choreography

Award winners in the 2024–2026 window share a specific motion vocabulary: clip-path/mask reveals instead of generic opacity fades, scroll-scrubbed decor layers that reverse on scroll-up, and fire-once content reveals rather than repeated entrance animations.[2] The project already uses clip-path reveals for the hero chapters and a custom `requestAnimationFrame` loop with exponential smoothing (`1 - Math.exp(-dt * 7)`), which is the right mathematical approach for scrub smoothing. The flat rope-and-type pull in checkpoints 02–04 maps cleanly to the "continuous horizontal scroll" pattern seen in winners like Son Daven and Cartier.[2]

**Gap:** The project uses a hand-rolled scroll loop instead of GSAP ScrollTrigger. Winners standardize on ScrollTrigger (or Lenis + ScrollTrigger) because it handles marker cleanup, responsive variants via `gsap.matchMedia()`, and teardown automatically.[10] The current loop also re-renders the entire scene every frame even when the active chapter hasn't changed, and the meter calls `getBoundingClientRect()` on every tick without throttling (we added a 100ms throttle, but winners typically separate meter updates from render loops entirely).[5]

### 1.2 Typography & editorial layout

The project's type system — Anton (display) + Space Mono (UI) — matches the award-winning pattern of "typography as art" with extreme scale contrast.[2][4] The fluid `clamp()` sizing, outlined numbers, and stamped labels align with the "brutalist broadsheet" genre. Award winners consistently use:
- Display serifs or geometric sans at 60–300px for headlines[2][4]
- A calm, highly legible body face at 16–18px equivalent[3]
- Multi-column grids with asymmetric widths and generous margins[3]

**Gap:** Anton is a single-weight display face. Winners almost always use variable fonts (PP Neue Montreal, Inter, GT Sectra) because they allow real-time `font-variation-settings` animation on hover and scroll.[2] The current system also lacks a true modular type scale — sizes are declared per-element rather than flowing from a baseline grid.[3]

### 1.3 WebGL performance

The Three.js scenes use `InstancedMesh` for repeated geometry (voxel text, rope segments, sparks), which is correct per the performance checklist.[5][6][7] Pixel ratio is capped at 2, and `frustumCulled = false` is applied only to instanced meshes that need it.[5][7]

**Gaps:**
- The summit scene creates a new `ShapeGeometry` per ridge (7 draw calls). Winners merge static geometry or use `InstancedMesh` where possible.[6][7]
- `textTexture()` creates a 2048×420 canvas per text element per orientation change. This allocates ~3.5MB of texture memory each time. Winners reuse textures or downsize to 1024px on mobile.[6][7]
- No draw-call budget is enforced. The forge scene has ~270 draw calls (2 bars + 270 sparks + 3 rings + 1 wall + 1 ground). Below 100 is the golden rule for mobile.[7]
- No LOD or culling for the summit ridges, which are always visible but could use simpler geometry at distance.[5][7]

### 1.4 Accessibility

The project has:
- `prefers-reduced-motion` guard that collapses animations to resolved states[8]
- Keyboard-operable buttons with `aria-label`s
- A skip link
- `aria-live="polite"` toast
- Noscript fallback styles

**Gaps:**
- The ticker/marquee auto-plays on a loop without a pause control. WCAG 2.2.2 requires a pause/stop/hide mechanism for animations exceeding 5 seconds.[8][12]
- `html.reduce` disables transitions but doesn't gate the GSAP intro timeline — `hasGsap&&!reduce` handles this, but the conditional is split between CSS and JS, which is fragile.[8]
- The smooth-scroll `scrollTo({behavior:'smooth'})` on the "Back to base camp" button is unconditional for non-reduced-motion users, which is correct, but the meter tick `scrollIntoView({behavior:'smooth'})` fires even when the user is already near the target — no debounce or distance check.[9]
- No `focus-visible` trap or focus management for the horizontal scroll panels on mobile.[8]

### 1.5 Loading & perceived performance

Awwwards winners treat loaders as content-driven or absent — never fake timed spinners.[2] The project's `LOADING…` overlay is a pulse animation that hides once WebGL boots. This is functional but minimal.

**Gap:** The loader doesn't tie to real asset progress. Winners resolve the loader *into* the first scene with no hard cut (Son Daven, Delvaux).[2] The current implementation also doesn't handle the case where fonts load after the loader hides — the masthead intro could fire before Anton is ready, causing layout shift or wrong glyph shapes.

### 1.6 Mobile & responsiveness

The project has breakpoints at 860px, 760px, and 560px, with `svh` units and `clamp()` throughout. This is solid. Winners use fluid typography with `clamp()` exclusively; breakpoint-based sizing is considered obsolete.[2]

**Gaps:**
- The horizontal pull (`300vw` track) on mobile is a known interaction risk. Winners avoid horizontal scroll hijacking; if used, they scope it to `prefers-reduced-motion: no-preference` and provide a clear vertical fallback.[8]
- The 3D scenes don't differentiate mobile vs desktop geometry complexity. Winners drop polygon counts, disable antialiasing, and reduce texture resolution on mobile.[6][7]

---

## 2. Recommended implementation roadmap

### Priority 1 — Fix the critical gaps

1. **Replace hand-rolled scroll with GSAP ScrollTrigger**
   - Attach the 3D chapter progress to `scrub: 1` ScrollTriggers instead of manual `getBoundingClientRect()` polling.[10]
   - This removes the 60fps meter readback, simplifies the render loop, and gives you `markers` for debugging.
   - Use `gsap.matchMedia()` for responsive variants instead of branching in the update loop.[10]

2. **Fix the ticker pause requirement**
   - Add a "PAUSE MARQUEE" toggle button (WCAG 2.2.2).[8][12]
   - On `prefers-reduced-motion`, auto-pause and hide it.

3. **Consolidate reduced-motion handling**
   - Move all reduced-motion guards into CSS `@media (prefers-reduced-motion: no-preference)` blocks where possible, and keep JS guards in one place (the ScrollTrigger config).[8][10]

### Priority 2 — Performance hardening

4. **Enforce a draw-call budget**
   - Target <100 draw calls on mobile, <300 on desktop.[7]
   - Merge summit ridges into one `BufferGeometry` or use `InstancedMesh`.
   - Downsize `textTexture()` canvas to 1024px on mobile, 2048px on desktop.
   - Consider `THREE.LOD` for the summit scene (far ridges can be simpler meshes).[5]

5. **Profile with `renderer.info.render.calls`**
   - Log draw calls per frame in dev mode and fail loudly if the budget is exceeded.[6][7]

6. **Add `will-change` discipline**
   - Remove `will-change: transform` from `.title .ch` after the intro animation completes. Winners use `will-change` only during movement, not permanently.[2][5]

### Priority 3 — Polish & award-craft

7. **Add a content-driven loader transition**
   - Instead of `LOADING…` pulsing, reveal the first scene's masthead through the loader with no hard cut.[2]
   - Tie loader hide to `document.fonts.ready` + WebGL init, not just a CSS animation end.

8. **Upgrade to a variable font stack**
   - Anton is single-weight. Replace with `PP Neue Montreal` or `Inter` (variable) for body, keeping Anton or switching to `Playfair Display` for display if you want the editorial serif look.[2][3]
   - This enables kinetic type animation via `font-variation-settings`.

9. **Refine the motion arc**
   - Winners reveal each content block once and then leave it still, while a reversible decor layer runs underneath.[2]
   - The current project re-animates `.num`, `.quote`, and `.tag` on every frame they're visible. Switch to fire-once reveals.

10. **Add a proper type scale**
    - Define a modular scale (e.g., `1.25` or `1.333`) and derive all `clamp()` values from it. Winners treat typography as a system, not per-element choices.[3]

---

## 3. What the project does better than most

- **Narrative structure.** The 7-checkpoint arc with a designated "breath" chapter (06) is more considered than 90% of scroll sites, which either peak too early or never vary tempo.
- **Deterministic 3D.** The fixed-seed PRNG for summit ridges, climber paths, and forge sparks is a deliberate craft choice that most winners skip in favor of randomness. It gives the piece a consistent "edition" feel.
- **Graceful degradation.** The `html.reduce` and `html.nogl` fallbacks are comprehensive. Most award sites ship without any reduced-motion handling.[8]
- **Self-contained.** A single HTML file with no build step is a legitimate distribution advantage for a personal broadsheet. Winners typically require bundlers, but the constraint forces discipline.

---

## Sources

[1] https://www.awwwards.com/websites/storytelling  
[2] https://github.com/coroboros/research/blob/main/articles/award-winning-websites-2025-2030/award-winning-websites-2025-2030.md  
[3] https://vikilinks.com/blog/editorial-web-design  
[4] https://hireadrian.com/award-winning-websites  
[5] https://webeyez.com/insights/guides/three-js-performance-profile-guide  
[6] https://github.com/Immanuel-Aristotle/agents/blob/main/skills/threejs-webgl/references/optimization_checklist.md  
[7] https://marceloretana.com/checklist/threejs-performance-checklist  
[8] https://github.com/css-scroll-driven/scroll-a11y-audit  
[9] https://gsap.com/resources/st-mistakes  
[10] https://github.com/menervatripolska/awwwards-gsap-scrolltrigger-skill/blob/main/SKILL.md  
[11] https://www.utsubo.com/blog/award-winning-website-design-guide  
[12] https://www.uiuxatlas.com/lessons/motion/prefers-reduced-motion-and-motion-accessibility  
