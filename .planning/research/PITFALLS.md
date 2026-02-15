# PITFALLS: Daily Intelligence Briefs System

## Executive Summary

This document catalogs the primary failure modes of automated daily intelligence systems, with specific focus on AI-generated briefs delivered via cron. Each pitfall includes concrete warning signs and prevention strategies.

---

## 1. Why Automated Daily Report Systems Get Abandoned

### 1.1 Notification Fatigue

**The Problem:**
- Daily emails/notifications become invisible after 2-3 weeks
- Users develop "banner blindness" to automated messages
- High-frequency delivery trains the brain to ignore the content
- The psychological effect compounds when content quality varies

**Specific Manifestations:**
- Emails moved to folders unread (check Gmail filters/rules)
- Slack messages dismissed without opening
- Desktop notifications cleared in batches
- Mobile push notifications disabled system-wide

**Prevention Strategies:**
```
HARD RULES:
1. Delivery time must match actual usage window (when you review briefs)
2. Subject lines must contain 1-2 words of high-signal content
   BAD:  "Daily Brief - February 15"
   GOOD: "Daily Brief - Figma AI Plugin Launch + 3 Design Leads"
3. First line of email/notification = highest value item
4. Variable delivery frequency based on signal strength
   - Skip days with <3 high-quality items
   - Send "nothing significant today" message MAX once per week
5. Require explicit weekly re-confirmation that briefs are valuable
   - Add "Reply STOP if you didn't read this week's briefs" footer
   - Auto-pause after 2 weeks of no engagement
```

**Warning Signs:**
- You can't recall yesterday's brief content without checking
- You've created an email filter to auto-archive briefs
- You open <3 briefs per week (check email analytics)
- You skim in <30 seconds (add read-time tracking)

---

### 1.2 Low Signal-to-Noise Ratio

**The Problem:**
- AI models default to verbosity over precision
- Generic "advice" dilutes actionable intelligence
- Lack of filtering creates 10:1 noise-to-signal ratio
- Users stop trusting the system's judgment

**Specific Manifestations:**
- 80% of content is "nice to know" vs "must act on"
- Same trends/topics repeated across multiple days
- Obvious information you'd see anyway (top HN posts, major launches)
- No clear prioritization or "why this matters to YOU" context

**Prevention Strategies:**
```
CONTENT FILTERING RULES:
1. Every item must pass "So what?" test
   - Include explicit "Why this matters:" section
   - Connect to your specific business domain (creative agency)
   - Quantify potential impact (revenue, time saved, competitive edge)

2. Maximum items per category
   - Content Ideas: 3 (not 10)
   - Business Leads: 5 (qualified only)
   - App Trends: 2 (actionable trends, not all launches)
   - Sales Leads: 5 (pre-filtered by ICP criteria)

3. Recency filter
   - Content ideas: Last 48 hours only
   - App trends: Last 7 days for launches, 30 days for trends
   - Sales leads: Last 24 hours (fresh = competitive advantage)

4. Deduplication
   - Track items shown in last 30 days
   - Block similar topics within 7-day window
   - Vary sources to prevent echo chambers

5. Confidence scoring
   - Only show items with >70% relevance confidence
   - Mark speculative items explicitly
   - Separate "confirmed" vs "emerging" signals
```

**Warning Signs:**
- More than 10 items in any single category
- No clear "top item" or prioritization
- Generic language: "consider", "might be worth", "interesting"
- You can't identify the #1 action item within 15 seconds

---

### 1.3 Stale Content

**The Problem:**
- Cached search results show week-old "news"
- API data sources lag reality by 24-48 hours
- Time zone mismatches create "yesterday's news" syndrome
- No differentiation between evergreen and time-sensitive content

**Specific Manifestations:**
- Reading about product launches you saw announced yesterday
- Sales leads from companies that already hired/decided
- Content trends past their viral peak
- App Store rankings from 3+ days ago (useless for trending apps)

