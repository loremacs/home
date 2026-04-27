# AGENTS.md

Read this before touching anything. `README.md` is for visitors; this
file is for whoever is building next — agent or human.

Last updated: 2026-04-23.

---

## 1. What this repo is

A static personal site for **@loremacs**. The landing page (`index.html`)
is an interactive 3D sphere of cards. Each card links to a sub-page
experiment under `projects/`. No build step, no framework — every file
is a self-contained HTML document.

Git remote: `https://github.com/loremacs/home` on branch `main`.
SSH note: the SSH key on this machine is registered to `jasolvren`, not
`loremacs` — pushes via SSH fail. Use HTTPS (Windows Credential Manager).

---

## 2. File structure

```
/
├── index.html                   landing page: the atlas sphere
├── README.md                    visitor-facing docs
├── AGENTS.md                    this file
├── .gitignore
└── projects/
    ├── playwright/
    │   ├── index.html           Playwright record/replay tool
    │   └── fixtures/            same-origin pages for recording
    ├── emergent-lab/
    │   └── index.html           editable agent swarm + self-healing runtime
    ├── curtain-lens/
    │   └── index.html           top-down game POC (read-only)
    ├── newsmap/
    │   └── index.html           open world-intel dashboard
    ├── this-is-fine/
    │   └── index.html           layoffs news + tech job tracker
    └── logic-lab/
        └── index.html           gate-puzzle game: wire IC chips, satisfy truth tables
```

---

## 3. How to add a new card

The catalog is a JS array in `index.html` around line 373 (search
`const CATALOG`). **7 entries are live**, 24 are named placeholders.

**Checklist — do all five steps:**

1. **Create the project.** Add `projects/<id>/index.html`. Self-contained
   single HTML file, no external assets, no framework.

2. **Add a CATALOG entry.** Either promote an existing placeholder (add
   `href`, `prompts`, `jist`) or append a new object before the
   placeholder block. Required fields:
   ```js
   { id: "my-id",
     title: "Display Name",
     desc:  "One-line teaser for the card.",
     href:  PROJECTS_BASE + "my-id/",
     prompts: N,       // cumulative AI prompt count across all sessions
     jist:  "One sentence: what was the core build goal." }
   ```

3. **Add a thumbnail.** Required for live cards. In `makeThumb()` in
   `index.html`, add an `if (n.id === "my-id")` branch before the
   `// Generic procedural glyph` comment. Rules:
   - `viewBox="0 0 160 90"` — fixed, matches the 16:9 `.thumb` CSS ratio.
   - Dark background thematically matched to the app's own colour palette.
   - Show recognisable elements: dominant colours, key UI shapes, data patterns.
   - Max ~50 SVG elements. No `<image>` tags or external refs.
   - Reference the existing 7 thumbnails for style guidance (`playwright`,
     `emergent`, `curtain-lens`, `newsmap`, `prism-break`, `this-is-fine`,
     `logic-lab`).

4. **Update the file tree** in §2 of this file and the live-card count
   in §3 above.

5. **Update `prompts`** in the CATALOG entry after each session that
   adds more work to an existing card.

**To remove a card:** delete `projects/<id>/`, then either remove the
CATALOG entry or strip its `href` (reverts to a placeholder dot).

---

## 4. Conventions

- **Single-file HTML.** Every experiment lives in one `index.html`.
  Only split files if an asset is genuinely too large to inline.
- **No frameworks.** Plain HTML/CSS/JS only. No React, Vue, Svelte, etc.
- **No comment churn.** Do not add or remove comments unless explicitly
  asked. Match the existing style: sparse section dividers, short notes.
- **No scratch files.** Do not create `progress.txt`, `TODO.md`, or
  similar unless asked. This `AGENTS.md` is the only meta-file.
- **Minimal edits.** Prefer the smallest correct change. Don't refactor
  surrounding code while fixing a targeted issue.
- **Process metadata required.** Every live CATALOG entry must have
  `prompts` (integer) and `jist` (string). These appear in the hover
  tooltip on the atlas and are part of the site's identity.

---

## 5. Open questions

1. **Hosting.** GitHub Pages vs Cloudflare Pages — recommended
   Cloudflare for speed + custom domain, but GitHub Pages is zero-setup.
2. **Custom domain.** Which domain should point here?
3. **Global ranking.** User wants click counts collected across all
   visitors, not just per-device. Needs a backend (Cloudflare Worker +
   KV, ~50 lines). Not built yet.
4. **Catalog externalization.** Move `CATALOG` to `catalog.json` so
   entries can be added without editing the HTML directly. Worth doing
   once the list passes ~40 entries.
5. **Root redirect.** A sibling repo at `d:\source\triptych-of-passage`
   has old copies of some files under `game/`. Left intentionally;
   do not touch that repo.

---

## 6. Known sharp edges

- **Playwright cross-origin:** browsers block iframe inspection across
  origins. The tool silently switches to view-only mode for external URLs.
  Do not attempt to work around this in client-only code.
- **Sphere fills equator-first by design.** `fibonacciSphere()` sorts
  its generated points by `|y|` ascending before returning them, so
  the highest-rank cards land at the middle band of each shell and
  lower-rank cards fill toward the poles. Do not remove this sort.
- **Atlas `renderFrame()` runs at rAF rate** even when idle — fine for
  demos, but notable on battery. Gate on a dirty flag if it ever matters.
- **`localStorage` keys are versioned** (`.v1` suffix). Bump to `.v2`
  and migrate if the schema changes.
- **Curtain Lens is read-only.** The perception mechanic is tied to a
  separate book project. Don't modify it unless explicitly asked.
- **No tests.** Demo-grade. Don't add a test harness unless asked.

---

## 7. Suggested next moves

1. **Pick a host and point a domain** — turns this into a live URL.
2. **Build the next placeholder** — ask the user which one and whether
   it should be a tool, a game, or a visual piece.
3. **Global ranking API** — Cloudflare Worker + KV, small lift.
4. **Externalize CATALOG** — only if the entry count outgrows inline editing.

---

## 8. Running locally

```powershell
python -m http.server 5174
# open http://localhost:5174/
```

