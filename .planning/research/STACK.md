# STACK Research — Daily Intelligence Briefs System

## Executive Summary

For a macOS-based automated AI brief system in 2026, the optimal stack is:
- **Scheduler**: launchd (not cron)
- **AI Engine**: Claude Code CLI with Sonnet 4.5/Opus 4.6
- **Data Sources**: Claude's native web search + targeted API integrations
- **Output Format**: Markdown primary, HTML/email secondary
- **Notifications**: terminal-notifier + Pushover for reliability
- **Orchestration**: Shell scripts + Python for complex workflows

---

## 1. AI-Powered Automation Tools for Daily Reports (2026)

### Primary Recommendation: Claude Code CLI

**Why Claude Code CLI:**
- Already integrated with your system
- Built-in web search capabilities (no separate API needed)
- Excellent at structured reasoning and markdown generation
- `--print` mode perfect for non-interactive automation
- Cost-effective via subscription vs. per-token API pricing
- Sonnet 4.5 offers speed, Opus 4.6 for complex analysis

**Implementation Pattern:**
```bash
claude code --print "Generate Sullivan Creative daily brief" < prompt.md > output.md
```

**Alternatives Evaluated:**

| Tool | Pros | Cons | Verdict |
|------|------|------|---------|
| **OpenAI GPT-4 API** | Strong reasoning, function calling | No native web search, higher cost, rate limits | Skip for now |
| **Anthropic API (direct)** | More control, batch processing | Requires separate subscription, no CLI convenience | Use if scaling beyond macOS |
| **Google Gemini** | Good at data synthesis | Less reliable for structured output, weaker reasoning | Not recommended |
| **Local LLMs (Ollama, LM Studio)** | Privacy, no API costs | No web search, lower quality, slower | Wrong tool for this use case |
| **LangChain + Claude** | Workflow orchestration | Overengineered for daily briefs, adds complexity | Only if adding complex multi-step workflows |

### Secondary Tools for Enhancement

**For Web Data Enrichment:**
- **Apify** (apify.com): Pre-built scrapers for Reddit, Google Maps, social media
  - Use case: MSP lead scanning on Reddit, local business discovery
  - Cost: $49/mo for 100k credits (sufficient for daily briefs)
  - Integration: REST API, returns JSON

- **Brave Search API** (brave.com/search/api): 2k free queries/month
  - Use case: Supplement Claude's web search with fresh data
  - Better than: Google Custom Search (deprecated features), Bing (higher cost)

- **RSS/Atom Feeds**: Free, reliable for trend monitoring
  - Tools: `feedparser` (Python), `rss` (curl + jq)
  - Use case: App trends from Product Hunt, Hacker News, IndieHackers

**For Data Processing:**
- **jq**: JSON parsing for API responses
- **Pandoc**: Format conversion if needed (markdown → HTML → PDF)
- **Python + Beautiful Soup**: Custom scraping when APIs unavailable

---

## 2. macOS Scheduling: launchd vs. Cron

### Recommendation: Migrate to launchd

**Why launchd Over Cron:**

| Feature | launchd | cron |
|---------|---------|------|
| **macOS Native** | Yes, Apple's recommended tool | Legacy Unix tool |
| **Runs when missed** | Can catch up if Mac was asleep | Skips missed jobs |
| **Environment variables** | Full user environment | Limited, requires sourcing |
| **Logging** | Integrated with Console.app | Requires manual setup |
| **Persistence** | Survives system updates | Can be overwritten |
| **StartCalendarInterval** | More flexible scheduling | Basic cron syntax |
| **Power management** | Better integration | No awareness of sleep/wake |

**Critical Limitation of Cron:**
If your Mac is asleep at 10am, cron will NOT run the job when it wakes. launchd can be configured to run missed jobs on wake.

### launchd Implementation

**Create**: `~/Library/LaunchAgents/com.sullivancreative.dailybriefs.plist`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.sullivancreative.dailybriefs</string>

    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>/Users/sullivancreativeco./daily-briefs/run-briefs.sh</string>
    </array>

    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>10</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>

    <key>StandardOutPath</key>
    <string>/Users/sullivancreativeco./daily-briefs/logs/stdout.log</string>

    <key>StandardErrorPath</key>
    <string>/Users/sullivancreativeco./daily-briefs/logs/stderr.log</string>

    <key>EnvironmentVariables</key>
    <dict>
        <key>PATH</key>
        <string>/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
    </dict>

    <!-- Run on wake if missed -->
    <key>LaunchOnlyOnce</key>
    <false/>
