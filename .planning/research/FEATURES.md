# FEATURES — Daily Intelligence Briefs System

**Research Date:** 2026-02-15
**System:** 4 Daily Intelligence Briefs (Sullivan Creative, Local Business, Keegareaux Labs, ThreatCaptain)

---

## Executive Summary

This document analyzes the feature requirements for an automated daily intelligence system that generates 4 distinct briefs. The analysis focuses on three critical dimensions:

1. **Table Stakes Features** — Without these, the system is useless
2. **Differentiators** — What makes automation better than manual checking
3. **Anti-Features** — What to deliberately NOT build to avoid abandonment

The primary risk with automated intelligence systems is **abandonment due to low signal-to-noise ratio**. Features must prioritize actionability over completeness.

---

## Table Stakes Features (Must Have)

### 1. Fresh, Real-Time Data (Complexity: HIGH)
**Without this, the system is literally useless.**

- **Requirement:** Data must be from last 24-48 hours maximum
- **Why it's table stakes:** The entire value prop is "what's trending RIGHT NOW"
- **Technical dependency:** Web scraping, API access to Reddit/social platforms
- **Failure mode:** If data is stale (7+ days old), users will immediately recognize the system is broken and stop trusting it

**Example from current prompts:**
- Sullivan Creative: "trending topics from TODAY"
- ThreatCaptain: "FRESH posts (last 24-48 hours)"
- Keegareaux Labs: "what's happening RIGHT NOW"

**Complexity Rating: 8/10**
- Requires reliable web access
- Rate limiting challenges
- Platform API changes break system
- Need caching to avoid re-fetching same data

**Dependencies:**
- Reddit API or scraping capability
- Social media platform access (TikTok, Instagram, LinkedIn)
- Google/Bing search for local business verification
- Product Hunt API

---

### 2. Structured, Scannable Output (Complexity: LOW)
**Without this, users won't read the briefs.**

- **Requirement:** Every brief must have a consistent, scannable format
- **Why it's table stakes:** People skim. If they can't extract value in 30 seconds, they'll stop opening the briefs
- **Current implementation:** All 4 briefs have clear section headers and consistent formatting

**Format requirements:**
- Section headers with visual separators
- Numbered lists for priority items
- "Quick win" or "hottest lead" highlighted at top
- Estimated time/effort included (critical for action bias)

**Complexity Rating: 2/10**
- Template-based output
- Markdown formatting sufficient

---

### 3. Source Attribution (Complexity: MEDIUM)
**Without this, claims aren't verifiable and trust erodes.**

- **Requirement:** Every insight must include a clickable source link
- **Why it's table stakes:** Trust is everything. If a lead is fake or a "trend" doesn't exist, users abandon the system permanently
- **Current implementation:** All prompts specify "Source: [Link]" requirements

**Example from Keegareaux Labs brief:**
```
Source: [Link to Reddit thread / PH launch / discussion]
```

**Complexity Rating: 5/10**
- Must preserve URLs during data collection
- Link rot is inevitable (archive links?)
- Some platforms make linking difficult (Stories, ephemeral content)

**Dependencies:**
- Web scraping that captures URLs
- URL validation to avoid 404s
- Consider archive.org integration for permanence

---

### 4. Automated Scheduling (Complexity: LOW)
**Without this, it's not automated—it's just a manual script.**

- **Requirement:** Briefs must run daily at a consistent time without human intervention
- **Why it's table stakes:** The whole point is to NOT manually check Reddit/trends every morning
- **Current implementation:** `run-briefs.sh` script exists but requires manual execution

**Ideal state:**
- macOS LaunchAgent or cron job
- Runs at 6 AM daily (before work day starts)
- macOS notification when complete
- Graceful failure handling (if one brief fails, others still run)

**Complexity Rating: 3/10**
- Simple cron/LaunchAgent setup
- Error handling adds complexity
- Need retry logic for transient failures

