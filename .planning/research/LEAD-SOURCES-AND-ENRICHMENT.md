# Lead Sources & Data Enrichment Research

> Research date: 2026-03-25

---

## Part 1: Finding Businesses Without Websites

### 1A. Google Places API (New) — Primary Source

The Google Places API (New) is the best primary data source. The `websiteUri` field is returned when present; **if it is null/missing, the business has no website**.

**How it works:**

1. Call `places:searchText` with a query like `"plumber in Austin TX"`
2. Include `websiteUri` in the FieldMask
3. Iterate results — if `websiteUri` is absent/null, that business is a prospect
4. Call `places/{placeId}` for full details on qualifying leads

**Key fields available (exact API field names):**

| Field | Tier | Cost/1K |
|---|---|---|
| `displayName` | Pro | $32 |
| `formattedAddress` | Essentials | $32 |
| `location` (lat/lng) | Essentials | $32 |
| `websiteUri` | Enterprise | $35 |
| `nationalPhoneNumber` | Enterprise | $35 |
| `internationalPhoneNumber` | Enterprise | $35 |
| `rating` | Enterprise | $35 |
| `businessStatus` | Pro | $32 |
| `googleMapsUri` | Pro | $32 |
| `regularOpeningHours` | Enterprise | $35 |

**Pricing (per 1,000 requests):**

| SKU | Essentials | Pro | Enterprise |
|---|---|---|---|
| Text Search | $32.00 | $32.00 | $35.00 |
| Nearby Search | $32.00 | $32.00 | $35.00 |
| Place Details | $5.00 | $17.00 | $20.00 |

**Free monthly allowances (per SKU):**

- Essentials: 10,000 free requests/month
- Pro: 5,000 free requests/month
- Enterprise: 1,000 free requests/month

**Critical: `websiteUri` is Enterprise tier**, so each Text Search costs $35/1K ($0.035 per request). But Place Details at Enterprise is only $20/1K ($0.02 per request).

**Optimal strategy for 50-100 lookups/month:** Well within the free tier. Even at Enterprise tier, 1,000 free requests/month covers this easily. You pay $0 for up to ~1,000 businesses/month.

**CORS:** Google Places API does NOT support CORS headers. You CANNOT call it directly from browser JavaScript. You MUST use a backend proxy or serverless function (Cloudflare Worker, Vercel Edge Function, etc.).

**Implementation pattern:**

```
Browser → Your API proxy (Cloudflare Worker) → Google Places API
```

### 1B. Apify — Pre-Built "No Website" Scraper

Apify offers a dedicated "Businesses Without Websites Leads Scraper" that wraps Google Maps scraping with built-in filtering.

- **Price:** $29.90/month for the actor rental + platform credits
- **Free tier:** $5/month in platform credits (hundreds of moderate scraping tasks)
- **Platform cost:** ~$0.001-$0.05 per result depending on complexity
- **Output:** Pre-filtered list of businesses without websites, with name, address, phone, rating
- **API access:** Full REST API for automation; results exportable as JSON/CSV
- **Rate:** Can scrape up to 100K leads/month at the $29.90 tier

**Verdict:** Good for bulk prospecting batches, but adds a dependency. Better to use Google Places API directly for real-time lookups in your app.

### 1C. Yelp Fusion API / Yelp Places API

**Business Details endpoint:** `GET /v3/businesses/{business_id_or_alias}`

Yelp returns a `url` field — but this is the **Yelp page URL**, not the business's own website. Yelp does NOT reliably expose the business's actual website URL in their API. The `phone` and `display_phone` fields ARE available.

**Pricing:** Free tier available (was 5,000 calls/day, now varies under Yelp Places API). Not ideal for "has website" filtering because the field isn't reliably exposed.

**Verdict:** Useful as a supplementary source for phone numbers and reviews, but NOT reliable for detecting "no website" status.

### 1D. Foursquare Places API

Foursquare returns venue data including `website` field when available. Free tier provides basic location data (name, address, geo-location). The `website` field may be available on paid tiers.

**Pricing:** Free tier with limited data. Paid plans start around $200/month for fuller access.

**Verdict:** Secondary source at best. Google Places API is superior for this use case.

### 1E. Facebook Graph API

Facebook is highly restrictive for business data:
- Page phone/email requires page admin access or approved app permissions
- No public API to search for business pages and get their website/contact info
- Owner info is NOT available through any public API endpoint
- Lead Ads data only available to page admins

