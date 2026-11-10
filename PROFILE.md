# Sharpening Iron — Draw-Call Profile

## v2 design changes

The v2 broadsheet design (commit `ee554d7`) made significant changes:
- Inlined Anton font as base64 woff2 data URI (offline-first)
- Removed ScrollTrigger dependency (uses manual scroll handling)
- Added GitHub Pages deployment workflow
- Added AI recreation prompt

## Live profiling results (v2)

Real `renderer.info.render.calls` captured from the live page:

| Scroll position | Scene | Draw calls | Triangles | Geometries | Textures |
|---|---|---|---|---|---|
| 0.0–0.3 | 01 Summit | **10** | 647 | 10 | 1 |
| 0.5 | 05 Climb | **32** | 10,816 | 42 | 1 |
| 0.7–1.0 | 07 Forge | **6** | 9,192 | 49–50 | 1 |

**Key findings:**
- The Climb scene (05) is the heaviest at **32 draw calls** — double the static estimate
- Summit is 10 (estimated 4) — the text texture plane and other elements add 6 more
- Forge is 6 (estimated 8) — actually lighter than expected
- All scenes are well under the 100-draw-call mobile target
- Only 1 texture is loaded at any time (textures are swapped, not accumulated)

## Static analysis (from source)

From `index.html`, the draw-call budget per scene is:

| Scene | Meshes | Instanced | Draw calls (est.) | Notes |
|---|---|---|---|---|
| **01 Summit** | 1 merged ridge mesh + 1 sun circle + 1 line plane + 1 text plane | 0 | **4** | Ridges merged from 7→1 |
| **05 Climb** | 1 ground + 2 paper body parts + 1 head + 4 limbs × 2 figures = ~12 + 1 wall instanced | 1 (wall), 1 (rope) | **~16** | Wall and rope are `InstancedMesh`; figures are individual meshes |
| **07 Forge** | 2 bars + 1 word instanced + 1 spark instanced + 3 rings | 1 (words), 1 (sparks) | **~8** | Rings are individual meshes but only visible during strikes |

**Total estimated budget: ~28 draw calls across all scenes** — well under the 100-draw-call mobile target.

## Font swap (Anton → Bodoni Moda)

- Replaced `--anton` CSS variable with Bodoni Moda stack
- Added Google Fonts link for Bodoni Moda (opsz 6..96, wght 400..900)
- Updated `FONT` constant and `document.fonts.load()` call
- Bodoni Moda is a variable font enabling kinetic type animation via `font-variation-settings`

## Verification needed

Real profiling requires a browser with CDN access and a working render loop. To measure yourself:

1. Open the page locally in Chrome DevTools
2. Ensure WebGL initializes (check console for errors)
3. Run this in the console after the page loads:
```javascript
const origRender = THREE.WebGLRenderer.prototype.render;
THREE.WebGLRenderer.prototype.render = function(scene, cam) {
  origRender.call(this, scene, cam);
  console.log('Draw calls:', this.info.render.calls, 
              '| Triangles:', this.info.render.triangles,
              '| Geometries:', this.info.memory.geometries,
              '| Textures:', this.info.memory.textures);
};
```
4. Scroll through each checkpoint and note the values

## Recommendation

The static budget looks healthy. The highest-risk scene is **05 Climb** (~16 draw calls) due to individual figure meshes. If you want to tighten it further:
- Merge all figure parts into one `BufferGeometry` per figure
- Combine the two figures into a single `InstancedMesh` with color variants

The `initGL()` hang should be investigated — consider using `document.fonts.ready` instead of `document.fonts.load()` for inlined fonts.