# Orphan Page Finder

Find pages your sitemap lists that no internal link on your site actually
leads to (genuine orphan pages), plus pages that are only weakly linked
internally, and pages your crawl finds that aren't in your sitemap at all.

## Why this needs a different method than the rest of this line

Click Depth Mapper, Canonical Chain Tracer, Crawl Budget Waste Finder, and
Two MB Crawl Limit Monitor all discover pages the same way: by following
internal links outward from your homepage. That approach can **never** find
a true orphan page, because an orphan is defined by having zero internal
links pointing to it. Link-following alone will never reach it.

So this tool works in two passes:

1. **Read your sitemap.xml** (following `Sitemap:` lines in robots.txt, or
   the usual default paths if there are none; recursing into sitemap
   indexes; decompressing `.xml.gz` files). This gives a list of pages that
   exist, independent of how they're linked.
2. **Crawl the site normally** (breadth-first from the homepage, following
   `<a href>` links, same engine as the rest of this line), counting how
   many distinct pages link to each URL along the way.

A sitemap URL the crawl never reaches is an **orphan**. A URL the crawl
reaches, but only from one or two places, is **weakly linked**, not
orphaned, but at risk. A URL the crawl reaches that **isn't** in the sitemap
is a different, separate problem worth knowing about too.

## The one thing this can't see

A page that is in **neither** your sitemap **nor** linked from anywhere
internally is invisible to this tool, or any tool that only has crawl
access. Finding that requires Google Search Console's indexed-pages report
or analytics data, which means logging into a Google account, which this
tool (like the rest of this line) deliberately doesn't ask for. If you need
that last mile of certainty, cross-check Search Console's page indexing
report against this tool's results.

## An accuracy safeguard worth knowing about

If a site has more pages than the 5,000-page crawl cap, the crawl can run
out of budget before reaching every page a sitemap URL might eventually connect
to. Rather than risk calling those pages "orphaned" when the truth is just
"not yet reached," this tool marks them **unconfirmed** instead, and says so
plainly in the popup and the PDF report. The orphan count you see is never
inflated by a crawl that simply ran out of room.

## Features

| Feature | Details |
|---|---|
| No URL entry | Detects the site from whatever tab is active, click the icon and go |
| Sitemap-aware | Reads robots.txt for `Sitemap:` directives, falls back to default paths, follows sitemap indexes, decompresses gzipped sitemaps |
| Full-site scan | One click crawls up to **5,000 pages** on the current site |
| Adjustable threshold | Set how many internal links count as "well linked" before scanning, remembered for next time |
| Four-way breakdown | Orphan, weakly linked, well linked, and not-in-sitemap, each with a real count |
| Honest about crawl limits | A capped crawl reports "unconfirmed," never a false "orphan" |
| Rendered-mode fallback | Falls back to a hidden tab (real JS execution) for client-rendered (SPA) pages that return no links on a plain fetch |
| Anti-bot detection | Flags pages blocked by Cloudflare-style challenges separately |
| Runs in the background | Closing the popup does not stop a scan in progress |
| Auto-saved progress | Saved to your browser every few seconds, so nothing is lost |
| Resume after closing the browser | Reopen the popup and resume, or export what was scanned so far |
| Per-category export | A download icon on each row exports just that category as `.xlsx` |
| Excel export | Export everything as a real `.xlsx` file |
| PDF report | A styled summary (stat cards, a breakdown chart, and every orphan/weak page listed) as a real `.pdf` file |
| Privacy | Everything stays in your browser's local storage, nothing is sent to any server |
| Cost | 100% free, no account, no activation code |

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped `orphan-page-finder` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Sitemap phase**: robots.txt is checked first for `Sitemap:` lines; if
  none are found, `/sitemap.xml` and `/sitemap_index.xml` are tried. Sitemap
  index files are followed recursively (up to 200 sitemap files, 20,000
  page URLs), and `.xml.gz` files are decompressed in-browser before parsing.
  Every `<loc>` URL is normalized (fragment stripped) and restricted to the
  same origin.
- **Crawl phase**: the same breadth-first, same-origin crawler used across
  this line, with one addition: every internal link found on every page
  increments an inlink counter for its target URL, regardless of whether
  that URL was already known. This is what "weakly linked" and "orphan" are
  actually computed from.
- **robots.txt is respected before fetching, not after**: a disallowed URL
  is never fetched, the same way Googlebot would skip it. But if it was
  reached via an internal link before being skipped, it still counts as
  linked, not orphaned, since robots.txt not fetching it and no link
  existing to it are two different problems.
- **Classification**, run once the crawl finishes:
  - **orphan**: in the sitemap, never reached by the crawl, and the crawl
    ran out of queue rather than running out of budget (see the accuracy
    safeguard above).
  - **weakly linked**: reached by the crawl, but with fewer inlinks than
    your threshold.
  - **well linked**: reached by the crawl, at or above your threshold.
  - **not in sitemap**: reached by the crawl, but absent from the sitemap.
- **Export**: one download icon per category exports just that category;
  "export all" exports every classified page; the PDF report lists every
  orphan and weakly-linked page with its inlink count.

## Language

English only.
