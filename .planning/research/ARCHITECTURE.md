# Architecture Patterns — Handled Prospect Homepage Batch Build

**Domain:** Static HTML batch production (outreach collateral)
**Researched:** 2026-03-25
**Confidence:** HIGH — architecture is empirical; based on existing codebase, brief, and established static-site production patterns

---

## System Overview

This is not a web application. There is no build tool, no framework, no database. The "system" is a repeatable human+AI production process that outputs 23 self-contained HTML files. Architecture here means: how the files are organized, how data flows into each file, and how to run the process consistently enough that site #23 is as good as site #1.

```
Data Sources
  (CSV + batch emails)
        │
        ▼
  Research Phase
  (WebFetch each business)
        │
        ▼
  Design Concept
  (palette + texture + copy direction)
        │
        ▼
  Build Phase
  (single HTML file from reference template)
        │
        ▼
  Output Directory
  ~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/[slug].html
```

---

## Component Boundaries

| Component | Responsibility | Communicates With |
|---|---|---|
| Master CSV | Ground truth for all 23 businesses (name, type, URL, contact, phone, priority) | Research phase reads this |
| Batch email files (batch1.md, batch2.md) | Build priority order, email-specific context, key sales hook per prospect | Build order determination |
| WebFetch (per site) | Live business data: real services, hours, address, branding details | Feeds design concept and copy |
| Logo directory | Pre-downloaded logos at `logos/[business-slug]-logo.[ext]` | HTML `<img>` src in nav/hero |
| Reference file (main-street-barbershop.html) | Quality standard and structural template — copy JS, layout pattern, CSS conventions | Every new build references this |
| Output HTML files | Deliverable — each is a complete, standalone page | None (no inter-file dependencies) |

---

## Data Flow

Data flows in one direction only: inward to each HTML file. No file depends on another file at runtime.

```
[2026-03-25-greenville-spartanburg-prospects.csv]
    Business name, type, URL, contact, phone, priority
         │
         ├──► WebFetch [business URL]
         │        Real services, pricing, hours, address,
         │        taglines, staff names, founding year
         │
         ├──► [logos/] directory
         │        Logo file presence check → include or omit
         │
         └──► [outreach-emails-batch1.md / batch2.md]
                  Email hook context → informs copy angle
                         │
                         ▼
              Design Concept Decision
                (palette, texture, hero concept)
                         │
                         ▼
              Single HTML File Build
              [business-slug.html]
                (embedded CSS + vanilla JS + all content)
                         │
                         ▼
              Saved to output directory
              ~/Desktop/.../prospect-sites/
```

**Key constraint:** Zero runtime data fetching. Everything embedded at build time. Each file is fully self-contained — no shared CSS files, no JS imports, no inter-page navigation.

---

## File Organization

### Output Directory Structure

```
prospect-sites/
├── logos/
│   ├── adw-plumbing-logo.jpg
│   ├── murphy-electric-logo.png
│   └── tc-cleaning-logo.png
├── main-street-barbershop.html    ← quality reference (DO NOT MODIFY during batch)
├── adw-plumbing.html
├── murphy-electric.html
├── affordable-hvac.html
├── tc-cleaning.html
└── [18 new files, named below]
```

### Naming Convention

Pattern: `[business-name-slug].html` — lowercase, hyphens, no underscores.

| Business | File Name |
|---|---|
| Upstate Professional CPA Services | upstate-cpa.html |
| The Skin Bar Med Spa | skin-bar-med-spa.html |
| Quality Electrical Contractors | quality-electrical.html |
| Cassy Electric | cassy-electric.html |
| OJ's Diner | ojs-diner.html |
| Fork and Plough | fork-and-plough.html |
| Compass Insurance Group | compass-insurance.html |
| The Smith Insurance Agency | smith-insurance.html |
| Jason Yardscaping | jason-yardscaping.html |
| Local Plumber LLC | local-plumber.html |
| To Dye For Salon | to-dye-for-salon.html |
| Eastergard HVAC | eastergard-hvac.html |
| Air Today Heating & Cooling | air-today-hvac.html |
| Priority Heating and Air | priority-heating-air.html |
| CB Smith Plumbing | cb-smith-plumbing.html |
| R. Mills Ariail Jr. Law Office | ariail-law.html |
| Charles A. Ford CPA | charles-ford-cpa.html |
| Wade's Restaurant | wades-restaurant.html |

