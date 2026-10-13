---
name: seo-launch
description: Make a web page discoverable and share-ready — title/meta/OG tags, sitemap, structured data, alt text, canonical URL. Use before launching any new public page, and whenever a landing/marketing page changes.
---

# SEO & Launch Tags

Treat this as a default engineering step for any public-facing page, not an afterthought after launch.

## Checklist

- `<title>` — specific, human, not generic ("BestVibeCoder — Vibe Coder Test" not "Home").
- Meta description — one sentence, written for a human clicking a search result or share preview, not keyword-stuffed.
- Canonical URL set.
- Open Graph tags: `og:title`, `og:description`, `og:image` (real image, correct aspect ratio ~1200x630), `og:url`, `og:type`.
- Twitter card tags (`summary_large_image` if there's a real image).
- Favicon present.
- `robots.txt` — not accidentally blocking the whole site.
- `sitemap.xml` — present for multi-page sites.
- Structured data (JSON-LD) where it's genuinely applicable (product, article, FAQ) — skip if there's nothing to mark up.
- Semantic heading hierarchy (`h1` once per page, no skipped levels).
- Alt text on meaningful images (skip decorative ones).
- Custom 404 page.
- Mobile-friendly (defer detailed check to `qa-browser`, but confirm meta viewport tag exists).

## After tags are in place

- Actually load the OG preview (e.g. via a card-preview tool or by checking the rendered `<head>`) — don't just assume the tags render correctly.
- Check for broken links on the page being launched.

## Output format

```
PAGE: <url/route>
TITLE: ✓/✗ <value>
META DESCRIPTION: ✓/✗ <value>
OG TAGS: ✓/✗ (image present? correct size?)
TWITTER CARD: ✓/✗
CANONICAL: ✓/✗
ROBOTS/SITEMAP: ✓/✗
STRUCTURED DATA: ✓/✗ / n/a
ALT TEXT: ✓/✗
404 PAGE: ✓/✗

MISSING/BROKEN:
1. ...
```

## Rules

- Don't add structured data types that don't actually apply — empty/wrong schema is worse than none.
- The OG image is the single highest-leverage item for a product meant to be shared (BestVibeCoder's certificate, Morrow's screenshots) — verify it renders, don't just check the tag exists.