**Dependencies:**
- Reliable Claude Code CLI availability
- Network connectivity at scheduled time

---

### 5. Actionable Prioritization (Complexity: MEDIUM)
**Without this, users face decision paralysis.**

- **Requirement:** Every brief must answer "What should I do FIRST?"
- **Why it's table stakes:** Information without prioritization creates work instead of eliminating it
- **Current implementation:** Most briefs include this:
  - Sullivan Creative: "QUICK WIN (15 minutes or less)"
  - Local Business: "Best lead to call first: [Which one and why]"
  - ThreatCaptain: "Recommended first action"

**Format requirements:**
- Top 3 items ranked by impact/effort ratio
- "Do this first" recommendation
- Time estimates for each action
- Skip anything that requires >30 min of analysis

**Complexity Rating: 6/10**
- Requires judgment/scoring logic
- LLM-based prioritization can be inconsistent
- Need to tune prompts to avoid generic "all are important" responses

---

## Differentiators (Why Automation Beats Manual)

### 1. Cross-Platform Aggregation (Complexity: HIGH)
**Manual approach:** Open Reddit, TikTok, Instagram, LinkedIn, Product Hunt tabs every morning = 45+ minutes
**Automated approach:** All sources aggregated in one daily email = 3 minutes to review

**Specific value:**
- Sullivan Creative brief checks 4 platforms (TikTok, IG, YouTube, LinkedIn)
- Keegareaux Labs scans 3+ subreddits plus Product Hunt
- ThreatCaptain monitors r/msp plus LinkedIn MSP groups

**Why humans won't do this manually:**
- Context switching between 10+ platforms is exhausting
- Easy to miss critical posts buried in feeds
- Algorithms show you what you engaged with before, not what's emerging

**Complexity Rating: 9/10**
- Each platform has different APIs/scraping requirements
- Rate limits across multiple services
- Authentication challenges (Instagram requires login)

**Dependencies:**
- Multi-platform API access
- Proxy/rotating IPs to avoid rate limits
- Session management for authenticated platforms

---

### 2. Signal Filtering with Context (Complexity: HIGH)
**Manual approach:** See hundreds of posts, most irrelevant
**Automated approach:** Only see posts matching specific business contexts

**Examples of context-aware filtering:**

**Sullivan Creative:**
- Not just "photography trends" but "photography trends relevant to Greenville SC market"
- Filters for content that matches their service offerings (senior portraits, weddings, branding)

**Local Business Scraper:**
- Not just "businesses without websites" but "businesses with 4+ star reviews AND evidence of need in reviews"
- Geographic filter: Upstate SC only (10+ cities specified)

**ThreatCaptain:**
- Not just "MSP discussions" but "MSP owners asking about sales/differentiation in last 48 hours"
- Buying signal detection ("looking for tools", "budget mentioned", "frustrated with current approach")

**Why this is a differentiator:**
- Human reviewers get fatigued and miss nuanced signals
- Consistency: AI applies same filters every day
- No "good enough" shortcuts that humans take when tired

**Complexity Rating: 9/10**
- Requires sophisticated prompt engineering
- Context understanding varies with LLM model quality
- False positives/negatives need ongoing tuning

---

### 3. Competitive Intelligence Synthesis (Complexity: MEDIUM)
**Manual approach:** Spot individual tactics, miss patterns
**Automated approach:** Identify trends across multiple data points

**Keegareaux Labs example:**
- "GROWTH TACTICS WITH REAL NUMBERS (3)"
- Synthesizes what's working across indie hacker community
- Pattern recognition: "3 different founders used same tactic, here are the results"

**ThreatCaptain example:**
- "Top pain point today: [Most common theme]"
- Aggregates signal across 5-10 individual MSP leads to identify market trends

**Why humans miss this:**
- Hard to remember what you saw yesterday vs. what's new today
- Pattern recognition requires reviewing notes across days/weeks
- Synthesis is cognitively expensive

