---
name: website-audit
description: Audit a live website from a URL and return a prioritised pass/warn/fail report with a concrete fix for every fail or warning. Use when the user asks for a website audit, launch checklist, or SEO/UX health check, or shares a URL to review. Fetch robots.txt, sitemap.xml, meta and Open Graph tags, response headers, and status codes; run Lighthouse if available; inspect the rest in the browser.
license: MIT
---

# Website audit

Given a URL, audit the live site and return a prioritised pass/warn/fail report. Every fail or warning needs a concrete fix, not a slogan.

British English throughout (colour, organised, towards, prioritised).

## Do not add

Do not include cookie banners, cookie consent, GDPR, or data-protection checks anywhere: not in the baseline, not in research additions, not in examples. The baseline is 18 items, not 19.

Do not invent extra checklist items. If you notice something outside these lists, mention it in a short "Out of scope" note only if it blocks the audit itself (for example the site is down).

## Verdicts

- **Pass:** you have evidence the criterion is met.
- **Warn:** present but incomplete, weak, unverified, or not applicable.
- **Fail:** missing, broken, or actively harmful.

Do not mark pass on a guess. If you could not verify it, mark warn and say what still needs a human look.

## Method

Automate first. Fall back to the browser only for what fetch cannot prove.

1. Normalise the URL (scheme, host, trailing slash). Record the final URL after redirects.
2. Fetch the homepage HTML and response headers.
3. Fetch `/robots.txt` and `/sitemap.xml` (and any sitemap index it points to).
4. Sample pages: homepage, one inner template from nav, privacy, terms, contact or footer address, a nonsense path for 404, and the post-form destination if you can find it. Cap at about eight URLs unless the user names more.
5. Parse each HTML document for title, meta description, canonical, `lang`, Open Graph, Twitter/X cards, favicon and apple-touch links, JSON-LD, image `alt`/`src`, analytics snippets, and form markup.
6. Record status codes for every URL you request, including assets you specifically check (Open Graph image, favicon, sitemap).
7. Inspect security-relevant headers on the homepage response.
8. Run Lighthouse if the environment has it (Chrome or Chromium plus the CLI). If it is missing, skip it and say so; do not fake scores.
9. In the browser, check above-the-fold CTA, sticky mobile CTA, breakpoints (375px, 768px, 1280px), loading and form error states, heading order, focus rings, and contrast you cannot prove from HTML alone.

Suggested fetches:

```bash
curl -sI "$URL"
curl -sL "$URL"
curl -sI "$ORIGIN/robots.txt"
curl -sL "$ORIGIN/robots.txt"
curl -sI "$ORIGIN/sitemap.xml"
curl -sL "$ORIGIN/sitemap.xml"
curl -sI "$ORIGIN/this-page-should-not-exist-9f3c"
```

Suggested Lighthouse, only when the binary works:

```bash
npx --yes lighthouse "$URL" --quiet \
  --chrome-flags="--headless --no-sandbox" \
  --only-categories=performance,accessibility,seo,best-practices \
  --output=json --output-path=stdout
```

If `lighthouse` or Chrome fails, continue with fetch plus manual inspection.

## Report order

1. Fails that block indexing, security, or conversion.
2. Remaining fails.
3. Warns that weaken sharing, trust, or accessibility.
4. Remaining warns.
5. Passes as a compact evidence list.

Each fail or warning row: item, verdict, evidence (URL, status, header, or snippet), then a concrete fix with an acceptable value or file to add.

## Baseline (18)

### 1. Custom 404 page

Request a path that cannot exist. Pass: a branded page with navigation or a way home, not a blank server/hosting default. Fail: default nginx/Cloudflare/platform text, or the homepage served instead. Fix: add a designed 404 template with the site nav and a link home.

### 2. Call-to-action above the fold

On the first screen at 1280px and 375px, one primary action must be visible without scrolling. Pass: a specific action (Book a call, Get a quote). Warn: a vague "Learn more" that only jumps down the page. Fail: no action until the footer. Fix: put one primary button in the hero, labelled as the next step.

### 3. Meta title

