# PolyCity Scroll Scene — Framer Integration Guide

## Overview

`index-scroll.html` is a scroll-driven cinematic flythrough of the PolyCity 3D scene. The camera starts with a far aerial view, descends into the streets, cruises through the city, and orbits back to the start. It's designed to be embedded in Framer via iframe.

## Files

- **`threejs/index-scroll.html`** — Scroll-driven scene (single file, CDN imports, no build step)
- **`threejs/index.html`** — Original free-orbit version (for reference)
- **`assets/*.glb`** — All 3D models (~15MB total, referenced via `../assets/`)

## Hosting

The scene must be hosted on a static server (it loads GLB files via relative paths). Options:

1. **Vercel** (recommended) — push the `3d/` folder to a repo, deploy
2. **Netlify** — same as above
3. **GitHub Pages** — works, but slower CDN
4. **Any static host** — S3, Cloudflare Pages, etc.

**Folder to deploy:** the entire `3d/` directory. The HTML references assets at `../assets/` relative to `threejs/`.

Recommended structure on the host:
```
/
├── assets/          ← all .glb files
└── threejs/
    └── index-scroll.html
```

The live URL will be: `https://your-host.com/threejs/index-scroll.html`

## Embedding in Framer

### Option A: Embed Component (simplest)

1. In Framer, add an **Embed** element
2. Set the HTML to:
```html
<iframe
  src="https://YOUR-HOST.com/threejs/index-scroll.html"
  style="width: 100%; height: 100%; border: none;"
  allow="autoplay"
></iframe>
```
3. Make the Embed element **100vw x 100vh** (full viewport)
4. The scene handles its own scroll internally (600vh scroll track)

### Option B: Code Component with Scroll Sync (advanced)

For tighter integration where **Framer's page scroll** drives the 3D camera:

```tsx
import { useEffect, useRef } from "react"

export default function PolyCityScene() {
  const iframeRef = useRef<HTMLIFrameElement>(null)

  useEffect(() => {
    const handleScroll = () => {
      const scrollTop = window.scrollY || document.documentElement.scrollTop
      const docHeight = document.documentElement.scrollHeight - window.innerHeight
      const progress = Math.max(0, Math.min(1, scrollTop / docHeight))

      iframeRef.current?.contentWindow?.postMessage(
        { type: "scroll", progress },
        "*"
      )
    }

    window.addEventListener("scroll", handleScroll, { passive: true })
    return () => window.removeEventListener("scroll", handleScroll)
  }, [])

  return (
    <iframe
      ref={iframeRef}
      src="https://YOUR-HOST.com/threejs/index-scroll.html"
      style={{
        position: "fixed",
        top: 0,
        left: 0,
        width: "100vw",
        height: "100vh",
        border: "none",
        zIndex: -1,
      }}
      allow="autoplay"
    />
  )
}
```

With this approach:
- The iframe is fixed behind the page content
- Framer's native scroll drives the 3D camera
- Page content scrolls over the 3D scene (use transparent sections to reveal it)

### Option C: Code Override (Framer-native)

Apply this override to any Frame that wraps the iframe:

```tsx
import type { ComponentType } from "react"
import { useEffect, useRef } from "react"

export function withScrollSync(Component): ComponentType {
  return (props) => {
    const ref = useRef<HTMLIFrameElement>(null)

    useEffect(() => {
      const onScroll = () => {
        const progress = window.scrollY / (document.body.scrollHeight - window.innerHeight)
        ref.current?.contentWindow?.postMessage({ type: "scroll", progress: Math.max(0, Math.min(1, progress)) }, "*")
      }
      window.addEventListener("scroll", onScroll, { passive: true })
      return () => window.removeEventListener("scroll", onScroll)
    }, [])

    return <Component {...props} ref={ref} />
  }
}
```

## How the Scroll Works

The scene accepts scroll input from **3 sources** (all built-in, no config needed):

| Source | When it's used | How it works |
|--------|---------------|--------------|
| Native scroll | Standalone (not in iframe) | 600vh scroll track inside the page |
| `postMessage` | Embedded in Framer | Send `{ type: "scroll", progress: 0-1 }` |
| Wheel events | Iframe without postMessage | Fallback, accumulates wheel delta |

**Scroll progress** is a number from `0` (start) to `1` (end):
- `0.00` — Far aerial establishing shot
- `0.15–0.35` — Descending, entering the streets
- `0.35–0.55` — Street-level, through the intersection
- `0.55–0.65` — Rising above rooftops
- `0.65–1.00` — Wide orbit back to start (loops seamlessly)

## Camera Path

The camera follows a CatmullRom spline with 17 control points. It always looks at the city center `(0, 2, 0)`, which guarantees smooth rotation with no sudden turns.

Journey:
1. Aerial → descend from SE corner
2. Drop onto Z-road, cruise north toward intersection
3. Rise through intersection, turn onto X-road heading west
4. Cruise X-road, then rise above rooftops
5. Wide orbit (SW → S → SE → E → NE) back to starting position

## Scene Features

- **PS1 shader** — pixelation + dithering post-processing
- **Bloom** — neon glow on Y2K shapes and signs
- **Animated characters** — 20+ characters walking, bouncing, wandering the city
- **Kaomoji reactions** — 3D Japanese emoticons pop up above characters
- **Cars & buses** — drive on roads with intersection yielding
- **Cherry blossom petals** — 250 falling particles
- **Floating Y2K shapes** — torus, icosahedron, etc. with neon materials
- **Birds, clouds, fountain spray** — ambient life
- **Dynamic FOV** — wider in streets, tighter during aerial shots

## Performance

- ~15MB assets (uncompressed GLB)
- Targets 60fps on mid-range laptop
- Half-resolution bloom for performance
- Pixel ratio capped at 1.5x
- BasicShadowMap (fast)

## Customization

To adjust the scroll speed/feel, edit these values in `index-scroll.html`:

```js
// Line ~280: scroll smoothing (lower = smoother/slower)
const scrollSmooth = 1 - Math.exp(-2.0 * dt);

// Line ~19: total scroll distance (higher = slower scroll)
#scroll-spacer { height: 600vh; }
```

## Ready Event

The scene posts a message when fully loaded (useful for hiding a loading state in Framer):

```js
window.addEventListener("message", (e) => {
  if (e.data?.type === "polycity-ready") {
    // Scene is loaded — hide your loading spinner
  }
})
```

## Quick Test

```bash
cd projects/PolyCity/3d
python3 -m http.server 8765
# Open http://localhost:8765/threejs/index-scroll.html
```
