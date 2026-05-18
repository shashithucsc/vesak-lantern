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
