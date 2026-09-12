# Two MB Crawl Limit Monitor

Find pages on your site that are approaching or over a page-size budget,
measured from the exact bytes a crawler downloads, not the total weight of
images and scripts a browser loads on top of it.

## A number worth getting right

Google's own documentation ([Search Central, "Googlebot"](https://developers.google.com/search/docs/crawling-indexing/googlebot))
states it plainly: "Googlebot crawls the first 2MB of a supported file type,
and the first 64MB of a PDF file. [...] Once the cutoff limit is reached,
Googlebot stops the fetch." 2MB is not a conservative guess here — it is the
actual, documented hard limit for HTML and other text-based files, which is
exactly why it's this tool's default budget. PDFs get a separate, much larger
64MB allowance. The budget is fully adjustable in the popup before every scan,
and both your chosen budget and Google's documented 2MB figure are shown
together in every report.

## Features

| Feature | Details |
|---|---|
| No URL entry | Detects the site from whatever tab is active — click the icon and go |
| Full-site scan | One click crawls up to **5,000 pages** on the current site |
| Adjustable budget | Set any size threshold before scanning, in MB, remembered for next time |
| Size breakdown | Every page sorted into under budget, approaching budget (80%+), or over budget |
| Measured correctly | Sizes come from the raw HTTP response body, the same bytes a crawler reads, not total page weight including images and scripts |
| Rendered-mode fallback | Falls back to a hidden tab (real JS execution) for client-rendered (SPA) pages that return no links on a plain fetch, so the crawl can still discover pages beyond them |
| Anti-bot detection | Flags pages blocked by Cloudflare-style challenges separately, instead of silently miscounting them |
| Runs in the background | Closing the popup does not stop a scan in progress |
| Auto-saved progress | Saved to your browser every few seconds, so nothing is lost |
| Resume after closing the browser | Reopen the popup and resume, or export what was scanned so far |
| Per-category export | A download icon on each row exports just that category as `.xlsx` |
| Excel export | Export everything as a real `.xlsx` file |
| PDF report | A styled summary report (stat cards, a size-breakdown chart, and the largest pages found) as a real `.pdf` file |
| Privacy | Everything stays in your browser's local storage, nothing is sent to any server |
| Cost | 100% free, no account, no activation code |

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped `two-mb-crawl-limit-monitor` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Crawl**: a background script fetches pages starting from the homepage and
  follows internal links outward (breadth-first, same origin only), up to
  5,000 pages, 8 fetches in parallel. Fetches use the browser's own cookies
  (`credentials: 'include'`), so a site that's already let you past a
  Cloudflare check in this browser generally lets the crawl through too.
- **Measurement**: for every page that loads successfully, the raw response
  body is measured in bytes (UTF-8 encoded, matching what actually travels
  over HTTP) and compared against your configured budget. A page at or above
  the budget is "over"; a page at 80% or more of the budget is "approaching";
  everything else is "under."
- **robots.txt is respected before fetching, not after**: a URL disallowed by
  robots.txt is never fetched at all, the same way Googlebot would skip it,
  so a huge disallowed page never distorts the report or wastes scan budget.
- **Rendered-mode fallback**: if a page loads fine but yields zero `<a href>`
  links, that usually means the content is client-rendered (a React/Vue shell
  with an empty initial HTML payload). The scan opens that one URL in a
  hidden, inactive tab, lets its JS actually run, reads the rendered DOM only
  to keep discovering further links, and continues from there. The page's
  measured *size* always comes from the original plain fetch, since that is
  what a crawler actually downloads — capped at 500 pages per scan since
  rendering is much heavier than a plain fetch.
- **Anti-bot detection**: a `503` status paired with a `cloudflare` server
  header, or a "Just a moment..." challenge page in the response body, is
  flagged as blocked-by-anti-bot rather than folded into the size numbers.
- **Export**: one download icon per category exports just that category's
  URLs with their sizes; "export all" exports every measured page.

## Language

English only.
