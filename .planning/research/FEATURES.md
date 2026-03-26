# Feature Landscape

**Domain:** Single-file prospect homepages — cold outreach tools for local business owners
**Researched:** 2026-03-25
**Confidence:** HIGH — grounded in direct analysis of 5 existing builds (main-street-barbershop, adw-plumbing, murphy-electric, affordable-hvac, tc-cleaning) and the validated anti-pattern list from the project brief

---

## Table Stakes

Features the prospect expects to see in a credible homepage. Missing any of these and the site fails the outreach purpose — the prospect won't take it seriously as a real deliverable.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Business name in nav + title | Instant recognition — they need to know it's built for them | Low | Name appears in `<title>`, logo/wordmark in nav. If logo exists in logos/ dir, use `<img>`. If not, styled wordmark in Oswald. |
| Real phone number (clickable `tel:` link) | Primary conversion goal for local service businesses; missing = site is dead on arrival | Low | Must appear in nav CTA, hero CTA, and footer CTA section. `href="tel:+1XXXXXXXXXX"` format. |
| Actual services listed | Proves the site was built specifically for them, not templated | Medium | Research via WebFetch before building. Services as horizontal rows, not icon cards. Each row: name + brief desc left, price/tag/note right. |
| Real address or service area | Hyperlocal signals — "Spartanburg, SC" in the hero tag reads as intentional, generic "Your City" reads as spam | Low | Source from WebFetch. Hero tag, footer, and location section all use real geography. |
| Mobile responsive | More than half of business owners view email on mobile; non-responsive = unprofessional | Medium | `viewport` meta, flex-wrap on hero buttons, single-column grid breakpoints at 768px and 600px. |
| Scroll animations (Intersection Observer) | 2026 baseline expectation — static pages feel like 2010 | Medium | `.reveal` class + IntersectionObserver. Stagger delay on multiple reveals (i * 80ms). No scroll libraries — vanilla JS only. |
| Fixed nav with scroll effect | Proves the site is interactive and "alive" | Low | `position:fixed` nav + `scrolled` class toggled at scrollY > 60. Backdrop blur when scrolled. |
| CTA section with large phone display | Direct response design — make the next step unmissable | Low | Full-width CTA section near bottom. Large Oswald phone number as `<a href="tel:...">`. Both a button AND the raw number displayed. |
| Footer with "Site by Handled" | Subtle agency credit — also tells the prospect this is a professional deliverable | Low | `Site by <a href="https://handledagency.co">Handled</a>`. Keep it small and subdued. |
| Under 320 lines | Practical constraint + discipline signal — site must be lean, not bloated | Low-Med | Single HTML file, all CSS embedded, minified where helpful without sacrificing readability. |
| Single self-contained HTML file | Deliverability — sent as attachment or hosted link; no build process, no dependencies | Low | No JS frameworks, no CDN libraries. Google Fonts is the only external call. |

---

## Differentiators

