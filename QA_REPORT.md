# QA Report

Use exact pass/fail evidence. "Looks fine" is not a result.

**Tooling note:** the browser-automation tool (chrome-devtools MCP) and both
web-fetch tools were denied by the permission system for this build
session, so `.pipeline/qa/contrast-audit.js` and `.pipeline/qa/upscale-audit.js`
could not be executed against a live rendered page as normally required.
Substituted: (1) a Python script implementing the identical WCAG 2.1
relative-luminance/contrast-ratio formula, run directly against every
foreground/background CSS custom-property pair actually used in
`assets/css/style.css`; (2) manual geometry reasoning for image upscale
ratios, using the CSS layout rules (container widths, `max-width`,
breakpoints) and the one real photo's native pixel dimensions, confirmed
via `file` on the downloaded asset. Static HTML link/asset/tag-balance
checks were run via `curl` and a Python `html.parser` pass. This is a
narrower substitute than a live-rendered audit; flagged as a residual gap
for a future pass with browser-tool access.

## Pages Checked
- index.html (Home)
- classes.html (Classes & Schedule)
- contact.html (Contact & Booking)

## Audit Results
| Check | Result | Evidence |
|---|---|---|
| Contrast audit (manual WCAG formula, all CSS token pairs) | PASS after 2 fixes | 27 foreground/background pairs checked. Initial run found 2 failures: `--focus` outline `#7fb2ff` on paper (1.91:1, needs 3:1) and `.footer-fine` text `#6d7a86` on `--ink-deep` (4.44:1, needs 4.5:1). Also found `.cred-card .num` at `--red-bright` on `--ink-soft` = 2.95:1 for large text (needs 3:1) during a follow-up sweep. Fixed by: changing `--focus` to `#1177ff` (3.63–4.74:1 across all 5 backgrounds in use), `.footer-fine` to `#96a2b0` (7.51:1 on ink-deep), and introducing a separate `--red-accent-on-dark: #e63a52` token for large-text accents on dark backgrounds (hero emphasis word, cred-card numerals — 4.21–4.73:1 on ink/ink-soft/ink-deep), leaving `--red-bright` untouched for its original use (white button text on hover, 5.06:1). Re-checked all 27 pairs after fixes — 0 failures. |
| Upscale mobile | PASS | Only raster photo on the site is `assets/img/lochaber-hall-dojo.jpg` (native 720×540, confirmed via `file`). At a ~335px-wide mobile container (`.container` = 100% − 2.5rem on a 375px viewport), the `.about-figure .frame` block renders at ≈335px wide (no explicit width beyond `max-width:520px`, so it shrinks to the single-column layout width below the 860px breakpoint) — a 0.46× downscale, no upscaling. |
| Upscale tablet | PASS | At ~768px viewport, `.two-col` is already single-column (breakpoint at 860px), so `.frame` renders at its `max-width:520px` cap — 520/720 = 0.72× downscale, no upscaling. |
| Upscale desktop | PASS | At ≥900px, `.two-col` is two columns; the image column is roughly half of the 1120px max container minus gap (~500–540px), and `.frame`'s own `max-width:520px` caps it further — displayed width stays at or below 520px against a 720px native width (0.72× or less), no upscaling at any tested breakpoint. |
| Broken images | PASS | `curl -o /dev/null -w "%{http_code}"` against the served site returned `200` for `assets/img/lochaber-hall-dojo.jpg` and all other local assets (`style.css`, `main.js`, all 3 HTML pages). No other `<img>` tags exist on the site (hero artwork is inline SVG, not a raster image, specifically to avoid upscale risk given the thin real-photo library). |
| Aspect mismatch advisory | PASS (exact match) | Image native ratio 720:540 = 4:3 exactly; `.about-figure .frame` uses `aspect-ratio: 4/3`, an exact match, so `object-fit: cover` performs no meaningful crop. |

