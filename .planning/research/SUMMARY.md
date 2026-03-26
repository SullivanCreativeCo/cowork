# Research Summary — Handled Agency Prospect Homepages

**Project:** 23 Personalized Cold Outreach Prospect Homepages (Greenville/Spartanburg, SC)
**Synthesized:** 2026-03-25
**Research Files:** STACK.md · FEATURES.md · ARCHITECTURE.md · PITFALLS.md
**Overall Confidence:** HIGH — all four research dimensions grounded in direct codebase analysis and verified technical sources

---

## Executive Summary

Handled Agency is building 23 personalized single-HTML prospect homepages to support cold email outreach targeting local service businesses in the Greenville/Spartanburg, SC market. Five sites already exist and need polish; eighteen are net-new. The quality reference is `main-street-barbershop.html`, which sets the floor every subsequent build must clear. The core value proposition is unconditional: each site must feel custom-designed for that specific business, not templated. If a prospect can swap their business name into another site's homepage and it still reads as theirs, the outreach fails.

The technical approach is intentionally minimal: a single self-contained HTML file per prospect, with all CSS embedded in `<style>`, vanilla JS for scroll animations, and Google Fonts as the only external dependency. No build tools, no frameworks, no CDN libraries. This constraint is not a limitation — it's the discipline that keeps sites lean, fast, and deliverable as file attachments or hosted links. The stack is fully resolved and production-ready: HTML5 semantic markup, CSS Grid/Flexbox for layout, CSS custom properties for per-site palettes, `clamp()` for fluid typography, Intersection Observer for scroll animations, and Unsplash direct CDN URLs for imagery.

The primary risk in this project is not technical — it is creative and operational. Design homogeneity (the "color swap" problem), generic copy that writes to the industry category rather than the specific business, and production fatigue as the batch scales past site ten are the three failure modes most likely to undermine the outreach campaign's effectiveness. The research is unambiguous: preventing these requires front-loaded planning (palette mapping, per-site design briefs, and research before any CSS is written) and mandatory quality gates every four sites throughout the production run.

---

## Key Findings

### Stack (STACK.md — Confidence: HIGH)

- **Single external dependency:** Google Fonts v2 API (Oswald + Inter), loaded via `<link>` with dual preconnect tags. Never `@import`. `display=swap` required on every file.
- **Scroll animations:** Intersection Observer API (97.06% global support, Baseline Widely Available). `threshold: 0.15`, `rootMargin: '0px 0px -40px 0px'`, fire-once via `unobserve()`. CSS scroll-driven animations (`animation-timeline`) are explicitly out — Firefox has zero support as of March 2026.
- **Fluid typography:** `clamp()` eliminates per-breakpoint font overrides and saves 30-50 lines per file. Combined with `vw + rem` addend for WCAG 1.4.4 compliance.
- **Color system:** CSS custom properties in `:root` (5-6 variables) allow full palette swaps in under 10 lines without separate CSS files.
- **Image handling:** `images.unsplash.com` direct CDN URLs with imgix params (`w=1400&q=80&fit=crop&auto=format`). `source.unsplash.com` is deprecated and non-functional — never use it. Preserve `ixid` parameter for attribution compliance.
- **Animation safety:** Only `opacity` and `transform` are GPU-composited — never animate `width`, `height`, `margin`, `top`, or `box-shadow` directly. `will-change` applied before animation, reset to `auto` after.
- **Line budget:** 265-312 lines per file across the full document. Minified CSS (no indentation) is the key lever for fitting design depth within the 320-line ceiling.

### Features (FEATURES.md — Confidence: HIGH)

- **Table stakes (must-have):** Business name in nav/title, real clickable `tel:` phone number in nav + hero + CTA, actual services from research, real address/service area, mobile responsiveness, scroll animations, fixed nav with scroll effect, full-width CTA with large phone display, footer "Site by Handled" credit, single-file self-containment, under 320 lines.
- **Differentiators (what makes prospects say "this is really mine"):** Industry-specific CSS texture in hero (derived from business type, not decorative noise), unique palette per site (not a hue rotation — a wholly distinct color identity), ghost/watermark Oswald text behind content, parallax background layer (hero only, not every section), industry-specific divider elements, top accent stripe, human-sounding copy that references specific facts (owner name, founding year, crew names, neighborhood), real statistics from research, named owner/operator in CTA copy, and logo integration where assets exist.
- **Anti-features (template signals that kill credibility):** Trust badge bars, icon grids with rounded card containers, gradient blob backgrounds, emoji icons as visual elements, generic placeholder copy, non-functional forms, navigation links that go nowhere, fake testimonials, stock photos without heavy dark overlays, rounded corners everywhere, glassmorphism on every element, and palette swaps of the same structural layout.
- **MVP build order per site:** Real data first (WebFetch) → unique palette + texture concept → hero → services as horizontal rows → about with real copy → hours/location + CTA → one differentiating motion technique (only one, executed well).

### Architecture (ARCHITECTURE.md — Confidence: HIGH)

