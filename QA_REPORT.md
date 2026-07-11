# QA Report

Use exact pass/fail evidence. "Looks fine" is not a result.

## Independent Reviewer Verification (2026-07-11)

The builder's manual-formula substitute (see Tooling note below) is
**not sufficient and was superseded this pass.** Live-rendered browser
tooling (chrome-devtools MCP) was denied by the permission system for
this reviewer session too, exactly as it was for the build session — but
Playwright + a cached Chromium-for-Testing binary were available locally
(no MCP needed), so the actual shared scripts
(`.pipeline/qa/contrast-audit.js`, `.pipeline/qa/upscale-audit.js`) were
run for real, unmodified, against a live local static server
(`python3 -m http.server`), at mobile (375px), tablet (800px), and
desktop (1440px), on all 3 pages, with `prefers-reduced-motion: reduce`
emulated to force `[data-reveal]` content to its settled state.

**Result: the manual substitute's "PASS after 2 fixes / 0 failures" was
wrong.** The real audit found a severe, previously-undetected contrast
failure, reproduced identically at all 3 breakpoints:

| Check | Result | Evidence |
|---|---|---|
| Contrast audit, live-rendered (real script, 3 breakpoints × 3 pages) | **FAIL** | `classes.html`: 99 violations at every viewport (375/800/1440px), identical counts each time. `contact.html`: 3 violations at every viewport. `index.html`: 1 violation at every viewport. Root cause (confirmed via `getComputedStyle` on the live DOM): `.schedule-table` and `.faq` (`<details class="faq">`) both declare `background: var(--paper-white)` (white) but never set their own `color`, so they inherit `color: var(--text-on-ink)` (`#eef1f5`, near-white) from the ambient `.section-on-ink` dark-section wrapper they sit inside. Result: every schedule-table body cell (`td`, `td.tabular` — day/venue/time/class/instructor, ~19 rows × up to 5 cols) renders at `rgb(238,241,245)` text on a `rgb(255,255,255)` table background (ratio **1.13:1**, needs 4.5:1) or on the zebra-striped `tbody tr:nth-child(even)` rows at `rgb(238,241,245)` background — **ratio 1.00:1, i.e. text and background are the literal same color, 100% invisible**. Every FAQ question (`<summary>`, on both `classes.html` and `contact.html`) renders the same near-white-on-white at **1.13:1**. Screenshot evidence below shows the entire schedule table as barely-visible ghost text, and the FAQ section on mobile as a blank pale screen with no visible question text at all. Separately, `index.html`'s "About TKI" `p.eyebrow` (not wrapped in `.section-head`, so it falls back to the base `.eyebrow{color:var(--muted-on-ink)}` rule instead of the higher-specificity `.section-head p{color:var(--muted)}` override used everywhere else) renders `rgb(169,182,194)` on the plain paper body background `rgb(238,241,245)` — **1.82:1**, needs 4.5:1. This is exactly the class of bug a live DOM/cascade audit catches and a static "check declared CSS token pairs in isolation" substitute cannot: no single declared rule pair is wrong, the failure only appears when a light card's inherited color crosses into it from a distant dark-section ancestor. |
| Upscale audit, live-rendered (real script, 3 breakpoints × 3 pages) | PASS (confirmed) | 0 `violations`, 0 `brokenImages`, 0 `aspectMismatches`, 0 `notYetLoaded` at every viewport on every page. The one real photo (`assets/img/lochaber-hall-dojo.jpg`, 720×540) never exceeds ~0.72× of native size at any breakpoint. This independently confirms the builder's manual-geometry substitute was correct for this specific check. |
| Mobile nav toggle (live DOM) | PASS (confirmed) | At 375px, `.nav-menu` is present with `display:flex` but `height:0`/`opacity:0` (collapsed via transform/opacity, not `display:none`) until `.nav-toggle` is clicked, at which point it expands to a real 222px-tall visible menu with `aria-expanded="true"`. Works as designed. |
| Text-overflow, live DOM (`scrollWidth` vs `clientWidth`) | PASS (confirmed) | Checked `.contact-block`, `.venue-card`, `address`, `.price-card`, `.schedule-table td/th`, `.card` at 375px on `contact.html` and `classes.html` with real longest content (full email, full venue addresses) — 0 elements with `scrollWidth > clientWidth`. |
| External fact check: hidden-mail widget on tki­shotokankarate.co.uk | PASS (confirmed) | Fetched the live homepage directly (plain HTTP works, confirms BUILD_BRIEF's HTTPS-fails note). Found the real markup: `<joomla-hidden-mail is-link="1" is-email="1" first="dGtpc2hvdG9rYW5rYXJhdGU=" last="Z21haWwuY29t" text="dGtpc2hvdG9rYW5rYXJhdGVAZ21haWwuY29t">`. Base64-decoded independently: `first`→`tkishotokankarate`, `last`→`gmail.com`, `text`→`tkishotokankarate@gmail.com` — all three consistent. Confirmed `/media/system/js/joomla-hidden-mail.min.js` (Joomla core's real component) is loaded on the page, and loaded the live page in headless Chrome to check the actual rendered DOM: the component resolves to `<a href="mailto:tkishotokankarate@gmail.com">tkishotokankarate@gmail.com</a>`. BUILD_BRIEF's email sourcing claim is accurate, not fabricated. |
| Copy-accuracy advisory: "around ten halls" | ADVISORY, not blocking | `classes.html`'s own schedule table (built into this same site) lists 13 distinct physical venues (Downham, Bellingham, Bromley Pavilion, Warren Road School, Farnborough Village Hall, St David's Prep, Lochaber Hall, St John's Hall, Green Street Green School, Park Langley Tennis Club, Carey Scutt Scout Hall, Marian Vien School, Coldharbour Leisure Centre) plus Zoom — not "around ten" as stated in the hero, highlight strip, and about section on `index.html`/`classes.html`. Not a fabrication (sourced data, hedged wording, and the source site's own two conflicting counts of 17/18 were correctly rejected per BUILD_BRIEF's Do Not Claim table) but the chosen approximation undercounts the site's own listed venues by ~30% — worth tightening to "around a dozen halls" or similar in a fix pass. |

