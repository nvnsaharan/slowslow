---
name: performance-audit
description: Check real page performance — bundle size, image weight, font loading, Core Web Vitals — using the chrome-devtools MCP's performance trace/lighthouse tools. Use before launch and whenever a page feels slow or gets noticeably heavier.
---

# Performance Audit

For small consumer products, load speed is a product feature (first impression, bounce rate on mobile data), not an infra concern to defer.

## How

Use the `chrome-devtools` MCP: `performance_start_trace` / `performance_stop_trace` / `performance_analyze_insight`, and `lighthouse_audit` if available, on the real running page — not a guess from reading the code.

## Checklist

- **Core Web Vitals**: LCP, CLS, INP — get actual numbers from the trace, not estimates.
- **Bundle size** — is there an unexpectedly large JS bundle (check build output size); any unused/duplicate large dependency?
- **Images** — correctly sized/compressed for their display size, modern format (webp/avif) where easy, lazy-loaded below the fold.
- **Fonts** — `font-display: swap` or similar, not blocking first paint; not loading unused weights.
- **Third-party scripts** — anything render-blocking that doesn't need to be (analytics, widgets)?
- **Caching/compression** — static assets cached, responses gzipped/brotli'd (usually handled by the host, but verify for custom servers).

## Output format

```
PAGE: <url>
LCP: <value> | CLS: <value> | INP: <value>
BUNDLE SIZE: <value, notable large deps>
IMAGES: pass / issues (list oversized ones)
FONTS: pass / issues
THIRD-PARTY: pass / issues

TOP FIXES (ranked by impact/effort):
1. ...
```

## Rules

- Measure before recommending a fix — don't guess that "the bundle is probably too big."
- Don't over-invest here pre-launch for a product with near-zero traffic; flag genuine red flags (multi-second LCP, multi-MB bundle) and defer minor optimization until there's real usage to justify the time (CLAUDE.md §2/§19 — cost/effort discipline applies to your own time too).
