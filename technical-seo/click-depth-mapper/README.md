# Click Depth Mapper

See how many clicks away any page is from the homepage, instantly while you
browse, or as a full-site scan.

## Features

| Feature | Details |
|---|---|
| Passive click-tracking | Records this page's click depth as you browse, instant, no scan needed |
| Full-site scan | One click maps up to **5,000 pages** on the current site |
| Time estimate | Scales with site size; the popup shows a live progress bar and ETA |
| Runs in the background | Closing the popup does not stop a scan in progress |
| Auto-saved progress | Saved to your browser every few seconds, so nothing is lost |
| Resume after closing the browser | If the browser closes mid-scan, reopen the popup and resume, or export what was scanned so far |
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
  recorded for each page is the *shortest* path from the homepage. Links are
  found by scanning each page's HTML for `<a href="...">` tags, so pages that
  only reveal their links after running JavaScript (some single-page apps)
  won't be fully mapped this way.

## Known limits (by design, to stay honest and fast)

- **5,000-page cap per scan.** Sites larger than that will only get a
  partial, representative map. Raising this would make scans take
  proportionally longer, and a browser extension can't crawl faster than the
  browser allows.
- **Same-origin only.** Subdomains and external links are not followed.
- **No JavaScript rendering.** Content added dynamically by client-side
  frameworks may not be found by the scan (passive click-tracking is
  unaffected by this, since it just watches your real clicks).
- **A scan pauses if the browser is fully closed.** Progress up to that
  point is saved, reopen the popup to resume or export what was found.

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