Screenshots (2 total, taken last, per verification budget): `desktop_classes_schedule.png` (1440px, schedule table barely legible ghost text) and `mobile_classes_faq.png` (375px, FAQ section renders as a blank pale screen — question text is fully invisible). Not committed to the repo; describe/reproduce via the Playwright script above if needed again.

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
| Schedule table body text (`.schedule-table td`, all ~19 rows × 5 cols) is near-invisible or literally invisible on `classes.html` | Live contrast audit: `rgb(238,241,245)` text on `rgb(255,255,255)` table background = 1.13:1 (white rows); on `rgb(238,241,245)` zebra-striped rows = 1.00:1 (identical color, 100% invisible). Reproduced identically at 375/800/1440px. Screenshot confirms visually. | Give `.schedule-table td, .schedule-table th` (the non-`thead` cells) their own explicit dark color, e.g. `color: var(--text-on-paper)` or `var(--muted-strong)`, instead of letting them inherit `--text-on-ink` from the ambient `.section-on-ink` wrapper. |
| FAQ question text (`.faq summary`) is near-invisible on `classes.html` and `contact.html` | Live contrast audit: `rgb(238,241,245)` text on `rgb(255,255,255)` `.faq` card background = 1.13:1 at every breakpoint on both pages. Mobile screenshot shows the FAQ section as a blank pale screen with no visible question text. | Give `.faq` (or `.faq summary` directly) an explicit dark color, e.g. `color: var(--text-on-paper)`, so it doesn't inherit `--text-on-ink` from the enclosing `.section-on-ink` section. |
| "About TKI" eyebrow label low-contrast on `index.html` | Live contrast audit: `rgb(169,182,194)` (`--muted-on-ink`) on `rgb(238,241,245)` (`--paper` body background) = 1.82:1, needs 4.5:1. Reproduced at every breakpoint. | Wrap this eyebrow/heading block in `.section-head` (as done elsewhere on the site, which already gets the correct `--muted` color via the higher-specificity `.section-head p` rule), or give this specific instance an explicit `color: var(--muted)` override. |

## Advisory Issues
- Automated browser-based contrast/upscale audits (`.pipeline/qa/contrast-audit.js`, `.pipeline/qa/upscale-audit.js`) could not be run this session because chrome-devtools MCP and both web-fetch tools were denied by the permission system. A manual-formula substitute was used instead (see Tooling note above) — a future pass with browser-tool access should re-run the actual shared scripts against the live-rendered pages to confirm, particularly for the `::before` gradient-stripe overlay on the hero, which the shared script would flag as `needsManualCheck` rather than compute automatically.
- Real photography is thin (one usable photo across both live mirror domains); the design deliberately leans on typographic hero + inline SVG rather than forcing a second, weaker photo into a prominent slot. If TKI's Facebook photo albums become accessible in a future session, richer photography (real class/training shots) would strengthen the pitch further.