---

## Design System Approach: Shared Patterns, Unique Identity

This batch uses a **partial design system** — structural patterns are shared, visual identity is unique per site.

### Shared Structural Patterns (the skeleton — copy from reference)

Every site follows this section order:
```
[Optional: industry-specific accent bar — like barbershop pole]
nav (fixed, scroll-effect)
hero (full-height, dark background, accent color, 2 CTAs)
[section-divider]
services (horizontal rows, NOT icon cards)
about (two-column: visual left, copy right)
[section-divider]
hours + location (two-column)
cta (bold call-to-action, centered)
footer ("Site by Handled" link)
```

Shared CSS conventions:
- `container` class: `max-width: 1100px; margin: 0 auto; padding: 0 24px`
- `reveal` / `visible` pattern for Intersection Observer scroll animations
- `section-label` (small caps, uppercase, 0.7rem) above `section-title`
- Fixed nav with `.scrolled` class on scroll (backdrop-filter blur)
- `h1,h2,h3,h4`: Oswald, uppercase, letter-spacing 0.04em
- `body`: Inter, dark background, light text

Shared JS (verbatim from reference — copy unchanged):
```javascript
// Nav scroll effect
const nav = document.getElementById('nav');
window.addEventListener('scroll', () => {
  nav.classList.toggle('scrolled', window.scrollY > 60);
});

// Scroll reveal
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry, i) => {
    if (entry.isIntersecting) {
      setTimeout(() => entry.target.classList.add('visible'), i * 80);
      observer.unobserve(entry.target);
    }
  });
}, { threshold: 0.15, rootMargin: '0px 0px -40px 0px' });

document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
```

### Unique Per Site (the skin — design fresh for each)

| Element | How to Differentiate |
|---|---|
| Color palette | `--primary`, `--accent`, `--dark`, `--light` — unique combination per business type and brand |
| Hero background | Gradient direction, color mix, CSS texture overlay unique to industry |
| Industry texture | CSS `repeating-linear-gradient` or `background-image` pattern representing the business (barber pole stripes, circuit traces, watercolor bleed, wood grain, etc.) |
| Accent bar | Optional top/bottom decorative strip — barbershop pole, measuring tape, leaf pattern, legal scales, etc. |
| Hero headline | Specific to the business's actual value prop — never a template |
| Hero body copy | Researched and specific — references real neighborhood, real service detail |
| Services | Real services pulled from WebFetch — actual names, accurate descriptions, real prices where available |
| About copy | Human, specific — references founding year, owner name, community angle |
| Stats | Real data pulled from research (years in business, number of clients, etc.) |
| Section divider icon | Industry-appropriate symbol (scissors, wrench, leaf, scales) |
| Photo | Real Unsplash image relevant to business type with overlay treatment |

### Palette Strategy by Business Type

Each industry has a natural palette territory — use it, then personalize within it:

| Industry | Palette Territory |
|---|---|
| Barbershop | Black, cream, gold — warm masculine |
| Electrical | Deep navy or charcoal, electric yellow, white |
| Plumbing | Dark slate, copper/bronze accent, cream |
| HVAC | Steel blue or dark teal, silver, white |
| Restaurant | Varies by cuisine — rich earthy for diners, botanical for upscale |
| Med Spa / Salon | Soft blush, sage, gold, or deep eggplant |
| Insurance / CPA / Law | Deep navy, forest green, or slate — trust palette |
| Landscaping | Deep forest green, earth brown, cream |
| Dental / Chiropractic | Clean white, deep teal or blue, soft warmth |

Never reuse the same palette across two sites in the same industry.

---

## Build Workflow: Per-Site Process

Each site follows this exact sequence. Duration estimate: 20-40 minutes per new build.

### Step 0: Locate in data sources
- Find business in master CSV: get URL, phone, contact name, type, priority
- Find in batch email file: get the specific sales hook (what angle is being used)

### Step 1: Research
- WebFetch their current website
- Extract: real services (with names and prices if listed), hours, address, tagline, founding year, staff names, unique differentiators
- Note: brand colors from their current site (may inform palette direction)
- Check logos/ directory for an existing logo file

### Step 2: Design concept
- Decide palette: primary bg, accent color, text color (3 values = full palette)
- Decide hero concept: what texture, what gradient, what headline
- Decide industry-specific accent element (top bar, divider icon, hero decoration)
- Decide photo concept: what Unsplash search term will get the right image

