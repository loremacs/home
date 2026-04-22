# LOG.md — experiment history

A running record of what's been added to the sphere, in reverse chronological order.
This is a creative log, not a version changelog.

---

## 2026-04-21

### Project hygiene
- Added `BUGS.md` — security + bug review of all current pages ahead of going public.
- Added `LOG.md` (this file) — creative history of experiments.
- Rewrote `README.md` to reflect that this is an AI-directed creativity playground,
  not a traditional coding portfolio.

---

## 2026 (initial build)

### Triptych of Passage
A SNES-scale top-down game proof-of-concept. 40KB, fully self-contained, no assets.
Built around the "curtain-lens perception" mechanic from a separate book project —
the idea that consciousness oscillates between seeing and not-seeing, and that mechanic
is literally encoded in how you navigate the levels. Three sub-areas: a dig site, a
corporate sublevel, and a geometry-unstable rift seam. Collect Bridge Fragments, unlock
abilities, reach the end.

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
