# Canonical Chain Tracer

Trace canonical tag chains across a site and catch loops before search
engines do.

## Why this matters

A canonical tag should point directly to the one true version of a page.
When it instead points to another page that itself points elsewhere, that's
a chain, and when a chain eventually points back to where it started,
that's a loop. Google's own guidance is to avoid chains entirely and
canonicalize straight to the final URL. Chains and loops make it unclear
which page search engines should actually index, and can quietly cost
otherwise healthy pages their rankings.

## Features

| Feature | Details |
|---|---|
| Passive check | Reads this page's own canonical tag straight from the live DOM, then traces the chain forward, instant, no scan needed |
| Full-site scan | One click maps canonical chains for up to **5,000 pages** on the current site |
| Thorough scan | Opens real background tabs so client-side JavaScript actually runs before reading links and canonical tags, catches JS-only navigation a raw fetch can't see (slower, up to 500 pages) |
| Sitemap discovery | Automatically checks sitemap.xml (and sitemap indexes) and folds in anything found there, since that's usually a plain file even on JS-heavy sites |
| Live hop breakdown | Clear, labeled categories (no canonical tag, self-canonical, canonicalized, chain, loop, outside scan) with a per-category export, updated live while the scan runs |
| Loop detection | Loops are called out separately, with the exact chain path (A → B → A) and a one-click export |
| Multiple canonical tags | Flags pages with more than one `<link rel="canonical">` tag, a common templating bug, with its own export |
| PDF report | A styled summary report (stat cards, a hop chart, and the full chain path for every flagged page) |
| Excel export | Export one hop level, everything, or just the loops, as a real `.xlsx` file |
| Language | English only |
| Privacy | Everything stays in your browser's local storage, nothing is sent to any server |

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped `canonical-chain-tracer` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Passive check**: a content script reads the current page's own
  `<link rel="canonical">` tag directly from the DOM (so it also catches
  canonical tags injected by client-side JavaScript). The background
  script then follows that tag forward, fetching each next hop, until the
  chain resolves to a final page, loops back on itself, or hits a safety
  limit of 10 hops.
- **Full-site scan**: a background script fetches pages starting from the
  homepage and follows internal links outward, same as any crawler. For
  every page it fetches, it also reads that page's canonical tag. Before
  crawling starts, it also checks sitemap.xml (and sitemap index files) and
  adds anything found there straight into the queue, since a sitemap is
  usually a plain file even on sites whose navigation is JavaScript-only.
  Once the crawl finishes, chains are resolved entirely from that
  already-crawled data, no extra requests needed, unless a canonical points
  to a URL the crawl never reached, which is reported as "outside crawled
  scope".
- **Thorough scan**: for sites where even the sitemap doesn't cover
  everything (some link structures only exist after JavaScript runs), this
  mode opens a few real background browser tabs, navigates them to each
  page, waits for the page to fully load, and reads the links and
  canonical tag from the actually-rendered DOM, same idea as a real crawler
  rendering JavaScript, just using Chrome itself to do the rendering. It's
  meaningfully slower per page than a raw fetch, so it has its own lower
  page cap and is opt-in rather than the default.

## Known limits (by design, to stay honest and fast)

- **5,000-page cap for a normal scan, 500 for a thorough scan.** Sites
  larger than that will only get a partial, representative map.
- **Same-origin only.** Subdomains and external canonical targets are
  reported as "outside crawled scope" rather than followed.
- **The normal full-site scan doesn't render JavaScript.** The passive
  single-page check and the thorough scan both read the live DOM and catch
  JS-injected canonical tags and JS-only links; the normal scan fetches raw
  HTML and cannot. Sitemap discovery helps close that gap without the
  speed cost of thorough scanning.
- **A scan pauses if the browser is fully closed.** Progress up to that
  point is saved, reopen the popup to resume or export what was found. Any
  background tabs opened by a thorough scan are closed automatically, even
  if the browser closed unexpectedly mid-scan.

## Project structure

```
canonical-chain-tracer/
├── manifest.json     Manifest V3 config
├── background.js     scan engine, chain/loop resolution, exports
├── content.js         reads this page's own canonical tag from the DOM
├── popup.html/js/css  toolbar popup UI
├── lib/xlsx.full.min.js    bundled SheetJS, used only for local export
├── lib/jspdf.umd.min.js    bundled jsPDF, used only for the local PDF report
└── icons/            16/48/128px icons
```

## Support

For bugs or questions, reach out via
[github.com/mmrahmanbappi](https://github.com/mmrahmanbappi).

## License

All rights reserved. See [LICENSE](LICENSE). This software is proprietary;
the source code may not be copied, modified, or redistributed without
permission.