### Step 3: Build
- Copy structural skeleton from main-street-barbershop.html
- Replace all CSS custom properties with new palette
- Replace texture patterns with industry-specific version
- Build hero with real business name, real location, specific copy
- Build services section with real services from WebFetch
- Build about section with real history, real differentiators
- Build hours/location with real data
- Build CTA with real phone number
- Add logo if available: `<img src="logos/[slug]-logo.[ext]" alt="[Business Name] logo">`
- Verify mobile responsive (no changes needed — skeleton handles this)
- Count lines — stay under 320

### Step 4: Quality check
Before saving, verify:
- [ ] Every service is real (from WebFetch, not invented)
- [ ] Phone number is real (from CSV)
- [ ] Address is real (from WebFetch)
- [ ] Palette is unique (check other saved files)
- [ ] Industry texture is specific to this business
- [ ] Copy doesn't sound like a template
- [ ] No trust badge bars, no icon grids, no gradient blobs
- [ ] Footer: "Site by Handled" links to handledagency.co
- [ ] File is under 320 lines
- [ ] Mobile breakpoints are present

### Step 5: Save
- Save to: `~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/[slug].html`

---

## Build Order and Batching Strategy

### Order

Build in this exact sequence. Rationale: batch 1 emails go first; existing sites are polished in parallel with or immediately before the first new build to establish quality bar before the full run.

**Phase A: Polish existing 5** (before or concurrent with new builds)
- main-street-barbershop.html — reference quality, may need real Unsplash photos
- adw-plumbing.html
- murphy-electric.html
- affordable-hvac.html
- tc-cleaning.html

**Phase B: Priority 1 new builds** (4 sites with existing websites to research)
1. upstate-cpa.html — Timothy Moxie, accounting, http://upstatecpallc.com
2. skin-bar-med-spa.html — Helen Coquin, med spa, raisingtheskinbar.com
3. quality-electrical.html — Sherise Brown, electrical, qualityelectricalcontractors.org
4. cassy-electric.html — Cassandra Brayboy, electrical, cassyelectric.com

**Phase C: Priority 1 new builds** (3 sites with limited/no web presence)
5. ojs-diner.html — Greg Johnson, restaurant (no website — research via Google Maps/reviews)
6. fork-and-plough.html — Shawn Kelly, restaurant, forkandplough.com
7. compass-insurance.html — James Nittel, insurance, compassinsurance.biz

**Phase D: Priority 2 new builds** (11 sites)
8. smith-insurance.html
9. jason-yardscaping.html
10. local-plumber.html
11. to-dye-for-salon.html
12. eastergard-hvac.html
13. air-today-hvac.html
14. priority-heating-air.html
15. cb-smith-plumbing.html
16. ariail-law.html
17. charles-ford-cpa.html
18. wades-restaurant.html

### Batching within a session

Do not mix phases in a single session unless Phase A and Phase B can be started together. Within Phase B-D, build one site fully (research → design → build → save → verify) before starting the next. Do not start multiple builds simultaneously — quality drops when context splits.

---

## Patterns to Follow

### Pattern 1: CSS Custom Properties for the Palette
**What:** Declare all palette values as `:root` variables at the top of `<style>`
**When:** Every build
```css
:root {
  --dark: #0f1923;
  --primary: #1a3a5c;
  --accent: #d4a843;
  --light: #f0ece4;
}
```
This makes palette swaps surgical and readable. Change 4 lines, the whole theme changes.

### Pattern 2: Texture Overlay via Pseudo-Element
**What:** `::before` pseudo-element on section with `repeating-linear-gradient` or pattern
**When:** Hero, CTA, any section needing subtle texture
```css
.hero::before {
  content: '';
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    90deg, transparent, transparent 59px,
    rgba(212,168,67,.04) 59px, rgba(212,168,67,.04) 60px
  );
  pointer-events: none;
}
```
Industry-specific: vary angle, frequency, and opacity to evoke the business.

### Pattern 3: Service Row (not card)
**What:** Horizontal flex row with service name left, price right, left-border accent on hover
**When:** Every services section
This is the established pattern from the reference. Do not change it to cards.

### Pattern 4: Scroll Reveal
**What:** Add `.reveal` class to every visible section element; JS handles animation
**When:** Every build — copy JS verbatim from reference

