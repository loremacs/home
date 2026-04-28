# LOG.md — experiment history

A running record of what's been added to the sphere, in reverse chronological order.
This is a creative log, not a version changelog.

---

## 2026-04-27

### Transformer Slice
A visual, interactive toy decoder that flips the usual chat interface:
the AI asks the human to act as the model. The user starts with a short
prompt, watches it become token chips and fake embedding vectors, then
manually adjusts attention weights, feature-mixer dials, temperature,
and top-k before sampling the next token. It is intentionally not an
exact LLM implementation; it demonstrates the next-token flow from
tokens to embeddings, attention, residual/MLP-style mixing, logits,
softmax probabilities, sampling, and looped generation in a small slice.
Added a guided tutorial overlay that highlights the scenario picker,
tokenize button, token chain, attention sliders, feature dials, sampling
controls, persona shortcuts, candidate tokens, sample button, auto-step,
and reset so visitors can follow the decoder loop without guessing where
to click.
Added a slim collapsible neural-loop strip across the top of the page
showing the simplified decoder path as connected nodes: tokens,
embeddings, attention, mixer, logits, and sampling. The active node and
summary update as the user samples or manually picks tokens.
Reworked the strip into a compact neural-network-style SVG with input
nodes, hidden nodes, an activation/mixer window, output candidates, and
highlighted flow lines. Fixed auto-step so each click runs a fresh batch
from the current response length instead of stopping immediately once an
older generated-token cap had already been reached.

---

## 2026-04-22

### ThisIsFine
A real-time "apocalypse console" that scrapes news about layoffs and job cuts
alongside tech hiring opportunities. Built with a 5-minute polled timer,
it aggregates data from GDELT, Hacker News, and RemoteOK. The UI features
a custom "fire" aesthetic inspired by the iconic meme, and a visible "System Logs"
panel to trace the API fetching events in real-time.

### Gemini3Flash: Prism Break
A light-refracting neon arcade experience. Features glassmorphic UI, neon visuals,
and a responsive physics engine. Includes a "Process Info" overlay as part of the
new metadata requirement.

### New Protocol: Process Metadata
Updated `AGENTS.md` with a new rule: all future projects must capture the number
of prompts and a concise "jist" of the directing creative intent. This ensures
visitors can see the effort and direction involved in the AI-human collaboration.
Retroactively applied to *Prism Break* (3 prompts).

---


## 2026-04-21

### Project hygiene
- Added `BUGS.md` — security + bug review of all current pages ahead of going public.
- Added `LOG.md` (this file) — creative history of experiments.
- Rewrote `README.md` to reflect that this is an AI-directed creativity playground,
  not a traditional coding portfolio.

---

## 2026 (initial build)

### Newsmap
An open-data world dashboard: 2D/3D map, multi-source news feed, live
event overlays (earthquakes, NASA natural events, weather alerts, ISS,
aircraft), and a live-channel video wall. Strictly "no signup, no
paywall" — every data source is a public API or RSS feed, every video
tile is a keyless YouTube live embed or an HLS stream. The whole thing
is a single self-contained HTML file that pulls three libs from a CDN
(Leaflet, globe.gl, hls.js) and otherwise runs entirely in the browser.

### Curtain Lens
A SNES-scale top-down game proof-of-concept. 40KB, fully self-contained, no assets.
Built around the "curtain-lens perception" mechanic — the idea that consciousness
oscillates between seeing and not-seeing, and that mechanic is literally encoded in
how you navigate the levels. Three sub-areas: a dig site, a corporate sublevel, and a
geometry-unstable rift seam. Collect Bridge Fragments, unlock abilities, reach the end.

### Emergent Logic Lab
A boid-swarm simulation where the agent rule is a live editable JavaScript object.
Hit Apply and the simulation rebuilds around your code. If the code crashes or drives the
system into chaos, a self-healing runtime detects it and clamps params back into safe ranges.
The point is to demonstrate "self-testing, self-healing" as an AI concept in a toy you
can poke at directly.

### Playwright Playground
A browser-based Playwright code generator. Left pane: an iframe. Right pane: generated
test code that streams as you interact. Works on same-origin pages (the included fixtures)
with full record and replay. Cross-origin pages drop to view-only mode — the browser
blocks cross-origin inspection, and the tool is honest about that rather than pretending.
Outputs TypeScript, JavaScript, or Python. Keyboard shortcuts for every action.

### Atlas (landing page)
A rotating 3D sphere of nodes built with CSS perspective and per-frame JS transforms —
no WebGL, no 3D library. Fibonacci-sphere distribution, concentric shells that reveal
at different zoom levels, a parallax starfield with twinkling and nebula blobs.
Click counts stored in localStorage and used to promote frequently visited nodes toward
the outer shell. Placeholder nodes (ideas not yet built) show on the sphere and bump
their rank when clicked so the system can be exercised before the pages exist.
