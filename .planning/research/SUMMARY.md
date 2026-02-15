# Research Summary — Daily Intelligence Briefs System

**Date:** 2026-02-13
**Sources:** STACK.md, FEATURES.md, ARCHITECTURE.md, PITFALLS.md

---

## Key Findings

### 1. Stack Decision: Keep It Simple
- **Claude Code CLI** is the right engine — already integrated, subscription-based, has web search
- **Cron is fine for validation**, migrate to launchd after proving value (catches missed jobs on wake)
- **Markdown files** are the right output format — readable, versionable, no dependencies
- **osascript → terminal-notifier** is a quick win for better notifications
- **Total MVP cost: $20/mo** (just the Claude Code subscription)

### 2. Features: Action Bias Over Completeness
- **Table stakes:** Fresh data, structured output, source attribution, scheduling, prioritization
- **Differentiators:** Cross-platform aggregation, contextual signal filtering, outreach templates
- **Anti-features (don't build):** Archive search, customizable formats, manual curation, real-time alerts, auto-posting
- **North star metric:** >30% of brief items actioned within 48 hours

### 3. Architecture: Modular, Sequential, Observable
- Current architecture (bash + Claude CLI + cron) is sufficient for validation
- Add: timeout protection, retry logic, structured logging, basic validation
- Parallel execution is premature — sequential is fine for 4 briefs
- Feedback loops are critical but come after proving content quality

### 4. Pitfalls: Top 3 Risks
1. **Notification fatigue** → Mitigate with variable frequency, compelling subject lines, quality gating
2. **Hallucinated sources** → Mitigate with URL verification, explicit prompt instructions
3. **Cron reliability** → Mitigate with launchd migration, catch-up logic, heartbeat monitoring

---

## Critical Decisions

| Decision | Recommendation | Rationale |
|----------|---------------|-----------|
| Start with 2 briefs, not 4 | Sullivan Creative + Keegareaux Labs only | Validate before scaling |
| Keep cron for now | Migrate to launchd in Phase 2 | Working system > perfect system |
| No external APIs yet | Claude's web search only | Reduce complexity, validate content quality first |
| Markdown only | No HTML, PDF, or email delivery | Simplest viable output |
| Weekly manual review | Track "acted on 2-3 ideas/week" manually | Don't over-instrument during validation |

---

## Build Priority

1. **Foundation** — Error handling, logging, timeout protection for existing runner script
2. **Content Quality** — Prompt tuning based on first week of output quality
3. **Reliability** — launchd migration, catch-up logic
4. **Engagement Tracking** — Simple file-access tracking, feedback mechanism
5. **Expansion** — Add ThreatCaptain + Local Business briefs after validation
6. **Advanced** — Parallel execution, email delivery, analytics dashboard

---

## Validation Criteria (30-Day Test)

**Success = keep building:**
- Acted on 2-3 ideas per week from briefs
- Open rate >80% (checking briefs daily)
- <10 minutes total to review all briefs
- Would miss it if it stopped

**Failure = pause and rethink:**
- <1 action per week from briefs
- Stopped checking briefs for 3+ consecutive days
- Content feels generic or stale
- Spending more time debugging than reading

---

*Synthesized from 4 research documents totaling ~4,000 lines of analysis*
