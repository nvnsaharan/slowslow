---
name: qa-browser
description: Actually test a web app in a real browser instead of assuming it works — use the chrome-devtools MCP to click through flows, check mobile width, bad inputs, and console errors. Use after building or changing any user-facing web flow, before saying "this works" or before launch-readiness.
---

# QA Browser

Never say "this should work" for a web UI change without actually driving it in a browser. Use the `chrome-devtools` MCP tools (`navigate_page`, `click`, `fill`, `take_snapshot`, `take_screenshot`, `list_console_messages`, `emulate`, `resize_page`) to verify for real.

## Workflow

1. **Build** the change.
2. **Run** the app locally (or navigate to the deployed URL).
3. **Happy path** — walk the primary user flow start to finish exactly as a real user would (click/fill, don't just read the code).
4. **Desktop width** — take a screenshot, check layout.
5. **Mobile width** — `emulate`/`resize_page` to ~390px, repeat the flow. Most bugs live here.
6. **Keyboard** — tab through interactive elements, check focus order and that nothing is a mouse-only trap.
7. **Bad/edge inputs** — empty fields, very long strings, special characters, double-submit, back button mid-flow.
8. **Refresh mid-flow** — does state survive or fail gracefully?
9. **Console check** — `list_console_messages` / `get_network_request` for JS errors and failed requests; a silent console error is still a bug.
10. **Fix, then re-test the same flow** — don't assume the fix worked, re-run steps 3-9 for anything touched.

## Output format

```
FLOW TESTED: ...
DESKTOP: pass / issues found
MOBILE (390px): pass / issues found
KEYBOARD NAV: pass / issues found
BAD INPUT HANDLING: pass / issues found
CONSOLE ERRORS: none / list them
REFRESH/BACK BEHAVIOR: pass / issues found

BUGS FOUND:
1. ...

FIXED: ...
RE-TESTED: yes/no
```

## Rules

- "I read the code and it looks right" is not QA. Drive the actual page.
- Mobile is not optional — most of these products are consumer-facing; a broken mobile layout is a P0, not polish.
- Report bugs found even if you also fixed them — don't silently patch and stay quiet (CLAUDE.md §31: Observation vs Recommendation vs Action — say what you found and what you changed).
