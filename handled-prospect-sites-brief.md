# Handled Agency — Prospect Homepage Builds

## Project Brief for GSD

### What We're Building
23 personalized prospect homepages for cold email outreach. Each homepage is built for a real business in Greenville/Spartanburg, SC — using their actual name, phone, services, and logo. The goal is to show them what a modern site looks like so they book a call with Handled.

### Why
This is the outreach strategy from the AI Agency Playbook: "Do the work first — build their actual homepage before reaching out." Each homepage gets attached to a cold email as a Loom recording (their current site vs ours side by side).

### Design Quality Bar
5 sites have already been built. The best one is **Main Street Barbershop** — use it as the reference:
`~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/main-street-barbershop.html`

**What works about it:**
- Oswald headlines + Inter body (strong typography hierarchy)
- Dark palette with one accent color (black/cream/gold)
- Subtle CSS textures specific to the business (barber pole stripe, pinstripes)
- Services as clean horizontal rows, NOT bloated icon cards
- Copy that sounds human and specific to the business
- Parallax, layered sections with shadows, overlapping elements for depth

**What to avoid (learned the hard way):**
- No trust badge bars with checkmarks
- No icon grids with rounded cards
- No gradient blobs or glowing circles
- No cookie-cutter layouts that all look identical
- No emoji icons
- If it could be any business with a color swap, it's not good enough

### Tech Constraints
- Single HTML file per site, all CSS embedded
- Only external dependency: Google Fonts (Oswald + Inter)
- No placeholder images — use CSS gradients, textures, patterns, or real Unsplash photos with overlays
- Mobile responsive
- Intersection Observer scroll animations
- Under 320 lines per file
- Depth techniques: layered section shadows, parallax backgrounds, overlapping panels, frosted glass where appropriate
- Each site gets its own distinct palette and CSS texture — no color swaps

### Output Location
`~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/`
Logos already downloaded to: `~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/logos/`

### Data Sources
- **Master prospect list:** `~/Desktop/Claude Projects/Handled-Agency/leads/2026-03-25-greenville-spartanburg-prospects.csv`
- **Email batch 1 (Priority 1):** `~/Desktop/Claude Projects/Handled-Agency/leads/outreach-emails-batch1.md`
- **Email batch 2 (Priority 2):** `~/Desktop/Claude Projects/Handled-Agency/leads/outreach-emails-batch2.md`
- **Outreach strategy:** `~/Desktop/Claude Projects/Handled-Agency/leads/OUTREACH-STRATEGY.md`
- **Brand reference:** `~/Desktop/Claude Projects/Handled-Agency/HANDLED-BRAND.md`

### Sites Already Built (5)
These exist and need polish but don't need to be rebuilt from scratch:
1. `adw-plumbing.html` — ADW Plumbing, Spartanburg (has logo)
2. `murphy-electric.html` — Murphy Electric, Moore SC (has logo)
3. `affordable-hvac.html` — Affordable HVAC, Woodruff SC
4. `tc-cleaning.html` — TC Cleaning, Spartanburg (has logo)
5. `main-street-barbershop.html` — Main Street Barbershop, Spartanburg (quality reference)

### Sites To Build (18 remaining with emails)

**Priority 1 (4 more):**
| Business | Type | Contact | Email | Key Detail |
|---|---|---|---|---|
| Upstate Professional CPA | accounting | Timothy Moxie | admin@upstatecpallc.com | HTTP site, Greer SC |
| The Skin Bar Med Spa | med spa | Helen Coquin | hello@raisingtheskinbar.com | Domain/name mismatch, Greenville |
| Quality Electrical Contractors | electrical | Sherise Brown | sherise@qualityelectricalcontractors.org | .org domain, since 1972 |
| Cassy Electric | electrical | Cassandra Brayboy | info@cassyelectric.com | Woman-owned, Greenville |

**Priority 1 (no website — build from scratch):**
| Business | Type | Contact | Email | Key Detail |
|---|---|---|---|---|
| OJ's Diner | restaurant | Greg Johnson | ojsdiner@bellsouth.net | Famous fried chicken, since 2005 |
| Fork and Plough | restaurant | Shawn Kelly | catering@forkandplough.com | Locally sourced, daily menu changes |
| Compass Insurance | insurance | James Nittel | Info@compassinsurance.biz | .biz domain, since 2008, Spartanburg |

**Priority 2 (7 with emails):**
| Business | Type | Contact | Email | Key Detail |
|---|---|---|---|---|
| The Smith Insurance | insurance | Dwight Smith | dwight@thesmithinsurance.com | 70 years, Greenville |
| Jason Yardscaping | landscaping | Jason | jason@jasonyardscaping.com | Owner-operator, Greenville |
| Local Plumber LLC | plumbing | Grant Rhodes | localplumberllc@gmail.com | Simpsonville |
| To Dye For Salon | salon | Kristy Ciarla | kristy.ciarla@todyeforsalon.com | Downtown GVL since 2001 |
| Eastergard HVAC | HVAC | George Eastergard Sr. | service@eastergard.com | 35+ years family-owned |
| Air Today Heating | HVAC | Frank Taylor | sales@iwantairtoday.com | 3 generations, 40+ years |
| Priority Heating | HVAC | Kendall Smith | priority@priorityheatingandairhvac.com | 35 years, Spartanburg |

**Priority 2 (4 more with emails):**
| Business | Type | Contact | Email | Key Detail |
|---|---|---|---|---|
| CB Smith Plumbing | plumbing | Revill MacGregor | revill@cbsmithplumb.com | 35+ years, Spartanburg |
| R. Mills Ariail Jr. | law firm | R. Mills Ariail Jr. | mills@rmalawoffice.com | Solo practice, Greenville |
| Charles A. Ford CPA | accounting | Charles A. Ford | office@charlesfordcpa.com | Small practice, Greenville |
| Wade's Restaurant | restaurant | Lindsey family | comments@eatatwades.com | 1947, James Beard 2024 |

### Process
For each site:
1. WebFetch their current website to get services, hours, address, branding details
2. Download their logo if available
3. Design a unique palette + texture concept for their industry
4. Build the HTML with real business info, depth effects, and personality
5. Save to prospect-sites folder

### Definition of Done
- 23 total prospect sites built (18 new + 5 existing)
- Each has its own distinct visual identity
- Each uses the business's real info (name, phone, services, address)
- Each is mobile responsive and under 320 lines
- Each has "Site by Handled" in the footer linking to handledagency.co
- All files in `~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/`
