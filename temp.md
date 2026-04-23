Let me read the current fetch logic first, then search for what's out there.Good — I can see the `type: "forum"` field is already set on Reddit posts, and the render even adds a FORUM badge. Now let me search properly for what's actually available.That's the key discovery. Google News RSS is a goldmine. Let me verify the exact URLs work for layoffs queries.The Google News RSS search is the big find here. Here's the updated, complete picture for the agent:

---

## The Real Answer: Google News RSS Is What You're Missing

**Google News has a free, no-auth, keyword-searchable RSS feed** that aggregates from thousands of publishers — Reuters, Bloomberg, WSJ, AP, NYT, all of it — in a single request. No API key, no proxy needed (it's CORS-friendly via proxy), and the query syntax supports booleans. This should be the primary source, not GDELT.

The URL pattern is:
```
https://news.google.com/rss/search?q=QUERY&hl=en-US&gl=US&ceid=US:en
```

---

## What to Tell the Agent

### 1. Replace GDELT with Google News RSS searches

GDELT is unreliable, rate-limited, and returns news from obscure domains. Google News aggregates Reuters, AP, Bloomberg, WSJ, CNBC, etc. — exactly what you want. Replace the 3 GDELT queries with these 4 Google News RSS searches, each fetched via `fetchViaProxy` → `parseRSSFull` (the enhanced parser already in the code that extracts the real source name and URL from Google News items):

```js
const GOOGLE_NEWS_QUERIES = [
    {
        label: "Google News — layoffs",
        url: "https://news.google.com/rss/search?q=layoffs+%22employees%22+OR+%22workers%22+OR+%22workforce%22&hl=en-US&gl=US&ceid=US:en",
        region: "us"
    },
    {
        label: "Google News — job cuts",
        url: "https://news.google.com/rss/search?q=%22job+cuts%22+OR+%22laid+off%22+OR+%22workforce+reduction%22&hl=en-US&gl=US&ceid=US:en",
        region: "us"
    },
    {
        label: "Google News — tech layoffs",
        url: "https://news.google.com/rss/search?q=tech+layoffs+2026+OR+%22cutting+jobs%22+OR+%22downsizing%22&hl=en-US&gl=US&ceid=US:en",
        region: "us"
    },
    {
        label: "Google News — EU layoffs",
        url: "https://news.google.com/rss/search?q=layoffs+redundancies+%22job+cuts%22&hl=en-GB&gl=GB&ceid=GB:en",
        region: "uk"
    },
];

for (const gq of GOOGLE_NEWS_QUERIES) {
    try {
        log(`  → ${gq.label}...`);
        const text = await fetchViaProxy(gq.url);
        const items = await parseRSSFull(text, gq.region, "en");
        const fresh = items.filter(i => LAYOFF_KEYWORDS.test(i.title) && i.date && new Date(i.date) >= cutoff);
        fresh.forEach(i => all.push({ ...i, type: "news" }));
        log(`  ✓ ${gq.label}: ${fresh.length} articles`, fresh.length ? "ok" : "warn");
    } catch(e) {
        log(`  ✗ ${gq.label} failed: ${e.message}`, "error");
    }
    await new Promise(r => setTimeout(r, 500)); // stagger to avoid proxy rate limits
}
```

Each query can return up to 100 results from Google's index. These will be real news articles from named publishers like Reuters, AP, Bloomberg — the `parseRSSFull` function already extracts the `<source>` tag from Google News items which gives you the actual publisher name (e.g. "Reuters", "Bloomberg") to display in the card's source field.

**Important note for the agent:** Google News RSS items have their real URL buried inside the `<description>` HTML as an `href` — `parseRSSFull` already handles this (line 697–698 in the current code). Make sure to use `parseRSSFull`, not `parseRSS`, for these feeds.

---

### 2. Keep TechCrunch, Crunchbase, BBC — they're fine

These are already in `LAYOFF_RSS_FEEDS` and producing real journalism. Keep them.

---

### 3. Remove both Reddit sources

`r/layoffs` and `r/cscareerquestions` are personal forum posts. Delete both fetch blocks. They add noise and no news value.

---

### 4. Keep HN but add the number-in-title filter

HN is fine when it links to real articles. Add this extra filter condition:

```js
// Only keep HN stories from news domains OR with a headcount number in the title
const isNewsArticle = /\b\d+[\s,]+(?:employees|workers|jobs|positions|staff|people)\b/i.test(h.title)
    || /\b(?:techcrunch|reuters|bloomberg|wsj|ft\.com|theverge|cnbc|bbc|fortune|forbes|arstechnica|venturebeat|crunchbase)\b/i.test(h.url || "");

.filter(h => h.title && h.url && LAYOFF_KEYWORDS.test(h.title) && new Date(h.created_at) >= cutoff && isNewsArticle)
```

---

### 5. Add these to `LAYOFF_RSS_FEEDS` while you're in there

These all produce clean journalism, no personal posts, and cover non-tech industries too:

```js
{ url: "https://feeds.a.dj.com/rss/RSSWorldNews.xml",           region: "global", label: "WSJ",          prefilter: true },
{ url: "https://www.theverge.com/rss/index.xml",                 region: "us",     label: "The Verge",    prefilter: true },
{ url: "https://arstechnica.com/feed/",                          region: "us",     label: "Ars Technica", prefilter: true },
{ url: "https://www.engadget.com/rss.xml",                       region: "us",     label: "Engadget",     prefilter: true },
```

---

### Summary table of sources after changes

| Source | Type | Quality | Keep/Add/Remove |
|---|---|---|---|
| Google News RSS (4 queries) | News aggregator | ⭐⭐⭐⭐⭐ Reuters/AP/Bloomberg | **ADD** (replaces GDELT) |
| TechCrunch | Dedicated tech news | ⭐⭐⭐⭐⭐ | Keep |
| Crunchbase News | Startup-focused journalism | ⭐⭐⭐⭐ | Keep |
| BBC Business | Journalism | ⭐⭐⭐⭐⭐ | Keep |
| Fortune | Journalism | ⭐⭐⭐⭐ | Keep |
| HN Algolia (filtered) | News links | ⭐⭐⭐ with filter | Keep + tighten filter |
| WSJ RSS | Journalism | ⭐⭐⭐⭐⭐ | **ADD** |
| The Verge | Tech journalism | ⭐⭐⭐⭐ | **ADD** |
| Ars Technica | Tech journalism | ⭐⭐⭐⭐ | **ADD** |
| GDELT | Unreliable aggregator | ⭐⭐ flaky | **REMOVE** |
| r/layoffs | Personal forum posts | ✗ | **REMOVE** |
| r/cscareerquestions | Personal forum posts | ✗ | **REMOVE** |

The Google News RSS queries alone should pull 200–400 real news articles per scan, all from named publishers, all about specific companies and headcounts.