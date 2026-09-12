# Full Site Security Auditor (CMS & Framework Edition)

Runs 47 independent security, DNS, header, and CMS/framework checks against
a site and reports each one separately: exposed config files, missing
security headers, email spoofing risk, hidden spam injection, outdated
libraries, cookie-consent load order, and more.

## How this is different from the rest of this line

Click Depth Mapper, Canonical Chain Tracer, Crawl Budget Waste Finder, Two MB
Crawl Limit Monitor, and Orphan Page Finder all crawl a site page by page.
This tool doesn't: it runs a fixed checklist against one site (a handful of
targeted fetches, a light sample of pages, and some DNS lookups), and reports
each check on its own. Think of it as a checklist runner, not a crawler.

## Three things this deliberately does not do

- **No Google Safe Browsing lookups.** There is no free, keyless way to query
  it; using it for real would require an API key from Google Cloud Console,
  which breaks the no-account pattern every tool in this line follows.
- **No Spamhaus DNSBL check, even though one was built and tested.** Querying
  Spamhaus through a public DNS resolver (the only keyless option available
  to a browser extension) returns a fixed anti-abuse response
  (`127.255.255.254`) regardless of which IP is being checked, confirmed by
  querying several unrelated IPs, including Cloudflare's and Google's own,
  and getting the identical answer every time. That is not a real blocklist
  entry. A version of this check that didn't catch this would have flagged
  every single site audited as blocklisted. It's left out rather than shipped
  broken.
- **No raw TLS handshake inspection.** No browser extension API exposes a
  site's certificate details directly. Certificate expiry instead comes from
  [crt.sh](https://crt.sh)'s public Certificate Transparency log search,
  which is a real, current, keyless source for "what certificate is this
  domain using right now."
- **No active exploitation of anything found.** Open-redirect parameters are
  flagged for manual review, never followed through. Nothing here attempts
  to log in, submit a form, or verify a vulnerability by triggering it.

## A false positive worth knowing about, because it shaped the design

Early testing flagged `github.com/phpmyadmin` as an exposed database admin
tool. It isn't: that URL is a real, legitimate GitHub organization page (for
the phpMyAdmin project itself), and it genuinely contains the word
"phpMyAdmin" in its title. The original check treated any page that merely
*mentioned* the word as a hit. Every check that verifies content now requires
a **structural** signature (phpMyAdmin's actual login field name, Swagger
UI's actual DOM markers, a real `JSON.parse` of `package.json` rather than a
regex guess at its keys), not just a word appearing somewhere on the page.

## Soft-404 sites

Some sites (typically client-side-routed single-page apps behind a static
file server with a naive catch-all) return HTTP 200 for every path,
including ones that don't exist. Without accounting for this, every "is this
sensitive file exposed" check would produce a false positive on those sites.
This tool probes one random, guaranteed-nonexistent path before running any
exposure check, and treats a bare 200 status as meaningless on a site that
behaves this way, requiring the actual expected content pattern to match
before calling anything exposed.

## The 47 checks

**Transport & Header Security**: Security Headers Auditor, Clickjacking
Protection Checker, Mixed Content Finder, HTTP to HTTPS Force-Redirect
Checker, HSTS Preload List Checker, TLS Certificate Expiry Warner, CDN
Detector, CORS Misconfiguration Checker, Cross-Origin Isolation Checker
(COOP/COEP), Cross-Origin Resource Policy Checker, Server/X-Powered-By Info
Leak Checker

**Exposed Files & Attack Surface**: Exposed Config/Env File Finder, Exposed
Backup File Finder, Directory Listing Checker, Source Map Exposure Checker,
Admin/Login Panel Discovery, Security.txt Presence Checker, Open-Redirect
Parameter Flag, Exposed Log File Finder, Exposed DB Admin Tool Finder,
Exposed API Docs/Swagger Finder, Composer/NPM Lock File Exposure Checker,
robots.txt Sensitive-Path Leak Checker, Subdomain Takeover Risk Finder

**CMS & Framework Fingerprinting**: CMS/Framework Detector, Version
Disclosure Finder, Outdated Library Finder, WordPress xmlrpc.php Exposure
Checker, WordPress User Enumeration Checker

**Malware & Blocklist Reputation**: Cryptojacking/Malicious-Script Pattern
Scanner, Hidden Spam Injection Finder, Cloaking Checker, Fake-Googlebot Trust
Checker

**Bot & Form Abuse**: Bad-Bot Blocking Checker, Form Spam-Protection Signal

**DNS & Email Security**: SPF/DKIM/DMARC Checker, DNSSEC Validation Checker,
CAA Record Checker, Nameserver Redundancy Checker, MX Record Sanity Checker,
BIMI Record Checker

**Front-end & Ad Integrity**: Insecure Form Action Checker, Third-Party
Script Inventory + SRI Flag, Ads.txt/Sellers.json Validator

**Compliance**: Privacy Policy Presence Checker, Cookie Consent Presence
Checker, Cookie Consent Load-Order Auditor

## Install

1. Unzip the folder somewhere on your computer.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top-right toggle).
4. Click **Load unpacked** and select the unzipped
   `full-site-security-auditor` folder.
5. The icon appears in your toolbar, pin it for easy access.

## How it works

- **Gather phase**: the homepage, robots.txt, and up to 15 internal pages
  linked from the homepage are fetched once and shared across every check,
  rather than each check re-fetching the same pages.
- **DKIM detection is best-effort**: DKIM selectors are arbitrary strings
  chosen by whoever configured the mail system, and are not discoverable
  without either being told or guessing. A list of common selectors used by
  major email providers is checked; a domain using a custom selector will
  correctly show "no DKIM found under common selectors" rather than a false
  "no DKIM configured" claim.
- **DMARC falls back to the organizational domain**: if a subdomain has no
  DMARC record of its own, the parent domain is checked too, since DMARC
  policy commonly lives there instead.
- **Subdomain takeover detection actually verifies, not just flags a CNAME**:
  finding a CNAME pointing at a takeover-prone service (GitHub Pages, Heroku,
  AWS S3, Shopify) is not treated as proof by itself. A follow-up request to
  that subdomain checks for the specific "unclaimed" error page each service
  shows, before calling it a real risk rather than an informational note.
- **Export**: results export as a real `.xlsx` file (every check, its
  category, status, and summary) or a styled PDF report with stat cards, a
  pass/warn/fail breakdown, and every check listed by category.

## Language

English only.