**Prevention Strategies:**
```
FRESHNESS GUARANTEES:
1. Timestamp everything
   - Show "as of [time]" for all data
   - Highlight items <6 hours old with "BREAKING" tag
   - Separate "Today" vs "This Week" vs "Background" sections

2. Source-specific refresh rates
   - News/launches: Real-time to 6 hours
   - Sales leads: 12-24 hours max
   - Trends analysis: 24-48 hours acceptable
   - Market research: 7 days acceptable

3. Time zone handling
   - Run cron at 5-6 AM local time for 8 AM delivery
   - Ensures overnight data is included
   - Allows pre-market information advantage

4. Staleness warnings
   - Mark items >24 hours old explicitly
   - Skip items >7 days old unless tagged "evergreen"
   - Compare fetch time vs publish time

5. Cache invalidation
   - Clear search result caches daily
   - Re-fetch API data on each run (no caching)
   - Exception: Historical comparison data only
```

**Warning Signs:**
- Timestamps show dates in the past
- You've already seen 50%+ of content elsewhere
- No items from the last 6 hours
- Content feels "weekly recap" not "daily brief"

---

## 2. AI-Generated Intelligence Mistakes

### 2.1 Hallucinated Sources

**The Problem:**
- LLMs generate plausible-sounding but fake URLs
- Citations reference non-existent articles or studies
- Quoted statistics lack verifiable sources
- Mixed up attributions (real quote, wrong source)

**Specific Manifestations:**
```
HALLUCINATION EXAMPLES:
- "According to TechCrunch's Feb 2026 report..." (article doesn't exist)
- Statistics without source URLs
- Paraphrased "studies show" without linking study
- Product features that don't exist yet
- Misattributed quotes from similar articles
```

**Prevention Strategies:**
```
VERIFICATION REQUIREMENTS:
1. Source verification loop
   - Every factual claim must have URL
   - Auto-fetch URL to verify accessibility (HTTP 200 check)
   - Extract page title, compare to claimed content
   - Flag mismatches for review

2. Prompt engineering
   - "Only cite sources you can provide working URLs for"
   - "If uncertain, say 'source unverified' explicitly"
   - "Prefer recent primary sources over secondary summaries"
   - "Mark speculation with [SPECULATIVE] tag"

3. Post-generation validation
   - Parse output for all URLs
   - Batch verify all links (curl -I or HEAD request)
   - Remove items with 404/403 sources
   - Log hallucination rate for model tuning

4. Human-in-loop for high stakes
   - Flag business leads >$50k potential for manual review
   - Verify competitive intelligence claims
   - Double-check regulatory/legal information

5. Citation standards
   - Format: [Claim] - Source: [Title](URL) - [Date]
   - Include publication date in citation
   - Prefer direct links over aggregators
```

**Warning Signs:**
- URLs return 404 errors
- Source titles don't match claimed content
- Generic domain names (example.com, placeholder.io)
- Statistics without attribution
- "Studies show" without study name

---

### 2.2 Outdated Trends

**The Problem:**
- Training data cutoff creates blind spots
- AI suggests tactics that worked 12-18 months ago
- Deprecated technologies/platforms recommended
- Missing current platform algorithm changes

**Specific Manifestations:**
```
OUTDATED ADVICE EXAMPLES:
- SEO tactics that trigger current penalties
- Social media algorithms that changed
- Deprecated API endpoints or libraries
- Pricing strategies for old market conditions
- Platform features that were sunset
```

**Prevention Strategies:**
```
CURRENCY ENFORCEMENT:
1. Training data awareness
   - Know your model's cutoff date (Claude: January 2025)
   - Explicitly prompt: "Using only 2026 information..."
   - Cross-reference recommendations with current docs

2. Real-time data injection
   - Fetch current platform documentation
   - Include recent changelog data in prompts
   - Query current API status pages
   - Pull latest algorithm update announcements

3. Trend validation
   - Check recommendation against last 30 days of activity
   - Verify platform features still exist (API health checks)
   - Compare suggested tools against current market leaders
   - Flag recommendations >6 months old

4. Deprecation checking
   - Maintain list of known deprecated practices
   - Check recommended tools against shutdown announcements
   - Verify APIs/services are still operational
   - Track platform TOS/policy changes

5. Temporal context in prompts
   - "What's working in February 2026 specifically?"
   - "Avoid strategies from 2024-2025 unless still valid"
   - "Confirm this tactic works with [platform]'s current algorithm"
```

