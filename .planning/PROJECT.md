# Daily Briefs System — Sullivan Creative Co., ThreatCaptain & Keegareaux Labs

## What This Is

An automated daily intelligence system that generates actionable briefs for three brands — Sullivan Creative Co. (content ideas + local business leads), ThreatCaptain (MSP lead scanning), and Keegareaux Labs (app trends + growth tactics). Runs via cron + Claude Code at 10am daily, saves output as markdown files with macOS notifications.

## Core Value

Every morning, Keegan opens a folder and knows exactly what to create, who to reach out to, and what trends matter — without spending time researching. **If the briefs aren't actionable enough to act on 2-3 ideas per week, they're worthless.**

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Sullivan Creative brief generates 3 specific, trending content ideas daily
- [ ] Sullivan Creative brief includes a "quick win" idea doable in 15 minutes
- [ ] Local Business Scraper finds 5 real Upstate SC businesses without websites
- [ ] Each local business lead includes a personalized outreach angle
- [ ] Keegareaux Labs brief surfaces 3 trending app ideas with real sources
- [ ] Keegareaux Labs brief includes growth tactics with actual numbers
- [ ] ThreatCaptain scanner finds 5-10 MSPs asking about sales/growth on Reddit
- [ ] Each MSP lead includes a value-first outreach strategy
- [ ] All briefs run automatically at 10am daily via cron
- [ ] macOS notification fires when briefs are complete
- [ ] Output is readable, scannable, and actionable in under 10 minutes total

### Out of Scope

- SMS delivery — adds complexity, macOS notifications are sufficient for validation
- Email delivery — same reason, validate content quality first
- CRM integration — premature until briefs prove valuable
- Automated outreach — manual outreach first to validate lead quality
- Real-time scraping APIs — Claude's web search is sufficient for validation

## Context

- Briefs already exist at ~/daily-briefs/ with prompts and cron job configured
- Using Claude Code CLI (`--print` mode) to generate each brief
- Sullivan Creative is a photography/creative agency in Greenville, SC
- ThreatCaptain is a cybersecurity platform serving MSPs
- Keegareaux Labs is an indie software studio (Dispatch, Lab Partner apps)
- User's primary metric: "Did I act on 2-3 ideas this week from the brief?"
- Validation phase: Start with Sullivan Creative + Keegareaux Labs briefs only
- ThreatCaptain + Local Business briefs added after validation

## Constraints

- **Runtime**: Claude Code CLI must complete all briefs within ~15 minutes total
- **Cost**: Using Claude Code subscription (not separate API key)
- **Platform**: macOS only (cron + osascript notifications)
- **Data freshness**: Claude's web search is best-effort, not real-time
- **Mac must be awake**: Cron won't fire if laptop is asleep at 10am

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Start with Sullivan + Keegareaux only | Validate concept before scaling to all 4 briefs | — Pending |
| Files + macOS notification (no email/SMS) | Reduce complexity during validation | — Pending |
| 10am daily schedule | User preferred later morning over 8am | — Pending |
| Success = acted on 2-3 ideas/week | Actionability over volume | — Pending |

---
*Last updated: 2026-02-13 after initialization*
