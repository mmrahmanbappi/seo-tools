# Crawl Budget Waste Finder

Find every faceted or parameter URL on a site that's quietly wasting crawl
budget — sort/filter combinations, tracking params, session IDs — and see
which of them actually have a real defense (canonical, noindex, robots.txt)
versus which ones a crawler is free to index as duplicate content.

## Features

| Feature | Details |
|---|---|
| No URL entry | Detects the site from whatever tab is active — click the icon and go |
| Full-site scan | One click crawls up to **5,000 pages** on the current site |
| Parameter classification | Buckets every parameterized URL as tracking, sort/filter, or pagination |
| Protection check | Cross-checks each one against its canonical tag, meta robots, and robots.txt |
| Waste score | One line: what percent of discovered URLs are unprotected duplicates |
| Rendered-mode fallback | Falls back to a hidden tab (real JS execution) for client-rendered (SPA) pages that return no links on a plain fetch |
| Anti-bot detection | Flags pages blocked by Cloudflare-style challenges separately, instead of silently miscounting them |
| Runs in the background | Closing the popup does not stop a scan in progress |
| Auto-saved progress | Saved to your browser every few seconds, so nothing is lost |
| Resume after closing the browser | Reopen the popup and resume, or export what was scanned so far |
| Per-category export | A download icon on each row exports just that category as `.xlsx` |
| Excel export | Export everything as a real `.xlsx` file |
| PDF report | A styled summary report (stat cards, a waste-breakdown chart, and the full URL list) as a real `.pdf` file |
| Privacy | Everything stays in your browser's local storage, nothing is sent to any server |
| Cost | 100% free, no account, no activation code |

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped `crawl-budget-waste-finder` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Crawl**: a background script fetches pages starting from the homepage and
  follows internal links outward (breadth-first, same origin only), up to
  5,000 pages, 8 fetches in parallel. Fetches use the browser's own cookies
  (`credentials: 'include'`), so a site that's already let you past a
  Cloudflare check in this browser generally lets the crawl through too.
- **Classification**: every URL's query string is checked against known
  patterns — `utm_*`, `gclid`, `fbclid` and similar are "tracking"; `color`,
  `size`, `sort`, `price_min` and similar are "sort / filter"; `page`, `p`,
  `offset` are "pagination". A URL can only land in one bucket — tracking
  outranks sort/filter, which outranks pagination.
- **Protection check**: for each parameterized URL, the page's
  `<link rel="canonical">`, `<meta name="robots">`, and the site's
  `robots.txt` (fetched once per scan) decide whether it's actually protected
  from indexing. Protected URLs — whatever their parameter type — are counted
  separately from unprotected ones, which is where the waste score comes from.
- **Rendered-mode fallback**: if a page loads fine but yields zero `<a href>`
  links, that usually means the content is client-rendered (a React/Vue shell
  with an empty initial HTML payload). The scan opens that one URL in a
  hidden, inactive tab, lets its JS actually run, reads the rendered DOM, and
  continues from there — capped at 500 pages per scan since it's much heavier
  than a plain fetch.
- **Anti-bot detection**: a `503` status paired with a `cloudflare` server
  header, or a "Just a moment..." challenge page in the response body, is
  flagged as blocked-by-anti-bot rather than folded into the waste numbers —
  the report tells you it couldn't see those pages instead of guessing.
- **Export**: one download icon per category exports just that category's
  URLs; "export all" exports every parameterized URL found, with its
  category, protection status, canonical target, and HTTP status.

## What this does *not* measure

This scores **discoverable crawl-waste surface area** — how much of the
site's linked URL space is parameterized and left indexable — not what
Googlebot has *actually* spent crawl budget on. Only server logs or Google
Search Console's crawl stats show real Googlebot behavior. Treat this as
"here's what a crawler could fall into," which is exactly the actionable
part: it tells you what to fix in `robots.txt` or your canonical tags.

## Language

English only.