**Warning Signs:**
- Recommendations reference old interface versions
- Suggested tools you know shut down
- Tactics contradicting recent platform announcements
- No mention of recent major industry changes
- Generic advice that feels "2023-era"

---

### 2.3 Generic Advice

**The Problem:**
- AI defaults to broad, safe recommendations
- Lacks context about your specific business
- Suggests ideas you've already tried/rejected
- No personalization despite narrow domain

**Specific Manifestations:**
```
GENERIC ADVICE RED FLAGS:
- "Consider starting a newsletter" (obvious)
- "Focus on SEO" (not actionable)
- "Leverage social media" (which platform? how?)
- "Build an audience" (every brief says this)
- Content ideas matching top Google results
```

**Prevention Strategies:**
```
PERSONALIZATION REQUIREMENTS:
1. Context injection in every prompt
   - Your ICP: [specific customer profile]
   - Your services: [exact offerings]
   - Your constraints: [time, budget, team size]
   - Your past actions: [tried newsletter, failed on TikTok, etc.]
   - Your advantages: [design expertise, agency credibility]

2. Negative filtering
   - Maintain "already doing" list (newsletter, blog, etc.)
   - Block "already tried and failed" recommendations
   - Exclude ideas outside your service scope
   - Filter out tactics requiring >10 hours/week

3. Specificity requirements
   - Ideas must include exact platform + tactic
   - Business leads must match ICP explicitly
   - Content ideas must specify format + distribution
   - Trends must connect to specific service offerings

4. Differentiation scoring
   - Compare generated advice to top Google results
   - Reject ideas in >3 competitor blogs
   - Prioritize contrarian/emerging approaches
   - Favor niche tactics over mainstream advice

5. Action-orientation
   - Every item needs 1-3 sentence "how to execute"
   - Include time estimate for implementation
   - List prerequisites/dependencies
   - Provide success metrics
```

**Warning Signs:**
- Advice applies to any business (not just yours)
- Could have been generated by GPT-3 in 2023
- No specific numbers, names, or platforms
- You've seen identical advice in generic marketing blogs
- Zero references to your actual service offerings

---

## 3. Cron Job Reliability on macOS

### 3.1 Sleep/Lid Closed Issues

**The Problem:**
- Cron does not wake Mac from sleep
- Jobs scheduled during sleep are skipped (not queued)
- Laptop lid closed = missed jobs
- No automatic catch-up when machine wakes

**Specific Manifestations:**
- Brief arrives at 11 AM instead of 7 AM (when you opened laptop)
- Missed entirely if you worked remote that day
- Inconsistent delivery creates loss of habit
- Weekend/vacation gaps break the routine

**Prevention Strategies:**
```
SOLUTION HIERARCHY:
1. Use launchd instead of cron (macOS native)
   - Create ~/Library/LaunchAgents/com.yourdomain.dailybrief.plist
   - Better power management integration
   - Can wake system with proper permissions

2. launchd configuration for reliability
   <key>StartCalendarInterval</key>
   <dict>
       <key>Hour</key>
       <integer>6</integer>
       <key>Minute</key>
       <integer>30</integer>
   </dict>
   <key>RunAtLoad</key>
   <true/>  <!-- Run if missed during sleep -->

3. Power assertions (prevent sleep during execution)
   caffeinate -i -t 600 /path/to/your/script.sh
   - Keeps system awake for 10 min during execution
   - Prevents mid-execution sleep

4. Fallback detection
   - Log last successful run timestamp
   - On next run, check if >36 hours since last
   - Generate "catch-up brief" with extended timeframe
   - Send notification: "Missed 2 days, here's summary"

5. Always-on alternative
   - Cloud VM running job (AWS t4g.nano = $3/month)
   - Sends brief regardless of local machine state
   - Your Mac just receives results
   - Trade cost for reliability
```

