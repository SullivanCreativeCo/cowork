# Requirements: Daily Intelligence Briefs System

**Defined:** 2026-02-13
**Core Value:** Every morning, Keegan opens a folder and knows exactly what to create, who to reach out to, and what trends matter — if the briefs aren't actionable enough to act on 2-3 ideas per week, they're worthless.

## v1 Requirements

Requirements for initial release. Validation phase: Sullivan Creative + Keegareaux Labs only.

### Content Generation

- [ ] **GEN-01**: Sullivan Creative brief generates 3 specific, trending content ideas with platform recommendations
- [ ] **GEN-02**: Sullivan Creative brief includes a "quick win" idea doable in 15 minutes
- [ ] **GEN-03**: Keegareaux Labs brief surfaces 3 trending app ideas with real sources
- [ ] **GEN-04**: Keegareaux Labs brief includes growth tactics with actual numbers/results
- [ ] **GEN-05**: All generated content references data from the last 24-48 hours
- [ ] **GEN-06**: Every insight includes a clickable source link (no hallucinated URLs)

### Automation

- [ ] **AUTO-01**: Briefs run automatically at 10am daily via cron
- [ ] **AUTO-02**: macOS notification fires when briefs are complete with success/failure count
- [ ] **AUTO-03**: Runner script has timeout protection (120s per brief)
- [ ] **AUTO-04**: Runner script continues generating remaining briefs if one fails
- [ ] **AUTO-05**: Execution logs written to ~/daily-briefs/logs/ with timestamps

### Output Quality

- [ ] **QUAL-01**: Each brief is scannable and reviewable in under 5 minutes
- [ ] **QUAL-02**: Each brief has consistent, structured sections (headers, numbered lists, bold actions)
- [ ] **QUAL-03**: Total review time for all briefs is under 10 minutes
- [ ] **QUAL-04**: Each brief includes a clear "do this first" recommendation
- [ ] **QUAL-05**: Content is specific to the brand context (Greenville SC for Sullivan, indie dev for Keegareaux)

## v2 Requirements

Deferred until v1 validated (acted on 2-3 ideas/week for 30 days).

### Expansion Briefs

- **EXP-01**: ThreatCaptain MSP scanner finds 5-10 MSPs asking about sales/growth on Reddit
- **EXP-02**: Each MSP lead includes a value-first outreach strategy
- **EXP-03**: Local Business Scraper finds 5 real Upstate SC businesses without websites
- **EXP-04**: Each local business lead includes a personalized outreach angle with review quotes

### Reliability

- **REL-01**: Migrate from cron to launchd for sleep/wake catch-up
- **REL-02**: Retry logic with exponential backoff (3 attempts per brief)
- **REL-03**: Fallback to previous day's brief if generation fails
- **REL-04**: Heartbeat monitoring via healthchecks.io

### Enhanced Notifications

- **NOTF-01**: terminal-notifier replaces osascript (clickable, opens briefs folder)
- **NOTF-02**: Notification includes brief quality summary (not just count)
- **NOTF-03**: Pushover mobile notifications (optional)

### Engagement Tracking

- **ENGR-01**: Track which briefs are opened (file access time)
- **ENGR-02**: Weekly analytics report (open rate, action rate)
- **ENGR-03**: Feedback mechanism in briefs ("was this useful?")

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| SMS/email delivery | Validate content quality first; file + notification is sufficient |
| CRM integration | Premature until briefs prove valuable |
| Automated outreach | Manual outreach first to validate lead quality |
| Real-time alerts | Daily batch is sufficient; real-time creates notification fatigue |
| Auto-posting to social | Brand voice requires human judgment; liability risk |
| Web dashboard | "Dashboard nobody checks" antipattern; meet user where they are (Finder) |
| Searchable archive | Creates hoarding behavior; focus on today's actionable items |
| Customizable output formats | Choice paralysis; one format (markdown) is enough |
| Multi-user collaboration | Single user system; forwarding covers team sharing |
| External scraping APIs (Apify, Brave) | Claude's web search is sufficient for validation |
| Database storage | Markdown + filesystem is sufficient; no need for SQLite/Postgres |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| GEN-01 | Phase 1 | Pending |
| GEN-02 | Phase 1 | Pending |
| GEN-03 | Phase 1 | Pending |
| GEN-04 | Phase 1 | Pending |
| GEN-05 | Phase 2 | Pending |
| GEN-06 | Phase 2 | Pending |
| AUTO-01 | Phase 1 | Pending |
| AUTO-02 | Phase 1 | Pending |
| AUTO-03 | Phase 3 | Pending |
| AUTO-04 | Phase 3 | Pending |
| AUTO-05 | Phase 3 | Pending |
| QUAL-01 | Phase 2 | Pending |
| QUAL-02 | Phase 1 | Pending |
| QUAL-03 | Phase 2 | Pending |
| QUAL-04 | Phase 2 | Pending |
| QUAL-05 | Phase 2 | Pending |

**Coverage:**
- v1 requirements: 16 total
- Mapped to phases: 16
- Unmapped: 0

---
*Requirements defined: 2026-02-13*
*Last updated: 2026-02-13 after research synthesis*
