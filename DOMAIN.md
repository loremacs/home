# Domain setup — Namecheap → static host

A practical checklist for pointing a Namecheap-registered domain at this
site once it's live on a static host. Covers the two most likely hosts:
**GitHub Pages** and **Cloudflare Pages**. Pick one.

Fill these in once decided so the rest of the doc has concrete values:

- Domain: `_____________.com` (example: `loremacs.com`)
- Apex vs subdomain: apex (`loremacs.com`) or `www.loremacs.com`?
- Host: GitHub Pages **or** Cloudflare Pages

---

## 0. Prereqs

- The repo is pushed to `github.com/loremacs/home` on `main`.
- You can log in to Namecheap and edit DNS for the domain.
- You have access to the GitHub account that owns `loremacs/home`, or
  a Cloudflare account if going that route.

---

## Option A — GitHub Pages

Simplest path. Zero servers, free TLS, deploys on every push to `main`.

### A1. Turn on Pages

1. In GitHub: `Settings` → `Pages`.
2. **Source**: `Deploy from a branch`.
3. **Branch**: `main`, **Folder**: `/ (root)`.
4. Save. Wait 1–2 minutes for first deploy.
5. Confirm the default URL works: `https://loremacs.github.io/home/`.

### A2. Add the custom domain in GitHub

1. Same `Settings` → `Pages` page.
2. Under **Custom domain**, enter the domain (e.g. `loremacs.com` or
   `www.loremacs.com`). Save.
3. GitHub will create/commit a `CNAME` file at the repo root with that
   domain. Pull the change locally.
4. Leave **Enforce HTTPS** unchecked for now; turn it on later once DNS
   has propagated and the TLS cert has been issued (GitHub does this
   automatically, usually within ~15 minutes of DNS resolving).

### A3. Configure DNS in Namecheap

Log in to Namecheap → `Domain List` → `Manage` on the domain →
`Advanced DNS`.

Remove any default Namecheap parking records (`URL Redirect`, `CNAME www
→ parkingpage.namecheap.com`, etc.) so they don't conflict.

**If using apex (`loremacs.com`)**, add four A records pointing at
GitHub Pages:

| Type | Host | Value           | TTL       |
|------|------|-----------------|-----------|
| A    | @    | 185.199.108.153 | Automatic |
| A    | @    | 185.199.109.153 | Automatic |
| A    | @    | 185.199.110.153 | Automatic |
| A    | @    | 185.199.111.153 | Automatic |

And a `CNAME` for `www` so it redirects to the apex:

| Type  | Host | Value                  | TTL       |
|-------|------|------------------------|-----------|
| CNAME | www  | loremacs.github.io.    | Automatic |

**If using `www.loremacs.com` as primary**, skip the A records and use
only the `CNAME`:

| Type  | Host | Value                  | TTL       |
|-------|------|------------------------|-----------|
| CNAME | www  | loremacs.github.io.    | Automatic |

(Namecheap accepts an `ALIAS`/`CNAME`-at-root pseudo-record for the apex
too, but A records are the officially-supported path for GitHub Pages.)

### A4. Wait, verify, lock

1. Wait 10–30 minutes. DNS propagation is usually fast but not instant.
2. From a terminal: `nslookup loremacs.com` — should return the four
   GitHub IPs.
3. In GitHub `Settings → Pages`, the domain box should switch from
   "DNS check in progress" to a green check.
4. Tick **Enforce HTTPS**. Done.

---

## Option B — Cloudflare Pages

Slightly more setup, but faster globally, cleaner analytics, easy to add
serverless functions later (useful for the global-ranking idea in
`AGENTS.md`).

### B1. Connect the repo

1. Sign in at `dash.cloudflare.com`.
2. `Workers & Pages` → `Create` → `Pages` → `Connect to Git`.
3. Pick `loremacs/home`.
4. Build settings:
   - **Framework preset**: `None`
   - **Build command**: *(leave blank)*
   - **Output directory**: `/`
5. Deploy. First build URL will be something like
   `home-abc.pages.dev`.

### B2. Add the custom domain in Cloudflare Pages

