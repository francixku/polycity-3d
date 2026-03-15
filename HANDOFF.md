# PolyCity — Handoff to Webus

## What was built
An animated 3D town scene with Y2K Japanese aesthetic. Low-poly characters, neon-lit buildings, cherry blossoms, floating shapes, and kaomoji reactions.

**Two versions:**
- `index.html` — Free-orbit (user controls the camera with click+drag)
- `index-scroll.html` — Scroll-driven cinematic flythrough (designed for Framer embed)

## Files
- `3d/threejs/index.html` — Free-orbit version
- `3d/threejs/index-scroll.html` — Scroll-driven version (Framer-ready)
- `3d/assets/*.glb` — All GLB model files (~15MB total)
- `3d/3d-brief.md` — Full asset list, performance notes, color palette
- `3d/FRAMER-INTEGRATION.md` — Step-by-step Framer embedding guide with code snippets

## Framer Integration
See **`FRAMER-INTEGRATION.md`** for full instructions. Quick summary:
1. Host the `3d/` folder on Vercel/Netlify
2. Embed via iframe in Framer
3. Optionally sync Framer's page scroll to the 3D camera via `postMessage`

## Key details
- Three.js r164 loaded from CDN (no npm install needed)
- Scroll version: camera follows a CatmullRom spline, always looks at city center
- Bloom post-processing + PS1 pixelation shader
- ~15MB assets, targets 60fps on mid-range laptop
- Responsive (handles window resize)
- Accepts scroll via native scroll, postMessage, or wheel events

## Running locally
```bash
cd projects/PolyCity/3d && python3 -m http.server 8765
# Free-orbit: http://localhost:8765/threejs/
# Scroll:     http://localhost:8765/threejs/index-scroll.html
```