**Technical Implementation:**
```bash
# Check if script should run catch-up
LAST_RUN=$(cat ~/.dailybrief_lastrun 2>/dev/null || echo "0")
NOW=$(date +%s)
DIFF=$((NOW - LAST_RUN))

if [ $DIFF -gt 129600 ]; then  # 36 hours
    echo "Catch-up mode: generating 3-day brief"
    DAYS_MISSED=$((DIFF / 86400))
    # Adjust brief generation for extended timeframe
fi

# Update timestamp
echo "$NOW" > ~/.dailybrief_lastrun
```

**Warning Signs:**
- Inconsistent delivery times (varies by >2 hours)
- Missing briefs with no error logs
- Briefs only arrive when you're actively using Mac
- Weekend/Monday gaps

---

### 3.2 Permission and Path Issues

**The Problem:**
- Cron runs with minimal environment variables
- PATH doesn't include Homebrew, user bins
- API keys in .zshrc/.bashrc not loaded
- File permission errors in user directories

**Prevention Strategies:**
```
ENVIRONMENT SETUP:
1. Explicit PATHs in cron script
   #!/bin/bash
   export PATH="/usr/local/bin:/usr/bin:/bin:/opt/homebrew/bin:$PATH"
   export CLAUDE_API_KEY="sk-ant-..."  # Don't rely on shell profile

2. Use absolute paths everywhere
   BAD:  claude api messages...
   GOOD: /opt/homebrew/bin/claude api messages...

3. API key management
   - Store in dedicated config file with proper permissions
   - chmod 600 ~/.dailybrief_config
   - Source at script start: source ~/.dailybrief_config
   - Never rely on interactive shell environment

4. Test cron environment
   * * * * * env > /tmp/cron-env.txt
   - Compare to interactive shell: env > /tmp/shell-env.txt
   - Identify missing variables
   - Explicitly set in script

5. Logging with full paths
   LOGFILE="/Users/yourusername/logs/dailybrief.log"
   exec 1>>$LOGFILE 2>&1  # Redirect all output
   date; echo "Starting daily brief generation..."
```

**Warning Signs:**
- "Command not found" in logs
- Works manually, fails in cron
- API authentication errors in cron only
- Empty output files

---

### 3.3 Monitoring and Alerting

**The Problem:**
- Silent failures go unnoticed for days
- No visibility into why cron didn't run
- Errors lost in system logs

**Prevention Strategies:**
```
MONITORING IMPLEMENTATION:
1. Heartbeat monitoring
   - Use healthchecks.io (free tier: 20 checks)
   - curl https://hc-ping.com/YOUR-UUID at script end
   - Alerts if job doesn't ping within schedule window

2. Exit code checking
   claude api messages ... || {
       echo "ERROR: Claude API failed" | mail -s "Brief Failed" you@email.com
       curl https://hc-ping.com/YOUR-UUID/fail
       exit 1
   }

3. Log rotation and review
   - Rotate logs weekly: logrotate config
   - Keep last 4 weeks of logs
   - Weekly log review for warning patterns

4. Success criteria
   - Verify output file exists and has >1000 chars
   - Check for "[GENERATED:" tag in output
   - Confirm timestamp is current
   - Validate email sent (check mail queue)

5. Fallback notifications
   if [ ! -f "/tmp/dailybrief_success" ]; then
       osascript -e 'display notification "Daily brief failed to generate" with title "Brief System Alert"'
   fi
```

---

## 4. Cost Traps with Claude API at Scale

### 4.1 Token Usage Explosion

**The Problem:**
- Long prompts with full context = expensive
- Output tokens 3-5x more expensive than input
- Unnecessary verbosity multiplies costs
- Daily usage compounds quickly

