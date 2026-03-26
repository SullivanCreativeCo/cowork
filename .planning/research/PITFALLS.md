# Domain Pitfalls: Batch Prospect Homepage Builds

**Domain:** 23 personalized single-HTML prospect homepages for cold email outreach
**Researched:** 2026-03-25
**Confidence:** HIGH — based on direct analysis of the 5 existing builds (main-street-barbershop.html, adw-plumbing.html, affordable-hvac.html, tc-cleaning.html, murphy-electric.html) plus domain-specific constraints

---

## Critical Pitfalls

Mistakes that cause a site to fail its primary job: making a prospect feel seen.

---

### Pitfall 1: Design Homogeneity — The "Color Swap" Problem

**What goes wrong:** Sites share an identical structural skeleton (same hero layout, same section order, same CSS class names and values) with only the accent color and business name changed. The prospect opens the site and it reads as a template, not a custom design. The "wow, they built this for ME" reaction never happens.

**Why it happens:** The first build sets a CSS pattern (`.service-row`, `.about-grid`, `.hero-tag::before`, `.section-divider`). Every subsequent site copies the same structure to save time. By site 5, the CSS variables are the only thing changing.

**Consequences:** The core value proposition collapses. The Loom comparison fails — if the site looks like a generic template, the prospect's existing site doesn't look that much worse by comparison. Cold email conversion drops. The pitch becomes "we built you a template" not "we built you a site."

**Warning signs:**
- You open two completed files side-by-side and the section structure maps 1-to-1
- The `::before` CSS textures are the same pattern (e.g., `repeating-linear-gradient` at 80px intervals) across multiple sites
- The hero uses the same `clamp()` sizes and the same `hero-tag` + `hero-ghost` combination
- Every service section uses the same `services-grid` with `repeat(auto-fit, minmax(300px,1fr))`
- The about section always has the same `about-grid` with a 4/5 aspect-ratio visual on the left

**Prevention:**
- Before writing a single line of CSS, define the unique design concept in a one-line brief: "Blueprint-print technical feel with light background and dark-blue details" (for an electrical inspector) vs. "Warm kitchen light, cream and terracotta, handwritten-adjacent copy" (for a diner).
- Each site needs at least three structural differentiators from the previous one: different section order, different service display pattern (rows vs. stacked categories vs. menu-style), different hero layout (full-bleed photo vs. CSS texture vs. split layout).
- Industry dictates texture. Barber pole stripe is earned by a barbershop. A CPA firm should not inherit plumbing pipe grid textures. Map business type → texture before opening the editor.
- Reserve the `services-grid` with `border-left` hover pattern only for trade businesses (plumbing, electric, HVAC). Service businesses (salon, diner, med spa) need entirely different service presentation.

**Phase address:** Phase 1 (template bootstrap). Lock the per-site design brief before building.

---

### Pitfall 2: Generic Copy — Writing for the Category, Not the Business

**What goes wrong:** Hero copy describes what the business type does in general ("Trusted HVAC service for Greenville and Spartanburg") rather than what makes this specific business matter ("40 years under the same name, same phone number — George Eastergard Sr. still takes the call"). It reads like the prospect's own website, just with better fonts.

**Why it happens:** WebFetch returns service lists, phone numbers, and taglines. Pressure to ship 23 sites fast means the fetched content gets paraphrased into the template without transformation. The copy sounds professional but anonymous.

**Consequences:** Prospect reads the site and thinks "this could be anyone." The emotional hook — "someone actually researched my business and wrote about me specifically" — never fires. The Loom recording loses its persuasive edge.

**Warning signs:**
- The hero paragraph mentions only service categories with no specific detail (no year founded, no owner name, no neighborhood, no signature service)
- The about section says "family-owned and operated" without naming the family or saying what that means operationally
- Any sentence in the copy could survive unchanged if you swapped the business name for a competitor's