</dict>
</plist>
```

**Load/Manage:**
```bash
# Load agent
launchctl load ~/Library/LaunchAgents/com.sullivancreative.dailybriefs.plist

# Unload agent
launchctl unload ~/Library/LaunchAgents/com.sullivancreative.dailybriefs.plist

# Test immediately
launchctl start com.sullivancreative.dailybriefs

# Check status
launchctl list | grep dailybriefs

# View logs
tail -f ~/daily-briefs/logs/stderr.log
```

### Alternative: Keep Cron with Safeguards

If you prefer cron's simplicity, add runtime checks:

```bash
# Crontab entry
0 10 * * * /Users/sullivancreativeco./daily-briefs/run-briefs.sh >> /Users/sullivancreativeco./daily-briefs/logs/cron.log 2>&1

# In run-briefs.sh, add catchup logic
LAST_RUN_FILE="$HOME/daily-briefs/.last-run"
TODAY=$(date +%Y-%m-%d)

if [[ -f "$LAST_RUN_FILE" ]] && [[ "$(cat "$LAST_RUN_FILE")" == "$TODAY" ]]; then
    echo "Already ran today, skipping"
    exit 0
fi

# Run briefs...

echo "$TODAY" > "$LAST_RUN_FILE"
```

**Verdict**: Start with cron (already working), migrate to launchd after validation if sleep/wake becomes an issue.

---

## 3. Web Search & Scraping for Trend Detection

### Strategy: Hybrid Approach

**Tier 1: Claude's Native Web Search (Primary)**
- Built into Claude Code CLI
- No additional API costs
- Good for: Content trends, general research, app ideas
- Limitation: Not real-time, can't access certain sites, no fine-grained control

**Tier 2: Targeted APIs (When Needed)**

| Use Case | Tool | Cost | Integration |
|----------|------|------|-------------|
| **Reddit MSP Leads** | Apify Reddit Scraper | $49/mo (100k credits) | REST API → JSON |
| **Local Business Leads** | Google Maps API | $5/1000 requests | REST API (careful with quotas) |
| **App Trends** | Product Hunt API | Free (public posts) | GraphQL endpoint |
| **General Web Search** | Brave Search API | 2k free/mo, $5/1k after | REST API → JSON |
| **RSS Feeds** | Built-in (curl/feedparser) | Free | Native XML parsing |

**Tier 3: Custom Scraping (Last Resort)**

When APIs don't exist (e.g., indie hacker forums, niche communities):

**Tools:**
- **Python + Beautiful Soup 4**: HTML parsing
- **Playwright** (headless browser): For JavaScript-heavy sites
- **curl + jq**: Quick API endpoint testing

**Legal/Ethical Considerations:**
- Respect `robots.txt`
- Rate limit requests (1 req/sec minimum delay)
- Cache aggressively (daily briefs don't need sub-hour freshness)
- Prefer official APIs over scraping
- Don't scrape paywalled content

**Anti-Patterns:**
- **Selenium for scraping**: Too slow, use Playwright instead
- **Puppeteer**: Node.js adds dependency complexity
- **Scraping without User-Agent**: Gets blocked immediately
- **No caching**: Wastes bandwidth and risks rate limits

### Recommended Scraping Architecture

```
┌─────────────────┐
│  Claude Code    │  ← Primary intelligence layer
│  CLI + Search   │
└────────┬────────┘
         │
         ├─→ [Direct prompts] → Markdown output
         │
         └─→ [Enrichment needed?]
                    ↓
         ┌──────────────────────┐
         │  Scraper Scripts     │
         │  (Python/Bash)       │
         └──────────┬───────────┘
                    │
         ├──→ Apify API (Reddit, GMaps)
         ├──→ Brave Search API
         ├──→ RSS feeds (feedparser)
         └──→ Custom scrapers (BS4)
                    ↓
         ┌──────────────────────┐
         │  Cached JSON Data    │
         │  (~/.cache/briefs/)  │
         └──────────┬───────────┘
                    │
         [Inject into Claude prompts as context]
```

**Example Workflow (MSP Lead Scanning):**

```bash
# 1. Fetch Reddit data via Apify
curl -X POST "https://api.apify.com/v2/acts/apify~reddit-scraper/runs" \
     -H "Authorization: Bearer $APIFY_TOKEN" \
     -d '{"searchKeywords": "MSP lead generation", "subreddits": ["msp"]}' \
     > /tmp/reddit-raw.json

# 2. Process with jq
jq '[.posts[] | {title, author, url, upvotes, created}]' /tmp/reddit-raw.json \
   > ~/.cache/briefs/msp-leads-$(date +%Y-%m-%d).json

