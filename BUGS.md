# BUGS.md — loremacs/home

Tracked issues from the 2026-04-21 security + bug review.
Status: `[ ]` open · `[x]` fixed · `[-]` accepted/won't fix

Last reviewed: 2026-04-21.

---

## Security

### [S1] `new Function()` in Emergent Logic Lab executes user-typed JS in page context
- **File:** `projects/emergent-lab/index.html` line 312
- **Severity:** Accepted risk — this is the feature. Blast radius = visitor's own tab only.
- **Details:** `Function("\"use strict\"; return (" + code + ");")()` runs whatever the
  user types in the editor. No server impact. `healModel()` clamps params but does not
  sandbox the execution — `fetch()`, `localStorage.clear()`, etc. are reachable.
- **Action:** Add a UI note if/when the URL is shared more publicly.
- **Status:** `[-]` won't fix — accepted design

---

### [S2] Fake credentials hardcoded in login fixture
- **File:** `projects/playwright/fixtures/login.html` lines 90, 101
- **Severity:** Cosmetic — no backend, no real auth.
- **Details:** `admin` / `password123` shown in hint text and checked in JS. Fixture only.
- **Status:** `[-]` won't fix — intentional demo credential

---

### [S3] `referrerpolicy="no-referrer"` on Playwright iframe
- **File:** `projects/playwright/index.html` line 300
- **Severity:** N/A — this is correct, keep it.
- **Status:** `[x]` no action needed

---

### [S4] No Content-Security-Policy header
- **File:** All pages — no `<meta http-equiv="Content-Security-Policy">` set.
- **Severity:** Low. All JS is inline; no untrusted origins whitelisted.
- **Details:** GitHub Pages cannot set response headers without a proxy. If the project
  moves to Cloudflare Pages, add a `_headers` file:
  ```
  /*
    Content-Security-Policy: default-src 'self' 'unsafe-inline'; frame-src *;
  ```
- **Status:** `[ ]` open — defer until hosting decision is made

---

### [S5] `window.open(fullUrl())` passes user-typed URL to new tab
- **File:** `projects/playwright/index.html` line 1039
- **Severity:** Acceptable. `new URL()` resolution rejects `javascript:` schemes.
  `data:` URIs can still open but only content the user typed themselves.
- **Status:** `[-]` won't fix — acceptable

---

## Bugs

### [B1] `restoreView()` does not guard against NaN/Infinity in rotX / rotY — can freeze render loop
- **File:** `index.html` lines 1052–1058
- **Severity:** Low — only triggers if sessionStorage contains a corrupt or future-schema entry.
- **Details:**
  ```js
  // CURRENT (zoom is clamped but rotX/rotY are not):
  const z = Math.max(0, Math.min(1, v.zoom));   // ✅
  state.rotX = v.rotX; state.targetRotX = v.rotX; // ❌ no clamp / NaN guard
  state.rotY = v.rotY; state.targetRotY = v.rotY; // ❌ no clamp / NaN guard

  // FIX — replace lines 1057-1058 with:
  state.rotX = Number.isFinite(v.rotX) ? v.rotX : -0.18; state.targetRotX = state.rotX;
  state.rotY = Number.isFinite(v.rotY) ? v.rotY : 0.15;  state.targetRotY = state.rotY;
  ```
- **Status:** `[ ]` open — **should fix**

---

### [B2] `ensureFullCatalog()` mutates the module-level `CATALOG` array on every `buildNodes()` call
- **File:** `index.html` lines 374–381
- **Severity:** Very minor / harmless today — idempotent because `while` condition fails after first run.
- **Details:** `bumpClick()` → `buildNodes()` → `ensureFullCatalog()` on every node click.
  After the first call `CATALOG.length === TOTAL_CAPACITY` and the loop is a no-op.
  Becomes fragile if catalog size or shell capacity changes later.
- **Fix:** Add an early-return guard:
  ```js
  function ensureFullCatalog() {
    if (CATALOG.length >= TOTAL_CAPACITY) return; // ← add this
    let i = 0;
    while (CATALOG.length < TOTAL_CAPACITY) {
      const [title, desc] = DUMMY_POOL[i % DUMMY_POOL.length];
      CATALOG.push({ id: "stub_" + i, title, desc });
      i++;
    }
  }
  ```