Every sampled HTML page needs a unique `<title>` (roughly 50–60 characters) that matches the page. Pass: present and distinct across pages. Warn: present but duplicated, truncated, or stuffed. Fail: missing on any indexable page. Fix: set a unique title per route in the site's metadata config.

### 4. Meta description

Every sampled HTML page needs a unique `meta name="description"` (roughly 140–160 characters) that matches the page. Pass: present and distinct across pages. Warn: present but duplicated, truncated, or stuffed. Fail: missing on any indexable page. Fix: set a unique description per route in the site's metadata config.

### 5. Open Graph image

`og:image` must be an absolute HTTPS URL that returns an image, ideally 1200×630. Pass: image loads and is large enough to share. Warn: present but relative, too small, or a screenshot of the UI. Fail: missing or 404. Fix: add a 1200×630 `og:image` (and `og:image:alt`) on each indexable template.

### 6. Favicon set

The document or `/favicon.ico` must provide a tab icon that loads. Pass: `rel="icon"` (and ideally a 32×32 or SVG) returns 200. Warn: only a default framework favicon. Fail: 404 or missing link. Fix: generate a real favicon set and link it in `<head>`.

### 7. robots.txt

`GET /robots.txt` must return `text/plain` and 200. Pass: it does not `Disallow: /` on production and it references the sitemap. Warn: exists but no sitemap line. Fail: 404, HTML error page, or site-wide disallow on a live host. Fix: add a production `robots.txt` that allows the public site and points at the sitemap.

### 8. sitemap.xml

`GET /sitemap.xml` (or the URL named in robots.txt) must be valid XML listing canonical indexable URLs. Pass: 200, parseable, URLs match the live host. Warn: exists but stale, missing key pages, or includes noindex/redirects. Fail: 404 or HTML. Fix: generate the sitemap from routes and submit that URL in Search Console.

### 9. Alt text on every image

Every content `<img>` needs an `alt` that describes the image. Decorative images may use `alt=""`. Pass: all sampled images have alt. Warn: generic alt ("image", filename). Fail: missing alt on content images. Fix: write specific alt for each content image; use empty alt only when the image is purely decorative.

### 10. Mobile breakpoints

The layout must work at 375px, 768px, and 1280px: no horizontal scroll, readable type, tappable controls. Pass: those three widths hold together. Warn: cramped type or overflow on one width. Fail: unusable on a phone-width viewport. Fix: add a viewport meta tag if missing, then fix the overflow with a real mobile layout, not desktop scaled down.

### 11. Sticky mobile call-to-action

At 375px, the primary action must stay reachable while scrolling (sticky bar, sticky header button, or repeated CTA). Pass: always tappable and not covering form fields. Warn: present but obscures content or the keyboard. Fail: CTA only at the bottom of a long page. Fix: add a sticky mobile bar for the primary action, with padding so it does not cover inputs.

### 12. Loading states

Async actions (forms, route changes, fetches) need a pending state. Pass: buttons disable or a skeleton/spinner appears. Warn: a spinner with no label. Fail: a second click double-submits, or the UI looks frozen. Fix: disable the submit control and show a labelled pending state until the response lands.

### 13. Form error states

Invalid submit must explain what failed, on the field. Pass: inline messages that say how to fix the value, and focus moves to the first error. Warn: a generic alert with no field. Fail: silent failure or a 500 page. Fix: validate on the server and the client; render the error next to the field and keep the entered values.

### 14. Thank-you page

Successful submit should land on a real URL (not only a modal) that confirms the action and says what happens next. Pass: dedicated thank-you route. Warn: inline "sent" with no next step. Fail: reload of the same form with no confirmation. Fix: redirect to `/thank-you` (or equivalent) with expected reply time and a next step.

### 15. Privacy policy page

A linked, crawlable privacy page must exist. Pass: footer or nav link returns 200 and talks about this site. Warn: a stub or a copied policy that names the wrong company. Fail: 404 or no link. Fix: publish `/privacy` (or equivalent) and link it in the footer.

### 16. Terms page

A linked terms (or terms of use) page must exist. Pass: 200 and clearly about this service. Warn: buried PDF only. Fail: missing. Fix: publish `/terms` and link it next to the privacy page.

### 17. Analytics installed

