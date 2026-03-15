# PolyCity — 3D Brief

## Concept
A Y2K Japanese-aesthetic animated 3D town scene built for the web. Low-poly characters walk and bounce through neon-lit streets lined with buildings, cherry blossom trees, vending machines, and floating geometric shapes. Think Katamari meets Shibuya meets early 2000s web design.

## Tech Stack
- **Three.js r164** via CDN import map
- **Post-processing**: UnrealBloomPass for neon glow
- **OrbitControls** with auto-rotate
- **GLTFLoader** for all 3D assets
- Single HTML file, no build step required

## Assets (all from Poly.pizza, CC0/CC-BY)

### Characters (6, all animated)
| Asset | File | Target Height | Source |
|-------|------|---------------|--------|
| Platformer Character | platformer-character.glb | 2.0m | Quaternius, CC0 |
| Panda | panda.glb | 1.8m | Quaternius, CC0 |
| Rabbit Blond | rabbit-blond.glb | 1.6m | Quaternius, CC0 |
| Animated Robot | robot.glb | 1.8m | Quaternius, CC0 |
| Mako | mako.glb | 1.8m | Quaternius, CC0 |
| Astronaut | astronaut.glb | 2.0m | Quaternius, CC0 |

### Buildings (12 instances from 8 unique models)
| Asset | File | Target Height |
|-------|------|---------------|
| Large Building A | building-A.glb | 8-10m |
| Large Building B | building-B.glb | 7-10m |
| Building C | building-C.glb | 9m |
| Building D | building-D.glb | 9m |
| Building E | building-E.glb | 7m |
| Building F | building-F.glb | 8m |
| Small Building | building-small.glb | 6m |
| Town House | town-house.glb | 8m |
| Skyscraper | skyscraper.glb | 12m |
| Town Center | town-center.glb | 7m |

### Props
| Asset | File | Target Height |
|-------|------|---------------|
| Vending Machine (x2) | vending-machine.glb | 1.8m |
| Street Light (x8) | street-light.glb | 4m |
| Cherry Tree (x4) | cherry-tree.glb | 4-5.5m |
| Bench | bench.glb | 0.8m |
| Potted Plant (x2) | potted-plant.glb | 1.0m |
| Car 1 | car1.glb | 1.5m |
| Car 2 | car2.glb | 1.5m |

### Procedural Elements
- 600 cherry blossom petal particles (additive blending)
- 25 floating Y2K geometric shapes (torus, octahedron, icosahedron, torus knot, dodecahedron)
- 7 spinning star decorations
- 4 neon Japanese text signs (canvas-rendered)
- Neon road stripes on cross-intersection
- Grid helper floor

## Performance
- **Total asset size**: ~15MB (uncompressed GLB)
- **Target**: 60fps on mid-range laptop
- **Scene polycount**: ~80K triangles estimated
- **Post-processing**: single bloom pass
- **Shadows**: single directional light shadow map (2048x2048)

## Animations
- Character idle/walk animations (embedded in GLB, played via AnimationMixer)
- Characters bounce or walk in circles
- Cherry blossom petals fall and respawn
- Y2K shapes float and rotate
- Stars spin continuously
- Neon lights pulse between pink and cyan
- Cars drive back and forth on roads
- "POLY CITY" sign floats up and down
- Camera auto-rotates around the scene

## Lighting
- Ambient light (purple, 1.0 intensity)
- Directional light (lavender, 1.8) with shadows
- Pink point light (4.0 intensity, pulsing)
- Cyan point light (4.0 intensity, counter-pulsing)
- Warm point light (2.5) at center
- Hemisphere light (purple sky, pink ground)

## Integration
Serve the `threejs/` folder with any HTTP server. Assets are referenced from `../assets/` relative to the HTML file.

```
3d/
├── assets/          # All GLB models
├── threejs/
│   └── index.html   # Complete scene (single file)
└── 3d-brief.md      # This file
```

### To run locally:
```bash
cd projects/PolyCity/3d
python3 -m http.server 8765
# Open http://localhost:8765/threejs/
```

### To embed in a website:
Use an iframe or integrate the Three.js code into your page. The scene is self-contained in a single HTML file with CDN imports.

## Color Palette (Y2K Japanese)
- Background: `#1e1040` (deep indigo)
- Pink neon: `#ff69b4`
- Cyan neon: `#00e5ff`
- Purple accent: `#c44dff`
- Warm accent: `#ffaa44`
- Sky blue: `#6bc5ff`
- Hot pink: `#ff4488`

## Attribution
All 3D models sourced from Poly.pizza. Most are CC0 (Quaternius). Some are CC-BY — attribution included in the model metadata.