**Prevention:**
- During WebFetch, hunt for: founding year, owner name, signature service or specialty, a community tie (e.g., James Beard nomination, Bellsouth.net email meaning they've been around since dial-up), service area specificity (not "Upstate SC" — "Simpsonville and the surrounding 864").
- Write the hero headline about the most distinctive fact: "70 Years, One Name" (Smith Insurance) or "Famous Fried Chicken Since 2005" (OJ's Diner) or "The Only Landscaper in Greenville Who Picks Up on the First Ring" (Jason Yardscaping).
- The about section must include at least one concrete detail that could only apply to this business: a crew member's name, the year they moved to their current building, a specific client type they serve. See the Affordable HVAC build — it names the crew: "Drake, Chris, Chase, Chandler, Dex."
- Apply the substitution test before shipping: can you replace the business name with a competitor's without the copy falling apart? If yes, rewrite.

**Phase address:** Phase 1 (per-site research), Phase 2 (copy draft). Run substitution test as a gate before any site is marked complete.

---

### Pitfall 3: Unsplash Image Failures — Broken Photos in Production

**What goes wrong:** Unsplash photo URLs embedded in the HTML become unavailable, redirect to a different image, or load an NSFW/off-topic photo (Unsplash's redirect behavior when a photo is removed). The site the prospect sees has a broken hero background or, worse, an irrelevant image.

**Why it happens:** Unsplash direct-link URLs (`images.unsplash.com/photo-...?w=1920&q=80`) are stable but not permanent. Photos get removed by photographers, redirected, or replaced. A URL that worked at build time may not work at send time. Additionally, when building 23 sites rapidly, photo selection can be rushed and the query match can be weak (e.g., an HVAC unit photo used for a law firm).

**Consequences:** The prospect's Loom recording shows a broken or irrelevant hero image. The site's professionalism collapses instantly. The "your current site vs ours" comparison goes the wrong way.

**Warning signs:**
- Using Unsplash search terms that are too broad ("business," "office," "work") — these return generic stock photos
- Not verifying the image loads in a browser at the actual URL before embedding
- Using the same Unsplash photo across multiple sites (lazy reuse)
- Using hero background images that don't relate to the specific business type

**Prevention:**
- After embedding an Unsplash URL, open it in a browser tab and verify it loads the expected image before moving on.
- Use industry-specific search terms: "barbershop interior," "plumber under sink," "fried chicken plate," "hvac technician rooftop" — not "professional service."
- Add a CSS fallback background color to every `background:url()` declaration so if the image fails, the section degrades to the dark palette color and remains readable.
- Never reuse the same Unsplash photo across two sites.
- For businesses with no obvious visual category (CPA, insurance, law firm), use abstract but thematically relevant images: architectural details, downtown building exteriors, desk/workspace shots with strong light.

**Phase address:** Phase 2 (per-site build). Verify URLs as a final checklist item before marking a site done.

---

### Pitfall 4: Production Fatigue — Quality Drops After Site 10+

**What goes wrong:** The first 5-7 sites receive careful attention to design differentiation, copy specificity, and texture work. By site 12, shortcuts appear: CSS textures are copy-pasted with only the color changed, copy is templated, hero photos are chosen in 30 seconds, and the section dividers are all identical. By site 18, the work has regressed to exactly what the brief prohibits.

**Why it happens:** 23 is a large batch for a creative production task. Cognitive load is high — researching 23 different businesses, designing 23 distinct palettes, writing 23 sets of human copy. Decision fatigue makes the familiar pattern (the established CSS skeleton) irresistible.

**Consequences:** An inconsistent portfolio where sites 1-8 are impressive and sites 15-23 are generic. If prospects from batch 2 compare notes, the quality gap undermines Handled's credibility.

**Warning signs:**
- A site's CSS block is under 60 lines (not enough differentiation from a minimal template)
- The hero parallax background is `linear-gradient` with the same palette as two previous sites
- The business's CSS texture is a repeating-linear-gradient at the same interval as a previous site
- Copy writing time drops below 10 minutes per site (not enough research is happening)
- Two consecutive sites have the same section structure

**Prevention:**
- Work in batches of 4-5 with a quality gate between batches: review all completed sites in browser tabs simultaneously and confirm no two look like variations of the same layout.
- Keep a "used" list: track every CSS texture type (barber stripe, pipe grid, blueprint grid, herringbone, crosshatch, diagonal stripe, dot matrix) and every structural variation used. Force rotation before reuse.
- For Priority 2 sites (sites 12-23 by order), allocate more WebFetch research time, not less — these are the ones most at risk of becoming generic.
- If energy is low, ship the HTML skeleton and save copy writing for a fresh session. Generic copy is harder to fix later than generic CSS.

**Phase address:** Phase 3 (batch production). Implement quality check after every 4th site.

---

## Moderate Pitfalls

---

### Pitfall 5: Mobile Breakpoint Failures — Nav and Hero Breaking on Small Screens

**What goes wrong:** The nav hides desktop links at `max-width:768px` correctly, but leaves no mobile alternative (no hamburger, no phone-number-only nav, no sticky CTA bar). The hero `clamp()` text scales down but the hero `min-height:100vh` becomes too tall on mobile, trapping the user above the fold with no visible CTA.

**Why it happens:** Development and review happens on desktop. Mobile is checked quickly in DevTools but not tested on actual viewport sizes. The `hero-buttons` flex-wrap handles button stacking but the overall section padding (`padding:100px 0`) becomes disproportionate on mobile.

**Warning signs:**
- Hero section is taller than two phone screens on mobile
- CTA button is below the visible fold without scrolling on iPhone SE viewport (375px)
- Nav is completely empty on mobile (logo + nothing) with no fallback interaction

**Prevention:**
- Every site gets a 375px viewport check before completion: can the user see a headline, a subhead, and a CTA button without scrolling?
- Add `@media(max-width:600px){ .hero{min-height:auto;padding:120px 0 80px} }` to the mobile CSS block — don't rely solely on viewport height on mobile.
- The phone number in the nav CTA should remain visible on mobile even when the nav links are hidden. If the nav collapses to logo-only on mobile, add a standalone mobile CTA bar (`position:fixed;bottom:0`) with the phone number.
- Test specifically at 375px (iPhone SE/14 Pro) and 390px (iPhone 14) — these expose the most common breakage.

**Phase address:** Phase 2 (per-site build). Add to definition of done checklist.

---

### Pitfall 6: Line Count Creep — Exceeding 320 Lines

**What goes wrong:** CSS depth techniques (layered shadows, parallax layers, frosted glass) and thorough service lists push files over 320 lines. The constraint exists for a reason — bloated files indicate scope creep, embedded redundancies, or overly complex CSS that could be simplified.

**Why it happens:** Each additional CSS texture, each box-shadow layer, each `@keyframes` animation adds 2-6 lines. It compounds quickly when building 8-service businesses with grouped service categories.

**Warning signs:**
- CSS block exceeds 200 lines (leaves under 120 for HTML and JS)
- Multiple `@keyframes` declarations in a single file
- Identical CSS patterns duplicated across multiple selectors instead of sharing a class

**Prevention:**
- Write CSS minified (no indentation, semicolons on same line) — the existing builds do this correctly and a minified CSS block with full design depth fits in ~100-150 lines.
- If a service list is long (8+ items), use the grouped category pattern (see affordable-hvac.html) rather than individual cards — more semantic content, fewer HTML lines.
- `@keyframes` should be reserved for one signature animation per site maximum.
- Before adding a depth effect, ask: does this element need a box-shadow AND a gradient AND a border? Usually one of the three achieves the effect.

**Phase address:** Phase 2 (per-site build). Line count check before file save.

---

### Pitfall 7: Palette Collision — Two Sites Sharing the Same Visual Register

**What goes wrong:** Two sites in the same batch share the same color temperature and lightness register — e.g., both use dark navy + electric blue accent, or both use charcoal + warm gold. Even with different hex values, the visual feel is indistinguishable to a prospect.

**Why it happens:** Service industry businesses cluster into categories that suggest similar colors: electrical = yellow, plumbing = blue, HVAC = blue, legal = navy. With three HVAC companies and two plumbing companies in this batch, the default-industry-color impulse produces near-identical palettes.

**Warning signs:**
- Two files share the same background darkness level and accent hue family (e.g., both are dark-background + blue accent)
- Palette planning wasn't done before building (choosing color while writing CSS)

**Prevention:**
- Before building any site, map all 23 business types to a color direction and verify no two adjacent priority slots share the same temperature + lightness pair.
- For same-industry multiples (3 HVAC companies, 2 plumbing companies), actively force differentiation: Eastergard HVAC could use warm amber + dark brown (45 years of legacy); Air Today Heating could use cool steel blue + white (3-generation modernity); Priority Heating could use forest green + cream (Spartanburg community identity).
- Palettes don't follow industry convention. A law firm can be warm and inviting. A restaurant can be architectural and dark. Use the business's actual story as the palette brief, not the industry category.

**Phase address:** Phase 1 (template planning). Create palette plan for all 23 before building site 1.

---

### Pitfall 8: WebFetch Failures — Building on Stale or Missing Data

**What goes wrong:** WebFetch returns a 404, a redirect to a parking page, or a minimal page with no useful content (phone number listed but no services, hours, or about content). The site gets built with placeholder or assumed data that may be wrong, or not specific enough to pass the substitution test.

**Why it happens:** Seven of the 23 prospects have domain/name mismatches, .biz or .org domains, or no website at all (OJ's Diner, Fork and Plough, Compass Insurance). HTTP sites may redirect poorly. The Skin Bar has a domain mismatch (`raisingtheskinbar.com` vs "The Skin Bar Med Spa").

**Warning signs:**
- WebFetch returns a page under 500 words with no service list
- The fetched content mentions only a phone number and address
- The domain being fetched doesn't match the business name in the brief

**Prevention:**
- For businesses with no website (OJ's Diner, Fork and Plough, Compass Insurance): run a Google search for the business name + city to find Google Business Profile data, Yelp listings, Facebook pages, or local news mentions before inventing services.
- For domain mismatches (The Skin Bar): fetch both the email domain and any alternate domain found via search.
- If a WebFetch returns under 200 words of usable content, that's a signal to search for secondary sources (Google Maps, Yelp, Facebook) before building.
- Services invented without verification must be clearly marked in the HTML as `<!-- VERIFY -->` comments so they can be confirmed before the outreach email sends.

**Phase address:** Phase 1 (per-site research). Secondary source fallback is mandatory, not optional.

---

## Minor Pitfalls

---

### Pitfall 9: Placeholder Phone Numbers Left in Production Files

**What goes wrong:** `(864) 555-0000` or `tel:+18645550000` gets embedded in a file because the real number wasn't found during the initial build and wasn't flagged for review. The Loom recording shows a fake phone number. The prospect notices.

**Note:** The current main-street-barbershop.html has multiple `<!-- TODO: Replace (864) 555-0000 with real phone number -->` comments — this is a correctly flagged but unfixed instance.

**Prevention:**
- If the phone number can't be found via WebFetch, search Google Business Profile before substituting a placeholder.
- If a placeholder must be used, add a `<!-- VERIFY: phone number -->` HTML comment AND add the site to a "needs verification" list tracked outside the HTML.
- Do not mark any site as complete while it contains a `555-` phone number.

**Phase address:** Phase 2 (per-site build). Goes in the definition-of-done checklist.

---

### Pitfall 10: Emoji Characters Slipping In as Section Dividers or Icons

**What goes wrong:** The `&#9986;` scissors emoji divider in the Main Street Barbershop file is a deliberate, business-specific character (it's a scissors symbol, not a generic emoji). The pitfall is generalizing this pattern — using generic emoji (stars, lightning bolts, checkmarks) as section ornaments or service icons in other sites.

**Prevention:**
- The section divider character must be industry-specific or not present at all. Scissors for a barbershop is earned. A wrench for a plumber is fine. A star or checkmark for anything is prohibited.
- If there's no obvious industry-specific Unicode symbol, use the `section-divider` CSS line pattern (the `::before`/`::after` gradient lines) without any character.
- No emoji in service names, headings, or CTAs regardless of business type.

**Phase address:** Phase 2 (per-site build). Code review check.

---

### Pitfall 11: Intersection Observer Animation Stacking — All Elements Reveal Simultaneously

**What goes wrong:** When many `.reveal` elements are on the page simultaneously (e.g., a 10-item service list), the `setTimeout(() => ..., i * 80)` stagger is calculated from a single `entries` array index. If the browser fires the observer with all elements in a single batch, all items get `i * 80` from the same `i` counter and stagger correctly. But if sections are close together and enter the viewport at the same time, the stagger can compress and everything pops in together.

**Prevention:**
- Use `rootMargin: '0px 0px -40px 0px'` on the observer (already present in existing builds) — this creates a buffer that prevents the viewport from triggering all elements at once.
- Keep the `.reveal` class to major elements (section headings, service blocks, stat groups) rather than individual list items. Applying it to each `<li>` in a 10-item list amplifies the stagger problem.
- If a service list has more than 8 items, group them and animate the group container, not each row.

**Phase address:** Phase 2 (per-site build). Standard JS pattern is already correct — don't over-apply the `.reveal` class.

---

### Pitfall 12: Google Fonts Load Performance on Slow Mobile Connections

**What goes wrong:** The `<link>` to Google Fonts is a render-blocking resource. On a slow mobile connection (which is common for small business owners checking email on the go), the site can appear blank or unstyled for 1-2 seconds. If the prospect opens the site in a Loom recording on their phone, the fonts flicker in, which looks amateur.

**Prevention:**
- Add `&display=swap` to the Google Fonts URL (already present in existing builds: `display=swap`). This is the correct mitigation — verify it's present in every file.
- Preconnect to the Fonts server: add `<link rel="preconnect" href="https://fonts.googleapis.com">` and `<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>` before the stylesheet link.
- Both preconnect tags fit within the line count budget if the existing builds' pattern is followed.

**Phase address:** Phase 2 (per-site build). Add preconnect tags to the standard `<head>` pattern.

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|---|---|---|
| Phase 1: Per-site design brief | Color palette collision between same-industry sites | Map all 23 palettes before building site 1; enforce no same temperature/hue pair |
| Phase 1: WebFetch research | Stale/missing data for no-website businesses | Mandatory secondary search (Google Business, Yelp, Facebook) before building |
| Phase 2: First 5 builds | Locking in a structural template too early | Define 3 structural variations before starting; rotate between them |
| Phase 2: CSS texture design | Same texture pattern reused with only color change | Maintain a "used textures" log; require a different pattern type per site |
| Phase 2: Copy writing | Generic category copy passing review | Apply the substitution test to every hero paragraph before marking complete |
| Phase 2: Photo selection | Unsplash URL returning wrong/broken image | Browser-verify every Unsplash URL before embedding |
| Phase 2: Mobile QA | Hero overflow and nav collapse failures | Test at 375px viewport before marking any site complete |
| Phase 3: Sites 10-23 | Production fatigue + quality regression | Mandatory batch review every 4 sites; all completed sites open simultaneously |
| Phase 3: Priority 2 batch | These get less time but carry equal risk | Pre-plan design concepts for all Priority 2 sites before Priority 1 is complete |
| Final QA: All 23 | Placeholder phone numbers shipping | Verify no `555-` phone numbers; all TODOs resolved before outreach emails send |

---

## Sources

**Confidence: HIGH** — all pitfalls derived from direct code analysis of the 5 existing builds:
- `/Users/sullivancreativeco./Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/main-street-barbershop.html` — reference quality bar, active TODO comments on phone numbers
- `/Users/sullivancreativeco./Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/adw-plumbing.html` — demonstrates correct differentiation: copper palette, pipe-rule divider, ghost text
- `/Users/sullivancreativeco./Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/affordable-hvac.html` — demonstrates crew naming in copy, grouped service categories, Unsplash hero photo pattern
- `/Users/sullivancreativeco./Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/tc-cleaning.html` — light-background variant (linen palette), demonstrates structural variation is possible
- `/Users/sullivancreativeco./Desktop/Claude Projects/Handled-Agency/leads/prospect-sites/murphy-electric.html` — animated grid pulse, reveal-left/reveal-right directional animations
- `/Users/sullivancreativeco./cowork/handled-prospect-sites-brief.md` — project brief, known anti-patterns, business list with identifying details
- `/Users/sullivancreativeco./cowork/.planning/PROJECT.md` — validated constraints and out-of-scope items
