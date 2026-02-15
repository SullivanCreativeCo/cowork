# Roadmap: Daily Intelligence Briefs System

## Overview

Build an automated daily intelligence system in 5 phases: start with existing prompts and runner script, validate content quality with 2 briefs (Sullivan Creative + Keegareaux Labs), harden reliability, then expand to all 4 briefs and add engagement tracking. Each phase delivers measurable value before moving to the next.

## Phases

- [ ] **Phase 1: Prompt Setup & First Run** - Get Sullivan Creative + Keegareaux Labs briefs generating and verify output quality
- [ ] **Phase 2: Content Quality Tuning** - Iterate prompts until briefs are consistently actionable and specific
- [ ] **Phase 3: Runner Hardening** - Add error handling, logging, timeout protection to run-briefs.sh
- [ ] **Phase 4: Scheduling & Notifications** - Automate daily execution with cron and improve notifications
- [ ] **Phase 5: Validation & Expansion** - Run 30-day validation, then add ThreatCaptain + Local Business briefs

## Phase Details

### Phase 1: Prompt Setup & First Run
**Goal**: Generate first Sullivan Creative and Keegareaux Labs briefs and verify they produce useful output
**Depends on**: Nothing (first phase)
**Requirements**: GEN-01, GEN-02, GEN-03, GEN-04, QUAL-02
**Success Criteria** (what must be TRUE):
  1. Sullivan Creative brief generates with 3 content ideas, platform recommendations, and a quick win
  2. Keegareaux Labs brief generates with 3 app trends and growth tactics with numbers
  3. Both briefs have consistent markdown structure with clear sections
  4. Manual review confirms briefs contain specific, non-generic advice
**Plans**: 2 plans

Plans:
- [ ] 01-01: Finalize and test Sullivan Creative prompt, verify output quality
- [ ] 01-02: Finalize and test Keegareaux Labs prompt, verify output quality

### Phase 2: Content Quality Tuning
**Goal**: Iterate prompts until output is consistently actionable, specific to brand context, and includes verifiable sources
**Depends on**: Phase 1
**Requirements**: GEN-05, GEN-06, QUAL-01, QUAL-03, QUAL-04, QUAL-05
**Success Criteria** (what must be TRUE):
  1. Each brief reviewable in under 5 minutes with clear "do this first" item
  2. All content references data from last 24-48 hours
  3. Source links are real and clickable (spot-check 5 per brief)
  4. Content is specific to brand context (Greenville SC, indie dev ecosystem)
  5. Total review time for both briefs under 10 minutes
**Plans**: 2 plans

Plans:
- [ ] 02-01: Tune Sullivan Creative prompt for freshness, specificity, and source quality
- [ ] 02-02: Tune Keegareaux Labs prompt for freshness, specificity, and source quality

### Phase 3: Runner Hardening
**Goal**: Make run-briefs.sh reliable with error handling, timeouts, and logging
**Depends on**: Phase 2
**Requirements**: AUTO-03, AUTO-04, AUTO-05
**Success Criteria** (what must be TRUE):
  1. Each brief times out after 120s instead of hanging indefinitely
  2. If one brief fails, remaining briefs still generate
  3. All runs logged to ~/daily-briefs/logs/ with timestamps and success/failure status
  4. Script can be run manually and produces same results as automated run
**Plans**: 1 plan

Plans:
- [ ] 03-01: Add timeout, retry logic, graceful degradation, and structured logging to runner script

### Phase 4: Scheduling & Notifications
**Goal**: Automate daily 10am execution with reliable notifications
**Depends on**: Phase 3
**Requirements**: AUTO-01, AUTO-02
**Success Criteria** (what must be TRUE):
  1. Briefs generate automatically at 10am daily without manual intervention
  2. macOS notification fires with success/failure count when complete
  3. Cron job runs reliably for 7 consecutive days
**Plans**: 1 plan

Plans:
- [ ] 04-01: Configure cron job and verify notification delivery

### Phase 5: Validation & Expansion
**Goal**: Run 30-day validation, measure "acted on 2-3 ideas/week", then add remaining briefs
**Depends on**: Phase 4
**Requirements**: (v2 requirements triggered on success)
**Success Criteria** (what must be TRUE):
  1. Acted on 2-3 ideas per week from briefs for 4 consecutive weeks
  2. Checking briefs daily (open rate >80%)
  3. Total review time consistently under 10 minutes
  4. Decision made: expand to ThreatCaptain + Local Business or pivot
**Plans**: 2 plans

Plans:
- [ ] 05-01: Run 30-day validation with weekly check-ins
- [ ] 05-02: Add ThreatCaptain MSP scanner and Local Business Scraper briefs (conditional on validation success)

## Progress

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Prompt Setup & First Run | 0/2 | Not started | - |
| 2. Content Quality Tuning | 0/2 | Not started | - |
| 3. Runner Hardening | 0/1 | Not started | - |
| 4. Scheduling & Notifications | 0/1 | Not started | - |
| 5. Validation & Expansion | 0/2 | Not started | - |

---
*Roadmap created: 2026-02-13*
*Last updated: 2026-02-13 after requirements definition*