Features that make the prospect stop and go "this looks real." These are what separate a "wow, they built this for me" reaction from a "this is a template with my name on it" reaction.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Industry-specific CSS texture in hero | Makes the site feel designed for this exact business — barber pole stripes, pipe-fitting grids, electrical wire dashes | Medium | `repeating-linear-gradient` patterns in `::before` pseudo-elements. Must be semantically tied to the business type, not decorative noise. Each site gets a unique texture concept. |
| Business-specific accent color (not palette swaps) | Visual identity signals intentionality — a plumber in copper-brown vs a barbershop in gold reads as designed, not templated | Medium | Derive accent from brand research or industry convention. Define as CSS custom property (`--accent`). The entire palette must be unique per site — not just a hue rotation. |
| Ghost/watermark text element | Large, low-opacity Oswald text behind content sections creates editorial depth without imagery | Medium | `position:absolute`, `font-size: clamp(10rem, 22vw, 20rem)`, `opacity: .03-.06`. Can be a year ("SINCE '93"), brand word ("MURPHY"), or industry keyword. Parallax scrolling makes it feel premium. |
| Parallax background layer | Depth and motion without heavy JS libraries | Medium | Separate `hero-parallax` div with `will-change: transform`, moved at 40% scroll speed via `requestAnimationFrame`. Only in hero, not every section. |
| Industry-specific divider element | Replaces generic `<hr>` with something that signals the builder paid attention — barber pole, pipe fitting, electrical dash | Low | CSS-only. One line of markup. Strong signal of craft vs template. |
| Top accent stripe | Thin 3-4px bar at page top using industry-relevant pattern or gradient | Low | Barber pole repeating gradient, copper pipe gradient, dashed wire pattern. First thing eyes hit when the page loads. Sets tone immediately. |
| Human-sounding copy tied to the specific business | Difference between "Professional services for your home" and "Thirty-plus years, same phone number, same commitment" | High | Most labor-intensive differentiator. Requires WebFetching the real business, reading their history, then writing copy that could only apply to them. No template phrases. |
| Real statistics/social proof numbers | "30+ Years," "24/7," "Since 1972" — if real, these land harder than made-up badges | Low | Source from WebFetch. Use sparingly (2-3 stats max). Displayed in Oswald at 2-2.5rem in the about section. |
| Named owner/operator copy | "Call Bert" or "We started ADW in '93 because..." — name-drops make the copy human | Medium | Requires research. Only use if real name is findable. If owner is named, use first name in CTA ("Call Bert →"). |
| Logo integration (real asset) | If the prospect's logo is available, showing it in the nav proves the build is custom | Low | `<img src="logos/[slug]-logo.ext">` at 32-36px height in nav. No logo? Styled wordmark in Oswald instead. |
| Section-level layered shadows | Sections that feel like physical layers on a Z-axis — creates depth without 3D CSS | Low | `box-shadow: 0 -20px 60px rgba(0,0,0,.3)` on elevated sections. Combined with `z-index` stacking creates the illusion of panels lifting off the background. |
| Animated grid pulse on electrical/tech sites | Subtle `@keyframes` on the background grid — faint animation that rewards attention | Medium | Use only when industry-appropriate (electrical, tech, HVAC). `opacity` oscillating from .025 to .07. Not for every site — overuse kills the signal. |
| Staggered reveal from left/right on service rows | Services animate in from alternating directions — odd rows from left, even from right | Medium | `.reveal-left` / `.reveal-right` using `translateX`. Adds cinematic feel without complex GSAP-style libraries. Used in murphy-electric as a differentiator over basic vertical reveal. |
| CTA copy specific to the business | "Pipe Problem? Call ADW." vs "Contact Us Today" | Low | One line, but it's the difference between template and bespoke. Headline should name the business or the owner. |

---

## Anti-Features

Things that look "web-design-y" but signal template thinking to a business owner. These kill credibility.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| Trust badge bars ("Licensed ✓ Insured ✓ Bonded ✓") | Looks like a template component, not a designed element. Every budget site has this. Prospects recognize the pattern subconsciously. | Weave credibility into copy: "Licensed, insured electrical work with pricing you see before the job starts." |
| Icon grids with rounded card containers | The canonical cheap-website look — 6 icons in cards with a shadow. Business owners have seen this on every competitor's Wix site. | Services as horizontal rows: name + brief desc left-aligned, price or category tag right-aligned. Clean, editorial, specific. |
| Gradient blob / glow circle backgrounds | Purple-pink amorphous blobs in hero backgrounds. Signals "AI-generated landing page" immediately. | CSS `repeating-linear-gradient` textures that are industry-specific. Or real Unsplash photo with a heavy dark overlay. |
| Emoji icons used as visual elements | 🔧 🏠 ⚡ — read as amateur and informal in a business context | CSS symbols via HTML entities where appropriate (`&#9986;` for scissors, `&#9889;` for lightning). Industry-specific. Or no icon at all — strong typography carries. |
| Generic placeholder copy | "Your trusted [industry] professionals serving [city]" | Copy must include specific details: years in business, owner name if known, what makes this particular business different. |
| Cookie-cutter section order everyone uses | Hero → Features → Testimonials → Pricing → FAQ → Contact. The Wix/Squarespace default. | Hero → Services → About (with story) → Hours/Location → CTA. More specific to local service businesses. Testimonials require real social proof — skip if faked. |
| Form that submits nowhere | A contact form on an outreach demo page that doesn't actually work | Phone number CTAs only. Local business owners want to call, not fill out forms. Multiple `href="tel:..."` CTAs is the right pattern. |
| Multi-page navigation that goes nowhere | Links that lead to 404 or # when clicked | Three nav links max that anchor-scroll to real sections on the page. Everything in-page. |
| Testimonials with fake quotes | "Five stars!" — John D. | Omit entirely. Real testimonials require real research. If WebFetch surfaces a real Google review, use it. Otherwise skip the section. |
| Stock photo without overlay | Unsplash photos at full brightness look cheap and feel unowned | Unsplash photos must have a heavy dark overlay: `linear-gradient(160deg, rgba(0,0,0,.92), rgba(0,0,0,.80))` minimum. The photo provides texture, not the subject. |
| Rounded corners everywhere | `border-radius: 12px` on every card, button, container — the Tailwind "safe defaults" look | `border-radius: 2px` on buttons only. Service rows: no radius. About visual: 4px at most. Sharp edges read as intentional and editorial. |
| Shadow + border + backdrop-filter stacking on every element | Glassmorphism everywhere — when everything glows, nothing does | Reserve frosted glass (`backdrop-filter: blur()`) for one or two elements per site. Nav when scrolled, and one about-visual card at most. |
| Color swap of an existing site | Same layout, same structure, different hex values | Each site needs a distinct texture concept, palette, and at least one unique structural element that reflects the industry. The divider, the ghost text, the stripe — all must be invented fresh. |