**Cost Reality Check:**
```
EXAMPLE CALCULATION (Claude Opus 4.6):
Input tokens:  $15 per 1M tokens
Output tokens: $75 per 1M tokens

Daily brief generation:
- System prompt: 2,000 tokens
- Web search results: 8,000 tokens (4 searches)
- Output brief: 3,000 tokens
Total per day: 10k input + 3k output = $0.375/day

Monthly: $11.25
Yearly: $135

BUT if you add:
- Retry logic (3x on failures): $405/year
- Multiple brief versions: 2x = $810/year
- Verbose output (6k tokens): $337.50/year
- Extended context: +5k tokens/day = +$78.75/year

Realistic annual cost: $300-500
```

**Prevention Strategies:**
```
COST OPTIMIZATION:
1. Token budgets per section
   - Content ideas: 800 tokens max
   - Business leads: 1000 tokens max
   - App trends: 600 tokens max
   - Sales leads: 600 tokens max
   - Total output cap: 3000 tokens

2. Prompt optimization
   - Remove example outputs (save 1k+ tokens)
   - Use compact formatting instructions
   - Avoid repeating context in multi-turn conversations
   - Cache static portions if API supports it

3. Tiered intelligence
   - Quick scan (Haiku): $0.03/day for filtering
   - Full brief (Sonnet): $0.12/day for generation
   - Deep analysis (Opus): Only for weekly summaries
   - Could reduce daily cost by 60%

4. Smart retries
   - Don't retry immediately (rate limits)
   - Exponential backoff: 10s, 60s, 300s
   - Max 2 retries per generation
   - Log retry reasons to fix root cause

5. Usage monitoring
   - Log tokens used per run
   - Alert if >15k tokens/day (spike detection)
   - Monthly budget check: pause if >$20/month
   - Analyze which sections are most expensive
```

**Implementation Example:**
```python
# Token budget enforcement
MAX_OUTPUT_TOKENS = 3000

response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=MAX_OUTPUT_TOKENS,  # Hard cap
    messages=[{"role": "user", "content": prompt}]
)

# Log for monitoring
tokens_used = response.usage.input_tokens + response.usage.output_tokens
cost = (response.usage.input_tokens * 0.000015) + (response.usage.output_tokens * 0.000075)

with open("~/.dailybrief_usage.log", "a") as f:
    f.write(f"{date.today()},{tokens_used},{cost:.4f}\n")
```

**Warning Signs:**
- Monthly bill >$25 for simple daily briefs
- Token usage increasing week-over-week
- Output consistently hitting max_tokens limit
- Retry loops consuming 3x+ expected tokens

---

### 4.2 API Rate Limits and Failures

**The Problem:**
- Rate limits vary by tier (10 req/min default)
- Cascading failures if one search times out
- No graceful degradation on API errors
- Retries without backoff hit limits faster

**Prevention Strategies:**
```
RATE LIMIT HANDLING:
1. Understand your limits (check Anthropic dashboard)
   - Free tier: 5 requests/minute, 300k tokens/day
   - Tier 1: 50 requests/minute, 5M tokens/day
   - Know which tier you're on

2. Sequential vs parallel requests
   - Don't send 10 searches simultaneously
   - Rate limit: 1 request per 6 seconds = 10/minute
   - Use sleep between requests

   for search in searches; do
       result=$(claude api messages ...)
       sleep 7  # Stay under rate limit
   done

3. Graceful degradation
   - If web search fails, use cached results from yesterday
   - If API times out, send partial brief
   - Include "Generated with limited data" warning
   - Don't fail entirely on one component error

4. Timeout configuration
   - Set reasonable timeouts (30s for API calls)
   - Don't let one stuck request block entire job
   - Use command timeout: timeout 30s claude api ...

5. Error handling hierarchy
   - Retry on 429 (rate limit) with backoff
   - Retry on 500/502/503 (server error) once
   - Don't retry on 401/403 (auth errors)
   - Don't retry on 400 (bad request)
```

