# Build Brief

Keep this compact. Add only sourced facts and assets actually used or deliberately rejected.

## Contact
- Email: tkishotokankarate@gmail.com
- Email source URL: http://tkishotokankarate.co.uk/ (homepage "Email us" hidden-mail widget, decodes to tkishotokankarate@gmail.com) — re-confirmed on http://tkishotokankarate.co.uk/index.php/page ("On line Zoom classes" article, same address, same base64 encoding). Mirror domain http://tkikarate.co.uk/ (same Joomla install/content) also confirmed reachable. Note: HTTPS fails TLS handshake on both domains (curl: `curl: (35) ... SSL routines`), matching LEADS.md. Plain HTTP works (curl -o, HTTP 200) on both.
- Rechecked date: 2026-07-11
- Phone: 07956 372474 (mobile, Sensei Tom Tennick — stated on site's own Contact us page and Home page). Site's Contact us page also lists a landline "Head Office" number, 020 8325 8072, for Tom Tennick, and a separate number 020 8658 2629 for Sensei Sharon GoodwinDoash (Bromley clubs). LEADS.md logs 07956 372474 only — used as primary.
- Address: No single fixed studio/shop address — TKI trains across multiple hired halls in South East London & Kent. The logged LEADS.md address (Lochaber Hall, 1 Manor Lane Terrace, Lewisham SE13 5QL) matches one real, currently-active venue: the site's own Find a Club and Adult & Teenage Knife Self-Defence pages independently describe "Lochaber Hall Community Centre, Lochaber Rd (off Lee High Rd), Lewisham SE13 5QL" as a Tuesday & Thursday venue taught personally by Tom Tennick. Treated as one of several real venues, not the sole address — see Page Plan/multi-location note below.

## Business State Check
- Status: still open at logged address (one of several active venues) — actively trading, multi-venue
- Checked sources:
  - http://tkishotokankarate.co.uk/index.php/holidaysdaysclosed — "Holidays and days closed 2026", real School Term Dates April–July 2026 and Sept–Dec 2026 for Warren Rd School, Green St Green, Marion Vine venues.
  - http://tkishotokankarate.co.uk/index.php/club-price-list — pricing "available from September 2025" (current, forward-dated at time of check).
  - http://tkishotokankarate.co.uk/index.php/up-to-date-news — full current weekly class timetable across ~10 halls (Downham, Bellingham, Bromley, Orpington/Warren Rd, Farnborough, home Zoom, Beckenham x3 venues, Lee/Lewisham Lochaber Hall, Green Street Green), each with named instructor (Tom Tennick / Sharon GoodwinDoash / Nathan Tennick) and times.
  - http://tkishotokankarate.co.uk/index.php/findaclub-sp-1981493896 — "We currently have eighteen clubs running seven days a week", with named venues/addresses/times, matches up-to-date-news.
  - Domain check via curl: tkishotokankarate.co.uk (HTTP 200 plain HTTP, HTTPS fails), tkikarate.co.uk (HTTP 200 plain HTTP, identical mirrored content, HTTPS fails), tigrakarate.co.uk (HTTP 200 but is a bare STRATO "domain reserved" parked page — the dead domain LEADS.md refers to).
- Notes: This is a single instructor-led karate association (Tigra Karate International / TKI) running classes across many rented community halls/leisure centres/school halls, not a franchise of independent branches. Per AGENTS.md multi-location guidance, the honest approach is a full schedule/venues listing rather than implying one flagship studio — done via a schedule table on the Classes & Schedule page.
- Build decision: proceed

## Page Plan
- Scope: 3-page default
- Pages: index.html (Home), classes.html (Classes & Schedule — doubles as Services/Offer + venue/pricing listing), contact.html (Contact/Booking)
- Reason for any extra page: none — 3-page default used as-is; the venue list is handled as a table/section within Classes & Schedule rather than a separate "Locations" page, since it is one class-timetable dataset, not distinct branch pages.

## Pitch Hook
- Verified observation: The business's own site (tkishotokankarate.co.uk) is a ~2010s-era Joomla install that fails HTTPS entirely (browsers show a security warning/connection error before the page loads at all) and its internal main-menu links point to a broken `/tki/index.php/...` path that 500-errors — most visitors clicking "Contact us" from the homepage nav actually hit a server error, not the contact page (root-relative `/index.php/...` URLs work, but the visible nav links do not). Real content (2026 term dates, current pricing, full class timetable, 40 years of instructor experience, safeguarding credentials) is genuinely strong but buried in an outdated, half-broken template.
- Source URL: http://tkishotokankarate.co.uk/ (homepage), nav links inspected directly via curl; contact-us via nav path `/tki/index.php/contact-us` returns HTTP 500, confirmed same session.

## Allowed Facts
| Fact | Source URL | Used where |
|---|---|---|
| Business name "TKI Shotokan Karate" / "Tigra Karate International (TKI)" | http://tkishotokankarate.co.uk/ | All pages, header/footer |
| Chief Instructor: Sensei Tom Tennick, 7th Dan, founder of TKI, Chief examiner, Lead safeguarding officer, Registration officer | http://tkishotokankarate.co.uk/index.php/instructors | Home, Contact |
| Tom Tennick: over 35 years teaching experience (per About page and homepage banner graphic) | http://tkishotokankarate.co.uk/index.php/abouttki-sp-1618275448 | Home |
| Tom Tennick is a FEKO Qualified Instructor Assessor | http://tkishotokankarate.co.uk/images/stories/instructor%20qualifications%20new.jpg (image text, "Instructor Qualifications" page) | Home |
| All Black Belt instructors: NSPCC child-protection endorsed, CRB police-checked, fully insured, over 18, Dan grades, first-aid qualified | same instructor-qualifications image | Home, Contact |
| Member of FEKO, World Karate Confederation (WKC), Federation of Martial Arts (FMA), World Union Karate-do Federation (WUKF), English Karate Council (EKC) | http://tkishotokankarate.co.uk/index.php/abouttki-sp-1618275448 | Home |
| Safeguarding/child-protection policy aligned with NSPCC Child Protection in Sport Unit (CPSU); Tom Tennick is lead safeguarding officer | http://tkishotokankarate.co.uk/index.php/childprotection | Contact |
| Sensei Sharon GoodwinDoash, 5th Dan, senior/head coach, holds NVQ Level 3 BSL (British Sign Language), Cert teaching qualification | http://tkishotokankarate.co.uk/index.php/children-ith-special-needs and /index.php/contact-us | Home, Classes |
| Experience teaching children with dyslexia, autism, Asperger's, ADHD, delayed learning, partial sight, sickle cell, deafness/hearing impairment (30+ years) | http://tkishotokankarate.co.uk/index.php/children-ith-special-needs | Classes |
| First lesson free; dojo fees from 2nd lesson; annual membership/insurance £35 paid on 2nd/3rd lesson | http://tkishotokankarate.co.uk/index.php/first-class-info and /index.php/club-price-list | Home, Classes |
| Class pricing (effective from September 2025): pay-monthly calendar plans — up to 4×50min/1×75min lesson £35; up to 5×75min £50; up to 9×50min £55; up to 9×75min £65; unlimited hall+Zoom £80; Zoom-only £5/class; extra classes 50min £9 / 75min £12; Black Belt Kata lesson (brown/black belts only) £5; card payments 2% admin charge; no refunds on monthly fees/term bookings/membership | http://tkishotokankarate.co.uk/index.php/club-price-list | Classes |
| Ages: Junior 4–14, Senior 11–adult, Adult-only 18+; classes run 7 days a week across ~10 halls | http://tkishotokankarate.co.uk/index.php/findaclub-sp-1981493896 | Home, Classes |
| Class types: Junior Beginners/Advanced, Senior/Family all-grades, Family Fitness, Family Advanced/Sparring, Black Belt Kata (brown/black only), Adult & Teenage Knife Self-Defence (1st & 3rd lesson/month, Lochaber Hall, rubber knives, sweeps/takedowns/locks/holds/pressure points/breaks), Zoom home classes | http://tkishotokankarate.co.uk/index.php/up-to-date-news and /index.php/adult-a-teenage-knife-self-defence | Classes |
| Full weekly venue/day/time schedule (Downham, Bellingham, Bromley Pavilion, Orpington Warren Rd School, Farnborough Village Hall, Beckenham St John's Hall, Beckenham Park Langley Tennis Club, Beckenham St David's Prep School, Lee/Lewisham Lochaber Hall, Green Street Green School) with named instructors | http://tkishotokankarate.co.uk/index.php/up-to-date-news and /index.php/findaclub-sp-1981493896 | Classes |
| Lochaber Hall Community Centre, Lochaber Rd (off Lee High Rd), Lewisham SE13 5QL — Tue & Thu evening classes, taught by Tom Tennick | http://tkishotokankarate.co.uk/index.php/findaclub-sp-1981493896 and /index.php/adult-a-teenage-knife-self-defence | Contact |
| Shotokan Karate general background (developed from Okinawan karate, formalised by Gichin Funakoshi) | http://tkishotokankarate.co.uk/index.php/first-class-info | Sourced but not used — final About copy focused on TKI-specific facts instead of generic style history |
| Facebook page: facebook.com/TKI-Shotokan-Karate-135515973220878 | http://tkishotokankarate.co.uk/index.php/contact-us | Contact footer link |
| Photo: empty dojo hall interior (blue/red mats, wood-beamed hall) | http://tkishotokankarate.co.uk/images/stories/slide1.jpg%20new%202017%20.jpg (homepage slideshow image) | Home ("Where We Train" inset) |

## Do Not Claim
| Claim or uncertainty | Reason |
|---|---|
| "40 years teaching experience" | Instructors page says "over 40 years"; About page + homepage banner graphic both independently say "over 35 years." Used the lower, twice-corroborated figure and omitted the higher one to avoid an inflated/inconsistent claim. |
| "Seventeen clubs" / "eighteen clubs" exact count | About page says seventeen, Find a Club page says eighteen — internally inconsistent. Used descriptive "around ten halls" (matching the actual named-venue count on the live timetable) instead of quoting either specific number. |
| "DBS checked" | Site's own Instructor Qualifications graphic says "CRB police checked" (the pre-2012 predecessor scheme name), not DBS. Used the verbatim sourced term "CRB police-checked" rather than substituting the modern DBS term. |
| Google/Facebook review ratings or counts | Could not access Facebook (JS-rendered, no fetch tool available this session) or Google Business Profile to verify star rating/review count independently. Left out entirely. |
| Any single "studio address" framing | Business runs from ~10 hired halls, no fixed premises. Framing the Lochaber Hall address as "the" location would misrepresent a multi-venue club as single-site. |
| Exact street number for Lochaber Hall | LEADS.md logs "1 Manor Lane Terrace"; the business's own site says "Lochaber Rd (off Lee High Rd)" for the same postcode SE13 5QL. Used the site's own wording rather than guessing which is the precise postal number, both referring to the same building. |
| Squad training ("fightclub") description | Own page content is literally "TBA" — no real description exists yet. Mentioned only as a class name in the schedule, not elaborated. |
| Any competition results, named champion students' achievements, or specific event outcomes | Instructors page names several coached individuals (Joe Kellaway, Paul Herbert, etc.) but gives no verifiable outcomes/dates beyond "coached" — left out to avoid overstating. |

## Asset Manifest
| File | Source URL | Native size | License/credit | Watermark checked | Intended section | Copy match |
|---|---|---|---|---|---|---|
| assets/img/lochaber-hall-dojo.jpg | http://tkishotokankarate.co.uk/images/stories/slide1.jpg%20new%202017%20.jpg | 720×540 (51.6KB) | Business's own site asset (homepage slideshow) | yes — no third-party/photographer watermark visible, plain interior shot | Home, "Where We Train" inset (capped near native size, not full-bleed hero — quality/resolution not sufficient for full-bleed per QA gate) | Matches: real empty hall with sports mats, used as a "where training happens" illustration, not tied to a specific claim beyond "one of our halls" |

Other site images considered and rejected: homepage banner graphic (organisation logos + karate silhouettes, 720×540) — pure text/logo graphic, not a photo, credentials text re-created natively instead; "Contact us"/"grading dates"/"TKI Photo Album" banner graphics (735×150 / 735×135 / small) — text-only title banners, too small and not photographic content; "Find us on Facebook" badge (183×64) — replaced with a plain text link; Facebook-hosted photo albums (linked from Photo Album pages) — external, JS/login-gated, not fetchable this session and out of scope (business's own social media proper, but no direct image access available; not used).

## Design Notes
- Palette: Deep navy ink (#0f1b2c-family) as primary dark + a warm crimson/belt-red accent + a cool off-white paper tone (not cream/gold) — chosen deliberately to differ from the cream/gold/near-black palette already used by 4 other combat-sport builds in this workspace (gav-the-champ-kickboxing-gym, iskf-hampstead-karate, aikido-club-hampstead, aikido-alive-london). Navy is also drawn from the business's own real (if dated) site theme (`--template-special-color: #001B4C`, `--template-link-color: #2a69b8`) and the blue mats visible in the one real dojo photo.
- Image layout pattern: wrapper cover-fill for decorative/section images; the one real photo (dojo hall) is shown in a capped-size inset card, not full-bleed, given its modest native resolution (720×540) and average (not "wow") image quality.
- Risk notes: Very little real photography available (site's actual photo galleries are Facebook-hosted and inaccessible this session); design leans on typographic hero + custom SVG karate iconography + a credentials/affiliations strip rather than forcing photography. All class/pricing/schedule facts are sourced directly from the business's own live (if visually dated) site content, re-verified 2026-07-11.

## Builder QA
- Contrast: see QA_REPORT.md
- Upscale mobile: see QA_REPORT.md
- Upscale tablet: see QA_REPORT.md
- Upscale desktop: see QA_REPORT.md
- Broken images: see QA_REPORT.md
- Manual checks: see QA_REPORT.md