### Pattern 5: Logo Integration
**What:** If logo file exists in `logos/`, include in nav alongside text name
**When:** adw-plumbing, murphy-electric, tc-cleaning have logos; check for others
```html
<a href="#" class="logo">
  <img src="logos/adw-plumbing-logo.jpg" alt="ADW Plumbing" style="height:36px;vertical-align:middle;margin-right:8px">
  ADW <span>Plumbing</span>
</a>
```

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Icon Card Grid for Services
**What:** 3-column grid of rounded cards with emoji/SVG icons
**Why bad:** Looks template-generated; doesn't differentiate; referenced in brief as explicit no
**Instead:** Horizontal service rows with left-border hover accent (see Pattern 3)

### Anti-Pattern 2: Trust Badge Bar
**What:** Row of checkmark badges ("Licensed, Insured, 5-Star Rated")
**Why bad:** Looks like a $10 WordPress theme; doesn't build trust, signals the opposite
**Instead:** Weave credibility into hero subhead and about copy organically

### Anti-Pattern 3: Gradient Blobs / Glowing Orbs
**What:** `radial-gradient` circles with blur, often purple/blue
**Why bad:** Trend-chasing; dates quickly; doesn't match the professional service businesses in this list
**Instead:** Subtle `repeating-linear-gradient` textures at 2-4% opacity

### Anti-Pattern 4: Color Swap Palette Reuse
**What:** Using the same palette on two sites in the same or similar industry
**Why bad:** Destroys the "built just for you" illusion
**Instead:** Maintain a running palette log as sites are built (track used palettes in session notes)

### Anti-Pattern 5: Invented Services
**What:** Listing services that weren't found in research
**Why bad:** Prospect spots it immediately; damages credibility
**Instead:** If WebFetch yields no service list, write 4-5 broadly accurate services for the business type and label them without prices

### Anti-Pattern 6: Emoji Icons in Section Dividers or Features
**What:** Using emoji as decorative icons (✓ ⭐ 🔧)
**Why bad:** Explicit anti-pattern from project brief
**Instead:** HTML entities for industry symbols (✂ ✦ ⚡ ⌖) or CSS-only decorative elements

---

## Quality Consistency Across 23 Sites

### The Reference Contract
main-street-barbershop.html is the quality floor. Every site must clear it. Before delivering any site, open both files side by side in a browser and ask: does the new site feel as intentional, as specific, as polished?

### The Specificity Test
Remove the business name from the file. Would the site still read as being for this specific business? If yes (because of industry-specific texture, real services, real address, real copy tone) — it passes. If it could be any business with a name swap — rebuild.

### The Line Budget
320 lines maximum. This constraint enforces discipline: it prevents CSS bloat, redundant sections, and over-engineering. If you hit 320, cut — don't expand the limit.

### Palette Logging
Track used palettes during the build run:

| Site | Primary BG | Accent | Note |
|---|---|---|---|
| main-street-barbershop | #111111 | #C8A96E | Black + gold |
| adw-plumbing | [check file] | [check file] | — |
| murphy-electric | [check file] | [check file] | — |

Before choosing a palette for a new site, verify it isn't a near-match to an existing site in the same industry cluster.

---

## Build Order Implications for Roadmap

- Phase A (polish existing 5) should be sequenced first — it calibrates the quality bar and reveals any structural patterns that need standardizing before the new-build run
- Phases B and C represent the highest-stakes sites (batch 1 contacts get emailed first) — quality here directly affects outreach results
- Phase D can tolerate slightly less research time per site since those contacts are outreached later, but quality floor still applies
- OJ's Diner (no website) requires a different research method (Google Maps, Yelp, Facebook) — flag as needing fallback research strategy before that build session

---

## Sources

- Project brief: `/Users/sullivancreativeco./cowork/handled-prospect-sites-brief.md`
- PROJECT.md: `/Users/sullivancreativeco./cowork/.planning/PROJECT.md`
- Reference implementation: `~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/main-street-barbershop.html`
- Master CSV: `~/Desktop/Claude Projects/Handled-Agency/leads/2026-03-25-greenville-spartanburg-prospects.csv`
- Batch files: `outreach-emails-batch1.md`, `outreach-emails-batch2.md`
- Architecture patterns: HIGH confidence — derived directly from existing codebase and explicit project constraints