**Complexity Rating: 6/10**
- LLMs are good at summarization
- Requires memory/context across brief runs
- Need to track "seen this before" vs. "new pattern emerging"

---

### 4. Consistent Daily Habit Formation (Complexity: LOW)
**Manual approach:** "I'll check Reddit when I have time" = never happens consistently
**Automated approach:** Brief in inbox at 6 AM every day = becomes part of morning routine

**Psychological advantage:**
- Removes activation energy (don't have to remember to do it)
- Inbox placement = already in workflow
- macOS notification = ambient reminder

**Why this matters:**
- Weak signals compound over time (Day 1 lead + Day 7 lead = pattern)
- Consistency builds trust in the system
- Missing a day manually = often becomes missing a week

**Complexity Rating: 2/10**
- Just scheduling + notifications
- Output location matters (email vs. file system)

---

### 5. Personalized Outreach Templates (Complexity: MEDIUM)
**Manual approach:** Generic "Hey, I noticed your post..." DMs
**Automated approach:** Context-specific outreach scripts based on exact pain points

**ThreatCaptain example:**
```
Outreach Strategy:
1. Public comment: "[Exact comment to post — add value first, don't pitch]"
2. Follow-up: "[What to say if they respond]"
3. DM/Direct: "[Personalized message that references their specific problem]"
```

**Local Business example:**
```
Outreach angle:
"[Personalized 2-3 sentence pitch based on their specific situation]"

Notable review:
"[Quote from a review that supports why they need a website]"
```

**Why this is a differentiator:**
- Humans write lazy, generic outreach when doing volume
- Pre-written scripts reduce friction to action
- Personalization based on actual data (review quotes, post text)

**Complexity Rating: 7/10**
- Requires extracting specific quotes/context
- Outreach templates need to sound human, not robotic
- Different tone for each use case (B2B MSP vs. local HVAC company)

---

## Anti-Features (What NOT to Build)

### 1. Archive/Historical Search ❌
**Why not:** Creates hoarding behavior instead of action bias

**Temptation:** "Let's save all briefs in a searchable database!"
**Reality:** Users will never search old briefs. They'll just feel guilty about not actioning them.

**Better approach:**
- Keep last 7 days of briefs
- Auto-delete anything older
- Force focus on TODAY's actionable items

**Why this matters:**
- Old leads go cold (Reddit posts from 2 weeks ago = already answered/resolved)
- Historical trends don't matter (what was trending in January doesn't help in February)
- Storage creates obligation ("I should review old briefs before starting new ones")

**Exception:** Analytics on what types of content/leads convert could inform future briefs, but that's metadata, not full text search.

---

### 2. Customizable Output Formats ❌
**Why not:** Choice paralysis and maintenance burden

**Temptation:** "Let users choose JSON, CSV, HTML, PDF output!"
**Reality:** They'll spend 20 minutes configuring formats instead of 3 minutes actioning leads.

**Better approach:**
- One format: Markdown text files
- Optimized for readability, not flexibility
- If they want PDF, they can convert manually

**Why this matters:**
- More options = more decisions = less action
- Maintaining multiple output formats doubles testing burden
- Users customize once, never change it, then forget what their settings were

---

### 3. Manual Curation Interface ❌
**Why not:** Defeats the entire purpose of automation

**Temptation:** "Let users approve/reject items before the brief is sent!"
**Reality:** That's just manual checking with extra steps.

**Better approach:**
- Trust the filters
- If something doesn't belong, improve the prompt
- Feedback loop: "This lead was bad" → adjust filters, don't add manual gates

**Why this matters:**
- Manual review takes 30+ minutes
- Removes consistency (tired day = lower standards)
- Defeats "wake up to finished brief" workflow

**Exception:** After-the-fact feedback ("mark as good/bad lead") to improve future briefs is fine. But no pre-approval gates.

---

### 4. Real-Time Alerts/Webhooks ❌
**Why not:** Creates urgency theater without actual urgency

