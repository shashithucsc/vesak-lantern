# Vesak 3D App

A cinematic Three.js experience inspired by a Sri Lankan Vesak night, built as a Vite web project.

## Overview

This project renders an interactive night scene with:
- A stupa centerpiece
- Enhanced moonlit sky with animated wisps
- Side-positioned Buddhist flags that do not block the stupa view
- Animated Atapattam lanterns
- Floating clay lamps (Mati Pahan)
- Fireflies, stars, mist, aurora bands, and fireworks
- Local GLB lantern clusters loaded from `public/vesak_lanterns.glb`
- Music playback from `public/music.mp3`
- Bloom and cinematic post-processing for glowing highlights

## Tech Stack

- Vite
- Three.js (r128 via CDN)
- Tailwind CSS (CDN utility usage)
- Plain HTML/CSS/JavaScript

## Project Structure

- `index.html`: Main app (UI, styles, and all 3D scene logic)
- `public/vesak_lanterns.glb`: Local lantern model used in scene decoration
- `public/music.mp3`: Background music played by the audio toggle button
- `public/favicon.svg`, `public/icons.svg`: Static icons/assets
- `src/`: Default Vite starter files (not driving the main scene)
- `package.json`: Scripts and Vite dependency

## Controls

- Drag: Orbit camera
- Scroll/pinch: Zoom
- Click sky/ground: Spawn sky lanterns
- Click lantern mesh: Randomize lantern color
- Audio button: Toggle background music

## Visual Systems

### Environment

- Deep-gradient night background
- Layered sky dome and zenith glow
- Layered atmospheric fog
- Starfields (far and near)
- Water surface with animated wave displacement
- Horizon mist and distant silhouette layers
- Animated high-altitude sky wisps
- Buddhist flag rows placed on left/right corridors

### Lighting and Effects

- Ambient, moon, and warm key lights
- Local point lights inside decorative lanterns
- UnrealBloomPass for glow
- Custom cinematic shader pass for warm/vignette grading

### Loading Experience

- Multi-layer animated loading stage:
  - Glowing core pulse
  - Dual rotating rings
  - Aurora halo pulse
  - Twinkling specks
- Animated loading text

## Exact Build Ingredients (Geometry, Animation, Textures)

This section documents the actual building blocks used in the Three.js scene.

### Geometry and Shapes Used

- `SphereGeometry`:
  - Sky dome and zenith glow shells
  - Moon and moon glow
  - Stupa dome (half-sphere)
  - Distant tree canopies
  - Cinematic atmosphere veil and glow orb
- `RingGeometry`:
  - Moon halo ring
- `PlaneGeometry`:
  - Water surface mesh (high-segment plane for wave deformation)
  - Ground/bank plane
  - Aurora bands
  - Horizon mist cards
  - Sky wisps cards
  - Buddhist flag stripes
  - Lantern frills
- `CylinderGeometry`:
  - Stupa terrace rings
  - Stupa spire base
  - Flag poles and flag support arms
  - Mati Pahan lamp base
  - Sky lantern body
  - Distant silhouette trunks
- `ConeGeometry`:
  - Stupa main spire
  - Mati Pahan flame mesh
- `BoxGeometry`:
  - Stupa square section (Hathares Kotuwa)
- `OctahedronGeometry`:
  - Stupa crystal (Chudamanikya)
  - Atapattam lantern main shell (scaled to elongate shape)
- `BufferGeometry` (custom point clouds):
  - Far stars
  - Near stars
  - Fireflies
  - Firework particles (with custom `velocity` attribute)

### How They Are Created

- Procedural scene construction:
  - Most objects are generated directly in code using Three.js geometry constructors and grouped with `THREE.Group`.
- Composition approach:
  - Complex objects (stupa, lanterns, lamps, flags) are assembled from multiple primitive meshes.
- Imported asset:
  - `public/vesak_lanterns.glb` is loaded through `GLTFLoader` and cloned into two placements.
  - Loaded mesh materials are cloned so emissive and glow behavior can be tuned per instance.

### How They Are Animated

- Time-driven animation loop:
  - `clock.getDelta()` and `clock.elapsedTime` drive all procedural motion.
- Water animation:
  - Vertex displacement on the water plane using sine/cosine wave formulas per vertex.