**Warning Signs:**
- Frequent "Rate limit exceeded" errors
- Timeouts during peak hours
- Partial briefs sent regularly
- API calls taking >2 minutes

---

## 5. The "Dashboard Nobody Checks" Antipattern

### 5.1 Why Dashboards Fail

**The Problem:**
- Requires active pulling of information (high friction)
- No notification = out of sight, out of mind
- Dashboard staleness not visible to user
- Competes with existing tools (Slack, email, etc.)

**Specific Manifestations:**
- Built beautiful dashboard, checked it twice
- Data updates daily, last viewed 3 weeks ago
- Bookmarked but never in daily workflow
- Recreates information available elsewhere

**Prevention Strategies:**
```
DELIVERY MECHANISM RULES:
1. Meet users where they are
   - Email (primary): Zero friction, habitual inbox checking
   - Slack/Teams DM: For teams already in platform
   - SMS: For ultra-critical alerts only
   - Dashboard: For deep-dives only, not primary delivery

2. Push > Pull architecture
   - Brief comes to you (email at 7 AM)
   - Dashboard exists for "read more" links
   - Never require visiting URL to see primary value
   - Dashboard shows history, not current day

3. Notification design
   - Subject line = scannable summary
   - First 100 words = highest value items
   - "View full brief" link below fold
   - Mobile-optimized formatting

4. Integration over destination
   - Notion integration: Posts to your daily page
   - Obsidian: Appends to daily note
   - Calendar: Adds brief link to first meeting
   - Goes where you already work

5. Dashboard purpose redefinition
   - Archive of past briefs (search history)
   - Trend visualization (not daily content)
   - Settings/preferences configuration
   - Performance metrics (open rate, click rate)
```

**Warning Signs:**
- Dashboard analytics show <3 visits/week
- Last visit was to debug/configure, not consume
- Users ask "where do I see this again?"
- Built-in analytics show 80%+ bounce rate

---

### 5.2 Email Deliverability Issues

**The Problem:**
- Automated emails flagged as spam
- Consistent format trains spam filters
- No SPF/DKIM/DMARC on sending domain
- Large HTML emails trigger size filters

**Prevention Strategies:**
```
EMAIL DELIVERY BEST PRACTICES:
1. Sending infrastructure
   - Use real email provider (SendGrid, Mailgun, AWS SES)
   - Don't send from Gmail/personal email via SMTP
   - Configure SPF/DKIM/DMARC records
   - Use dedicated subdomain: briefs.yourdomain.com

2. Content signals
   - Plain text version included (multipart/alternative)
   - No spammy words: "FREE", "ACT NOW", excessive caps
   - Reasonable HTML (avoid huge images, tracking pixels)
   - Consistent from address and name

3. User engagement
   - Encourage "reply to this email with thoughts"
   - Track opens/clicks to show engagement
   - Remove inactive recipients after 30 days no-open
   - Whitelist instructions in first email

4. Format optimization
   - Keep emails <100KB total
   - Use email-safe HTML (tables, not divs)
   - Test across clients (Apple Mail, Gmail, Outlook)
   - Include text version for terminal users

5. Authentication testing
   - Send to mail-tester.com for spam score
   - Check SPF/DKIM pass: Gmail "show original"
   - Monitor bounce rates (<2% acceptable)
   - Track spam complaint rates (<0.1% acceptable)
```

**Warning Signs:**
- Emails in spam folder
- Inconsistent delivery times (delayed by filters)
- Bounces from corporate email servers
- Images/links broken in certain clients

---

## 6. System Failure Warning Signs

### 6.1 Early Warning Indicators

Monitor these metrics to catch degradation before abandonment:

```
METRIC TRACKING:
1. Engagement metrics (weekly review)
   - Open rate: <60% = declining interest
   - Time-to-open: >4 hours = not priority
   - Click-through: <20% = low actionability
   - Reply rate: <1/month = no dialogue

2. Content quality metrics (daily log)
   - Items per brief: <5 = not enough value
   - Items per brief: >20 = too much noise
   - Unique sources: <3 = echo chamber
   - Verification failures: >1/week = hallucination issue

3. System reliability metrics (automated alerts)
   - Missed jobs: >1/month = cron problem
   - Generation time: >5 min = API issues
   - Error rate: >5% = stability problem
   - Cost per brief: >$0.50 = optimization needed

4. Behavioral metrics (honest self-assessment)
   - Days since last action taken: >14 = not actionable
   - Briefs archived unread: >3/week = notification fatigue
   - Time spent reviewing: <2 min = skimming
   - Memory of yesterday's top item: Can't recall = not memorable
```

### 6.2 Kill Criteria (When to Pause/Pivot)

```
STOP CONDITIONS:
1. Immediate stop signals
   - Cost >$50/month with no ROI
   - Spam complaints from your own email
   - Failed deliveries >3 days consecutive
   - API key compromised/leaked

2. Two-week evaluation pause
   - <30% open rate for 2 weeks
   - Zero actions taken in 14 days
   - Can't identify value gained
   - Dread receiving the brief

3. Pivot signals (change approach, don't kill)
   - Good ideas, wrong delivery method
   - Valuable 2 days/week, not daily
   - One category valuable, others not
   - Format too long/short

4. Success criteria (keep going)
   - 1+ action taken per week from brief
   - Open rate >70%
   - Can articulate value in 1 sentence
   - Would miss it if it stopped
```

---

## 7. Prevention Strategy Summary

### 7.1 Pre-Launch Checklist

Before deploying daily briefs system:

```
VALIDATION STEPS:
☐ Content quality
  ☐ Generated 5 sample briefs
  ☐ 80%+ items were novel/valuable
  ☐ All sources verified (no hallucinations)
  ☐ Specific to your business (not generic)

☐ Delivery reliability
  ☐ Tested cron/launchd on sleep/wake cycle
  ☐ Verified email deliverability (inbox, not spam)
  ☐ Set up monitoring/alerting (healthchecks.io)
  ☐ Documented troubleshooting steps

☐ Cost controls
  ☐ Calculated monthly token budget
  ☐ Set up usage logging
  ☐ Configured max_tokens limits
  ☐ Tested API error handling

☐ User experience
  ☐ Brief readable in <3 minutes
  ☐ Top item identifiable in 10 seconds
  ☐ Mobile formatting tested
  ☐ "Read more" links work

☐ Monitoring
  ☐ Success metrics defined
  ☐ Weekly review calendar event
  ☐ Kill criteria documented
  ☐ Feedback mechanism in place
```

### 7.2 Weekly Health Check

Every Monday, review:

```
WEEKLY REVIEW (10 minutes):
1. Engagement check
   - Did I read all 7 briefs this week?
   - Did I take action on any items?
   - Which day had the best content?

2. System health
   - Check error logs for failures
   - Review token usage vs budget
   - Verify all 7 briefs delivered on time

3. Content quality spot check
   - Pick 2 random sources, verify accuracy
   - Check for duplicate items across days
   - Assess signal-to-noise ratio

4. Optimization opportunities
   - Which category was least useful this week?
   - Any repeated patterns that should be filtered?
   - Ideas for improving relevance?

5. Decision point
   - Continue as-is
   - Adjust filters/parameters
   - Pause for rework
   - Kill system
```

---

## 8. Recovery Playbook

### 8.1 When the System is Failing

If you notice warning signs, follow this recovery process:

```
RECOVERY STEPS:
1. Immediate triage (Day 1)
   - Stop daily generation (prevent more noise)
   - Review last 7 days of briefs
   - Identify specific failure mode(s)
   - Document what's not working

2. Root cause analysis (Day 2-3)
   - Engagement issue? → Content quality problem
   - Not reading? → Notification fatigue
   - Reading but not acting? → Actionability problem
   - Technical failures? → Infrastructure problem

3. Targeted fixes (Day 4-7)
   - Content: Tighten filters, reduce volume
   - Fatigue: Change delivery time or frequency
   - Actionability: Add "next steps" to each item
   - Infrastructure: Fix cron, add monitoring

4. Pilot restart (Week 2)
   - Generate 3 briefs with new parameters
   - Self-review before delivery
   - Manually verify quality meets bar
   - Resume delivery if passing quality check

5. Monitored operation (Week 3-4)
   - Daily quality checks
   - Track engagement closely
   - Iterate on feedback
   - Decide: full resume or kill
```

