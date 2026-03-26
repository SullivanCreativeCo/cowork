# Requirements: Handled Prospect Homepages

**Defined:** 2026-03-25
**Core Value:** Every prospect homepage must feel like it was designed specifically for that business — if a prospect could swap their name and the site still works, it's not good enough.

## v1 Requirements

Requirements for the full 23-site build. Each maps to roadmap phases.

### Research

- [ ] **RES-01**: WebFetch each business's current website for services, hours, address, branding details
- [ ] **RES-02**: Source real phone number for clickable `tel:` links (appears in nav, hero, CTA)
- [ ] **RES-03**: Use secondary sources (Google Maps, Yelp, Facebook) for 3 no-website businesses (OJ's Diner, Fork and Plough, Compass Insurance)
- [ ] **RES-04**: Download logo if available from business site to logos/ directory

### Design Identity

- [ ] **DES-01**: Each site gets a unique color palette — not hue rotations of another site's palette
- [ ] **DES-02**: Industry-specific CSS texture in hero (barber pole stripe, pipe grid, wire dash, etc.)
- [ ] **DES-03**: Industry-specific top accent stripe (3-4px bar at page top)
- [ ] **DES-04**: Ghost/watermark text element with parallax (year, brand word, or keyword)
- [ ] **DES-05**: Section-level layered shadows for depth (z-index stacking + box-shadow)
- [ ] **DES-06**: Industry-specific divider element replacing generic `<hr>`

### Content & Copy

- [ ] **COPY-01**: Human-sounding copy specific to the business — no template phrases
- [ ] **COPY-02**: Real services displayed as clean horizontal rows (name + desc left, tag right)
- [ ] **COPY-03**: Real statistics/social proof numbers (years in business, 24/7, service area) — 2-3 max
- [ ] **COPY-04**: Named owner/operator in copy where findable via research
- [ ] **COPY-05**: Business-specific CTA copy ("Pipe Problem? Call ADW." not "Contact Us Today")

### Structure & Layout

- [ ] **LAYOUT-01**: Fixed nav with scroll blur effect (backdrop-filter on scroll)
- [ ] **LAYOUT-02**: Hero with business name, location, and phone CTA above the fold
- [ ] **LAYOUT-03**: Section flow: Hero → Services → About → Hours/Location → CTA
- [ ] **LAYOUT-04**: Large phone number CTA section near bottom (Oswald, `tel:` link)
- [ ] **LAYOUT-05**: "Site by Handled" footer linking to handledagency.co
- [ ] **LAYOUT-06**: Logo integration — `<img>` if available in logos/, Oswald wordmark if not

### Technical

- [ ] **TECH-01**: Single self-contained HTML file with all CSS embedded
- [ ] **TECH-02**: Google Fonts only external dependency (Oswald + Inter via preconnect + display=swap)
- [ ] **TECH-03**: Real Unsplash photos with heavy dark overlays (min 80% opacity overlay)
- [ ] **TECH-04**: Mobile responsive — viewport meta, flex-wrap, breakpoints at 768px and 600px
- [ ] **TECH-05**: Intersection Observer scroll animations (.reveal class, staggered delays)
- [ ] **TECH-06**: Under 320 lines per file
- [ ] **TECH-07**: One signature motion technique per site (parallax, staggered reveals, animated grid, ghost text scroll)

### Quality & Process

- [ ] **QA-01**: All 5 existing sites polished to Main Street Barbershop quality bar
- [ ] **QA-02**: 18 new sites built with Priority 1 batch before Priority 2
- [ ] **QA-03**: No placeholder phone numbers (555-XXXX) — real numbers only
- [ ] **QA-04**: Structural variation across sites — 3+ distinct section layouts rotated
- [ ] **QA-05**: Quality review checkpoint every 4 sites to catch production fatigue

## v2 Requirements

Deferred beyond the initial 23-site build.

### Enhancements

- **V2-01**: Real Google review testimonials integrated where available
- **V2-02**: Multi-page variants (Services page, About page) for highest-interest prospects
- **V2-03**: Dark mode / light mode toggle
- **V2-04**: Animated hero video backgrounds for restaurant/salon businesses

## Out of Scope

| Feature | Reason |
|---------|--------|
| Contact forms | Forms submit nowhere on a demo page — phone CTAs are the right pattern |
| JavaScript frameworks | Adds complexity with no value for single-file static demos |
| CDN libraries (Bootstrap, Tailwind, etc.) | Constraint: Google Fonts only external dependency |
| Multi-page navigation | Single homepage demos — nav anchor-scrolls within page |
| Fake testimonials | Kills credibility — skip section entirely if no real quotes available |
| CMS or templating engine | 23 hand-crafted files is the point — each must feel bespoke |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| RES-01 | Phase 3 | Pending |
| RES-02 | Phase 3 | Pending |
| RES-03 | Phase 1 | Pending |
| RES-04 | Phase 1 | Pending |
| DES-01 | Phase 1 | Pending |
| DES-02 | Phase 2 | Pending |
| DES-03 | Phase 2 | Pending |
| DES-04 | Phase 2 | Pending |
| DES-05 | Phase 2 | Pending |
| DES-06 | Phase 2 | Pending |
| COPY-01 | Phase 2 | Pending |
| COPY-02 | Phase 2 | Pending |
| COPY-03 | Phase 2 | Pending |
| COPY-04 | Phase 2 | Pending |
| COPY-05 | Phase 2 | Pending |
| LAYOUT-01 | Phase 2 | Pending |
| LAYOUT-02 | Phase 2 | Pending |
| LAYOUT-03 | Phase 2 | Pending |
| LAYOUT-04 | Phase 2 | Pending |
| LAYOUT-05 | Phase 2 | Pending |
| LAYOUT-06 | Phase 2 | Pending |
| TECH-01 | Phase 2 | Pending |
| TECH-02 | Phase 2 | Pending |
| TECH-03 | Phase 2 | Pending |
| TECH-04 | Phase 2 | Pending |
| TECH-05 | Phase 2 | Pending |
| TECH-06 | Phase 2 | Pending |
| TECH-07 | Phase 2 | Pending |
| QA-01 | Phase 2 | Pending |
| QA-02 | Phase 3 | Pending |
| QA-03 | Phase 2 | Pending |
| QA-04 | Phase 1 | Pending |
| QA-05 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 30 total
- Mapped to phases: 30
- Unmapped: 0 ✓

---
*Requirements defined: 2026-03-25*
*Last updated: 2026-03-25 after roadmap creation — all 30 requirements mapped to phases*
