# Design System & Redesign — Working Notes

**This is a living document, not a one-time plan.** The whole site —
IA, philosophy, design system, tooling choices — is still WIP and will
keep changing. Whenever a decision here changes, gets superseded, or a
phase actually ships, update this file in the same session, not as a
followup. Stale docs are worse than no docs — if something below
contradicts the live site, fix the doc, don't just note the drift.

Status as of 2026-08-15: Phase 1 (colors/fonts) shipped. Work now has 4
real case studies (not the placeholder 2-3 originally planned). About
has a full career timeline + "Beyond the resume" + profile photo.
Favicon, Open Graph tags, robots.txt/sitemap.xml, icon-based social
links, and email-hover tooltips are all live. Astro migration (Phase 2)
has NOT happened — still hand-written HTML/CSS, and that's fine per the
original reasoning below, revisit only when Writings/case-study volume
actually makes it painful.

## Decided

- **Nav stays job-focused**: `Work / Automations / About & Resume / Contact`.
  Personal (Travel, Writings, Instagram/X) is demoted out of primary nav —
  not deleted. It gets a single low-emphasis text link ("Beyond the
  roadmap → see more") from the About page, pointing to the existing
  `personal.html` hub.
- **Resume** lives on the About page (paired with bio), not under Work.
  Work is for narrative case studies; About/Resume is the credential.
- **Automations section is real**: one confirmed working automation exists.
  The Beacon app also needs a home — see open question below.
- **Both light and dark mode stay** (OS-based auto-switch, as today).
- **Fonts**: Plus Jakarta Sans (headings/UI/nav), Merriweather (body/case
  study narrative), JetBrains Mono (tags/badges/tech stack labels).
  Self-hosted as woff2 (not Google Fonts CDN) — more reliable while the
  site goes untouched for stretches, and one less external dependency.
- **Travel page and Beacon privacy page are out of scope** for this
  redesign — leave untouched. Travel is Claude-Design-generated content;
  Beacon privacy is a legal page tied to a live Play Store listing —
  don't risk it in a design pass.
- **Color tokens — full set, light + dark** (from Google AI Studio):

  | Token             | Light              | Dark               |
  |--------------------|--------------------|--------------------|
  | `--bg-page`        | `#F9F6F0` (Cream)  | `#1C1917` (Espresso)|
  | `--bg-surface`      | `#FFFFFF` (White)  | `#292524` (Warm Charcoal) |
  | `--text-primary`    | `#2D3339` (Charcoal)| `#F5F2EB` (Off-White)|
  | `--text-secondary`  | `#6F6960` (Taupe)  | `#ABA6A0` (Warm Gray)|
  | `--accent-main`     | `#B05D35` (Terracotta)| `#C87A57` (Bright Terracotta)|
  | `--accent-hover`    | `#964F2D` (Deep)   | `#D99073` (Bright) |
  | `--border-subtle`   | `#EBE5DD` (Light Gray)| `#403C39` (Dark Gray)|

## Open questions (need an answer before implementation is "done", not
before it starts — some can be decided mid-flight)

1. **Beacon app placement** — does it have measurable outcomes (installs,
   ratings, a problem-solved story)? If yes → full Work case study
   (Card Type A), possibly with a lighter technical card also in
   Automations pointing back to it. If it's early/no numbers yet →
   Automations only (Card Type B), alongside the one confirmed automation.
   **Decide this once back**, it changes what content needs writing.
2. **Font count** — asked AI Studio to weigh in on whether 2 fonts
   (dropping one of the 3) would lose much of the intended feel, given
   load-cost concerns. **Not yet answered** — re-ask or decide directly.
3. **Mobile type scale** — asked AI Studio for a distinct Merriweather
   size/line-height spec at ~375px viewport, separate from desktop.
   **Not yet answered.**
4. **Full token table beyond color** — spacing scale, border-radius
   values, motion/transition durations. **Not yet answered** — only the
   color table has come back so far.
5. **Componentization guidance** — asked AI Studio to define Card Type A,
   Card Type B, and the case-study detail template as reusable
   components/templates (not one-off markup), since they'll be rendered
   from a template loop. **Not yet answered.**
6. **Single-item Automations layout** — asked how the Automations grid
   should look with only 1-2 real cards instead of a full 3-column grid.
   **Not yet answered.**
7. **Movie Reviews and Writings** — original IA had these as personal
   sections; current redesign draft doesn't mention Movie Reviews at all
   (dropped silently by AI Studio, not decided by us). Writings is
   pending WordPress content export. Confirm both still belong on the
   Personal hub page once back.

