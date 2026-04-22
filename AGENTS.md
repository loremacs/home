# AGENTS.md — handoff context

Read this first if you are a new agent (or a human) picking up work on
this repo. The `README.md` explains the site to a visitor; this file
explains the **project state** to whoever is building it next.

Last updated: 2026-04-21.

---

## 1. What this repo is

A static personal site for **@loremacs**. The landing page (`index.html`
at the repo root) is an interactive 3D sphere of "nodes". Each node is
a link into a sub-page experiment. New experiments are added under
`projects/` plus one entry in the catalog array on the landing page.
No build step, no framework, no dependencies — every file is a
self-contained HTML document.

Git remote: `https://github.com/loremacs/home` on branch `main`.

SSH note: the machine this was built on has an SSH key registered to
the GitHub account `jasolvren`, not `loremacs`, so SSH pushes get
"Repository not found". Current `origin` is the HTTPS URL and pushes
through Windows Credential Manager. If you want SSH, add this machine's
key to the `loremacs` account or use a host-alias in `~/.ssh/config`.

---

## 2. File structure

```
/
├── index.html                   landing page: the "atlas" sphere
├── README.md                    user-facing docs
├── AGENTS.md                    this file
├── .gitignore
└── projects/
    ├── playwright/
    │   ├── index.html           Playwright record/replay tool
    │   └── fixtures/            same-origin pages the tool can record
    │       ├── login.html
    │       ├── todo.html
    │       └── form.html
    ├── emergent-lab/
    │   └── index.html           editable agent swarm + self-healing runtime
    ├── curtain-lens/
    │   └── index.html           top-down game POC (40KB, self-contained)
    └── newsmap/
        └── index.html           open world-intel: map + news feeds + events + live channels
```

---

## 3. What's done and how each piece works

### 3.1 Atlas (landing page) — `index.html`

Interactive sphere of nodes built with CSS `perspective` + per-frame JS
transforms on absolutely-positioned `<a>` elements. Every node is a
real DOM anchor, so clicks, focus, and pointer events work without
any raycasting.

Key design decisions (do not "simplify" these without reason):

- **No 3D library.** We use Fibonacci-sphere unit vectors and
  per-frame `translate3d()` on each node. Rotation math is done in JS
  so we can also compute opacity fade for back-facing nodes. The
  container is *not* CSS-rotated; each node's transform already
  includes the rotation. See `renderFrame()` around line 440.

- **Nodes should read as a fixed-size field.** Scroll/pinch reveals more
  nodes by rank and shifts depth so it feels like diving deeper into a
  spherical tunnel. Node cards stay near a standard readable size while
  rotation simply exposes the back side better.

- **Rank gates visibility, not existence.** At low zoom, only top-rank
  nodes render; at high zoom, all of them. Rank is recomputed from
  `localStorage` click counts via `recomputeRanks()`. The score
  formula is `baseIndex - clicks * 2`. Lower score = higher rank =
  visible sooner.

- **View restore on return.** When the user clicks a live node, the
  current zoom + rotation is saved to `sessionStorage` (not local)
  under `loremacs.atlas.view.v1`. On reload, `restoreView()` reads it.
  This keeps coming back from a sub-page non-jarring. Clearing the
  tab's session discards it, which is the desired behavior.

- **Placeholders still count.** Catalog entries without an `href` pop
  a toast and bump their click count anyway so the rank algorithm can
  be tested before the corresponding page exists.

- **rotX clamped to ±1.2 rad** in `renderFrame()` to avoid gimbal
  flip. If you want full freedom, switch to quaternions (not done).

- **Auto-rotate after 2.5s idle** to signal the sphere is alive. Lives
  in `renderFrame()`. The moment the user interacts, `lastInteract`
  resets and it stops.

- **Background canvas `#sky`.** 500 twinkling stars + 4 nebula blobs
  (radial gradients). Both layers parallax ~80px with rotation so
  turning genuinely feels like looking at a different patch of sky.
  Implementation: `initSky()` and `drawSky()`.