The live HTML or network log must include a real analytics snippet (for example GA4, Plausible, Fathom, PostHog, or Vercel Analytics). Pass: script present and firing on the homepage. Warn: snippet present but blocked or on staging only. Fail: none. Fix: install one analytics tool on production and verify a page view appears.

### 18. Real contact address

A geographic address, or a clearly named service area plus a real support email or phone, must appear (footer, contact page, or LocalBusiness markup that matches the page). Pass: a real street or registered office. Warn: email or phone only, or a PO Box when a visiting address exists. Fail: no way to reach a human, or an obvious placeholder. Fix: put the registered or visiting address in the footer and on the contact page.

## Research additions

Run these as well. Each line is the reason to keep it.

- **Canonical tags.** Without a self-referencing canonical, duplicate URLs split ranking signals.
- **HTTPS, HSTS, and security headers.** Mixed content and missing HSTS, `X-Content-Type-Options`, and a frame policy read as insecure to browsers and crawlers.
- **Core Web Vitals / performance.** LCP, CLS, and INP predict both ranking and whether the visitor waits.
- **Accessibility.** Contrast, heading order, form labels, and keyboard focus are the cheapest WCAG failures, and they block conversion.
- **Structured data.** JSON-LD that matches visible facts is how search engines earn rich results instead of a plain link.
- **Twitter/X cards.** Shares without `twitter:card` (and image) fall back to a plain link with no image.
- **`html lang` attribute.** Screen readers and translation tools need an explicit language on `<html>`.
- **Broken links.** Dead internal or outbound links waste crawl budget and trust; spot-check nav, footer, and sampled body links.
- **Image optimisation.** Oversized originals, missing dimensions, and no `srcset` or lazy-loading are the usual LCP killers.
- **Apple-touch icon and web manifest.** iOS home-screen and install prompts need more than `favicon.ico`.
- **404 returns a real 404 status.** A styled page that returns 200 is a soft-404 and can be indexed as a real page.

Pass/warn/fail these the same way as the baseline. Still give a concrete fix.

Canonical: one absolute `link rel="canonical"` pointing at the live preferred URL, not staging.

HTTPS/HSTS: the site redirects HTTP to HTTPS; `Strict-Transport-Security` is present on the HTTPS response. Also note `X-Content-Type-Options: nosniff`, `Referrer-Policy`, and either `Content-Security-Policy` `frame-ancestors` or `X-Frame-Options`.

Core Web Vitals: use Lighthouse when available. Treat LCP over 2.5s, CLS over 0.1, or INP over 200ms as fail or warn by band. Name the heaviest resource.

Accessibility: one H1, headings in order, every input has a label, visible `:focus-visible`, and text/background contrast that meets WCAG AA where you can measure it.

Structured data: parse JSON-LD; types must match the page (Organization, WebSite, Article, and so on) and must not invent reviews or ratings.

Twitter/X: `twitter:card` (`summary_large_image` when you have an image) plus title, description, and image.

`lang`: `<html lang="en-GB">` or the correct language-region.

Broken links: fail on any 4xx/5xx in nav, footer, or sampled in-body links.

Image optimisation: serve WebP or AVIF (or tightly compressed JPEG/PNG), not multi-megabyte originals; typical content images well under 200 KB, and the hero not an uncompressed PNG. Pass: those size and format bars plus `width`/`height` or aspect-ratio to stop CLS, `loading="lazy"` below the fold, and `srcset` where the same image serves large desktops. Warn: one oversized hero but the rest are fine. Fail: several megabyte images on first paint. Fix: export WebP/AVIF, cap width to the rendered size, and compress before upload.

Apple-touch and manifest: `apple-touch-icon` 180×180 that loads, plus a linked `manifest.webmanifest` with name, icons, and `start_url`.

404 status: the nonsense path must return HTTP 404 (or 410), not 200.

## Example report