> Items 2, 3, 4, 5, 6 were all included in a consolidated prompt already
> given to Google AI Studio — only the dark-mode palette question (item
> "Decided" above) has a response back so far. Re-send or re-ask the rest
> before starting implementation, so the token table and templates are
> complete in one pass rather than patched piecemeal.

## Why static HTML → Astro, and when

Current site is hand-written HTML/CSS per page, no build step. That's
fine at 6 pages. It stops being fine once:
- Work gets more than 2-3 case studies (repeated Card Type A markup)
- Writings import happens (WordPress content is much easier as markdown
  files in a real templating system than hand-pasted HTML)
- Travel photos need fixing (currently one 12MB base64-inlined image
  blob from Claude Design — Astro's image pipeline solves this
  permanently: drop JPEGs in a folder, get responsive/optimized output)

**Recommendation stands: migrate to Astro at the same time as the
Writings import**, not before and not separately. Doing the design
system work first (this file) and migrating later means one clean
migration instead of two.

## Task breakdown, in order

### Phase 0 — Close remaining open questions (before touching code)
- [ ] Decide Beacon app placement (Work case study vs Automations card
      vs both)
- [ ] Get remaining AI Studio answers (items 2-6 above), or decide
      directly without AI Studio if simpler
- [ ] Confirm Movie Reviews / Writings status on Personal hub

### Phase 1 — Colors & fonts only (current site, no structural change) — DONE (2026-07-31)
- [x] Self-host Plus Jakarta Sans, Merriweather, JetBrains Mono
      (downloaded as 3 variable/static woff2 files into `fonts/`,
      `@font-face` rules added at the top of `css/style.css`)
- [x] Replace color tokens in `css/style.css` with the table above
      (both light and dark blocks — dark is now the default, light
      applies via `prefers-color-scheme: light`)
- [x] Apply font-family assignments: headings/nav/buttons → Jakarta Sans
      (body default), tagline/lead text → Merriweather, badges/tags/
      wip-tag → JetBrains Mono
- [x] Sanity-checked both light and dark mode via headless screenshots
      (home page + personal page with cards/badges) — renders correctly
- [x] No HTML structure changed — only `css/style.css` and the new
      `fonts/` folder, exactly as planned. All 6 pages picked it up
      automatically since they share one stylesheet.

Still open from Phase 1's original scope (not blocking, deferred to
Phase 0 answers): mobile-specific Merriweather type scale, spacing/
radius/motion tokens beyond what already existed in the CSS, 2-vs-3
font decision. Colors and font families are considered final.

### Phase 2 — Astro migration (bundle with Writings import)
- [ ] Scaffold Astro project, port existing pages as-is first (no visual
      change, just moving to the new tool)
- [ ] Build Card Type A / Card Type B as real components
- [ ] Build case-study detail page as a reusable template
- [ ] Set up markdown content collections for case studies and writings
- [ ] Import WordPress export as markdown
- [ ] Extract travel page images to real files via Astro's image
      pipeline (fixes the 12MB single-file problem)
- [ ] Re-deploy via GitHub Action to GitHub Pages, confirm custom domain
      + Beacon privacy path (`/beacon/privacy/`) still resolve correctly
      post-migration

### Phase 3 — Content
- [x] About Me copy + career timeline (school through Loco, expandable
      detail per entry) + "Beyond the resume" highlights + profile photo
- [x] Work case studies — shipped 4, not the originally-scoped 2-3:
      Audience Platform (Featured), Timeline Missions, Subscriptions,
      Quest Engine. Ordered featured-first, then chronological per
      company (Junglee before Loco).
- [ ] Beacon app placement — still not decided/executed (Apps page is
      still an empty WIP stub)
- [ ] Automations card(s) — not started
- [ ] Personal hub: Writings content, confirm Movie Reviews status —
      not started, Personal page currently only has Travel live
