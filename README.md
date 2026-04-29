# loremacs / home

A personal playground where I direct AI to build the things I imagine —
then use those things, break them, and show them to people.

This isn't a portfolio of my coding ability. It's a record of my ability
to **prompt, direct, and iterate**: to turn an idea into something real by
working with AI as a creative partner. The experiments here reflect
imagination and curiosity more than any particular technical skill.

The landing page is an interactive 3D star-map. Each card floating in the sphere is one experiment. Drag to rotate it. Scroll to dive deeper into
the field. Cards you visit drift toward the front over time.

---

## What's here

| Experiment | What it is |
|------------|-----------|
| [Playwright Playground](projects/playwright/) | Record interactions in an iframe, replay them, export runnable Playwright test code in TypeScript, JavaScript, or Python. |
| [Emergent Logic Lab](projects/emergent-lab/) | A live boid-swarm where you edit the agent rule in a textarea and the simulation heals itself if your code breaks it. |
| [Curtain Lens](projects/curtain-lens/) | A SNES-scale top-down game POC with curtain-lens perception as a playable metaphor. |
| [Newsmap](projects/newsmap/) | Open world-intel dashboard: 2D/3D map, multi-source news feed, live event overlays (quakes, wildfires, ISS, aircraft), and a live-channel video wall. No signup, no paywall. |
| [Prism Break](projects/gemini-prism-break/) | Light-refracting neon arcade game with glassmorphic UI and particle physics. Created by Gemini3Flash. |
| [ThisIsFine](projects/this-is-fine/) | Real-time layoffs news and tech job tracker with a 5-minute polling timer and custom fire-themed UI. |
| [Logic Lab](projects/logic-lab/) | Gate-puzzle game: drag IC components onto a PCB grid, wire them up, and satisfy truth table test cases to advance through 10 levels. |
| [Transformer Slice](projects/transformer-slice/) | A visual toy decoder where the human turns attention, feature, logit, and sampling dials to generate next tokens. |
| [Transformer Slice v2](projects/transformer-slice-v2/) | Step-locked decoder toy with Q.K attention, positional encoding, residual notes, and reversed AI/human chat roles. |
| [Ghostline Ops](projects/ghostline/) | Simulated hacker desk with fake terminal commands, decoy scans, fictional user traces, network monitors, and a global ping map. |
| *(+24 placeholders)* | Ideas queued up. They show on the sphere and get promoted as experiments ship. |

See [`LOG.md`](LOG.md) for a running history of what's been added and when.

---

## How it works

Every experiment is a single self-contained `index.html` file under `projects/`.
No build step. No framework. Most experiments have no runtime dependencies; a few pull CDN libraries for map/video demos. Open any file in a browser and it runs.

The sphere on the landing page reads a catalog array in its own `<script>` block.
Adding a new experiment is two steps:

1. Drop a folder under `projects/` with an `index.html` inside.
2. Give an existing placeholder entry an `href` in the `CATALOG` array
   (or add a new entry at the end).

The sphere picks it up automatically.

---

## Running locally

Requires Node 18+. First time only:

```powershell
npm install
```

Then:

```powershell
npm start    # serves the repo at http://localhost:5173/
npm stop     # frees port 5173
npm restart  # stop + start
```

No build step runs — `serve` just hands out the static files as-is. If you
don't want to install anything, `python -m http.server 5173` works too.

---

## Deploying

Any static host: GitHub Pages, Cloudflare Pages, Netlify, Vercel.
No build command needed — publish the repo root as-is.

---

## Other files

| File | Purpose |
|------|---------|
| [`AGENTS.md`](AGENTS.md) | Handoff context for AI agents (or anyone) picking up work on this repo. Architecture decisions, sharp edges, open questions. |
| [`LOG.md`](LOG.md) | Running log of experiments added — creative history, not a version changelog. |
