# REACTION // DIFFUSION

An organic pattern synthesizer in a single dependency-free HTML file, in the
same dark instrument-panel style as the lissajous app. The reaction-diffusion
model is inspired by [Jason Webb's playground](https://jasonwebb.github.io/reaction-diffusion-playground/app.html);
four more generative models sit alongside it.

## Models

Every model produces a scalar field, so the shading, contour export, and MIDI
mapping are shared across all of them. Switch models from the panel:

- **Reaction–diffusion** — the dynamic Gray–Scott solver (drawing, bias,
  presets, SVG import; all described below).
- **Voronoi cells** — organic cellular partition with cell count, gap,
  smoothness, jitter, and animated drift.
- **Marble veins** — domain-warped noise for flowing marble / vein structures.
- **Metaballs 3D** — raymarched blobs merged with a smooth minimum. The field
  is the surface depth seen from the camera, so relief shading and the contour
  export follow the real geometry. Drag to orbit the cluster, wheel to zoom.
- **Flow lines** — warped wavefronts for fingerprint / topographic contours.

Generative models have a **Generate** button that reshuffles the random seed.

Open `index.html` in a browser (Chrome or Edge for Web MIDI). No build step, no server needed —
though MIDI and some export features prefer `https://` or `localhost`, so
`python3 -m http.server` works well.

## 3D projection

Any model's field can be mapped, live, onto an orbiting object: **sphere,
torus, hyperboloid, cylinder, cone, torus knot**. Drag the canvas to turn it,
wheel to zoom, `o` to toggle. The simulation keeps running underneath, so a
reaction-diffusion pattern grows across the surface in real time.

- **Surface styles** — shaded, wireframe, or both. The wireframe is a
  see-through grid in the style of the classic wireframe-primitive plates; its
  line spacing is independent of how finely the surface is tessellated.
- **Displacement** — the field pushes vertices along the surface normal, so
  chemical B becomes actual relief on the object.
- **Embossing** — the field gradient perturbs the surface normal through a
  cotangent frame, so the pattern is lit as if it were carved into the object.
- **Tiling** — repeat the field across the surface, 0.5× to 6×.

PNG and video export capture whatever is on the canvas, so they record the
projected object; SVG export always traces the flat field.

## Features

- **GPU simulation** — WebGL2 ping-pong Gray–Scott solver with a toroidal
  (wrapping) field, up to 40 sim steps per frame,
  quarter / half / full / 2× resolution.
- **Live band** — most of the (feed, kill) plane is dead: the field collapses to
  uniform A or floods with B, and anything you draw is erased in a few frames.
  With the band locked (the default), the kill slider spans only the living
  interval and rides the saddle-node curve of the kinetics, `k_c(f) = √f⁄2 − f`,
  as feed moves — so the whole slider is useful instead of a few pixels of it,
  and feed can be swept without killing the pattern. The interval is measured,
  not guessed: the solver was swept over feed × (k − k_c), each cell reseeded
  and run ~6k steps, keeping the spatial standard deviation of B; zero means
  the field went uniform. Sampled across five feed rates, all 25 positions of
  the locked slider produce a live pattern. Presets are applied exactly — the
  two that sit just outside the measured band widen it rather than being moved.
  The timestep is capped at the solver's own stability limit
  (forward Euler on the 9-point Laplacian is stable while `dt · D · 1.6 < 2`;
  past it the field saturates in one jump), and B is held to half of A's
  diffusion rate, without which no Turing instability exists. Unlock for the
  full plane.
- **Diffusion bias** — an X/Y anisotropy that weights the Laplacian stencil so
  growth drifts in a chosen direction (as in the original playground).
- **Lit rendering** — the B field is treated as a height map and shaded with
  diffuse + specular lighting (adjustable relief, specular, and light angle),
  plus in-shader bilinear smoothing, for a much crisper, 3-D look. Runs full
  resolution by default.
- **Drawing tools** — brush, line, rectangle, and ellipse, with fill/outline
  and paint/erase modes and a stabilizer for smooth freehand strokes. All
  drawing goes through GPU stamps that are **independent of the reaction**, so
  you can pause and sketch a seed without the simulation running.
- **SVG import** — load a vector file and stamp its silhouette into the field
  as chemical B (fit and centered), then let it react.
- **MIDI-mappable parameters** — click the `m` button beside any slider, move a
  knob or fader, and it's bound (CC + channel). Mappings persist in
  localStorage; alt-click a binding to clear it.
- **SVG export** — traces the current field with marching squares at 1–6
  contour levels and downloads a stroked vector file (dark/ink or
  plotter/white). Plotter-friendly.
- **Video export** — records the live canvas to WebM via MediaRecorder
  (VP9/VP8, 60 fps target).
- **PNG export**, ten f/k pattern presets (mitosis, coral, worms, maze, …),
  six color palettes, pause/reseed/clear.

## Keys

| key | action |
| --- | --- |
| `space` | pause / resume (draw mode) |
| `r` | reseed random spots |
| `c` | clear field |
| `1`–`4` | select brush / line / rect / circle |
| `e` | toggle erase |
| `x` | toggle fill |
| `s` | export PNG |
| `v` | start / stop video recording |
| `o` | toggle 3D projection |
| `h` | toggle control panel |

Drag on the canvas to draw with the current tool; hold `shift` to erase.
Pause first if you want to sketch a seed without the reaction advancing.
While a 3D view is on screen — the projection, or the 3D metaball model — the
drag orbits the camera instead, and the drawing tools are put away.