```markdown
# Website audit: https://example.com

Final URL: https://www.example.com/
Sampled: homepage, /services, /privacy, /terms, /contact, /thank-you, /this-page-should-not-exist-9f3c
Method: curl for robots, sitemap, headers, and status; HTML parse for meta/OG; Lighthouse 12.x; browser at 375 / 768 / 1280
Lighthouse: performance 64, accessibility 81, SEO 92, best practices 73

## Summary

- Fail: 4
- Warn: 6
- Pass: 19

## Fails (fix first)

### 404 returns a real 404 status — fail
`/this-page-should-not-exist-9f3c` returns 200 and renders the homepage.
Fix: serve the custom 404 template with HTTP status 404 (or 410). In most hosts this is a `trailingSlash` / SPA-fallback misconfig; stop rewriting unknown paths to `index.html`.

### Open Graph image — fail
Homepage has no `og:image`. `/services` points at `https://www.example.com/og.png`, which returns 404.
Fix: add a 1200×630 PNG or JPG at `/og.png`, set `og:image` to the absolute HTTPS URL on every indexable template, and include `og:image:alt`.

### Sticky mobile call-to-action — fail
At 375px the only "Book a call" button is in the footer. After two screens of scroll it is gone.
Fix: add a sticky bottom bar on viewports below 768px with "Book a call" linking to the same target as the hero, and give the page `padding-bottom` so it does not cover form fields.

### Form error states — fail
Submitting `/contact` with an empty email reloads the page with no message. The network response is 200.
Fix: return the form with `aria-invalid="true"` on the email field, an error such as "Enter a valid email so we can reply", and move focus to that field. Keep the other values.

## Warns

### Meta title — warn
Homepage title is unique. `/services` and `/contact` both use "Example Ltd | Home".
Fix: give `/services` a title such as "Brand strategy and web builds | Example Ltd"; do the same for contact.

### Meta description — warn
Homepage description is unique. `/services` and `/contact` reuse the homepage description.
Fix: write a description for `/services` that names those services; do the same for contact.

### Image optimisation — warn
Hero `hero.png` is 1.8 MB PNG. Other images are WebP under 120 KB, with `width`/`height` and `loading="lazy"` below the fold.
Fix: export the hero as WebP at the rendered width (here 1280px) aiming under 200 KB, and keep PNG only if you still need lossless.

### HTTPS, HSTS, and security headers — warn
HTTPS redirects work. No `Strict-Transport-Security`. `X-Content-Type-Options` is missing.
Fix: send `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload` on HTTPS, and `X-Content-Type-Options: nosniff`.

### Accessibility — warn
Contact form email input has a placeholder but no `<label>`. Heading order on `/services` jumps from h1 to h3.
Fix: associate a visible label with the email input (`for`/`id`) and change the skipped heading to h2.

### Apple-touch icon and web manifest — warn
Favicon.ico loads. No `apple-touch-icon` and no web manifest.
Fix: add a 180×180 `apple-touch-icon.png` and a `site.webmanifest` with name, icons, and `start_url`, then link both from `<head>`.

## Passes

- Custom 404 page — branded 404 template exists (but see status-code fail).
- Call-to-action above the fold — "Book a call" in the hero at 375 and 1280.
- Favicon set — `/favicon.ico` 200, SVG icon linked.
- robots.txt — 200, allows `/`, points at `https://www.example.com/sitemap.xml`.
- sitemap.xml — 200, 12 canonical URLs on this host.
- Alt text on every image — 8/8 content images have specific alt.
- Mobile breakpoints — no overflow at 375 / 768 / 1280.
- Loading states — contact submit disables the button and shows "Sending".
- Thank-you page — `/thank-you` 200 after a valid submit.
- Privacy policy page — `/privacy` 200, linked in footer.
- Terms page — `/terms` 200, linked in footer.
- Analytics installed — Plausible script on homepage, page view in the network log.
- Real contact address — "14 Example Street, London, E1 6AN" in the footer and on `/contact`.
- Canonical tags — self-referencing absolute canonicals on sampled pages.
- Core Web Vitals / performance — LCP 2.1s, CLS 0.02, INP 140ms (Lighthouse).
- Structured data — Organization + WebSite JSON-LD matches the visible name and URL.
- Twitter/X cards — `summary_large_image` with title, description, and image on homepage.
- html lang attribute — `lang="en-GB"`.
- Broken links — nav, footer, and sampled body links returned 200.

## Out of scope

None.
```