- **Status:** `[ ]` open — nice to have

---

### [B3] Playwright replay: `resolveByRole()` splits role+name on first `:` only — breaks if name contains `:`
- **File:** `projects/playwright/index.html` line 903–904
- **Severity:** Low — replay silently fails to find the element if accessible name contains a colon.
- **Details:**
  ```js
  // CURRENT:
  const [role, name] = value.split(":"); // ❌ "button:Sign in: Admin" → name = "Sign in"

  // FIX — replace that line with:
  const colonIdx = value.indexOf(":");
  const role = value.slice(0, colonIdx);
  const name = value.slice(colonIdx + 1);
  ```
- **Status:** `[ ]` open — **should fix**

---

### [B4] `downloadCode()` calls `URL.revokeObjectURL` before browser has fully initiated the download
- **File:** `projects/playwright/index.html` lines 1019–1028
- **Severity:** Very minor — works in practice; technically a race condition / minor memory concern.
- **Details:** `a.click()` is synchronous but the download dispatch happens asynchronously.
  Revoking immediately after `.click()` is technically incorrect.
- **Fix:**
  ```js
  // Replace:
  a.click();
  URL.revokeObjectURL(a.href);

  // With:
  a.click();
  setTimeout(() => URL.revokeObjectURL(a.href), 100);
  ```
- **Status:** `[ ]` open — nice to have

---

### [B5] `makeHealedScript()` replaces the user's rule body with a no-op when "Rewrite editor" is clicked
- **File:** `projects/emergent-lab/index.html` lines 433–455
- **Severity:** UX — user loses their rule even if only a param was out of range.
- **Details:** The rewritten rule is always hardcoded to `return { ax: 0, ay: 0, hueShift: 0 };`
  regardless of what the original rule was. User cannot recover their logic.
- **Possible fix:** Only rewrite params, preserve rule/mutate bodies from the original textarea.
  More invasive change — requires parsing the original textarea source.
- **Status:** `[ ]` open — low priority, accepted design per AGENTS.md

---

### [B6] Triptych: `keydown`/`keyup` listeners bound to bare `addEventListener` (implicit window), never removed
- **File:** `projects/triptych/index.html` line 700–706
- **Severity:** None in current context — full-page single-file game, window never outlives the page.
- **Details:** Would leak listeners if this code were copy-pasted into a SPA. Fine as-is.
- **Status:** `[-]` won't fix — correct for this use-case

---

### [B7] Playwright `copyCode()` uses deprecated `document.execCommand('copy')`
- **File:** `projects/playwright/index.html` line 1013
- **Severity:** Low — works in all current browsers but is scheduled for removal.
- **Details:**
  ```js
  // CURRENT:
  try { document.execCommand("copy"); ... }

  // FUTURE-PROOF FIX (requires HTTPS, which GitHub Pages provides):
  navigator.clipboard.writeText($(\"code\").value)
    .then(() => setStatus("Code copied.", "ok"))
    .catch(() => setStatus("Copy failed.", "err"));
  // Remove the el.select() / removeAllRanges() calls too.
  ```
- **Status:** `[ ]` open — nice to have / future-proofing

---

## Not a bug — things confirmed correct

| Item | File | Notes |
|------|------|-------|
| `escapeHtml()` | `index.html` line 516 | Properly escapes `& < > " '` before innerHTML insertion. No XSS. |
| Cross-origin iframe detection | `playwright/index.html` line 389 | `try { iframe.contentDocument }` is the correct pattern. |
| `CSS.escape()` in selector generation | `playwright/index.html` line 456 | Used correctly before building CSS selectors. |
| `sessionStorage` for view state | `index.html` line 387 | Correct — tab-scoped, discarded on session end. |
| `.gitignore` | `.gitignore` | Clean; no `.env`, secrets, or build artifacts tracked. |
| Zoom clamp in `restoreView` | `index.html` line 1055 | `Math.max(0, Math.min(1, v.zoom))` correct. |
| Pinch-zoom bounds | `index.html` lines 950–951 | Clamped to `[0, 1]`. |
| `referrerpolicy` on iframe | `playwright/index.html` line 300 | Correct. |
