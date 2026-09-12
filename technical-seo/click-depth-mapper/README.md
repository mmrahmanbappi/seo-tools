# Click Depth Mapper

See how many clicks away any page is from the homepage, instantly while you
browse, or as a full-site scan.

## Features

| Feature | Details |
|---|---|
| Passive click-tracking | Records this page's click depth as you browse, instant, no scan needed |
| Full-site scan | One click maps up to **5,000 pages** on the current site |
| Thorough scan | Opens real background tabs so client-side JavaScript actually runs before reading links, catches JS-only navigation a raw fetch can't see (slower, up to 500 pages) |
| Sitemap discovery | Automatically checks sitemap.xml (and sitemap indexes) and folds in anything found there; pages only reachable this way are shown as "sitemap" (depth unknown) rather than guessed at |
| Time estimate | Scales with site size; the popup shows a live progress bar and ETA |
| Runs in the background | Closing the popup does not stop a scan in progress |
| Auto-saved progress | Saved to your browser every few seconds, so nothing is lost |
| Stop or resume anytime | Pause and resume, fully stop and discard, or start a fresh scan without losing track of what's running |
| Depth breakdown | Page counts per depth level, with an export icon on each row |
| Excel export | Export one depth level, or everything, as a real `.xlsx` file |
| PDF report | A styled summary report (stat cards, a depth chart, and the full page list) as a real `.pdf` file |
| Language | English only |
| Privacy | Everything stays in your browser's local storage, nothing is sent to any server |
| Cost | 100% free, no account, no activation code |

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped `click-depth-mapper` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Passive click-tracking**: a content script watches which links you click.
  When you click from a page whose depth is known (the homepage always starts
  at depth 0) to another page on the same site, the next page's depth is
  recorded instantly. No network requests, no waiting, and no on-page UI.
- **Full-site scan**: a background script fetches pages starting from the
  homepage and follows internal links outward (breadth-first), so the depth
  recorded for each page is the *shortest* path from the homepage. Before
  crawling starts, it also checks sitemap.xml (and sitemap index files) and
  adds anything found there straight into the queue. A page found only
  through the sitemap (never reached by following an actual link) is shown
  as depth "sitemap" rather than a guessed number, since its real click-depth
  genuinely isn't known; if a real click path to it turns up later in the
  same scan, its depth is upgraded automatically.
- **Thorough scan**: for sites where even the sitemap doesn't cover
  everything (some link structures only exist after JavaScript runs), this
  mode opens a few real background browser tabs, navigates them to each
  page, waits for the page to fully load, and reads the links from the
  actually-rendered DOM. It's meaningfully slower per page than a raw fetch,
  so it has its own lower page cap and is opt-in rather than the default.

## Known limits (by design, to stay honest and fast)

- **5,000-page cap for a normal scan, 500 for a thorough scan.** Sites
  larger than that will only get a partial, representative map.
- **Same-origin only.** Subdomains and external links are not followed.
- **The normal full-site scan doesn't render JavaScript.** The thorough scan
  and passive click-tracking are unaffected by this; sitemap discovery also
  helps close the gap without the speed cost of thorough scanning.
- **A scan pauses if the browser is fully closed.** Progress up to that
  point is saved, reopen the popup to resume or export what was found. Any
  background tabs opened by a thorough scan are closed automatically, even
  if the browser closed unexpectedly mid-scan.

## Project structure

```
click-depth-mapper/
├── manifest.json     Manifest V3 config
├── background.js     scan engine, click-depth storage, exports
├── content.js        passive click tracking (no on-page UI)
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