# 3. Pass to Claude Code
claude code --print "Analyze MSP leads and generate ThreatCaptain brief" \
     --context "Reddit data: $(cat ~/.cache/briefs/msp-leads-*.json)" \
     < threatcaptain-prompt.md > threatcaptain-brief.md
```

---

## 4. Output Format Best Practices

### Recommendation: Markdown Primary, HTML/Email Secondary

**Format Decision Matrix:**

| Format | Pros | Cons | Use Case |
|--------|------|------|----------|
| **Markdown** | Human-readable, version control friendly, universal | Requires viewer/editor | **Primary** - Daily file storage |
| **HTML** | Rich formatting, embeddable images, universal rendering | Harder to edit, verbose source | Secondary - Email delivery (future) |
| **PDF** | Professional, print-ready, consistent rendering | Not editable, requires tooling | Skip for now |
| **JSON** | Machine-readable, API-friendly | Not human-readable | Skip - No integrations yet |
| **Plain Text** | Universal compatibility | No formatting | Skip - Markdown is just as universal |

### Markdown Best Practices for Daily Briefs

**Structure Template:**

```markdown
# [Brand] Daily Brief — [Date]

## Quick Wins (< 15 min)
- **[Idea]**: [Specific action] → [Expected outcome]

## Content Ideas
1. **[Trending Topic]**
   - **Why it matters**: [Context]
   - **Angle**: [Your unique take]
   - **Source**: [Link]

## Leads / Opportunities
- **[Business/Person]**
  - **Context**: [Why they matter]
  - **Outreach angle**: [Value-first approach]
  - **Contact**: [Where to reach them]

## Trends to Watch
- [Trend 1]: [Why + source]
- [Trend 2]: [Why + source]

---
*Generated by Claude Code CLI at [timestamp]*
```

**Formatting Guidelines:**
- Use `##` for sections (not `#` - reserved for title)
- Bold for **emphasis** on actionable items
- Links must be absolute URLs (not relative)
- Dates in ISO format (YYYY-MM-DD) for sortability
- Emojis: NO (distracting, not professional)
- Code blocks: Only if showing actual code examples
- Tables: For comparison data (e.g., app metrics)

**File Naming Convention:**
```
~/daily-briefs/
├── sullivan-creative/
│   ├── 2026-02-15-sullivan.md
│   ├── 2026-02-14-sullivan.md
│   └── archive/
├── keegareaux-labs/
│   ├── 2026-02-15-keegareaux.md
│   └── ...
└── threatcaptain/
    ├── 2026-02-15-threatcaptain.md
    └── ...
```

### HTML Generation (For Future Email Delivery)

**Use Pandoc for Conversion:**

```bash
pandoc sullivan-2026-02-15.md \
       --from markdown \
       --to html5 \
       --standalone \
       --css=/path/to/email-styles.css \
       --metadata title="Sullivan Creative Brief - Feb 15" \
       -o sullivan-2026-02-15.html
```

**Email-Specific HTML Considerations:**
- Inline CSS (email clients strip `<style>` tags)
- No external images (embed as base64 or use hosted URLs)
- Test in Apple Mail, Gmail, Outlook
- Plain text fallback required

**Tools for Email Delivery (Future):**
- **msmtp**: Lightweight SMTP client for macOS
- **Mailgun API**: Reliable, $35/mo for 50k emails
- **Postmark**: Developer-friendly, similar pricing
- **SendGrid**: More enterprise, avoid unless scaling

**Verdict**: Keep markdown for validation phase. Add HTML/email after proving content value.

---

## 5. Notification Systems Beyond osascript

### Current Setup: osascript (macOS Display Notifications)

```bash
osascript -e 'display notification "Daily briefs complete" with title "Intelligence System"'
```

**Limitations:**
- Only works when Mac is unlocked
- Disappears quickly (no persistence)
- No mobile notifications
- Can't attach files or links
- Easily missed if working on other screen

### Recommended Alternatives

**Tier 1: terminal-notifier (Enhanced osascript)**

```bash
# Install via Homebrew
brew install terminal-notifier

# Send notification with action
terminal-notifier \
    -title "Daily Briefs Ready" \
    -message "Sullivan + Keegareaux briefs generated" \
    -open "file:///Users/sullivancreativeco./daily-briefs/" \
    -sound default \
    -group dailybriefs
```

**Advantages:**
- Clickable notifications (opens folder)
- Sound alerts
- Grouped notifications (avoid spam)
- Still native macOS, no external service

**Tier 2: Pushover (Cross-Device Notifications)**

