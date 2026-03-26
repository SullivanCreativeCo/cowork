# Handled Agency — Prospect Homepage Builds

## What This Is

23 personalized prospect homepages for cold email outreach targeting real businesses in Greenville/Spartanburg, SC. Each homepage uses the business's actual name, phone, services, address, and logo — built as a single HTML file with embedded CSS, real Unsplash photography, and a distinct visual identity. The goal: show each prospect what a modern site looks like so they book a call with Handled.

## Core Value

Every prospect homepage must feel like it was designed specifically for that business — unique palette, textures, real photos, and copy that sounds human. **If a prospect could swap their name for another business and the site still works, it's not good enough.**

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] All 23 sites built to Main Street Barbershop quality standard
- [ ] 5 existing sites polished up to quality bar (modern, photos, depth, non-generic)
- [ ] 18 new sites built from scratch with web-researched business info
- [ ] Each site has its own distinct color palette and CSS texture concept
- [ ] Real Unsplash photos relevant to each business type with overlays
- [ ] Oswald headlines + Inter body typography hierarchy
- [ ] Depth techniques: layered shadows, parallax, overlapping panels, frosted glass
- [ ] Services displayed as clean horizontal rows (no icon card grids)
- [ ] Copy that sounds human and specific to the business
- [ ] Subtle CSS textures specific to each business's industry
- [ ] Each site WebFetched for real services, hours, address, branding details
- [ ] Logos integrated where available (from logos/ directory)
- [ ] Single HTML file per site, all CSS embedded
- [ ] Google Fonts only external dependency (Oswald + Inter)
- [ ] Mobile responsive
- [ ] Intersection Observer scroll animations
- [ ] Under 320 lines per file
- [ ] "Site by Handled" footer linking to handledagency.co
- [ ] All files saved to ~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/

### Out of Scope

- Multi-page sites — single homepage only, this is outreach collateral
- Backend functionality — static HTML only, no forms that submit
- JavaScript frameworks — vanilla JS for scroll animations only
- Placeholder/stock imagery without overlays — all photos get overlay treatment
- Cookie-cutter layouts — if it could be any business with a color swap, rebuild it

## Context

- **Outreach strategy:** Build their actual homepage before reaching out, attach Loom recording comparing their current site vs ours
- **Quality reference:** Main Street Barbershop at ~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/main-street-barbershop.html
- **5 existing sites:** adw-plumbing, murphy-electric, affordable-hvac, tc-cleaning, main-street-barbershop
- **Data sources:** Master CSV (2026-03-25-greenville-spartanburg-prospects.csv), email batches (outreach-emails-batch1.md, batch2.md), outreach strategy, brand reference
- **Logos directory:** ~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/logos/
- **Anti-patterns learned:** No trust badge bars, no icon grids with rounded cards, no gradient blobs/glowing circles, no emoji icons

## Constraints

- **File size**: Under 320 lines per HTML file
- **Dependencies**: Google Fonts only (Oswald + Inter) — no CDN libraries
- **Images**: Real Unsplash photos with CSS overlays, no placeholder images
- **Format**: Single self-contained HTML file per business
- **Design**: Each site must have a distinct visual identity — unique palette + texture per business
- **Output path**: ~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Web research each business | Maximum accuracy for services, hours, branding details | — Pending |
| Real Unsplash photos (not CSS-only) | More polished, professional feel that impresses prospects | — Pending |
| Polish existing 5 to quality bar | Consistency matters — all 23 should feel premium | — Pending |
| Main Street Barbershop as reference | Best of the 5 existing builds — proven design direction | — Pending |
| Priority 1 before Priority 2 | Batch 1 emails go out first, build those sites first | — Pending |

---
*Last updated: 2026-03-25 after initialization*