**Temptation:** "Notify immediately when a hot lead appears!"
**Reality:** Hot leads can wait until tomorrow morning. Nothing in these use cases requires instant response.

**Better approach:**
- Batch once daily
- Resist the dopamine hit of "breaking news" notifications
- Daily rhythm > constant interruption

**Why this matters:**
- ThreatCaptain MSP leads: Responding 2 hours vs. 8 hours later doesn't matter
- Sullivan Creative trends: Content can be created tomorrow, doesn't need to be today
- Real-time alerts train users to ignore notifications

**Exception:** Maybe for high-value enterprise sales leads (not current scope). But even then, daily is probably sufficient.

---

### 5. Social Media Auto-Posting ❌
**Why not:** Crosses the line from intelligence to automation liability

**Temptation:** "Auto-post the trending content ideas to Instagram!"
**Reality:** Brand voice, visual assets, timing nuance all require human judgment.

**Better approach:**
- Provide the idea + hook + format
- Human decides if/when/how to execute
- Keep system as intelligence layer, not execution layer

**Why this matters:**
- Bad auto-posts damage reputation permanently
- Content quality requires human review (especially for client-facing brands)
- Legal liability if auto-post violates platform rules

**Scope boundary:** Intelligence briefs suggest what to do. Humans decide if/how to do it.

---

### 6. Multi-User Collaboration Features ❌
**Why not:** These are personal intelligence tools, not team dashboards

**Temptation:** "Let the whole agency team comment on leads!"
**Reality:** Sullivan Creative has maybe 2-5 people. ThreatCaptain is a solo founder + 1-2 sales people.

**Better approach:**
- One email recipient per brief
- If they want to share, they forward the email
- Keep it simple: file system → notification → done

**Why this matters:**
- Collaboration features require user accounts, permissions, auth
- Team dynamics create diffusion of responsibility ("someone else will follow up")
- Solo or 2-person teams don't need Slack integration

---

## Feature Complexity Matrix

| Feature | Complexity | Build Time | Maintenance | Priority |
|---------|-----------|------------|-------------|----------|
| **TABLE STAKES** |
| Fresh data collection | HIGH (8/10) | 3-4 weeks | HIGH | P0 |
| Structured output | LOW (2/10) | 2 days | LOW | P0 |
| Source attribution | MEDIUM (5/10) | 1 week | MEDIUM | P0 |
| Automated scheduling | LOW (3/10) | 1 day | LOW | P0 |
| Actionable prioritization | MEDIUM (6/10) | 1 week | MEDIUM | P0 |
| **DIFFERENTIATORS** |
| Cross-platform aggregation | HIGH (9/10) | 4-5 weeks | HIGH | P1 |
| Signal filtering w/ context | HIGH (9/10) | 3-4 weeks | HIGH | P1 |
| Competitive intelligence synthesis | MEDIUM (6/10) | 1-2 weeks | MEDIUM | P2 |
| Consistent habit formation | LOW (2/10) | 1 day | LOW | P1 |
| Personalized outreach templates | MEDIUM (7/10) | 2 weeks | MEDIUM | P2 |

**Priority Levels:**
- **P0:** System is useless without this
- **P1:** System works but isn't better than manual
- **P2:** Nice to have, enhances value

---

## Feature Dependencies

### Dependency Chain 1: Data Collection
```
Platform APIs/Scraping
  ↓
Fresh Data Collection (Table Stakes #1)
  ↓
Cross-Platform Aggregation (Differentiator #1)
  ↓
Signal Filtering with Context (Differentiator #2)
  ↓
Source Attribution (Table Stakes #3)
```

**Critical path:** Can't filter or attribute sources until you have reliable data collection working.

---

### Dependency Chain 2: Output Generation
```
Structured Output Templates (Table Stakes #2)
  ↓
Actionable Prioritization (Table Stakes #5)
  ↓
Personalized Outreach Templates (Differentiator #5)
```

