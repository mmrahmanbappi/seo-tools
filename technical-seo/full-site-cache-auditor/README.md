# Full Site Cache Auditor (Cache & CDN Edition)

Runs 54 real cache, CDN, and DNS checks against a site and reports each one
separately: Cache-Control directives, CDN and edge-cache detection,
provider-specific cache headers, DNS TTL behavior, conditional requests,
cache security risks, and more.

## Three things ruled out before writing any code for them

- **No Early Hints (HTTP 103) detection.** Informational HTTP responses are
  consumed by the browser's own network stack and never exposed to
  JavaScript's `fetch()` at all. This is a platform limitation, confirmed
  against the Fetch specification, not something a cleverer check could
  work around.
- **No Cloudflare Page Rules detection.** No response header reveals which
  rule fired for a request. There is nothing to observe from outside.
- **No deep Service Worker inspection** (Cache Storage API contents,
  service worker update staleness). Checking those needs executing script
  inside the live page rather than fetching from the background, a
  different and more invasive permission model than every other check
  here. Only presence-level checks (does a service worker or manifest
  exist) are included.

## A real signal worth naming

Cloudflare's Tiered Cache (their version of an origin shield) is genuinely
detectable: it sends a `cf-placement` header confirming whether a request
was served from an upper-tier cache. This was verified live against a real
Cloudflare-fronted site before being written, rather than assumed.

## The 54 checks

**HTTP Cache Headers & Directives**: Cache-Control Directive Auditor,
Expires Header Checker, ETag Presence & Format Checker, Last-Modified
Header Checker, Vary Header Auditor, Cache-Control + Cookie Conflict
Detector, Stale-While-Revalidate / Stale-If-Error Checker, Immutable
Directive Checker, Age Header Inspector, Legacy Pragma Header Checker

**CDN & Edge Cache Detection**: CDN Cache Status Header Checker, CDN
Provider Detector, Edge Cache Hit-Ratio Sampler, Multi-Layer CDN Detector

**Provider-Specific Cache Headers**: Fastly Surrogate-Control Checker,
Akamai Edge-Control Checker, Cloudflare Tiered Cache Detector, Vercel Edge
Cache Status Checker, Netlify Edge Cache Status Checker

**DNS Caching**: DNS TTL Auditor, DNS Resolution Consistency Sampler

**Static Asset Caching**: Static Asset Cache Duration Checker,
Cache-Busting Filename Checker, Font File Cache Checker, Image Cache
Checker, Third-Party Script Cache Checker

**HTML & Dynamic Page Cache Safety**: HTML Cache-Control Sanity Checker,
Full-Page Cache Detector, Personalized-Content Cache Leak Flag

**Compression & Vary Consistency**: Compression & Vary Consistency Checker

**Browser-Side Caching**: Service Worker Presence Checker, Web App
Manifest / PWA Cache Checker, Legacy AppCache Detector

**Cache Security Risks**: Unkeyed Header Cache-Poisoning Risk Flag, Web
Cache Deception Risk Flag, Cache Key Normalization Checker

**Cache Performance & Timing**: Cache Hit Latency Sampler, TTFB
Consistency Checker

**Platform-Specific Detection**: WordPress Cache Plugin Detector, Varnish
Detector, Nginx/Apache Cache Module Detector

**Compliance & Edge Cases**: 404/Error Page Cache Checker, Redirect Cache
Checker, API/JSON Endpoint Cache Leak Checker, robots.txt/sitemap Cache
Checker

**Resource Hints**: Preload/Prefetch Tag & Header Checker, Preconnect
Usage Checker, Client Hints + Vary Consistency Checker

**Range Requests & Streaming**: Range Request Support Checker, Partial
Content Cache Correctness Checker

**AMP / Signed Exchanges**: AMP Cache Eligibility Checker, Signed HTTP
Exchange (SXG) Detector

**Invalidation**: Cache-Tag / Surrogate-Key Header Checker, Exposed Purge
Endpoint Flag

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped
   `full-site-cache-auditor` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Gather phase**: the homepage is fetched twice (a moment apart, to
  observe Age header progression and cache hit-ratio behavior), plus
  robots.txt, sitemap.xml, and up to 15 internal pages linked from the
  homepage, all shared across every check rather than re-fetched per check.
- **Edge cache hit-ratio sampling is a real measurement**, not a guess: the
  same URL is fetched twice a little over a second apart, and the actual
  cache-status headers from both requests are compared.
- **A full report opens in its own tab** (not just the popup), with a
  sidebar organized by category and live progress while a scan is still
  running.
- **Export**: results export as a real `.xlsx` file or a styled PDF report.

## Language

English only.