- [x] Personal teaser link wired into About page ("Also travel, and
      occasional writing — on the Personal page")

### Analytics — DONE (2026-08-15)
Cloudflare Web Analytics chosen over Google Analytics: cookieless (no
consent banner needed), free, and the domain's already on Cloudflare so
it's not really adding a new third party. Gives page-level breakdown
(top pages, referrers), which is what was asked for over just a raw
visitor count.
- [x] Dinesh created a Web Analytics site in the Cloudflare dashboard,
      grabbed the manual JS snippet/token (DNS is grey-cloud/DNS-only
      for the GitHub Pages cert, so "Automatic setup" doesn't work —
      manual snippet is required, not optional, for this domain)
- [x] Snippet added to all 14 pages (main site + both Work sub-pages +
      Travel index/trip page + both Beacon legal pages) — full picture,
      not just the portfolio pages
- [ ] Verify it's tracking (Cloudflare dashboard shows hits) — check
      this once traffic has had a chance to come in

### Phase 4 — RAG chat bot + voice bot ("talk to my portfolio")
Goal: let visitors ask a bot questions and have it answer as/about
Dinesh, both professionally (PM work, case studies) and personally
(travel, writings, interests) — text chat first, voice as a later
sub-phase.

**Why this comes after Phase 3, not before:** RAG retrieves from real
content. Running this against an empty/WIP site has nothing to answer
from — the case studies, About copy, and Writings written in Phase 3
*are* the corpus this phase indexes. Don't start Phase 4 until Phase 3
content exists.

**Why this can't live on GitHub Pages alone:** Pages only serves static
files. A RAG bot needs server-side code (to embed the question, query a
vector store, call an LLM) and a place to hold an API key that never
reaches the browser. This means adding a small backend service
alongside the static site — the portfolio pages and domain don't
change, this is additive.

- [ ] Pick a backend host for the bot API — Cloudflare Workers or Vercel
      are the natural fit (generous free tier, deploys from git like
      everything else here)
- [ ] Pick a vector store — Cloudflare Vectorize, Pinecone free tier, or
      a simple file-based store (corpus size here is small: resume +
      case studies + writings is a few hundred chunks at most, doesn't
      need enterprise infra)
- [ ] Content pipeline: chunk + embed About/Work/Writings markdown from
      the Phase 2 Astro content collections into the vector store
- [ ] Backend endpoint: takes a question, retrieves relevant chunks,
      calls the Claude API with them as context, returns an answer.
      API key lives server-side only, never shipped to the browser.
- [ ] Decide a rate limit / budget cap (e.g. N messages per visitor per
      day) before shipping publicly — LLM calls cost money per request,
      unlike static hosting
- [ ] Chat widget on the site calling the backend endpoint
- [ ] Ship and validate text chat end-to-end before starting voice
- [ ] **Voice sub-phase** (separate, bigger lift — don't bundle with
      text chat): add speech-to-text/text-to-speech (e.g. a Realtime
      API) and a WebSocket/WebRTC audio loop on top of the same
      RAG backend

### Backlog — small items, not urgent
- [ ] Per-page Open Graph images (currently one shared `images/og-image.png`
      across every page — works fine, but ideally Work case studies would
      show page-specific preview cards instead of the generic name/role/chip
      card). Needs either per-page generated images or a template approach.

## Non-negotiables to remember when resuming
- Beacon privacy policy (`dineshkaliki.com/beacon/privacy/`) is tied to
  a live Play Store listing. Any deploy/build change must verify this
  path still works before considering the change done.
- Domain `dineshkaliki.com` is single-owner: only the
  `dineshkaliki.github.io` repo should ever claim it in GitHub Pages
  settings (this bit us once already with `beacon-site` and
  `travel2026june`).
- **`sitemap.xml` must stay in sync with the site.** Whenever a page is
  added, removed, or its URL changes (new case study, new Work/Apps/
  Personal content, a page moved), update `sitemap.xml` in the same
  change — don't treat it as a separate followup. `robots.txt` itself
  rarely needs edits (it's just `Allow: /` + the sitemap pointer) unless
  a future page genuinely needs to be excluded from crawling.
- **DNS for `dineshkaliki.com` stays "DNS only" (grey cloud) in
  Cloudflare — do not proxy it (orange cloud), even to fix an unrelated
  problem.** Reasoning: GitHub Pages issues and auto-renews its own SSL
  certificate, and needs to directly verify the domain resolves to
  GitHub's servers to do that — both at initial setup and on every
  renewal (~90 days). If the domain is proxied, that verification can
  fail. The dangerous part is *renewal*, not setup: a renewal failure
  doesn't show up immediately, it shows up ~90 days later as a sudden
  site-wide HTTPS failure with zero warning beforehand — discovered by
  a visitor (e.g. a recruiter), not by us. This already happened to be
  the reason Cloudflare Web Analytics needed manual setup with
  `auto_install` disabled (2026-08-15) rather than the "Automatic"
  proxy-based install — that's the accepted trade-off, not a bug to
  fix by proxying. Only reconsider proxying if something genuinely
  needs it (DDoS protection, edge caching, WAF) and only after
  deliberately re-weighing this risk — never as a quick fix for an
  unrelated tool.