**Critical path:** Basic output must work before adding prioritization logic. Prioritization must work before generating personalized scripts.

---

### Dependency Chain 3: Automation Loop
```
Automated Scheduling (Table Stakes #4)
  ↓
Consistent Habit Formation (Differentiator #4)
  ↓
[Future: Feedback loop for continuous improvement]
```

**Critical path:** Scheduling is prerequisite for habit formation. Habit formation enables long-term trust and feedback collection.

---

### Dependency Chain 4: Trust & Credibility
```
Source Attribution (Table Stakes #3)
  ↓
Competitive Intelligence Synthesis (Differentiator #3)
  ↓
[User trusts system enough to act on recommendations]
```

**Critical path:** Without sources, users don't trust insights. Without trust, competitive intelligence is ignored.

---

## What Makes Intelligence Systems Actually Useful vs. Abandoned

### Abandoned Systems Have:
1. **High noise-to-signal ratio** — 90% of content is irrelevant
2. **No clear next action** — "Here's 50 things happening, good luck deciding"
3. **Inconsistent quality** — Some days great, some days garbage
4. **Generic insights** — Could apply to anyone, not specific to your context
5. **Delayed/stale data** — By the time you see it, the opportunity passed
6. **Overwhelming volume** — 10-page daily reports no one reads

### Actually Used Systems Have:
1. **Ruthless filtering** — Show me 5 things, not 50
2. **Clear prioritization** — "Do this first, here's why"
3. **Consistent quality** — Predictable value every day
4. **Contextual relevance** — Speaks to your specific business/market
5. **Real-time freshness** — Data from last 24-48 hours
6. **Scannable brevity** — 3 minutes to read, 30 minutes to action

---

## Design Principles for This System

### 1. Action Bias Over Completeness
**Don't show everything trending. Show the 3 trends worth acting on.**

Example: Sullivan Creative brief shows "TOP 3 CONTENT IDEAS" not "TOP 20"
- Constraint forces prioritization
- 3 ideas = actionable
- 20 ideas = overwhelming

### 2. Time Estimates Drive Action
**Every suggestion must include estimated effort.**

Example: "Estimated time: 15 minutes to create"
- Removes ambiguity
- Helps users slot into available time
- Prevents "I'll do this later" (later = never)

### 3. Specificity Over Generality
**No "consider creating content about photography trends." Yes: "Film a 30-second Reel about [specific trend] using [specific hook] today."**

Example from prompts:
- Not: "Explore local businesses"
- Yes: "LEAD 1: Johnson's HVAC, Greenville SC, 4.8 stars, no website, review quote: 'Had to call 3 times because no online booking'"

### 4. Evidence-Based Recommendations
**Every claim must be backed by a source or data point.**

Example: Keegareaux Labs - "Results: [Specific numbers — MRR, users, conversion rate]"
- Not: "This growth tactic works well"
- Yes: "This tactic got them from $2K to $8K MRR in 3 months [source link]"

### 5. Freshness Over Comprehensiveness
**Better to show 3 fresh leads from today than 10 leads from last week.**

Example: ThreatCaptain - "Focus on FRESH posts (last 24-48 hours)"
- Recency = relevance
- Old leads = already resolved/cold
- Fresh = competitive advantage

---

## Success Metrics (How to Know If Features Are Working)