1. In the Pages project → `Custom domains` → `Set up a custom domain`.
2. Enter the domain (`loremacs.com`). Cloudflare will tell you exactly
   which DNS records to create.

There are two sub-paths from here.

### B3a. Sub-path 1: Keep Namecheap as the DNS host

If you don't want to move nameservers, just add records at Namecheap.

For the apex: Cloudflare will give a target like
`loremacs.pages.dev` and request a `CNAME` at `@`. Namecheap supports
"CNAME-at-apex" via `ALIAS` records:

| Type  | Host | Value                  | TTL       |
|-------|------|------------------------|-----------|
| ALIAS | @    | home-abc.pages.dev.    | Automatic |
| CNAME | www  | home-abc.pages.dev.    | Automatic |

If `ALIAS` isn't available in your UI (some Namecheap accounts only
expose `CNAME (Alias)`), use that instead — same thing.

### B3b. Sub-path 2: Move DNS to Cloudflare (recommended long-term)

1. Cloudflare will prompt you to add the domain as a full zone.
2. It gives you **two Cloudflare nameservers** (e.g.
   `lana.ns.cloudflare.com`, `rick.ns.cloudflare.com`).
3. In Namecheap: `Domain` → `Nameservers` → switch from "Namecheap
   BasicDNS" to **Custom DNS**, paste the two Cloudflare nameservers,
   save.
4. In Cloudflare: once the zone is active (can take up to a few hours
   but is usually under 30 minutes), Cloudflare auto-creates the
   correct `CNAME` records for the Pages project.
5. Turn on `Always Use HTTPS` and `Automatic HTTPS Rewrites` in the
   Cloudflare SSL/TLS panel.

Moving nameservers to Cloudflare is the better long-term choice because
you get free caching, analytics, and Workers for ~zero effort. You also
gain the ability to point `api.loremacs.com` at a Worker later, which
is what the global-ranking feature in `AGENTS.md` would use.

---

## Post-setup checks

Regardless of host:

- `curl -I https://loremacs.com` should return `200` with an HTTPS cert
  matching the domain.
- `curl -I http://loremacs.com` should 301-redirect to HTTPS.
- `https://www.loremacs.com` should resolve to the same site (either
  directly or by redirect, depending on which host you chose as canonical).
- Open the site on mobile over cellular, not wifi, to confirm DNS
  resolves on a fresh network.

---

## Rollback / changing hosts later

Because this repo is just static files, switching hosts is cheap:

- **GitHub → Cloudflare**: delete the `CNAME` file at repo root,
  follow Option B.
- **Cloudflare → GitHub**: delete the Pages project, add `CNAME` file,
  swap DNS records back to the four GitHub IPs, turn on Pages in the
  repo settings.

DNS TTLs are the only thing that slows a switch. Keep them at
`Automatic` (usually 30 min) while iterating; raise them later if you
want.

---

## Gotchas worth remembering

- **Namecheap parking records.** The default `CNAME www → parkingpage`
  and any `URL Redirect` records silently break custom DNS. Delete them
  before adding your own.
- **`CNAME` file conflict.** If GitHub Pages adds a `CNAME` file and you
  later switch to Cloudflare without deleting it, GitHub keeps trying
  to serve under the old domain. Remove that file on host-switch.
- **`www` vs apex canonical.** Pick one, 301-redirect the other. Both
  hosts handle this for you if you only register one in their custom
  domain UI, but don't register both as "primary" in the same host.
- **TLS issuance timing.** Both hosts provision certs automatically but
  it needs DNS to resolve first. If HTTPS doesn't work in the first
  10 minutes, wait 30 more minutes before assuming anything's broken.
- **`user-scalable=no`.** The atlas disables browser pinch-zoom so its
  own pinch handler can work cleanly. Worth noting for accessibility
  reviews; if a visitor complains, we can revisit.

---

## My recommendation

Start with **GitHub Pages + Namecheap DNS** (Option A). It's zero
infrastructure and done in under ten minutes. If/when we add a
cross-visitor ranking API, move DNS to Cloudflare and switch the site
to Cloudflare Pages in one sitting — also under an hour.
