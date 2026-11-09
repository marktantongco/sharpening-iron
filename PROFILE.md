# Sharpening Iron — Draw-Call Profile

## v2 design changes

The v2 broadsheet design (commit `ee554d7`) made significant changes:
- Inlined Anton font as base64 woff2 data URI (offline-first)
- Removed ScrollTrigger dependency (uses manual scroll handling)
- Added GitHub Pages deployment workflow
- Added AI recreation prompt

## Live profiling attempt (v2)

THREE and GSAP load successfully in the browser. The canvas renders at 629×525. However, `initGL()` never resolves — `glx` stays undefined and RAF count is 0. The render loop never starts. Root cause: `document.fonts.load('400 120px Anton')` hangs on the inlined data-URI font. The 2500ms `Promise.race` timeout should catch this, but it doesn't fire (possibly a Promise.race issue with the Font Loading API).

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