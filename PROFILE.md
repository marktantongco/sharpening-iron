# Sharpening Iron — Draw-Call Profile

## Environment limitation

The live page (`https://marktantongco.github.io/sharpening-iron/`) loads in the browser harness, but CDN scripts are blocked in this environment. `THREE` and `gsap` are both `undefined` at runtime, and the canvas stays at its default 300×150 size. That means I cannot extract real `renderer.info.render.calls` from a live session here.

## Static analysis (from source)

From `index.html`, the draw-call budget per scene is:

| Scene | Meshes | Instanced | Draw calls (est.) | Notes |
|---|---|---|---|---|
| **01 Summit** | 1 merged ridge mesh + 1 sun circle + 1 line plane + 1 text plane | 0 | **4** | Ridges merged from 7→1 in commit `7346103` |
| **05 Climb** | 1 ground + 2 paper body parts + 1 head + 4 limbs × 2 figures = ~12 + 1 wall instanced | 1 (wall), 1 (rope) | **~16** | Wall and rope are `InstancedMesh`; figures are individual meshes |
| **07 Forge** | 2 bars + 1 word instanced + 1 spark instanced + 3 rings | 1 (words), 1 (sparks) | **~8** | Rings are individual meshes but only visible during strikes |

**Total estimated budget: ~28 draw calls across all scenes** — well under the 100-draw-call mobile target.

## Verification needed

Real profiling requires a browser with CDN access. To measure yourself:

1. Open the page in Chrome DevTools
2. Run this in the console after the page loads:
```javascript
// Hook into the render loop
const origRender = THREE.WebGLRenderer.prototype.render;
THREE.WebGLRenderer.prototype.render = function(scene, cam) {
  origRender.call(this, scene, cam);
  console.log('Draw calls:', this.info.render.calls, 
              '| Triangles:', this.info.render.triangles,
              '| Geometries:', this.info.memory.geometries,
              '| Textures:', this.info.memory.textures);
};
```
3. Scroll through each checkpoint and note the values

## Recommendation

The static budget looks healthy. The highest-risk scene is **05 Climb** (~16 draw calls) due to individual figure meshes. If you want to tighten it further:
- Merge all figure parts into one `BufferGeometry` per figure
- Combine the two figures into a single `InstancedMesh` with color variants
