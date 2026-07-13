# REACTION // DIFFUSION

A Gray–Scott reaction-diffusion field synthesizer in a single dependency-free
HTML file, in the same dark instrument-panel style as the lissajous app.
Inspired by [Jason Webb's reaction-diffusion playground](https://jasonwebb.github.io/reaction-diffusion-playground/app.html).

Open `index.html` in a browser (Chrome or Edge for Web MIDI). No build step, no server needed —
though MIDI and some export features prefer `https://` or `localhost`, so
`python3 -m http.server` works well.

## Features

- **GPU simulation** — WebGL2 ping-pong Gray–Scott solver with a toroidal
  (wrapping) field, up to 40 sim steps per frame,
  quarter / half / full / 2× resolution.
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
| `h` | toggle control panel |

Drag on the canvas to draw with the current tool; hold `shift` to erase.
Pause first if you want to sketch a seed without the reaction advancing.
