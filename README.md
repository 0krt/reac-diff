# REACTION // DIFFUSION

A Gray–Scott reaction-diffusion field synthesizer in a single dependency-free
HTML file, in the same dark instrument-panel style as the lissajous app.
Inspired by [Jason Webb's reaction-diffusion playground](https://jasonwebb.github.io/reaction-diffusion-playground/app.html).

Open `index.html` in a browser (Chrome or Edge for Web MIDI). No build step, no server needed —
though MIDI and some export features prefer `https://` or `localhost`, so
`python3 -m http.server` works well.

## Features

- **GPU simulation** — WebGL2 ping-pong Gray–Scott solver with a toroidal
  (wrapping) field, up to 40 sim steps per frame, quarter/half/full resolution.
- **MIDI-mappable parameters** — click the `m` button beside any slider, move a
  knob or fader on your controller, and it's bound (CC + channel). Mappings
  persist in localStorage; alt-click a binding to clear it.
- **SVG export** — traces the current field with marching squares at 1–6
  contour levels and downloads a stroked vector file (dark/ink or
  plotter/white style). Plotter-friendly.
- **Video export** — records the live canvas to WebM via MediaRecorder
  (VP9/VP8, 60 fps target).
- **PNG export**, ten f/k pattern presets (mitosis, coral, worms, maze, …),
  six color palettes, paint/erase brush, pause/reseed/clear.

## Keys

| key | action |
| --- | --- |
| `space` | pause / resume |
| `r` | reseed random spots |
| `c` | clear field |
| `s` | export PNG |
| `v` | start / stop video recording |
| `h` | toggle control panel |

Drag on the canvas to inject chemical B; hold `shift` (or right-drag) to erase.