- Lantern systems:
  - Atapattam lanterns: vertical bobbing + rotation; temporary spin boost on click.
  - Lantern frills: swinging with sinusoidal rotation.
  - Sky lanterns: upward drift with horizontal wobble; removed when too high.
- Lamp system (Mati Pahan):
  - Small drifting motion on x/z, bobbing on y, flame flicker by scaling flame mesh and light intensity.
- Atmospheric layers:
  - Moon glow pulse by scale oscillation.
  - Horizon mist slow drift.
  - Aurora opacity and height modulation.
  - Sky wisps drifting in x/y with opacity pulsing.
  - Distant silhouette group subtle yaw sway.
  - Flag stripes sway in rotation and slight y movement.
- Particle systems:
  - Fireflies orbit around base positions with phase offsets.
  - Fireworks launch periodically; each particle updates by velocity, gravity, and drag, then fades out.
- Post FX animation:
  - Cinematic shader pass updates a time uniform (`uTime`) for subtle warm-lift grading variation.

### Materials and Texture Usage

- Core materials:
  - `MeshStandardMaterial` for most lit scene objects (stupa, flags, lantern shells, ground, trunks, etc.).
  - `MeshBasicMaterial` for unlit/glow cards and simple glow elements (sky layers, wisps, mist, halo, flames in some cases).
  - `PointsMaterial` for stars, fireflies, and fireworks.
- Visual treatment:
  - Heavy use of `emissive` and `emissiveIntensity` for festival glow.
  - Transparent materials and `AdditiveBlending` for atmospheric light accumulation.
  - Wireframe overlay on Atapattam lanterns to represent bamboo framing.
- Texture maps:
  - No explicit external image texture maps are used for the procedural Three.js scene objects in code.
  - The look is primarily color/emissive/roughness/metalness driven (material properties), plus animated geometry deformation and post-processing bloom.
  - The GLB file may contain its own embedded/baked material data from the source model.
- Non-Three.js visual textures (UI layer):
  - The loading/UI visuals rely on CSS gradients, blur, and blend effects rather than bitmap textures.

## Mobile Optimization

The scene automatically scales quality based on device capabilities:

- Reduced particle/lantern/firefly counts on coarse-pointer devices
- Lower water mesh segment count on mobile
- Tuned bloom strength/radius for performance
- Reduced pixel ratio target on mobile
- Mobile-specific CSS adjustments for safe viewport sizing and readability

Additional runtime optimizations:

- Visibility-aware rendering updates pause heavy animation work when the tab is hidden
- Mobile update stride reduces frequency of expensive dynamic updates (water/fireflies)
- Sky lantern cap prevents unbounded object growth and memory pressure
- Firework simulation uses delta-time for stable behavior across different frame rates
- Raycast helper objects are reused to avoid repeated allocations during interaction

## Local Asset Pipeline

### Lantern GLB

`loadLanternClusterGLB()` loads `vesak_lanterns.glb` from `public/` and places it in two decorative positions:
- Left side placement
- Right side placement

Each cluster receives:
- Material cloning
- Emissive intensity tuning
- Local warm point light
- Gentle floating animation

### Music

`setupAudio()` uses:

```js
const music = new Audio('music.mp3');
music.loop = true;
```

Keep `music.mp3` inside `public/` so the app can fetch it from `/music.mp3`.

## Getting Started

### 1) Install dependencies

```bash
npm install
```

### 2) Run development server

```bash
npm run dev
```

### 3) Production build

```bash
npm run build
```

### 4) Preview production build

```bash
npm run preview
```

## Troubleshooting

### Audio does not play

- Confirm file exists: `public/music.mp3`
- Browser may block autoplay until user interaction; click the audio button

### GLB not visible

- Confirm file exists: `public/vesak_lanterns.glb`
- Check browser console for GLTF loading errors

### Performance is low on mobile

- Ensure quality detection is active (`isMobile` path)
- Avoid increasing object counts and bloom values
- Keep sky lantern cap and dynamic update stride in place for smoother frame times

## Notes

- The main experience is intentionally implemented in `index.html` to keep deployment simple.
- The `src/` directory remains available if you want to migrate this into a modular architecture later.
