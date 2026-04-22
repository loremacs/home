# loremacs / home

A personal playground where I direct AI to build the things I imagine —
then use those things, break them, and show them to people.

This isn't a portfolio of my coding ability. It's a record of my ability
to **prompt, direct, and iterate**: to turn an idea into something real by
working with AI as a creative partner. The experiments here reflect
imagination and curiosity more than any particular technical skill.

The landing page is an interactive 3D star-map. Each node floating in the
sphere is one experiment. Drag to rotate it. Scroll to dive deeper into
the field. Nodes you visit drift toward the front over time.

---

## What's here

| Experiment | What it is |
|------------|-----------|
| [Playwright Playground](projects/playwright/) | Record interactions in an iframe, replay them, export runnable Playwright test code in TypeScript, JavaScript, or Python. |
| [Emergent Logic Lab](projects/emergent-lab/) | A live boid-swarm where you edit the agent rule in a textarea and the simulation heals itself if your code breaks it. |
| [Triptych of Passage](projects/triptych/) | A SNES-scale top-down game POC tied to a separate book project. Curtain-lens perception mechanic as a playable metaphor. |
| *(+27 placeholders)* | Ideas queued up. They show on the sphere and get promoted as experiments ship. |

See [`LOG.md`](LOG.md) for a running history of what's been added and when.

---

## How it works

Every experiment is a single self-contained `index.html` file under `projects/`.
No build step. No framework. No dependencies. Open any file in a browser and it runs.

The sphere on the landing page reads a catalog array in its own `<script>` block.
Adding a new experiment is two steps:

1. Drop a folder under `projects/` with an `index.html` inside.
2. Give an existing placeholder entry an `href` in the `CATALOG` array
   (or add a new entry at the end).

The sphere picks it up automatically.

---

## Running locally

```powershell
python -m http.server 5174
# then open http://localhost:5174/
```

---

## Deploying

Any static host: GitHub Pages, Cloudflare Pages, Netlify, Vercel.
No build command needed — publish the repo root as-is.

---

## Other files

| File | Purpose |
|------|---------|
| [`AGENTS.md`](AGENTS.md) | Handoff context for AI agents (or anyone) picking up work on this repo. Architecture decisions, sharp edges, open questions. |
| [`BUGS.md`](BUGS.md) | Tracked bugs and security notes from a 2026-04-21 review. |
| [`LOG.md`](LOG.md) | Running log of experiments added — creative history, not a version changelog. |