### 8.2 Common Recovery Scenarios

```
SCENARIO: Notification Fatigue
Symptoms: Not opening emails, auto-archiving
Fix:
  1. Reduce frequency: 3x/week instead of daily
  2. Improve subject lines: specific value prop
  3. Change delivery time: experiment with evening
  4. Add weekly digest option

SCENARIO: Low Signal-to-Noise
Symptoms: Skimming in <1 minute, can't recall content
Fix:
  1. Cut items per brief in half (10 → 5)
  2. Add confidence scoring, filter <80%
  3. Strengthen "why this matters" explanations
  4. Remove entire category if consistently weak

SCENARIO: Stale Content
Symptoms: Already seen 50%+ elsewhere
Fix:
  1. Reduce search timeframe (7 days → 24 hours)
  2. Add deduplication against popular sources
  3. Prioritize niche/emerging over mainstream
  4. Run cron earlier (5 AM vs 7 AM)

SCENARIO: Cron Unreliability
Symptoms: Inconsistent delivery, missed days
Fix:
  1. Switch to launchd from cron
  2. Add RunAtLoad for catch-up
  3. Set up heartbeat monitoring
  4. Consider cloud VM alternative

SCENARIO: Cost Overruns
Symptoms: >$30/month for daily briefs
Fix:
  1. Reduce max_tokens by 50%
  2. Use Haiku for filtering, Sonnet for generation
  3. Optimize prompts (remove examples)
  4. Reduce retry attempts
```

---

## 9. Success Patterns

Conversely, systems that survive long-term share these traits:

### 9.1 Characteristics of Sustainable Brief Systems

```
WHAT WORKS:
1. Hyper-specific domain
   - "React component library trends" vs "web development"
   - "B2B SaaS leads in healthcare" vs "business leads"
   - Narrow = high signal, defensible value

2. Variable frequency
   - Daily scan, but only send if 5+ quality items found
   - "No brief today, low signal" is acceptable
   - Quality gating prevents fatigue

3. Opinionated filtering
   - Explicit bias toward your ICP, services, goals
   - Aggressive filtering (show 5, hide 50)
   - Confidence scoring visible

4. Actionability requirement
   - Every item has "suggested action" section
   - Time estimates for implementation
   - Clear success metrics

5. Feedback loop
   - "Was this useful?" link with quick vote
   - Track which items you acted on
   - Adjust filters based on engagement
   - User input shapes future briefs

6. Integration into workflow
   - Lands in existing tool (email, Slack, Notion)
   - Arrives at optimal time (pre-workday)
   - Format matches consumption habit (mobile, desktop)
   - Zero friction to review
```

---

## Conclusion

The majority of automated intelligence systems fail not from technical issues, but from violating core product principles:

1. **Respect attention**: Every notification must earn its interruption
2. **Prioritize ruthlessly**: 5 great items > 20 mixed quality items
3. **Stay current**: Stale intelligence is worse than no intelligence
4. **Be specific**: Generic advice is ignored advice
5. **Prove value constantly**: Users re-evaluate ROI weekly

This daily brief system will succeed if it:
- Surfaces 1-2 items per day you couldn't have found yourself
- Arrives reliably at the same time daily
- Takes <3 minutes to review
- Generates 1+ action per week
- Costs <$20/month

If it fails any of these criteria for 2+ consecutive weeks, pause and diagnose using the frameworks in this document.

**The ultimate test**: If the system stopped tomorrow, would you notice within 3 days? If no, it has already failed.