**Why Pushover:**
- $5 one-time per platform (iOS/Android)
- No monthly fees
- 10k API calls/month free
- Works when Mac is closed
- Priority levels (silent → emergency)
- Rich notifications (links, images)

**Setup:**
```bash
# Get API token from pushover.net
curl -s \
  --form-string "token=YOUR_APP_TOKEN" \
  --form-string "user=YOUR_USER_KEY" \
  --form-string "title=Daily Briefs Complete" \
  --form-string "message=Sullivan Creative: 3 ideas | Keegareaux: 2 trends" \
  --form-string "url=file:///Users/sullivancreativeco./daily-briefs/" \
  --form-string "priority=0" \
  https://api.pushover.net/1/messages.json
```

**Tier 3: Slack/Discord Webhooks (Team Notifications)**

If scaling to team usage:

```bash
# Slack webhook
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"Daily briefs ready: Sullivan Creative + Keegareaux Labs"}' \
  https://hooks.slack.com/services/YOUR/WEBHOOK/URL

# Discord webhook
curl -X POST -H "Content-Type: application/json" \
  -d '{"content":"Daily briefs complete!"}' \
  https://discord.com/api/webhooks/YOUR_WEBHOOK
```

### Anti-Recommendations

**Do NOT Use:**
- **Growl**: Deprecated, no longer maintained
- **SMS via Twilio**: Overkill for personal use, costs add up
- **Email notifications**: Adds complexity, email fatigue
- **Apple Shortcuts notifications**: Less reliable than terminal-notifier
- **Custom notification daemons**: Overengineered

### Recommended Notification Stack

```
┌──────────────────────┐
│  Brief Generation    │
│  Completes           │
└──────────┬───────────┘
           │
           ├─→ terminal-notifier (macOS native)
           │   └─> Opens ~/daily-briefs/ on click
           │
           └─→ Pushover API (mobile backup)
               └─> Sends to iPhone/Watch
```

**Implementation:**

```bash
#!/bin/bash
# notify.sh

MESSAGE="$1"
BRIEFS_PATH="$2"

# Local notification
terminal-notifier \
    -title "Daily Briefs" \
    -message "$MESSAGE" \
    -open "file://$BRIEFS_PATH" \
    -sound Ping \
    -group dailybriefs

# Mobile notification (if configured)
if [[ -n "$PUSHOVER_TOKEN" ]]; then
    curl -s -X POST https://api.pushover.net/1/messages.json \
        -F "token=$PUSHOVER_TOKEN" \
        -F "user=$PUSHOVER_USER" \
        -F "message=$MESSAGE" \
        -F "priority=0" \
        > /dev/null
fi
```

---

## 6. What NOT to Use and Why

### Avoid These Tools/Approaches

| Tool/Approach | Why to Avoid | Use Instead |
|---------------|--------------|-------------|
| **GPT-3.5** | Outdated reasoning, hallucinates more | Claude Sonnet 4.5 minimum |
| **Zapier/Make** | Expensive for AI workflows, rate limits | Shell scripts + Python |
| **Airtable Automations** | Not built for AI agents, clunky | Direct file storage |
| **n8n self-hosted** | Overengineered for daily briefs | Simple cron/launchd |
| **Docker containers** | Adds complexity, slower startup | Native macOS scripts |
| **Notion API for storage** | API limits, slow, offline access issues | Local markdown files |
| **Firebase/Supabase** | Unnecessary cloud dependency | Local file system |
| **Electron apps** | Memory hog for simple notifications | terminal-notifier |
| **Selenium scraping** | Slow, brittle, high resource usage | Playwright or Apify |
| **WordPress as CMS** | Massive overkill | Static markdown files |
| **Custom LLM fine-tuning** | Not needed for general briefs | Claude's native capabilities |
| **LangChain for simple prompts** | Overengineered, frequent breaking changes | Direct API calls |
| **Redis for caching** | Overkill for daily runs | JSON files in ~/.cache/ |
| **MySQL/PostgreSQL** | Unnecessary database overhead | Markdown + git versioning |
| **Kubernetes** | Absurdly overengineered | Single macOS script |

### Anti-Patterns to Avoid

**1. Over-Engineering the MVP:**
- Don't build API wrappers before validating content quality
- Don't create web dashboards before proving daily usage
- Don't implement user authentication for a single-user system

**2. Premature Optimization:**
- Don't parallelize brief generation (sequential is fine for 3-4 briefs)
- Don't cache API responses longer than 24 hours (defeats "daily" purpose)
- Don't optimize for <5 minute runtime when 15 minutes is acceptable