## Fixed Verification
| Issue | Fix | Recheck result |
|---|---|---|
| `--focus` outline `#7fb2ff` failed 3:1 UI-contrast on paper background (1.91:1) | Changed `--focus` to `#1177ff` | 3.63:1 (paper), 4.11:1 (white), 4.22:1 (ink), 3.63:1 (ink-soft), 4.74:1 (ink-deep) — all ≥3:1, PASS |
| `.footer-fine` text `#6d7a86` failed 4.5:1 on `--ink-deep` (4.44:1) | Changed to `#96a2b0` | 7.51:1 on `--ink-deep`, PASS |
| `.cred-card .num` large-text accent `--red-bright` failed 3:1 on `--ink-soft` (2.95:1) | Introduced `--red-accent-on-dark: #e63a52`, applied to `.cred-card .num` and `.hero h1 span` (both large text on dark backgrounds), left `--red-bright` unchanged for its button-hover use | 4.57:1 (ink-soft), 5.31:1 (ink), 4.73:1 (ink-deep) for the new token; button hover white-on-`--red-bright` unaffected at 5.06:1 — PASS |

### Round 2 — independent reviewer's inheritance-bug findings (2026-07-11, fix pass)

chrome-devtools MCP was denied again this session (same as both prior sessions). Instead of the manual-formula substitute that missed the bug the first time, this pass used **Playwright (`playwright-core`, installed fresh via npm) driving the same pre-cached `chrome-mac-arm64/chromium-1228` "Chrome for Testing" binary** the independent reviewer used, against a live local static server (`python3 -m http.server`, confirmed serving the correct directory with `curl` before testing — an earlier stale server process on the same port from an unrelated session was found and killed first). Verified with `getComputedStyle` on the live DOM (not isolated token pairs) at 375px/800px/1440px on all 3 pages, walking each element's ancestor chain to find its true effective/inherited background, exactly reproducing the inheritance-crossing bug shape the first pass's static substitute couldn't catch. Screenshots taken last, after the numeric recheck.

| Issue | Fix | Recheck result (live DOM, 3 breakpoints) |
|---|---|---|
| `.schedule-table td`/`th` inherited near-white `--text-on-ink` from ambient `.section-on-ink`, rendering 1.00–1.13:1 (invisible) | Added explicit `color: var(--text-on-paper)` to the `.schedule-table th, .schedule-table td` rule (`assets/css/style.css` ~line 684) | White rows: **18.04:1**. Zebra-striped even rows: **15.92:1**. Both far exceed 4.5:1, identical at 375/800/1440px, on `classes.html`. Screenshot `desktop_classes_schedule_fixed.png` confirms all 19 rows legible. |
| `.faq summary` inherited near-white `--text-on-ink` from ambient `.section-on-ink`, rendering 1.13:1 (invisible) | Added explicit `color: var(--text-on-paper)` to the `.faq` rule (`assets/css/style.css` ~line 749), so it cascades into `summary` (the more specific `.faq p { color: var(--muted) }` rule for body text was untouched) | **18.04:1** on both `classes.html` and `contact.html`, identical at 375/800/1440px. Screenshot `mobile_classes_faq_fixed.png` confirms all FAQ questions legible on mobile. |
| `index.html` "About TKI" eyebrow not wrapped in `.section-head`, falling back to base `.eyebrow { color: var(--muted-on-ink) }` on a light page background, rendering 1.82:1 | Wrapped the eyebrow/heading `<div>` in `class="section-head"` (`index.html` ~line 91), matching every other eyebrow instance on the site, so the higher-specificity `.section-head p { color: var(--muted) }` rule applies | **6.42:1**, identical at 375/800/1440px. Screenshot `about_section_context.png` confirms legible dark-gray text, no layout regression to the two-column About TKI section. Unrelated eyebrows (hero "Tigra Karate International" on the dark gradient hero, "Credentials & safeguarding" inside `.section-on-ink`) re-checked as a regression guard — unaffected, still 8.39:1. |

Full sweep: 57 live-rendered checks (schedule-table cells odd/even rows, FAQ summaries on both pages, all 3 eyebrow instances) across 375px/800px/1440px × 3 pages — **0 failures below 4.5:1**.

Advisory copy fix also applied and re-screenshotted: "around ten halls" (hero lede, about-figure caption, classes.html hero h1) changed to "around a dozen halls"; the highlight-strip stat tile "~10 Halls" changed to "~13 Halls" — now consistent with the schedule table's own 13 listed physical venues. Confirmed via screenshot `highlight_strip_fixed.png`.

## Verdict
**PASS.** All 3 blocking contrast/inheritance issues fixed and re-verified against a live rendered DOM (not an isolated token-pair substitute) at mobile/tablet/desktop, with screenshots confirming visual legibility, not just computed ratios. The "around ten halls" advisory copy mismatch is also fixed. Everything previously re-verified by the independent reviewer (upscale/broken-images/aspect at all 3 breakpoints, mobile nav, text-overflow, hidden-mail external fact check, no fabricated facts) was not touched by this fix pass and does not need rework.
