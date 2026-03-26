# Roadmap: Handled Agency Prospect Homepages

## Overview

23 personalized prospect homepages built in five phases: pre-build planning locks in distinct identities before any HTML is written; quality calibration polishes the 5 existing sites to establish the standard; then three production phases deliver 18 new sites in priority order, with mandatory quality checkpoints preventing creative fatigue across the run.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Pre-Build Planning** - Map all 23 palettes, define design briefs, identify research challenges, establish structural variation menu
- [ ] **Phase 2: Quality Calibration** - Polish 5 existing sites to Main Street Barbershop standard and lock in all build quality norms
- [ ] **Phase 3: Priority 1 Builds — With Websites** - Build 4 priority-1 sites (upstate-cpa, skin-bar-med-spa, quality-electrical, cassy-electric) with reliable web research
- [ ] **Phase 4: Priority 1 Builds — Limited Data** - Build 3 priority-1 sites requiring secondary-source research (OJ's Diner, Fork and Plough, Compass Insurance)
- [ ] **Phase 5: Priority 2 Builds** - Build 11 remaining sites with quality gates every 4 sites

## Phase Details

### Phase 1: Pre-Build Planning
**Goal**: Every site's visual identity, palette, and design brief is defined before any HTML is written — preventing palette collision, design homogeneity, and rushed decisions mid-build
**Depends on**: Nothing (first phase)
**Requirements**: DES-01, QA-04, QA-05, RES-03, RES-04
**Success Criteria** (what must be TRUE):
  1. All 23 sites have a named palette (primary, accent, background darkness) logged in a tracking doc, with no two same-industry sites sharing the same temperature/lightness pair
  2. All 18 new sites have a one-line design brief: industry texture concept, structural variation type, hero approach
  3. The 3 HVAC companies (Eastergard, Air Today, Priority Heating) each have a distinct palette direction documented before any HVAC site is built
  4. The 3 no-website businesses (OJ's Diner, Fork and Plough, Compass Insurance) have a secondary-source research strategy confirmed (Google Business Profile, Yelp, Facebook)
  5. The `logos/` directory is audited: every business either has a confirmed logo file path or is marked as "Oswald wordmark" in the palette plan
**Plans**: TBD

Plans:
- [ ] 01-01: Map all 23 palettes and create design briefs document
- [ ] 01-02: Audit logos directory and define secondary-source research strategy for no-website businesses

### Phase 2: Quality Calibration
**Goal**: All 5 existing sites meet the quality bar (real phones, verified Unsplash URLs, mobile-responsive, visually distinct from each other) and every build standard is defined, tested, and locked in for the 18 new sites
**Depends on**: Phase 1
**Requirements**: QA-01, QA-03, DES-02, DES-03, DES-04, DES-05, DES-06, COPY-01, COPY-02, COPY-03, COPY-04, COPY-05, LAYOUT-01, LAYOUT-02, LAYOUT-03, LAYOUT-04, LAYOUT-05, LAYOUT-06, TECH-01, TECH-02, TECH-03, TECH-04, TECH-05, TECH-06, TECH-07
**Success Criteria** (what must be TRUE):
  1. All 5 existing sites have real phone numbers — no `555-` strings in any file
  2. All 5 sites open in a browser and display their hero photos (no broken Unsplash images)
  3. All 5 sites pass a 375px mobile viewport check: headline, subheadline, and CTA button visible without scrolling
  4. All 5 sites opened side-by-side feel visually distinct — no two could be mistaken for palette swaps of each other
  5. Every build standard from DES, COPY, LAYOUT, and TECH is confirmed working in at least one polished site, serving as the living reference for Phases 3-5
**Plans**: TBD

Plans:
- [ ] 02-01: Polish main-street-barbershop.html (fix placeholder phones, verify assets, quality checklist)
- [ ] 02-02: Polish adw-plumbing, murphy-electric, affordable-hvac, tc-cleaning (full quality checklist on each)

### Phase 3: Priority 1 Builds — With Websites
**Goal**: 4 priority-1 prospect sites (upstate-cpa, skin-bar-med-spa, quality-electrical, cassy-electric) are built and feel custom-designed for their specific business, ready before the first outreach batch goes out
**Depends on**: Phase 2
**Requirements**: RES-01, RES-02, QA-02
**Success Criteria** (what must be TRUE):
  1. Each of the 4 sites has real services sourced from WebFetch — no template service names
  2. Each site has a real, clickable phone number in nav, hero, and CTA section
  3. Each site passes the substitution test: remove the business name and the site still reads as built for that specific business (industry texture, real services, real copy)
  4. All 4 sites open in a browser and display correctly at both desktop and 375px mobile viewport
**Plans**: TBD

Plans:
- [ ] 03-01: Research + build upstate-cpa and skin-bar-med-spa
- [ ] 03-02: Research + build quality-electrical and cassy-electric
- [ ] 03-03: Quality checkpoint — review all 4 new builds against the 5 polished existing sites

### Phase 4: Priority 1 Builds — Limited Data
**Goal**: 3 priority-1 sites for businesses with no or weak web presence (OJ's Diner, Fork and Plough, Compass Insurance) are built using secondary-source research, with all invented or unverified services marked
**Depends on**: Phase 3
**Requirements**: RES-03
**Success Criteria** (what must be TRUE):
  1. Each of the 3 sites is built from real secondary-source data (Google Business Profile, Yelp, or Facebook) — not invented services
  2. Any service that could not be verified is marked with a `<!-- VERIFY -->` comment in the HTML
  3. Each site has a real phone number from secondary sources — no placeholder numbers
  4. All 3 sites pass the same quality checklist as Phase 3 builds (substitution test, mobile check, Unsplash URL verification)
**Plans**: TBD

Plans:
- [ ] 04-01: Research OJ's Diner and Fork and Plough via secondary sources, then build both sites
- [ ] 04-02: Research Compass Insurance via secondary sources, build site, quality checkpoint on all 7 Priority 1 builds

### Phase 5: Priority 2 Builds
**Goal**: All 11 priority-2 sites are built to the same quality standard as Priority 1, with mandatory quality gates every 4 sites preventing production fatigue from degrading the final batch
**Depends on**: Phase 4
**Requirements**: QA-05
**Success Criteria** (what must be TRUE):
  1. All 11 priority-2 sites are complete and pass the per-site definition of done (real data, real phone, real address, unique palette, industry texture, substitution test, mobile check, under 320 lines)
  2. No two sites in the priority-2 batch share the same structural layout pattern
  3. A quality review was conducted after every 4th completed site — at least 2 reviews total
  4. All 23 sites opened together show a coherent collection: each feels like a distinct custom design, not a template with swapped content
**Plans**: TBD

Plans:
- [ ] 05-01: Build priority-2 sites 1-4, quality checkpoint
- [ ] 05-02: Build priority-2 sites 5-8, quality checkpoint
- [ ] 05-03: Build priority-2 sites 9-11, final collection review across all 23 sites

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Pre-Build Planning | 0/2 | Not started | - |
| 2. Quality Calibration | 0/2 | Not started | - |
| 3. Priority 1 — With Websites | 0/3 | Not started | - |
| 4. Priority 1 — Limited Data | 0/2 | Not started | - |
| 5. Priority 2 Builds | 0/3 | Not started | - |
