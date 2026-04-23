Yes, that's exactly the right instinct — cast a wide net across many independent sources, then deduplicate aggressively. The current dedup logic is already there, it just needs more feeds coming in. Let me search for what's actually reliably available right now.Good — I have everything I need. Here's the updated, consolidated plan with the "wide net + dedup" strategy baked in:

---

## Updated Plan: Wide Net + Deduplicate

The agent's diagnosis is **partially right but incomplete**. Yes, rss2json's 10-item cap is a problem. Yes, GDELT is flaky under network load. But the root cause of "5 results" is actually a combination of three things hitting simultaneously:

1. GDELT times out when fired alongside 20+ other parallel requests
2. rss2json caps TechCrunch at 10 items, most of which dedup against GDELT
3. The `"us"` boot default on the cuts filter is still hardcoded and **hiding everything tagged `"global"`** (all Reddit, most HN)

The agent's fix only addressed #2 partially. Here's the complete corrected plan:

---

### 🔴 FIX 1 — Delete the duplicate `fetchHimalayas`
The second definition at line ~1107 overwrites the first. Delete lines 1107–1137 entirely. Keep only the proxy version.

---

### 🔴 FIX 2 — Change the boot default for cuts-country
**Line 1341, change:**
```js
document.getElementById("cuts-country").value = "us";
// → change to:
document.getElementById("cuts-country").value = "";
```
The `"us"` default is hiding all Reddit posts and most HN stories because they're tagged `"global"`. Show everything by default, let users filter down.

---

### 🔴 FIX 3 — Stop running `fetchLayoffs` and `fetchJobs` in parallel
**Line ~1314, change:**
```js
await Promise.allSettled([fetchLayoffs(), fetchJobs()]);
// → change to:
await fetchLayoffs();
await fetchJobs();
```
Firing 3 GDELT requests + 16 Greenhouse requests + 6 other job board fetches simultaneously at boot is what causes GDELT's 14s timeout to fire. Run them sequentially and GDELT will actually respond.

---

### 🔴 FIX 4 — Stagger the 3 GDELT queries inside `fetchLayoffs`
Instead of `Promise.allSettled(GDELT_QUERIES.map(...))`, run them with a short delay between each:
```js
for (const gq of GDELT_QUERIES) {
    await fetchGDELTQuery(gq); // pull the logic into a named function
    await new Promise(r => setTimeout(r, 600));
}
```
GDELT explicitly documents that burst parallel requests trigger rate limiting. Staggering by 600ms each costs ~1.2 seconds total but prevents dropping all 3 queries at once.

---

### 🟡 FIX 5 — Add more layoff RSS sources (wide net strategy)

All of these are fetchable via `fetchViaProxy` → `parseRSS`, same pattern already used for TechCrunch. Add them as a new `fetchLayoffFeeds()` function:

| Source | RSS URL | Notes |
|---|---|---|
| Crunchbase News | `https://news.crunchbase.com/sections/layoffs/feed/` | High-quality, startup-focused |
| LayoffsTracker | `https://layoffstracker.com/feed/` | Dedicated tracker, global |
| The Guardian Business | `https://www.theguardian.com/business/layoffs/rss` | UK/EU coverage |
| Reuters Business | `https://feeds.reuters.com/reuters/businessNews` | Filter with `LAYOFF_KEYWORDS` |
| BBC Business | `http://feeds.bbci.co.uk/news/business/rss.xml` | Filter with `LAYOFF_KEYWORDS` |
| Fortune | `https://fortune.com/section/finance/feed/` | Filter with `LAYOFF_KEYWORDS` |

All go through the same `fetchViaProxy` → `parseRSS` → `LAYOFF_KEYWORDS.test(title)` pipeline already in the code. Add them in a loop:
```js
const LAYOFF_RSS_FEEDS = [
    { url: "https://news.crunchbase.com/sections/layoffs/feed/", region: "us",     label: "Crunchbase" },
    { url: "https://layoffstracker.com/feed/",                   region: "global", label: "LayoffsTracker" },
    { url: "https://feeds.reuters.com/reuters/businessNews",     region: "global", label: "Reuters" },
    { url: "http://feeds.bbci.co.uk/news/business/rss.xml",      region: "uk",     label: "BBC Business" },
    { url: "https://fortune.com/section/finance/feed/",          region: "us",     label: "Fortune" },
];

for (const feed of LAYOFF_RSS_FEEDS) {
    try {
        const text = await fetchViaProxy(feed.url);
        const items = await parseRSS(text);
        const fresh = items.filter(i => 
            LAYOFF_KEYWORDS.test(i.title) && new Date(i.date) >= cutoff
        );
        fresh.forEach(i => all.push({ ...i, source: feed.label, region: feed.region, lang: "en" }));
        log(`  ✓ ${feed.label}: ${fresh.length} articles`, fresh.length ? "ok" : "warn");
    } catch(e) {
        log(`  ✗ ${feed.label} failed: ${e.message}`, "error");
    }
}
```

The existing dedup (URL fingerprint + title fingerprint) will handle duplicates across all these sources automatically. More sources = more total unique articles, not more noise.

---

### 🟡 FIX 6 — Improve the dedup fingerprint so more near-duplicates get caught

The current title fingerprint is: `title.toLowerCase().replace(/[^a-z0-9 ]/g, "").trim().slice(0, 80)`

This is good but won't catch "Amazon Cuts 500 Jobs" vs "Amazon to Cut 500 Jobs". Add word-based normalization:

```js
function titleFingerprint(title) {
    return title.toLowerCase()
        .replace(/\b(the|a|an|to|is|are|was|were|will|of|in|at|by|for|on|and|or)\b/g, "")
        .replace(/[^a-z0-9]/g, "")
        .trim()
        .slice(0, 60);
}
```

---

### 🟡 FIX 7 — Fix job dedup key, Arbeitnow type, silent Greenhouse errors

These are all one-liners from the previous plan, still unaddressed:

```js
// Dedup key (line ~856):
const key = j.id || `${j.title}|${j.company}|${j.location}`;

// Arbeitnow type (line ~968):
type: j.job_type || "full_time",

// Greenhouse silent catch (line ~1067):
} catch(e) { log(`  ✗ Greenhouse/${slug}: ${e.message}`, "warn"); }
```

---

### 🟠 FIX 8 — Fix extractSalary to handle £ and €

```js
const m = desc.match(/[\$£€][\d,]+(?:k)?(?:\s*[-–]\s*[\$£€]?[\d,]+(?:k)?)?(?:\s*\/\s*(?:hr|hour|yr|year|mo))?/i);
```

---

### Expected result after all fixes

| Source | Expected articles | Method |
|---|---|---|
| GDELT (3 queries, staggered) | 30–75 | Direct JSON |
| TechCrunch | 10–30 | Proxy → RSS |
| Crunchbase News | 10–20 | Proxy → RSS |
| LayoffsTracker | 5–15 | Proxy → RSS |
| Reuters (filtered) | 5–20 | Proxy → RSS |
| BBC Business (filtered) | 3–10 | Proxy → RSS |
| Fortune (filtered) | 3–10 | Proxy → RSS |
| HN Algolia | 10–30 | Direct JSON |
| r/layoffs | 20–50 | Direct JSON |
| r/cscareerquestions | 5–15 | Direct JSON |

**Total before dedup: ~100–275 articles. After dedup: expect 60–150 unique items** — a massive improvement from 5. The dedup handles the overlap. That's the whole point.