### Usage Metrics
- **Daily open rate** — What % of briefs get opened within 3 hours of delivery?
  - Target: >80% (if falling below, briefs aren't valuable)
- **Action rate** — What % of leads/ideas get actioned within 48 hours?
  - Target: >30% (if <10%, prioritization is broken)
- **Retention** — Do users still open briefs after 30 days?
  - Target: >90% retention after first month (if <50%, system is abandoned)

### Quality Metrics
- **Lead quality** — For Local Business + ThreatCaptain, what % of leads are actionable?
  - Target: >60% (if <30%, filtering is broken)
- **Trend relevance** — For Sullivan Creative + Keegareaux Labs, do recommended trends/tactics actually get engagement when executed?
  - Target: >50% of executed ideas perform above baseline (if <25%, trend detection is broken)
- **Source validity** — What % of included sources are live/valid links?
  - Target: >95% (if <80%, attribution is failing)

### Efficiency Metrics
- **Time to review** — How long does it take to read and prioritize the brief?
  - Target: <5 minutes (if >15 minutes, too much content)
- **Time to first action** — How long from opening brief to taking first action?
  - Target: <30 minutes (if >2 hours, next steps aren't clear enough)

### Abandonment Signals (Red Flags)
- Briefs go unopened for 3+ consecutive days
- User manually checks Reddit/social instead of waiting for brief
- User says "I'll read it later" repeatedly (indicates low perceived value)
- User starts forwarding briefs without reading (delegating = not personally valuable)

---

## MVP Feature Set (First Version)

**Goal:** Prove the system is better than manual checking within 7 days of use.

### Must Have (Week 1)
1. ✅ Structured output templates (already done in prompts)
2. ✅ Automated scheduling via cron/LaunchAgent (run-briefs.sh exists, needs scheduling)
3. ✅ Basic source attribution (prompts specify this, need to verify Claude follows through)
4. ⚠️ Fresh data collection (currently using Claude's web search, need to verify recency)
5. ⚠️ Actionable prioritization (prompts request this, need to verify output quality)

### Should Have (Week 2-3)
6. Cross-platform aggregation (expand beyond what Claude's web search provides)
7. Signal filtering improvements (tune prompts based on initial output quality)
8. Notification improvements (macOS notification exists, consider email delivery)

### Could Have (Month 2+)
9. Personalized outreach template quality improvements
10. Competitive intelligence synthesis enhancements
11. Feedback loop for continuous improvement

---

## Open Questions & Risks

### Technical Risks
1. **Claude Code web search limitations** — Current implementation relies on Claude's web search. Can it access Reddit posts from last 24 hours? Instagram/TikTok trends?
   - **Mitigation:** May need dedicated scrapers/APIs for each platform

2. **Rate limiting** — Running 4 briefs daily with extensive web searches may hit rate limits
   - **Mitigation:** Stagger brief execution, cache common queries

3. **Cost** — Each brief requires Claude API calls + web searches. 4 briefs × 365 days = significant cost
   - **Mitigation:** Estimate cost per brief, determine if ROI justifies expense

### Product Risks
1. **Prompt drift** — Over time, Claude's output quality may degrade if prompts aren't maintained
   - **Mitigation:** Version control prompts, track output quality metrics

2. **Source reliability** — Reddit/social platforms change APIs, block scrapers
   - **Mitigation:** Multi-platform redundancy, graceful degradation

3. **Context staleness** — Business contexts change (Sullivan Creative adds new service, Keegareaux Labs launches new product)
   - **Mitigation:** Quarterly prompt reviews, easy editing of context sections

### Business Model Risks
1. **Single user dependency** — If one person uses all 4 briefs, what happens if they get sick/vacation?
   - **Mitigation:** Pause functionality, don't send briefs that won't be actioned

2. **Attribution failure** — If users stop trusting sources, entire system value collapses
   - **Mitigation:** Source verification as #1 quality metric

---

## Conclusion

The Daily Intelligence Briefs system succeeds or fails based on **action bias**.

**Table stakes features** ensure the system works at all (fresh data, sources, automation).

**Differentiators** ensure it's better than manual checking (cross-platform aggregation, context filtering, personalized outreach).

**Anti-features** prevent abandonment by avoiding common pitfalls (information hoarding, choice paralysis, notification fatigue).

The north star metric is: **What % of briefs lead to action within 48 hours?**

If that number is >30%, the system is valuable.
If <10%, the system is noise and will be abandoned.

Every feature decision should optimize for **action rate**, not **information completeness**.