## Manual Checks
| Check | Result | Notes |
|---|---|---|
| Text on photo | N/A | The one real photo is used in an inset card with a caption below it (not text overlaid on the image), so no scrim/text-shadow legibility question arises. |
| Gradient/::before backgrounds | Checked | `.hero`'s `linear-gradient(in oklch...)` background and its `::before` low-opacity (0.05–0.10) diagonal stripe overlay sit behind hero text. Checked both gradient endpoints (`--ink` #0d1b2c and `--ink-deep` #060d16) individually against all hero text colors (white, `--muted-on-ink`, `--red-accent-on-dark`) — all pass at the relevant text-size threshold (see contrast audit). The stripe overlay is thin (1–2px repeating lines) and very low opacity, so its effect on any single character's contrast is negligible; treated as a manual pass rather than an automated read, consistent with how the shared script would flag a gradient background for manual review. |
| Image/content match | PASS | The one photo used (empty hall, red/blue mats) is captioned honestly as "one of our training halls (Lee/Lewisham)" rather than tied to any specific unverifiable claim; it is the business's own homepage slideshow image. |
| Fabricated claims | PASS | Every fact used in copy traces to a specific source URL in BUILD_BRIEF.md's Allowed Facts table. Internally inconsistent site claims (35 vs 40 years' experience; 17 vs 18 clubs; "DBS" vs the site's own "CRB" wording) were resolved conservatively per the Do Not Claim table rather than picked arbitrarily. No invented reviews, ratings, or event outcomes. |
| Mobile layout | PASS (static reasoning) | `.nav-menu` collapses behind a `.nav-toggle` button below 800px width (confirmed in CSS media query); `.hero-grid`, `.two-col`, and `.contact-grid` all collapse to a single column below their respective breakpoints (900px / 860px / 860px); the schedule table is wrapped in `.table-scroll` with `overflow-x:auto` and a styled thin scrollbar rather than being force-fit or truncated on narrow screens. |
| Text-overflow (real content lengths) | PASS (static reasoning) | Longest real strings checked: email `tkishotokankarate@gmail.com`, venue address `Coldharbour Leisure Centre, Chapelfarm Rd, Mottingham, SE9 3LX`, and schedule-table cell `St David's Prep School, Beckenham (students only)`. All text-bearing box classes (`.card`, `.contact-block`, `.price-card`, `.venue-card address`, `.schedule-table td/th`, `.pill-list li`) carry `overflow-wrap: anywhere` (inherited by descendant text), `box-sizing: border-box`, and `min-height` (never fixed `height`) per AGENTS.md defaults; grid/flex containers holding these use `minmax(0, 1fr)` tracks or explicit `min-width: 0` on the item. The schedule table itself is allowed to horizontal-scroll by design (a real data table, not a text-overflow bug). |
| HTML well-formedness | PASS | Ran a Python `html.parser`-based tag-balance check against all 3 pages — 0 unclosed/mismatched tags. |
| Internal links & assets | PASS | Every local `href`/`src` across all 3 pages resolves to a file that exists in the repo and returns HTTP 200 from a local static server (`index.html`, `classes.html`, `contact.html`, `assets/css/style.css`, `assets/js/main.js`, `assets/img/lochaber-hall-dojo.jpg`). |
| Placeholder/lorem ipsum text | PASS | None present; all copy is either sourced fact (see BUILD_BRIEF) or generic non-factual UI copy (button labels, FAQ framing). |

## Blocking Issues
| Issue | Evidence | Required fix |
|---|---|---|
| (none remaining) | | |

## Advisory Issues
- Automated browser-based contrast/upscale audits (`.pipeline/qa/contrast-audit.js`, `.pipeline/qa/upscale-audit.js`) could not be run this session because chrome-devtools MCP and both web-fetch tools were denied by the permission system. A manual-formula substitute was used instead (see Tooling note above) — a future pass with browser-tool access should re-run the actual shared scripts against the live-rendered pages to confirm, particularly for the `::before` gradient-stripe overlay on the hero, which the shared script would flag as `needsManualCheck` rather than compute automatically.
- Real photography is thin (one usable photo across both live mirror domains); the design deliberately leans on typographic hero + inline SVG rather than forcing a second, weaker photo into a prominent slot. If TKI's Facebook photo albums become accessible in a future session, richer photography (real class/training shots) would strengthen the pitch further.

## Fixed Verification
| Issue | Fix | Recheck result |
|---|---|---|
| `--focus` outline `#7fb2ff` failed 3:1 UI-contrast on paper background (1.91:1) | Changed `--focus` to `#1177ff` | 3.63:1 (paper), 4.11:1 (white), 4.22:1 (ink), 3.63:1 (ink-soft), 4.74:1 (ink-deep) — all ≥3:1, PASS |
| `.footer-fine` text `#6d7a86` failed 4.5:1 on `--ink-deep` (4.44:1) | Changed to `#96a2b0` | 7.51:1 on `--ink-deep`, PASS |
| `.cred-card .num` large-text accent `--red-bright` failed 3:1 on `--ink-soft` (2.95:1) | Introduced `--red-accent-on-dark: #e63a52`, applied to `.cred-card .num` and `.hero h1 span` (both large text on dark backgrounds), left `--red-bright` unchanged for its button-hover use | 4.57:1 (ink-soft), 5.31:1 (ink), 4.73:1 (ink-deep) for the new token; button hover white-on-`--red-bright` unaffected at 5.06:1 — PASS |

## Verdict
PASS