- **Data flow:** One-directional. Master CSV → WebFetch per business → design concept → single HTML file. No runtime data fetching; everything embedded at build time. Files have zero inter-dependencies.
- **Shared structure, unique identity:** Every site follows the same section order (nav → hero → services → about → hours/location → CTA → footer) and reuses the same JS verbatim. Everything visual (palette, texture, copy, imagery, accent elements) is designed fresh per site.
- **Build workflow (per site):** Step 0 — locate in CSV and batch email files. Step 1 — WebFetch research (real services, hours, address, founding year, owner names). Step 2 — design concept (palette, texture, hero concept, Unsplash search term). Step 3 — build from reference skeleton. Step 4 — quality checklist. Step 5 — save to output directory.
- **Build order:** Phase A (polish 5 existing) → Phase B (4 Priority 1 sites with websites) → Phase C (3 Priority 1 sites with limited/no web presence) → Phase D (11 Priority 2 sites).
- **Quality floor:** The "Specificity Test" — remove the business name from the file. If the site still reads as built for this specific business (because of industry texture, real services, real copy), it passes. If another business name fits without adjustment, rebuild.
- **Palette logging:** Track every used palette during the production run. Never reuse the same temperature + lightness combination for two sites in the same industry cluster.

### Pitfalls (PITFALLS.md — Confidence: HIGH)

**Critical (cause the outreach to fail):**
1. **Design homogeneity** — structural skeleton copied with only accent color changed. Prevention: define a one-line design brief per site before touching CSS; require three structural differentiators from the previous build; industry dictates texture.
2. **Generic copy** — writing for the category, not the business. Prevention: hunt for founding year, owner name, specific crew names, neighborhood specifics during WebFetch; apply the substitution test to every hero paragraph before shipping.
3. **Unsplash image failures** — broken or irrelevant hero photos. Prevention: browser-verify every URL before embedding; use industry-specific search terms; add CSS fallback background color to every `background:url()` declaration.

**Moderate (degrade quality and consistency):**
4. **Production fatigue** — quality regression after site 10. Prevention: mandatory batch review every 4 sites; maintain "used textures" log; pre-plan Priority 2 design concepts before Priority 1 is complete.
5. **Mobile breakpoint failures** — nav collapse and hero overflow on 375px viewports. Prevention: 375px viewport check is part of every site's definition of done.
6. **Line count creep** — exceeding 320 lines via CSS depth techniques. Prevention: write CSS minified; limit `@keyframes` to one per site; avoid stacking box-shadow + gradient + border on the same element.
7. **Palette collision** — same visual register across same-industry multiples (3 HVAC, 2 plumbing companies in this batch). Prevention: map all 23 palettes before building site 1.
8. **WebFetch failures** — building on stale/missing data for no-website businesses. Prevention: Google Business Profile, Yelp, Facebook as mandatory secondary sources; mark invented services with `<!-- VERIFY -->` comments.

**Minor (checklist items):**
- Placeholder phone numbers (`555-`) shipping in production files
- Generic emoji characters used as section dividers (only industry-specific Unicode symbols are permitted)
- Over-applying `.reveal` class (animate containers, not individual list items)
- Missing `display=swap` or preconnect tags for Google Fonts

---

## Implications for Roadmap

The research points to a three-phase structure. The phases are not arbitrary sequencing — each addresses a specific failure mode identified in the pitfall research.

### Suggested Phase Structure

**Phase 1: Pre-Build Planning (before any HTML is written)**

Rationale: The two highest-stakes pitfalls (palette collision, design homogeneity) both originate before a single line of code is written. Front-loading planning prevents creative debt that compounds across 23 sites.

Tasks:
- Map all 23 businesses to palette territory (primary, accent, background darkness) and verify no two same-industry sites share the same temperature/lightness pair
- Define a one-line design brief for each of the 18 new sites: industry texture concept, structural variation type, hero approach
- Identify all WebFetch research challenges: flag OJ's Diner (no website), Fork and Plough (no website), Compass Insurance (.biz domain), The Skin Bar (domain mismatch) for secondary-source research strategy
- Establish "used" tracking logs: CSS textures, structural patterns, palettes
- Confirm logo assets in `logos/` directory for all 23 businesses

**Phase 2: Polish Existing 5 (calibrate quality bar before new-build run)**

Rationale: The existing sites are the quality reference and the regression-prevention anchor. Polishing them first (a) resolves known issues like the Main Street Barbershop placeholder phone numbers, and (b) calibrates what "good" looks like before the pressure of 18 new builds begins.

Tasks:
- main-street-barbershop.html: replace `555-` placeholder phone number, verify Unsplash URLs load, confirm preconnect tags present
- adw-plumbing.html, murphy-electric.html, affordable-hvac.html, tc-cleaning.html: run full quality checklist (phone numbers, Unsplash URL verification, 375px mobile check, line count, no emoji, no trust badge bars)
- Open all 5 simultaneously in browser to confirm visual distinctiveness — no two should feel like palette swaps of each other

