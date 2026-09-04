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

- **Grow on surface** (default) — the pattern is evaluated at points *on the
  object* rather than wrapped around it from a flat rectangle. A surface map,
  baked from the same parametric function the mesh is built from, carries the
  object-space position of every point on the skin. The generative models read
  that position and evaluate a three-dimensional pattern there — a Voronoi cell
  is a volume the surface cuts through, a marble vein runs through the solid —
  so there is no seam and no pinch at the poles. Reaction–diffusion solves with
  the surface metric instead, in three parts:
  - **The grid is shaped to the object.** The resolution setting fixes a texel
    budget; the grid's *aspect* comes from the object's own parameterisation. A
    torus knot is nine times longer along its length than round its tube, so it
    gets a 751×85 field rather than a 320×200 one — a texel covers the same
    patch of surface either way, and the pattern stops coming out drawn along
    the knot.
  - **The Laplacian carries the metric.** Each tap is weighted by the inverse
    square of the world distance a texel spans there, so the stencil equals a
    fixed multiple of the true surface Laplacian everywhere and a spot is the
    same size wherever it grows.
  - **Where a ring shrinks, the grid coarsens with it.** At the top of a sphere
    a whole run of texels covers one speck of object, and a weight big enough
    to correct for that would break the explicit scheme. So the row is solved
    on a coarser ring of cells instead — the reduced grid weather models use at
    the poles — and the texels between cell centres are filled in smoothly.
    Each ring staggers its cells by its own offset: lined up row on row they
    make a lattice as symmetric as the sphere, and Gray–Scott answers that with
    a bullseye on the pole. The field also continues past the v edge the way
    the object does — across a pole it carries on down the opposite meridian,
    an open rim has nothing beyond it, a closed tube wraps — so the two poles
    of a sphere no longer diffuse into each other.

  Turn it off for the old behaviour — the flat field projected on, with tiling.
- **Surface styles** — shaded (default), wireframe, or both. The wireframe is a
  see-through grid in the style of the classic wireframe-primitive plates; its
  line spacing is independent of how finely the surface is tessellated.
- **The torus knot's tube** is swept on a transported frame, not a Frenet one.
  A Frenet frame turns over at every inflection and is undefined where the
  curvature vanishes; sweeping a tube along one wrings the surface round on
  itself, which shears the uv and rakes the pattern. The frame here is carried
  along the curve by double reflection and the mismatch left after one lap is
  spread back over the loop, so it closes up periodic with the least twist a
  closed tube can have.
- **Displacement** — the field pushes vertices along the surface normal, so
  chemical B becomes actual relief on the object. It rides a softened,
  bilinearly sampled read of the field (**disp. softness**), so the surface
  swells across a whole feature instead of stepping between texels, and the
  shading normal is rebuilt from that same softened field — geometry and light
  agree rather than fighting.
- **Embossing** — the sharp field gradient additionally perturbs the surface
  normal through a cotangent frame, carving detail into a surface that never
  actually moved.
- **Tiling** — repeat the field across the surface, 0.5× to 6× (projection
  mode only; on a surface the field *is* the parameterisation).

PNG and video export capture whatever is on the canvas, so they record the
projected object. SVG export follows: with the projection on it carries the
marching-squares contours onto the object through the shape's own parametric
function — displacement included — projects them with the camera that drew the
frame, drops the points whose surface faces away, and traces the silhouette
from the same parameterisation so the contours have an edge to sit inside.
Occlusion is by facing only, so on a torus or a knot a far limb shows through a
near one, the way a wireframe plate does. With the projection off it traces the
flat field as before.

## Colour

The fifteen palettes are starting points, not the whole choice:

- **Custom palette** — four colour pickers (background, shadow, mid,
  highlight). Stepping into `custom` seeds them from whichever preset you were
  looking at.
- **Ramp shape** — smooth, mirrored, repeated, or banded into seven steps. The
  same colours read four ways, so every palette is really four.
- **Hue shift / saturation / brightness** — applied to the ramp itself, so they
  work on a preset and on a custom palette alike. Brightness lifts upward and
  *multiplies* downward, all the way to black at −1, and the lights the shader
  adds on top of the ramp come down with it — so the whole picture goes dark,
  background included, rather than the mid-tones being pushed about while a
  bright palette stays bright.
- **Hue spread** — fans the hue out along the ramp, from a single-hue ramp at 0
  to a duotone at the extremes.
- **Ramp curve** — where the colours land across the level window.
- **Hue drift** — rotates the whole ramp over time.

## Liquid and pulse

Two shading controls that read on the flat field and on a projected object.
Both work on the pattern rather than on the surface it sits on:

- **Liquid** — the pattern turns to poured metal. The field read drifts on a
  slow rotational flow, so the shapes themselves crawl and stretch; the
  pattern's own gradient then bends a bead normal steep enough that the rim of
  a blob turns right through to grazing, and that bead mirrors a small
  environment tinted by the top of the palette — so liquid metal in `ember` is
  hot brass and in `ice` is chrome. All of it is masked to where the pattern
  is, so the ground around it stays the matte surface it was. **Flow** sets how
  fast it moves.
- **Pulse** — a swell in thickness whose phase is the field itself, so the wave
  runs *along* the pattern: outward through each blob, down the length of a
  vein, instead of sweeping over the frame regardless of what is drawn on it. A
  little of the geometric distance stays in the phase so the waves also drift,
  and the swell drives the displacement too, so a projected object breathes
  where the pattern is thick. **Pulse rate** and **pulse waves** set its speed
  and wavelength.

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
- **Preset files** — `save json` writes the whole panel to a file: the model
  and its own parameters, the colour section, liquid and pulse, the projection,
  the camera and the resolution. `load json` puts them all back, applying them
  in the order the UI depends on and restoring a saved kill rate exactly rather
  than pulling it back onto the curve.
- **Double-click any slider** to put it back where it started.
- **PNG export**, ten f/k pattern presets (coral growth is where it opens;
  mitosis, worms, maze, …), fifteen colour palettes, pause/reseed/clear.

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