- **Pinch + wheel zoom.** Both wired. Wheel uses
  `Math.exp(-deltaY * 0.0012)` so it feels multiplicative, not linear.

- **Hint overlay on first visit.** Dismissed permanently via
  `localStorage` key `loremacs.atlas.hintSeen.v1`.

### 3.2 Playwright Playground — `projects/playwright/`

Codegen-in-the-browser. Left pane is an iframe; right pane is a
textarea editor that streams generated Playwright code as the user
interacts with the iframe.

Critical constraint to know: **browsers block cross-origin iframe
inspection**. We can only record + replay when the iframe's src is
same-origin with the parent. The tool detects this via
`iframe.contentDocument` access in a try/catch (`detectSameOrigin()`)
and flips between two modes:

- same-origin → "recording enabled" green badge; event listeners are
  attached to `iframe.contentDocument` and translated to Playwright
  actions.
- cross-origin → "view-only" orange badge; overlay card explains the
  constraint; Record button refuses to activate.

Three same-origin fixtures live in `projects/playwright/fixtures/` and cover
the main Playwright primitives (`fill`, `check/uncheck`, `selectOption`,
`press`, click-by-role, click-by-testid). Every form element has a
`data-testid` so generated locators are stable.

Selector picker priority is: `getByTestId` → unique `#id` →
`getByRole(role, {name})` → `getByLabel` → `getByText` → CSS path with
`:nth-of-type` as last resort. See `bestSelector()`.

**Replay resets the iframe first** — `reloadIframeAndWait()` navigates
through `about:blank` back to the URL and awaits the load event before
stepping through actions. This was a user request; don't remove it.

Code output has three languages: TypeScript, JavaScript, Python. The
textarea is regenerated in full from the action list whenever anything
changes (`rebuildCode()`). Manual edits to the textarea between
recordings are discarded — this is known and acceptable given scope.

Keyboard shortcuts: `Ctrl+Shift+R` toggle record, `Ctrl+Shift+P`
replay, `Ctrl+Shift+L` clear, `Ctrl+Shift+C` copy.

### 3.3 Emergent Logic Lab — `projects/emergent-lab/`

An agent-swarm (boids-style) where the user edits a JavaScript object
describing the `rule(agent, neighbors, state)` and optional `mutate()`
functions. On Apply, we:

