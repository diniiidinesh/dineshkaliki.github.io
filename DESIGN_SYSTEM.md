# Design System & Redesign — Working Notes

Status as of 2026-07-31: parked, picking back up later. This file is the
single source of truth for what's decided, what's still open, and the
order to tackle things in. Nothing below has been implemented in the CSS
yet — current live site still uses the original blue/dark palette.

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
- [ ] About Me copy + resume PDF
- [ ] Work case studies (from resume, Dinesh to review/edit each)
- [ ] Beacon app placement executed per Phase 0 decision
- [ ] Automations card(s)
- [ ] Personal hub: Writings content, confirm Movie Reviews status
- [ ] Personal teaser link wired into About page

## Non-negotiables to remember when resuming
- Beacon privacy policy (`dineshkaliki.com/beacon/privacy/`) is tied to
  a live Play Store listing. Any deploy/build change must verify this
  path still works before considering the change done.
- Domain `dineshkaliki.com` is single-owner: only the
  `dineshkaliki.github.io` repo should ever claim it in GitHub Pages
  settings (this bit us once already with `beacon-site` and
  `travel2026june`).