**Phase 3: New-Build Production (18 sites in priority order)**

Rationale: Priority determines sequencing (batch 1 outreach goes first), but quality gates prevent the production fatigue that hits after site 10.

Sub-phases within production:
- Phase 3A (Priority 1 with websites — 4 sites): upstate-cpa, skin-bar-med-spa, quality-electrical, cassy-electric. Highest-care builds; these contact windows open first.
- Phase 3B (Priority 1 with limited data — 3 sites): ojs-diner (Google Maps/Yelp fallback), fork-and-plough, compass-insurance. Require extended research time due to weak web presence.
- Phase 3C (Priority 2 — 11 sites): All remaining. Pre-planned design concepts from Phase 1 prevent quality regression. Mandatory batch review after every 4th completed site.

Quality gate cadence:
- Between Phase 3A and 3B: review all completed new builds against the 5 polished existing sites
- After every 4 sites within Phase 3C: open all completed sites simultaneously; confirm no structural repetition, no palette collision, no copy that fails the substitution test

**Definition of Done (per site):**
- [ ] Every service is real (WebFetch-sourced or marked `<!-- VERIFY -->`)
- [ ] Phone number is real (no `555-`)
- [ ] Address is real
- [ ] Palette is unique (checked against palette log)
- [ ] Industry texture is specific to this business
- [ ] Hero copy passes substitution test
- [ ] No trust badge bars, icon grids, gradient blobs, or emoji icons
- [ ] All Unsplash URLs verified in browser
- [ ] 375px mobile viewport check passes (headline + subhead + CTA button visible without scrolling)
- [ ] File is under 320 lines
- [ ] Google Fonts preconnect tags present; `display=swap` in URL
- [ ] Footer: "Site by Handled" links to handledagency.co

---

## Research Flags

Areas where the roadmap team should conduct deeper investigation or make explicit decisions before execution begins.

| Flag | Phase | Issue | Recommended Action |
|------|-------|-------|-------------------|
| OJ's Diner (no website) | Phase 3B | No website; research must use Google Business Profile, Yelp, or Facebook | Confirm secondary source access before this build session; identify which sources are reachable |
| Fork and Plough (no website) | Phase 3B | No website found; same as OJ's Diner | Same as above |
| The Skin Bar (domain mismatch) | Phase 3A | Brief says `raisingtheskinbar.com`; business name is "The Skin Bar Med Spa" | Fetch both domains; verify which is live and current |
| Compass Insurance (.biz domain) | Phase 3B | Uncommon TLD; site content quality unknown | Pre-fetch before build session; assess if secondary sources are needed |
| Logo directory completeness | Phase 2/3 | Current logos confirmed for adw-plumbing, murphy-electric, tc-cleaning; status unknown for the 18 new businesses | Audit `logos/` before Phase 3 begins; note which sites will need Oswald wordmark instead |
| Placeholder phone in main-street-barbershop | Phase 2 | Multiple `<!-- TODO: Replace (864) 555-0000 -->` comments confirmed in existing reference file | Resolve before polishing phase is marked complete; this is the quality reference file |
| Palette plan for 3 HVAC companies | Phase 1 | Eastergard HVAC, Air Today Heating, Priority Heating — three sites in same industry risk palette collision | Differentiation strategy should be decided in Phase 1, not during each build; proposed: Eastergard = warm amber/dark brown (legacy), Air Today = steel blue/white (modern), Priority = forest green/cream (community) |
| Services for businesses with thin web presence | Phase 3B/3C | 7+ businesses have minimal or no web content | For any site where services cannot be verified, use broadly accurate trade-type services without prices and mark each with `<!-- VERIFY -->` |

---

## Confidence Assessment

| Research Dimension | Confidence | Basis |
|---|---|---|
| Technology stack | HIGH | All recommendations verified against MDN, caniuse.com, and 2025/2026 sources; deprecations confirmed (source.unsplash.com, animation-timeline Firefox gap) |
| Feature set (table stakes + differentiators) | HIGH | Derived from direct code analysis of all 5 existing builds; anti-patterns validated from project brief |
| Architecture and build workflow | HIGH | Empirical — based on existing codebase structure, file naming conventions, and explicit project constraints |
| Pitfall identification | HIGH | All critical pitfalls drawn from direct inspection of the 5 existing builds, including confirmed issues (placeholder phone numbers in reference file) |
| Phase structure and roadmap implications | MEDIUM-HIGH | Logical derivation from research; sequencing rationale is sound, but exact time-per-site estimates (20-40 min per new build) are not empirically validated for the full 18-site run |
| Secondary source research strategy (no-website businesses) | MEDIUM | General guidance is sound (Google Business Profile, Yelp, Facebook), but specific data availability for OJ's Diner, Fork and Plough, and Compass Insurance is unverified until actually fetched |

**Overall synthesis confidence: HIGH.** The research base is thorough, grounded in the actual codebase, and internally consistent across all four dimensions. The primary uncertainty is operational (time estimates, secondary source quality for no-website businesses) rather than technical or strategic.