---

## Feature Dependencies

```
Real business info (WebFetch) → Actual services list
Real business info (WebFetch) → Human-sounding copy
Real business info (WebFetch) → Real phone number in tel: links
Real business info (WebFetch) → Named owner/operator copy (conditional)
Real business info (WebFetch) → Real statistics/social proof numbers

Industry choice → CSS texture concept (must be derived from industry)
Industry choice → Accent color (must reflect brand or industry convention)
Industry choice → Accent stripe pattern (must be industry-specific)
Industry choice → Ghost/watermark text content (year, brand word, or keyword)

Accent color → All hover states, section labels, stat numbers, CTA button
Accent color → Section divider element color
Accent color → nav-cta button background

Logo availability (logos/ dir) → Nav logo type (img vs Oswald wordmark)

Parallax layer → requestAnimationFrame scroll handler
Parallax layer → Hero section must use .hero-parallax child div, not background on .hero itself

Staggered left/right reveals → Two IntersectionObserver instances (standard + stagger)
Staggered left/right reveals → .reveal-left and .reveal-right classes + transitionDelay via JS

Section-level shadows → z-index stacking on sections (z:1, z:2, z:3 ascending order from bottom)
```

---

## MVP Recommendation

For each prospect homepage, build in this order:

1. **Real phone + address + services (WebFetch first)** — Without this, nothing else matters. Research before touching HTML.
2. **Unique palette + CSS texture concept** — Decide on accent color and industry texture before writing a line of CSS. This drives every visual decision downstream.
3. **Industry-specific top stripe + hero with texture** — The first 3 seconds of impression. Hero must have the business name, location, and phone CTA above the fold.
4. **Services as horizontal rows** — The highest-signal section. Shows this is built for this business specifically.
5. **About section with real copy** — Must name the owner or year founded if findable. This is where "wow this looks real" happens for the prospect.
6. **Hours/Location + CTA section** — Table stakes sections. Straightforward to build once data is sourced.
7. **One differentiating motion technique** — Pick one: parallax hero, ghost text, staggered reveals, animated grid, or parallax about-bg-text. Not all of them — one, executed well.

**Defer:**
- Testimonials: Skip unless real quotes are findable via WebFetch (Google reviews, etc.)
- Multiple motion techniques: Each technique used once is premium; stacked everywhere is noise
- Unsplash photos: Use only when the photo adds something CSS gradient cannot. Every photo needs a heavy overlay. CSS-only is faster and often cleaner for B2B trades.

---

## Sources

- **Primary:** Direct code analysis of `main-street-barbershop.html`, `adw-plumbing.html`, `murphy-electric.html`, `affordable-hvac.html` — all in `~/Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/`
- **Project brief:** `/Users/sullivancreativeco./cowork/handled-prospect-sites-brief.md`
- **Project config:** `/Users/sullivancreativeco./cowork/.planning/PROJECT.md`
- **Confidence:** HIGH — findings are drawn from the actual builds in this codebase, not external sources. Anti-patterns are validated from the project's own documented lessons learned.
