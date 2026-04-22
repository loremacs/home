# home

A static site of small web experiments by **@loremacs**.

The landing page is an interactive 3D sphere of nodes — each node is a
sub-page in this repo. Drag to rotate, scroll to dive deeper. Visited
nodes drift toward the front over time (persisted in `localStorage`).

## Structure

```
/
├── index.html              # atlas (the rotating cosmic hub)
├── playground/             # Playwright record/replay playground
│   ├── index.html
│   └── fixtures/           # same-origin pages that can be recorded
│       ├── login.html
│       ├── todo.html
│       └── form.html
├── emergent-lab/           # editable agent-swarm with self-healing runtime
│   └── index.html
└── triptych/               # Triptych of Passage — top-down game POC
    └── index.html
```

Everything is plain HTML + CSS + JS. No build step. No dependencies.
Open any file in a browser (or serve the folder statically) and it runs.

## Local preview

Any static file server will do. For example:

```powershell
python -m http.server 5173
```

Then visit `http://localhost:5173/`.

## Adding a new page

1. Create a folder at the repo root, e.g. `prism-field/`, with an
   `index.html` inside. Anything referenced with a relative path from
   that folder will work.
2. In `index.html` (the atlas at the repo root), open the `CATALOG`
   array near the top of the `<script>` block and either:
   - add a new entry with an `href` pointing at your folder, or
   - edit an existing placeholder entry and add the `href`.

Example entry:

```js
{
  id: "prism",
  title: "Prism Field",
  desc: "Signals get sorted.",
  href: "prism-field/"
}
```

That's it. The sphere picks up the new node automatically.

## Removing a page

Delete the folder and remove (or blank the `href` on) its catalog entry.
Placeholder-only entries still show on the sphere and flash a "coming
soon" toast when clicked — useful for teasing future work.

## Ranking

Each visitor's click counts live in `localStorage` under
`loremacs.atlas.clicks.v1`. The ranking algorithm is:

```
score = catalogIndex - clickCount * 2
```

Lower score = higher priority = visible sooner at low zoom. The "reset"
link in the top bar wipes the local click history.

Cross-visitor aggregated ranking would need a backend; not in this
repo yet.

## Deploying

Any static host works: GitHub Pages, Cloudflare Pages, Netlify,
Vercel, or a plain bucket + CDN. No build command is required; just
publish the root of the repo.