1. `compileModel()` via `new Function("return (...)")` (sandboxed-ish;
   this runs user JS in the parent page — that's fine because it's
   the user's own browser, but worth noting).
2. `selfTestModel()` runs structural + behavioral tests.
3. `healModel()` clamps params into safe ranges, wraps `rule` and
   `mutate` in try/catch, installs a fallback rule if missing.
4. The simulation loop also has a runtime integrity self-heal that
   nudges params back if the simulation goes chaotic.

This is a demo of "self-testing, self-healing" more than a serious
tool. Don't over-engineer it.

### 3.4 Newsmap — `projects/newsmap/`

One-screen open-data world dashboard. Floating-panel layout: four
draggable, resizable panels (News, Map, Events, Channels) sit on an
`#workspace` div with a CSS dot-grid background. Panels snap to a 24px
grid; positions are saved to `localStorage` under `newsmap.panels.v1`.
No CSS grid for the main layout — everything is `position:absolute`.

Everything runs client-side. The whole design is "no signup, no paywall"
— if a source requires an API key, it's deliberately excluded. CDN deps:
Leaflet, globe.gl, hls.js, satellite.js.

**Data sources (all keyless):**
- OpenStreetMap raster tiles (dark-inverted via CSS filter)
- USGS earthquake GeoJSON (all_day)
- NASA EONET v3 open events (7d window)
- NOAA weather.gov active alerts (US only)
- `api.wheretheiss.at` ISS position
- OpenSky Network `/states/all` for aircraft — proxied via
  `api.allorigins.win` to work around browser CORS block; off by default
- RainViewer radar tiles (`api.rainviewer.com`) — added as a Leaflet
  tile overlay, no point markers; off by default
- Launch Library 2 (`ll.thespacedevs.com`) upcoming orbital launches —
  free, 15 req/hr, no key; on by default
- Extra satellite tracking (`tle.ivanstanojevic.me` TLE API +
  `satellite.js` SGP4 propagation) — Tiangong, Hubble, Starlink;
  off by default
- Reddit JSON (r/worldnews, r/news), HN Algolia, Wikinews MediaWiki API,
  GDELT DOC 2.0
- RSS via three-tier fallback: direct → `api.allorigins.win/raw` →
  `api.rss2json.com`. The DOM parser runs client-side on the first two.
- YouTube live channel embeds via `/embed/live_stream?channel=ID`.
  Channels split into News, Space/Science, and Webcams/Live categories.

**Key design choices:**
- All feed/event results cached in `localStorage` under
  `newsmap.cache.v1` with a 5-minute TTL. User preferences persist
  under `newsmap.state.v1`; panel positions under `newsmap.panels.v1`.
- Polling intervals are user-configurable in the ⚙ Settings panel
  (events default 60s, feeds default 5m). `restartPolling()` applies
  changes immediately without a page reload.
- Channels panel is a vertical scrollable strip of fixed-height tiles
  (122px) with drag-to-reorder. Close button appears on hover.
- `initPanels()` runs at boot: reads saved layout or falls back to
  `defaultPanelLayout()` which tiles the four panels side-by-side
  across the viewport. Each panel gets drag (`makeDraggable`) and resize
  (`makeResizable`) handlers; minimize button toggles `.minimized` class.
- Settings slide-over lists every source/layer/channel with its URL and
  a toggle, plus polling controls. Opened with ⚙ in the header.
- The 2D/3D toggle buttons live inside the Map panel bar (not the
  header) to avoid duplicate IDs.

**Known gotchas:**
- YouTube channel-based live embed returns a "no live stream" page when
  a channel isn't currently streaming. Not a bug.
- OpenSky still rate-limits harshly even via allorigins proxy; Aircraft
  layer is off by default.
- Launch Library 2 free tier is 15 req/hr — don't fire it on every poll
  tick if you shorten the event interval below 60s.
- RainViewer tile path changes with each new radar frame; `loadRainviewer`
  refetches the JSON each time and swaps the layer. Old layer is removed
  before adding the new one.
- Several RSS feeds send no CORS header; the fallback chain handles them.

### 3.5 Curtain Lens — `projects/curtain-lens/`

A top-down tile-based POC I preserved from an earlier project. It is
~40KB of self-contained HTML with no external assets. The curtain-lens
perception mechanic is intentional and tied to a separate book project.
Treat as read-only unless the user asks otherwise.

---

## 4. Node catalog

The landing page imports nothing; the catalog is a literal array at
the top of the `<script>` block in `index.html` (currently lines
~293–323). Three entries are live; 27 are placeholders with
intentionally poetic names (Prism Field, Loom, Fathom, Glyph Forge,
Palimpsest, etc.) so the sphere has enough mass to feel like a real
constellation.

**To add a page:**
1. Create a folder under `projects/` with an `index.html` inside.
2. Edit the `CATALOG` array — either convert an existing placeholder
   (give it an `href`) or add a new entry at the end.
3. Optionally add a custom SVG thumbnail by extending `makeThumb()`
   with a new `if (n.id === "...")` branch. Placeholders get auto-
   generated line-art glyphs seeded by their id, so this is optional.

**To remove a page:** delete the folder from `projects/` and either remove the entry
or strip its `href` (turning it back into a placeholder).

---

## 5. Open questions still awaiting the user

These are questions I asked but the user has not yet answered. Don't
make the call unilaterally without prompting:

1. **Hosting.** GitHub Pages vs Cloudflare Pages. Recommended
   Cloudflare Pages for speed + clean custom-domain support, but
   GitHub Pages is zero-setup if the repo stays on GitHub.

2. **Custom domain.** Which domain points here? Needed before writing
   DNS instructions.

3. **Cross-visitor ranking.** Currently per-device via `localStorage`.
   User expressed interest in *global* ranking ("I'd like all user
   selections to be collected to rank the nodes position"). Requires
   a backend — a trivial serverless function + KV store (Cloudflare
   Workers + KV is my suggested path, ~50 lines). Not built yet.

4. **Catalog externalization.** Whether to move the `CATALOG` array
   out to `catalog.json` so pages can be added without editing the
   landing HTML. Small refactor, probably worth doing once the list
   passes ~40 entries or when the user wants non-technical edits.

5. **Thumbnail style.** Real nodes currently have hand-drawn SVG
   thumbnails; placeholders get seeded procedural line-art. Ask the
   user before changing either style.

6. **Root redirect.** The book project (`d:\source\triptych-of-passage`,
   sibling of this repo) still has copies of the same files under
   `game/`. Those were intentionally *left in place* — this repo is a
   sibling, not a fork. The user may want to delete the game/
   subtree from the book repo eventually but has not asked for it.
   Do not touch the book repo.

---

## 6. Known limitations and sharp edges

- **Playwright playground cross-origin recording**: impossible in a
  pure static page. The only paths to enable it are a browser
  extension, an Electron wrapper, or a server-side headless Chromium
  driven via WebSocket. The last one is the right move if the user
  ever asks for "actually run the Playwright code" rather than "just
  export it" — scaffold would go in `playground/server/` with
  `express` + `playwright`.

- **Mobile touch rotation**: works via pointer events. Pinch-to-zoom
  works via `touchstart/touchmove`. Not heavily tested on real
  devices — try before promising.

- **SafeToAutoRun**: the atlas `renderFrame()` runs at rAF rate even
  when idle. On laptops this hits the GPU more than it needs to. If
  this becomes a problem, gate the loop on a dirty flag that flips
  on input/zoom/idle-auto-rotate tick.

- **localStorage schema version**: keys are namespaced with `.v1`
  suffix. If you change the schema, bump to `.v2` and migrate.

- **No tests.** The nature of these experiments is demo-grade.
  Don't add a test harness unless the user asks.

---

## 7. Suggested next moves, in rough priority

Pending the user's answers above, the highest-leverage next steps:

1. **Pick a host + point a domain.** This is what turns the repo
   into a live thing. See §5 Q1 and Q2.
2. **Decide on placeholder-to-real conversion order.** The user said
   placeholders are pages "we can build later" — ask which one to
   build next and whether it should be a tool (like the playground)
   or a visual/interactive piece (like the emergent lab).
3. **Optional: move `CATALOG` out to JSON.** Only if the user wants
   to keep adding entries without touching `index.html`.
4. **Optional: global ranking API.** Cloudflare Worker with a single
   KV namespace. Fetch on atlas load, POST on click.

---

## 8. Conventions to follow

- **Single-file HTML pages.** Keep each experiment self-contained
  unless an asset is genuinely too big to inline. This makes the
  "add a folder, done" UX work.
- **No frameworks.** The user has asked for simple HTML/JS. If you
  feel the urge to add React/Vue/Svelte, don't; just structure the
  JS cleanly.
- **No comment churn.** User rule: don't add or remove comments
  unless explicitly asked. The existing code style is "a few helpful
  section dividers and sparse per-function notes". Match that.
- **Don't auto-create `.md` scratch files.** This `AGENTS.md` is
  intentional; do not spawn others (progress.txt, TODO.md, etc.)
  unless asked.
- **Prefer minimal, focused edits.** See the user's bug-fixing
  discipline rule.

---

## 9. Running locally

```powershell
# from the repo root
python -m http.server 5174
# then open http://localhost:5174/
```

As of handoff, a `python -m http.server` is still running in the
background on port 5174 from an earlier Cascade session. It's
harmless; kill it when you want to recover the port.

---

*End of handoff.*