**Verdict:** NOT viable as a data source for prospecting. Ignore this.

### 1F. Other Tools (Outscraper, SiteSeeker, Grape Leads, Webless Leads)

Several SaaS tools exist specifically for this niche:

| Tool | Model | Notes |
|---|---|---|
| [Outscraper](https://outscraper.com/) | Pay-per-result | Google Maps scraper with "no website" filter |
| [SiteSeeker](https://siteseeker.ai/) | SaaS | Dedicated tool for finding businesses without websites |
| [Grape Leads](https://grapeleads.com/) | SaaS | Scours GMB for businesses without websites |
| [Webless Leads](https://weblessleads.com/) | SaaS | Instant no-website business finder |
| [Targetron](https://targetron.com/) | SaaS | Google Maps lead extraction |

These are competitors, not data sources for your tool. Useful for understanding the market.

---

## Part 2: Enriching Business Contact Info

### 2A. Hunter.io — Email Finder

**Best for:** Finding email addresses given a person's name + domain (or domain-only search).

**API endpoints:**
- `GET https://api.hunter.io/v2/domain-search` — find all emails at a domain
- `GET https://api.hunter.io/v2/email-finder` — find specific person's email (name + domain)
- `GET https://api.hunter.io/v2/email-verifier` — verify an email exists

**Pricing:**

| Plan | Monthly | Annual/mo | Credits/mo |
|---|---|---|---|
| Free | $0 | $0 | 25 searches + 50 verifications |
| Starter | $49 | $34 | 2,000 |
| Growth | $149 | $104 | 10,000 |
| Scale | $299 | $209 | 25,000 |

- 1 credit = 1 email found
- Bulk domain search: 1 credit per 10 emails found
- Email verification: 0.5 credits per verification

**Limitation for our use case:** Hunter works best when you have a domain. For businesses WITHOUT websites (no domain), Hunter is less useful. It's better for enriching businesses that DO have websites but you want owner email.

**CORS:** Hunter.io API does NOT support direct browser calls. Requires backend proxy.

### 2B. Apollo.io — Contact Database

**Best for:** Finding business owner names, emails, phone numbers from a company name/domain.

**Pricing:**
- Free: 100 credits/month
- Basic: $49/user/month (annual) or $59/month (monthly)
- Professional: $99/user/month (annual)
- Organization: $149/user/month (annual)

**API:** REST API with person and company enrichment endpoints. 99.9% uptime claimed.

**Verdict:** Good for enrichment when you have a company name. The free tier (100 credits) covers 50-100 lookups/month.

### 2C. People Data Labs (PDL) — Raw Data API

**Best for:** Cheapest per-record enrichment at scale.

**API endpoints:**
- `GET https://api.peopledatalabs.com/v5/company/enrich` — company enrichment
- `GET https://api.peopledatalabs.com/v5/person/enrich` — person enrichment

**Pricing:**
- Free: 100 requests/day, but **excludes contact data** (emails/phones)
- Pro: $98/month — 350 person enrichments + 1,000 company lookups
- Enterprise: ~$2,500/month+
- Per-record: $0.20-$0.28 per person enrichment

**Database:** 1.5 billion profiles, 250 million companies.

**Rate limits:** Free = 10/min, Paid = 1,000/min.

**Limitation:** Free tier excludes the actual contact data you need. Pro at $98/mo may be overkill for 50-100 lookups.

### 2D. Snov.io — Email Finder + Outreach

**Pricing:**
- Trial (Free): 50 credits/month, 100 recipients, 1 mailbox warm-up
- Starter: $39/month — 1,000 credits, 5,000 recipients, 3 warm-ups
- Annual billing: 25% discount

**API:** REST API with 60 requests/minute rate limit. Free users can request API access via support.

**Verdict:** Comparable to Hunter. Slightly cheaper starter plan. Good option.

### 2E. Clearbit (now part of HubSpot)

**Pricing:** ~$140/1,000 enrichments. Custom pricing for API access.

**Verdict:** Expensive for a small agency. Better suited for enterprise. Skip.

### 2F. ZoomInfo

**Pricing:** Starts at $12,000/year minimum. 100M company profiles, 200M contacts.

**Verdict:** Way too expensive. Enterprise only. Skip.

### 2G. Secretary of State Business Filings — Owner Names

This is a **free data source** for finding business owner/officer names.

**The challenge:** Each of the 50 US states has its own registry with different interfaces and no standard API.

**Best options:**

1. **OpenCorporates API** — Aggregates data from multiple jurisdictions
   - Free: 200 requests/month, 50/day
   - Endpoint: `GET https://api.opencorporates.com/v0.4/officers/search?q=john+smith`
   - Returns: officer name, position, company, address, start/end dates
   - Paid: Starts at GBP 2,250/year (~$2,850/year) — too expensive for small agency
   - **Free tier is viable for 50-100 lookups/month** (200/month limit)

2. **Apify US Business Entity Search** — Scrapes SOS databases across 20+ states
   - Searches official Secretary of State databases
   - Returns: names, filing numbers, entity types, status, formation dates, registered agents, addresses, officers
   - Platform credit-based pricing

3. **California Business Registry** — Has a semi-official RESTful API returning JSON
   - Best state-level API available
   - Requires registration on their API Management Portal

4. **Cobalt Intelligence** — Commercial SOS API covering all 50 states
   - 20+ attributes per business
   - Custom pricing

**Strategy:** Use OpenCorporates free tier (200/month) to look up owner names from the business name found via Google Places. This is enough for 50-100 lookups.

### 2H. Google Custom Search API — Email Discovery

**How it works:** Set up a Programmable Search Engine, then query it programmatically to find emails.

- Search query: `"business name" "city" email OR contact`
- Endpoint: `GET https://www.googleapis.com/customsearch/v1?q=...&key=...&cx=...`
- **Free:** 100 queries/day
- **Paid:** $5 per 1,000 queries after free tier

**IMPORTANT WARNING:** Google Custom Search JSON API is **closed to new customers** as of 2025. Existing customers have until January 1, 2027 to transition. This is NOT a viable long-term solution.

**Alternative:** Use SerpApi ($50/month for 5,000 searches) as a Google Search API proxy.

---

## Part 3: Practical Implementation Plan

### Architecture: What Needs a Backend

| API | Direct from Browser? | Reason |
|---|---|---|
| Google Places API | NO | No CORS headers |
| Hunter.io | NO | No CORS; exposes API key |
| Apollo.io | NO | No CORS; exposes API key |
| Snov.io | NO | No CORS; exposes API key |
| People Data Labs | NO | No CORS; exposes API key |
| OpenCorporates | YES (limited) | Returns CORS headers, but exposes API key |
| Yelp Fusion | NO | No CORS headers |

**Bottom line:** ALL enrichment APIs need a backend proxy. A simple serverless function (Cloudflare Worker, Vercel Edge Function, or Supabase Edge Function) is the right pattern.

### Recommended Stack for 50-100 Lookups/Month

**Tier 1: Free / Near-Free (recommended starting point)**

| Service | Purpose | Monthly Cost | Lookups |
|---|---|---|---|
| Google Places API (Enterprise fields) | Find businesses + detect no website | $0 | 1,000 free/month |
| OpenCorporates API (free) | Owner/officer names | $0 | 200 free/month |
| Hunter.io (free) | Email finder | $0 | 25 searches/month |
| Snov.io (trial) | Email finder (supplement) | $0 | 50 credits/month |
| **Total** | | **$0/month** | **~75 enriched leads** |

**Tier 2: Low-Cost Growth ($50-80/month)**

| Service | Purpose | Monthly Cost | Lookups |
|---|---|---|---|
| Google Places API | Find businesses + detect no website | $0 | 1,000 free/month |
| Apollo.io (free) | Contact enrichment | $0 | 100 credits/month |
| Hunter.io Starter | Email finder | $34/month (annual) | 2,000/month |
| OpenCorporates (free) | Owner names | $0 | 200/month |
| **Total** | | **$34/month** | **~300 enriched leads** |

**Tier 3: Scale ($100-200/month)**

| Service | Purpose | Monthly Cost | Lookups |
|---|---|---|---|
| Google Places API | Business discovery | ~$0-35 | 1,000-2,000/month |
| Hunter.io Growth | Email finder | $104/month | 10,000/month |
| Apollo.io Basic | Full contact enrichment | $49/month | Unlimited with credits |
| Apify (no-website scraper) | Bulk prospecting | $30/month | Up to 100K/month |
| **Total** | | **~$183/month** | **Thousands of leads** |

### Recommended Implementation Order

1. **Google Places API** — Core business discovery. Set up a Cloudflare Worker proxy. Query `places:searchText`, check for null `websiteUri`. This alone gives you: business name, address, phone, rating, Google Maps link.

2. **OpenCorporates** — Free owner name lookup. Given a business name from step 1, search officers. This gives you the owner's name.

3. **Hunter.io or Snov.io** — Given the owner's name (from step 2), search for their email. If the business has any web presence (Facebook page, Yelp listing with a domain), use domain search.

4. **Apollo.io** — Backup enrichment source. Use when Hunter/Snov miss.

### API Proxy Architecture

```
┌─────────────┐     ┌──────────────────────┐     ┌─────────────────┐
│  Browser UI  │────▶│  Cloudflare Worker   │────▶│  Google Places  │
│  (React/JS)  │     │  or Supabase Edge Fn │────▶│  Hunter.io      │
│              │◀────│                      │────▶│  OpenCorporates │
└─────────────┘     └──────────────────────┘     │  Apollo.io      │
                                                  └─────────────────┘
```

The proxy:
- Holds all API keys server-side (never exposed to browser)
- Handles CORS (adds `Access-Control-Allow-Origin` headers)
- Can cache results (reduce API costs)
- Can rate-limit per user
- Can chain requests (Places → OpenCorp → Hunter in one call)

---

## Part 4: Key Findings & Recommendations

1. **Google Places API is the clear winner** for finding businesses without websites. The `websiteUri` field at Enterprise tier is free for up to 1,000 requests/month. No other API is as comprehensive for local business data.

2. **No single API gives you everything.** You need to chain: Google Places (business + phone) → OpenCorporates (owner name) → Hunter/Snov (email). This "waterfall enrichment" pattern is standard in the industry.

3. **All APIs require a backend proxy.** None of the business data APIs support CORS for browser-side calls. Plan for a thin serverless proxy from day one.

4. **At 50-100 lookups/month, you can run entirely on free tiers** — $0/month total. The free tiers of Google Places (1,000), OpenCorporates (200), Hunter (25), Snov (50), and Apollo (100) collectively cover this volume.

5. **Facebook Graph API is a dead end** for this use case. Do not invest time here.

6. **Secretary of State data is gold for owner names** but fragmented across 50 states. OpenCorporates aggregates it for free at low volume.

7. **Google Custom Search API is sunsetting** (dead by Jan 2027). Do not build on it.

---

## Sources

- [Google Places API Data Fields](https://developers.google.com/maps/documentation/places/web-service/data-fields)
- [Google Places API Pricing](https://developers.google.com/maps/billing-and-pricing/pricing)
- [Google Places API CORS Issue](https://issuetracker.google.com/issues/35827564)
- [Hunter.io Pricing](https://hunter.io/pricing)
- [Hunter.io Email Finder API](https://hunter.io/api/email-finder)
- [Snov.io Pricing](https://snov.io/pricing)
- [Snov.io API](https://snov.io/api)
- [Apollo.io](https://www.apollo.io/)
- [People Data Labs Pricing](https://www.peopledatalabs.com/pricing/person)
- [People Data Labs Company Enrichment API](https://docs.peopledatalabs.com/docs/reference-company-enrichment-api)
- [OpenCorporates API Reference](https://api.opencorporates.com/documentation/API-Reference)
- [Apify Businesses Without Websites Scraper](https://apify.com/xmiso_scrapers/businesses-without-websites-leads-scraper-google-maps)
- [Apify Pricing](https://apify.com/pricing)
- [Yelp Business Details API](https://docs.developer.yelp.com/reference/v3_business_info)
- [Google Custom Search API](https://developers.google.com/custom-search/v1/introduction)
- [Outscraper Google Maps Scraping](https://outscraper.com/google-maps-scrape-businesses-without-websites/)
- [SiteSeeker](https://siteseeker.ai/)
- [Grape Leads](https://grapeleads.com/)
- [Webless Leads](https://weblessleads.com/)
- [B2B Data Enrichment Tools Pricing Comparison 2026](https://derrick-app.com/en/pricing-data-enrichment-tools/)
- [Top Data Enrichment APIs 2025](https://web.superagi.com/top-10-data-enrichment-apis-of-2025-a-comparative-analysis-of-features-and-pricing)
- [Secretary of State Business Filings Programmatically](https://dev.to/avabuildsdata/how-to-search-secretary-of-state-business-filings-programmatically-multi-state-2n9b)