**3. Unnecessary Dependencies:**
- Don't use Node.js if Python/Bash is sufficient
- Don't use virtual environments for system-level cron scripts (PATH issues)
- Don't install GUI apps for CLI automation

**4. Wrong Output Formats:**
- Don't generate PDFs daily (waste of storage, not editable)
- Don't use proprietary formats (Google Docs API, Notion blocks)
- Don't create separate files per idea (one file per brand per day)

**5. Fragile Scheduling:**
- Don't rely on "always-on" Mac (use launchd's catch-up features)
- Don't chain multiple cron jobs (one job runs orchestrator script)
- Don't skip error logging (you'll debug blind)

**6. Security Mistakes:**
- Don't hardcode API keys in scripts (use environment variables)
- Don't commit `.env` files to git
- Don't use unencrypted HTTP for API calls
- Don't scrape without respecting `robots.txt`

---

## Recommended Tech Stack Summary

### Core Stack (MVP)

```
┌─────────────────────────────────────────────┐
│  SCHEDULER: launchd (migrate from cron)     │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  ORCHESTRATOR: Bash script                  │
│  - Calls Claude Code CLI for each brand    │
│  - Handles errors and retries               │
│  - Logs to ~/daily-briefs/logs/             │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  AI ENGINE: Claude Code CLI                 │
│  - Sonnet 4.5 for speed                     │
│  - Opus 4.6 for complex research            │
│  - Native web search (no separate API)      │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  DATA SOURCES:                              │
│  - Claude web search (primary)              │
│  - RSS feeds (Product Hunt, HN)             │
│  - Apify Reddit scraper (MSP leads)         │
│  - Brave Search API (supplement)            │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  OUTPUT: Markdown files                     │
│  - ~/daily-briefs/[brand]/YYYY-MM-DD.md     │
│  - Git version control                      │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  NOTIFICATIONS:                             │
│  - terminal-notifier (local)                │
│  - Pushover (mobile, optional)              │
└─────────────────────────────────────────────┘
```

### Future Enhancements (Post-Validation)

**After 30 days of daily usage:**
- HTML email delivery via Postmark API
- Slack/Discord webhooks for team sharing
- Apify integration for local business scraping
- Markdown → HTML conversion via Pandoc

**After 90 days:**
- Dashboard for trend analysis (static site generator)
- API wrapper for mobile app integration
- A/B testing different prompt strategies

---

## Cost Breakdown (Monthly)

| Service | Cost | Usage | Required? |
|---------|------|-------|-----------|
| **Claude Code Subscription** | $20/mo | Unlimited briefs | ✅ Yes |
| **Apify (100k credits)** | $49/mo | Reddit/GMaps scraping | Later |
| **Brave Search API** | $0-5/mo | 2k-3k searches | Later |
| **Pushover** | $5 once | Lifetime license | Optional |
| **Postmark Email** | $15/mo | 10k emails | Later |
| **Total MVP Cost** | **$20/mo** | — | — |
| **Total Full Stack** | **$89/mo** | — | — |

---

## Implementation Priorities

### Phase 1: Validation (Weeks 1-4)
- ✅ Cron (already working) → migrate to launchd later
- ✅ Claude Code CLI with native web search
- ✅ Markdown output to ~/daily-briefs/
- ✅ osascript notifications → upgrade to terminal-notifier
- ✅ Manual review of brief quality
- ✅ Track "acted on 2-3 ideas/week" metric

### Phase 2: Enhancement (Weeks 5-8)
- Apify Reddit scraper for ThreatCaptain leads
- Brave Search API for trend validation
- RSS feed parsing for app trends
- Pushover mobile notifications
- Git versioning for briefs

### Phase 3: Distribution (Weeks 9-12)
- HTML email generation via Pandoc
- Postmark integration for email delivery
- Slack/Discord webhooks (if team expands)
- Static dashboard for trend visualization

---

## Key Takeaways

1. **Keep it simple**: Claude Code CLI + launchd + markdown is 80% of the solution
2. **Validate first**: Don't add APIs/integrations until content quality is proven
3. **macOS-native wins**: Leverage built-in tools (launchd, terminal-notifier) over third-party
4. **Avoid over-engineering**: No databases, containers, or complex frameworks needed
5. **Cost-conscious**: $20/mo is sufficient for MVP; scale spending with value
6. **Reliability over features**: launchd > cron, terminal-notifier > osascript
7. **Human-readable output**: Markdown beats JSON/PDF for daily editing/review

---

*Last updated: 2026-02-15*
*Next review: After 30 days of production usage*
