# ARCHITECTURE: Daily Intelligence Briefs System

**Research Date:** 2026-02-15
**Current State:** macOS cron + bash script + Claude Code CLI
**Goal:** Understand architectural patterns for scalable, reliable daily intelligence systems

---

## 1. How Daily Intelligence Systems Are Typically Structured

### Industry Standard Patterns

Daily intelligence systems (e.g., Bloomberg Terminal morning briefs, Gartner reports, internal executive dashboards) follow a common architectural pattern:

```
┌─────────────────────────────────────────────────────────────┐
│                      ORCHESTRATION LAYER                     │
│  (Scheduler, Job Queue, Workflow Engine)                    │
└────────────┬────────────────────────────────────────────────┘
             │
    ┌────────┴────────┐
    │                 │
┌───▼────┐      ┌────▼─────┐       ┌──────────┐
│ DATA   │      │ GENERATION│      │ QUALITY  │
│ SOURCE │─────▶│ ENGINE    │─────▶│ CONTROL  │
│ LAYER  │      │           │      │          │
└────────┘      └──────┬────┘      └────┬─────┘
                       │                 │
              ┌────────▼─────────────────▼──────┐
              │    STORAGE & VERSIONING          │
              └────────┬─────────────────────────┘
                       │
              ┌────────▼─────────────────────────┐
              │    DELIVERY & NOTIFICATION        │
              │  (Email, Slack, Mobile, Web UI)   │
              └───────────────────────────────────┘
```

### Key Architectural Principles

1. **Separation of Concerns**: Each component has a single responsibility
2. **Idempotency**: Re-running a brief for the same date produces the same result
3. **Observability**: Every stage logs metrics, errors, and execution times
4. **Graceful Degradation**: Partial failures don't break the entire system
5. **Feedback Loops**: Track which briefs are opened, read, and acted upon

### Current vs. Target Architecture

| Aspect | Current (v1) | Target (v2+) |
|--------|-------------|--------------|
| **Scheduler** | macOS cron | Configurable scheduler with retry logic |
| **Generator** | Sequential bash loop | Parallel generation with timeout controls |
| **Validation** | None | Schema validation, content quality checks |
| **Storage** | Flat markdown files | Versioned storage with metadata |
| **Delivery** | macOS notification | Multi-channel delivery (email, Slack, etc.) |
| **Monitoring** | Basic stdout logs | Structured logging + metrics |
| **Error Handling** | Silent failures | Retry logic, fallbacks, alerts |
| **Feedback** | None | Usage tracking, engagement metrics |

---

## 2. Component Boundaries

### 2.1 Scheduler Component

**Responsibility:** Trigger brief generation at specified times

**Key Features:**
- Cron-based or programmatic scheduling
- Timezone-aware execution
- Holiday/weekend awareness (skip or adjust timing)
- Manual trigger capability for testing
- Distributed locking (prevent duplicate runs)

**Interfaces:**
- **Input:** Schedule configuration (time, frequency, timezone)
- **Output:** Trigger events to Orchestrator

**Implementation Options:**
```bash
# Current: cron
0 10 * * * /path/to/run-briefs.sh

# Future: Programmatic (with more control)
# - systemd timers (Linux)
# - launchd (macOS) - better than cron for persistence
# - Node.js with node-cron
# - Dedicated job queue (BullMQ, Agenda, etc.)
```

**Best Practice:** Use `launchd` on macOS instead of cron for better:
- Process management
- Error logging
- Automatic restart on failure
- Environment variable handling

---

### 2.2 Generator Component

**Responsibility:** Convert prompts into intelligence briefs

**Key Features:**
- Prompt template management
- LLM API integration (Claude Code CLI)
- Concurrent generation (parallel processing)
- Timeout controls (prevent hanging jobs)
- Rate limit handling
- Prompt versioning

**Interfaces:**
- **Input:** Prompt files (markdown/text)
- **Output:** Raw generated content (markdown)

**Current Implementation Analysis:**
```bash
# Current: Sequential processing
for brief in "${BRIEFS[@]}"; do
  claude --print "$PROMPT" > "$OUTPUT"
done

# Issues:
# - Sequential = slow (4 briefs × ~30s each = 2+ minutes)
# - No timeout protection
# - No rate limit handling
# - Errors swallowed by 2>/dev/null
```

**Improved Design:**
```bash
# Parallel with GNU parallel or background jobs
for brief in "${BRIEFS[@]}"; do
  generate_brief "$brief" &
done
wait # Wait for all background jobs

# Or use xargs for parallel execution:
printf '%s\n' "${BRIEFS[@]}" | xargs -P 4 -I {} generate_brief {}
```

**Generator Configuration:**
```yaml
generator:
  max_concurrent: 4
  timeout_seconds: 120
  retry_attempts: 3
  retry_backoff: exponential
  rate_limit:
    requests_per_minute: 10
    burst_size: 5
```

---

### 2.3 Validator Component

**Responsibility:** Ensure brief quality before delivery

**Key Features:**
- Schema validation (required sections present)
- Content quality checks (minimum length, readability)
- Hallucination detection (verify facts, dates)
- Duplicate detection (comparing to previous briefs)
- Brand voice consistency

**Interfaces:**
- **Input:** Raw generated content
- **Output:** Validated content + quality score

**Validation Rules Example:**
```javascript
// Pseudo-code validation rules
const validationRules = {
  required_sections: [
    'TOP 3 CONTENT IDEAS FOR TODAY',
    'QUICK WIN',
    'TRENDING IN UPSTATE SC',
    'HASHTAGS TO USE TODAY'
  ],
  min_content_length: 500, // characters
  max_age_mentions: 1, // Only mention "today" or current date
  forbidden_patterns: [
    /\[PLACEHOLDER\]/i,
    /\[TODO\]/i,
    /lorem ipsum/i
  ],
  date_validation: {
    must_match_current_date: true,
    format: 'YYYY-MM-DD'
  }
}

function validate(brief) {
  const errors = [];
  const warnings = [];

  // Check required sections
  for (const section of validationRules.required_sections) {
    if (!brief.includes(section)) {
      errors.push(`Missing required section: ${section}`);
    }
  }

  // Check content length
  if (brief.length < validationRules.min_content_length) {
    warnings.push(`Brief is short (${brief.length} chars)`);
  }

  // Check for placeholders
  for (const pattern of validationRules.forbidden_patterns) {
    if (pattern.test(brief)) {
      errors.push(`Contains placeholder text: ${pattern}`);
    }
  }

  return {
    valid: errors.length === 0,
    errors,
    warnings,
    quality_score: calculateQualityScore(brief)
  };
}
```

**Quality Metrics:**
- Completeness: 0-100 (all sections present)
- Freshness: 0-100 (references to current date/trends)
- Actionability: 0-100 (specific, not generic advice)
- Readability: Flesch reading score

---

### 2.4 Delivery Component

**Responsibility:** Get briefs to users in preferred format

**Key Features:**
- Multi-channel delivery (email, Slack, mobile push)
- User preferences (time, channels, frequency)
- Delivery confirmation tracking
- Fallback channels (if email fails, try Slack)
- Formatting for each channel (HTML email, markdown for Slack)

**Interfaces:**
- **Input:** Validated briefs + delivery manifest
- **Output:** Delivery status (sent, failed, pending)

**Delivery Channels:**

| Channel | Use Case | Priority |
|---------|----------|----------|
| **File System** | Current: Local markdown files | Primary |
| **macOS Notification** | Current: System notification | Primary |
| **Email** | Daily digest to inbox | Future |
| **Slack** | Team channel posting | Future |
| **Mobile App** | iOS/Android push | Future |
| **Web Dashboard** | Browser-based reading | Future |

**Email Template Example:**
```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: -apple-system, sans-serif; }
    .brief { margin-bottom: 2em; }
    .section { margin: 1em 0; }
  </style>
</head>
<body>
  <h1>Daily Intelligence Briefs - {{DATE}}</h1>

  {{#briefs}}
  <div class="brief">
    <h2>{{name}}</h2>
    <div class="section">{{content}}</div>
    <p><small>Generated at {{timestamp}} | <a href="{{feedback_url}}">Was this useful?</a></small></p>
  </div>
  {{/briefs}}
</body>
</html>
```

---

### 2.5 Storage Component

**Responsibility:** Persist briefs with versioning and metadata

**Key Features:**
- Versioned storage (track changes over time)
- Metadata tracking (generation time, version, quality score)
- Searchable archive (find briefs by date, topic, keyword)
- Retention policies (auto-delete old briefs)
- Export capabilities (PDF, JSON, CSV)

**Interfaces:**
- **Input:** Brief content + metadata
- **Output:** Storage confirmation + retrieval API

**Current Storage Structure:**
```
~/daily-briefs/output/
├── 2026-02-15/
│   ├── sullivan-creative-brief.md
│   ├── local-business-scraper.md
│   ├── keegareaux-labs-brief.md
│   └── threatcaptain-msp-scanner.md
└── 2026-02-14/
    └── ...
```

**Enhanced Storage Structure:**
```
~/daily-briefs/
├── output/
│   └── 2026-02-15/
│       ├── sullivan-creative-brief.md      # Human-readable
│       ├── sullivan-creative-brief.json    # Machine-readable metadata
│       └── sullivan-creative-brief.pdf     # PDF export
├── archive/
│   └── 2026-01/                            # Monthly archives
├── metadata/
│   └── manifest.json                       # Index of all briefs
└── analytics/
    └── usage-stats.json                    # Engagement metrics
```

**Metadata JSON Example:**
```json
{
  "brief_id": "sullivan-creative-brief",
  "date": "2026-02-15",
  "generated_at": "2026-02-15T10:03:42Z",
  "generator_version": "claude-code-2.1.41",
  "model": "claude-opus-4-6",
  "prompt_hash": "sha256:abc123...",
  "generation_time_ms": 28450,
  "validation": {
    "valid": true,
    "quality_score": 87,
    "warnings": ["Brief is slightly shorter than average"]
  },
  "delivery": {
    "channels": ["filesystem", "macos_notification"],
    "delivered_at": "2026-02-15T10:04:15Z",
    "status": "success"
  },
  "engagement": {
    "viewed": true,
    "viewed_at": "2026-02-15T10:30:22Z",
    "action_taken": false,
    "feedback_score": null
  }
}
```

---

## 3. Data Flow

### End-to-End Flow Diagram

```
┌──────────────────────────────────────────────────────────────┐
│ SCHEDULER                                                     │
│ Trigger: Daily at 10:00 AM EST                               │
└────────────┬─────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────────┐
│ ORCHESTRATOR                                                  │
│ 1. Load prompt files from ~/daily-briefs/prompts/            │
│ 2. Create output directory for today's date                  │
│ 3. Initialize job queue (4 briefs)                           │
└────────────┬─────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────────┐
│ GENERATOR (Parallel Execution)                                │
│                                                               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ Brief 1     │  │ Brief 2     │  │ Brief 3     │  ...      │
│  │ Load prompt │  │ Load prompt │  │ Load prompt │          │
│  │ Call Claude │  │ Call Claude │  │ Call Claude │          │
│  │ Timeout:120s│  │ Timeout:120s│  │ Timeout:120s│          │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘          │
│         │                │                │                  │
│         ▼                ▼                ▼                  │
│    [Raw Output]    [Raw Output]    [Raw Output]              │
└─────────┬────────────────┬────────────────┬─────────────────┘
          │                │                │
          ▼                ▼                ▼
┌──────────────────────────────────────────────────────────────┐
│ VALIDATOR                                                     │
│ For each brief:                                               │
│  - Check required sections present                            │
│  - Validate date matches today                                │
│  - Check for placeholder text                                 │
│  - Calculate quality score                                    │
│  - Flag warnings (low quality, missing data)                  │
└────────────┬─────────────────────────────────────────────────┘
             │
             ├─── Valid ────────────────┐
             │                          │
             └─── Invalid ────┐         │
                              ▼         ▼
                         [Log Error] [Continue]
                         [Notify]
                              │         │
                              └────┬────┘
                                   │
                                   ▼
┌──────────────────────────────────────────────────────────────┐
│ STORAGE                                                       │
│ - Save markdown to ~/daily-briefs/output/[date]/             │
│ - Save metadata JSON with generation stats                   │
│ - Update manifest index                                       │
│ - Record validation results                                   │
└────────────┬─────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────────┐
│ DELIVERY                                                      │
│ - macOS notification: "4 briefs ready, 0 failed"             │
│ - Optional: Email digest                                      │
│ - Optional: Slack post                                        │
│ - Record delivery timestamp                                   │
└────────────┬─────────────────────────────────────────────────┘
             │
             ▼
┌──────────────────────────────────────────────────────────────┐
│ FEEDBACK TRACKING                                             │
│ - Did user open the brief? (track file access time)          │
│ - Did user act on it? (manual feedback button)               │
│ - Store engagement data for analytics                         │
└──────────────────────────────────────────────────────────────┘
```

### Detailed Data Flow Stages

#### Stage 1: Prompt → Generation

**Input:**
```markdown
# Prompt File: sullivan-creative-brief.md
You are a content strategist...
[Full prompt content]
```

**Processing:**
```bash
# Command executed
claude --print --dangerously-skip-permissions "$PROMPT" > output.md

# Environment:
# - Working directory: /Users/sullivancreativeco./daily-briefs
# - Timeout: None (should add timeout)
# - API key: From ~/.config/claude/credentials
```

**Output:**
```markdown
# Generated Brief: sullivan-creative-brief.md
===============================================
SULLIVAN CREATIVE DAILY BRIEF
Date: 2026-02-15
===============================================
[Generated content...]
```

**Failure Modes:**
- API timeout (no response in 120s)
- Rate limit exceeded (429 error)
- Network error (DNS, connection)
- Invalid API key
- Claude Code CLI crash

---

#### Stage 2: Generation → Validation

**Input:** Raw markdown file + metadata

**Processing:**
```javascript
// Validation checks
const checks = {
  schema: validateSchema(content),          // Required sections present
  date: validateDate(content, today),       // Date matches today
  quality: calculateQuality(content),       // Quality score 0-100
  placeholders: checkPlaceholders(content), // No [TODO] or [PLACEHOLDER]
  length: validateLength(content)           // Minimum content length
};

const isValid = Object.values(checks).every(c => c.passed);
```

**Output:**
```json
{
  "valid": true,
  "quality_score": 87,
  "checks": {
    "schema": { "passed": true },
    "date": { "passed": true },
    "quality": { "passed": true, "score": 87 },
    "placeholders": { "passed": true },
    "length": { "passed": true, "chars": 1250 }
  },
  "warnings": ["Brief is 15% shorter than 30-day average"],
  "errors": []
}
```

**Failure Modes:**
- Missing required sections (incomplete generation)
- Wrong date in output (model confusion)
- Placeholder text not replaced
- Output too short (API timeout mid-response)

---

#### Stage 3: Validation → Output

**Input:** Validated brief + metadata

**Processing:**
```bash
# Save to filesystem
OUTPUT_DIR="$HOME/daily-briefs/output/$(date +%Y-%m-%d)"
mkdir -p "$OUTPUT_DIR"

# Save markdown
echo "$BRIEF_CONTENT" > "$OUTPUT_DIR/sullivan-creative-brief.md"

# Save metadata
echo "$METADATA_JSON" > "$OUTPUT_DIR/sullivan-creative-brief.json"

# Update manifest
update_manifest "$OUTPUT_DIR" "$BRIEF_ID" "$METADATA"
```

**Output:**
```
~/daily-briefs/output/2026-02-15/
├── sullivan-creative-brief.md    (2.1 KB)
├── sullivan-creative-brief.json  (0.8 KB)
├── local-business-scraper.md     (1.9 KB)
├── local-business-scraper.json   (0.8 KB)
└── manifest.json                 (3.2 KB)
```

**Failure Modes:**
- Disk full (no space for output)
- Permission denied (can't write to directory)
- Filesystem corruption
- Race condition (concurrent writes to same file)

---

#### Stage 4: Output → Notification

**Input:** Completion status (X completed, Y failed)

**Processing:**
```bash
# macOS notification
osascript -e "display notification \
  \"$COMPLETED briefs ready, $FAILED failed\" \
  with title \"Daily Briefs Ready\" \
  subtitle \"Sullivan Creative Co.\" \
  sound name \"Glass\""
```

**Output:**
- macOS notification center alert
- Sound played (Glass.aiff)
- Banner displayed for 5 seconds
- Stored in notification history

**Future Enhancements:**
```javascript
// Email delivery
sendEmail({
  to: 'user@example.com',
  subject: `Daily Briefs - ${date}`,
  html: renderTemplate('daily-briefs-email', { briefs, date }),
  attachments: briefs.map(b => ({
    filename: `${b.id}.pdf`,
    path: `${outputDir}/${b.id}.pdf`
  }))
});

// Slack delivery
postToSlack({
  channel: '#daily-briefs',
  blocks: briefs.map(b => ({
    type: 'section',
    text: { type: 'mrkdwn', text: b.summary },
    accessory: {
      type: 'button',
      text: { type: 'plain_text', text: 'Read Full Brief' },
      url: b.url
    }
  }))
});
```

**Failure Modes:**
- macOS notification service down
- Email server unreachable (SMTP timeout)
- Slack API rate limit
- User notification preferences disabled

---

## 4. Quality Feedback Loops

### Why Feedback Loops Matter

Daily briefs are only valuable if they drive action. Without feedback, you can't:
- Identify which topics are most valuable
- Improve prompt engineering over time
- Justify the cost of generation (API fees, compute time)
- Optimize delivery timing and channels

### Feedback Loop Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ GENERATION METRICS                                           │
│ - Generation time (milliseconds)                             │
│ - API cost per brief                                         │
│ - Quality score (0-100)                                      │
│ - Token count (input + output)                               │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│ ENGAGEMENT METRICS                                           │
│ - Did user open the brief? (file access time)                │
│ - How long was it open? (file handle duration)               │
│ - Did user copy content? (clipboard monitoring - optional)   │
│ - Explicit feedback: 👍 / 👎 buttons                        │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│ ACTION METRICS                                               │
│ - Content created based on brief (manual tracking)           │
│ - Revenue attributed to leads (business impact)              │
│ - Time saved vs. manual research                             │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│ OPTIMIZATION ENGINE                                          │
│ - Identify high-value briefs (optimize prompts)              │
│ - Deprecate low-value briefs (save costs)                    │
│ - Adjust timing (when do briefs get read?)                   │
│ - A/B test prompt variations                                 │
└─────────────────────────────────────────────────────────────┘
```

### Implementation: Tracking Brief Opens

**Basic File Access Tracking (macOS):**
```bash
#!/bin/bash
# Check if briefs were opened today

BRIEF_DIR="$HOME/daily-briefs/output/$(date +%Y-%m-%d)"
METADATA_FILE="$BRIEF_DIR/engagement.json"

for brief in "$BRIEF_DIR"/*.md; do
  brief_name=$(basename "$brief" .md)

  # Get last access time (macOS stat)
  access_time=$(stat -f "%a" "$brief")
  generation_time=$(stat -f "%B" "$brief")

  if [ "$access_time" -gt "$generation_time" ]; then
    # Brief was accessed after generation = user opened it
    echo "{ \"brief\": \"$brief_name\", \"opened\": true, \"opened_at\": $access_time }" \
      >> "$METADATA_FILE"
  else
    echo "{ \"brief\": \"$brief_name\", \"opened\": false }" \
      >> "$METADATA_FILE"
  fi
done
```

**Advanced Engagement Tracking:**
```javascript
// Track engagement via file watcher
const chokidar = require('chokidar');
const fs = require('fs');

const briefsDir = `${process.env.HOME}/daily-briefs/output/${today}`;
const engagementLog = `${briefsDir}/engagement.json`;

// Watch for file access
const watcher = chokidar.watch(`${briefsDir}/*.md`, {
  awaitWriteFinish: false,
  ignoreInitial: true
});

watcher.on('change', (path) => {
  // File was opened/modified
  const engagement = {
    brief: path.split('/').pop().replace('.md', ''),
    event: 'opened',
    timestamp: new Date().toISOString()
  };

  fs.appendFileSync(engagementLog, JSON.stringify(engagement) + '\n');
});
```

### Explicit Feedback Collection

**Add Feedback Buttons to Briefs:**
```markdown
# At the end of each brief:

---
## Was this brief helpful?

Run one of these commands to provide feedback:

```bash
# This was useful
echo "👍 $(date)" >> ~/daily-briefs/feedback/sullivan-creative-brief.log

# This was not useful
echo "👎 $(date)" >> ~/daily-briefs/feedback/sullivan-creative-brief.log
```

Or click: [Useful](brief-feedback://useful/sullivan-creative-brief) | [Not Useful](brief-feedback://not-useful/sullivan-creative-brief)
```

**Feedback Analytics Dashboard:**
```bash
#!/bin/bash
# Generate weekly feedback report

echo "=== WEEKLY BRIEF ANALYTICS ==="
echo "Week of $(date -v-7d +%Y-%m-%d) to $(date +%Y-%m-%d)"
echo ""

for brief_id in sullivan-creative-brief local-business-scraper keegareaux-labs-brief threatcaptain-msp-scanner; do
  total_generated=$(find ~/daily-briefs/output -name "$brief_id.md" -mtime -7 | wc -l)
  total_opened=$(grep -l "\"opened\": true" ~/daily-briefs/output/*/engagement.json 2>/dev/null | wc -l)
  positive_feedback=$(grep -c "👍" ~/daily-briefs/feedback/$brief_id.log 2>/dev/null || echo 0)
  negative_feedback=$(grep -c "👎" ~/daily-briefs/feedback/$brief_id.log 2>/dev/null || echo 0)

  open_rate=$((total_opened * 100 / total_generated))

  echo "Brief: $brief_id"
  echo "  Generated: $total_generated"
  echo "  Opened: $total_opened ($open_rate%)"
  echo "  Feedback: 👍 $positive_feedback | 👎 $negative_feedback"
  echo ""
done
```

### Optimization Based on Feedback

**Example Decision Tree:**
```
If brief_open_rate < 30% for 7 days:
  → Adjust delivery time (try different hour)
  → Send preview notification 1 hour before
  → Make brief title more compelling

If brief_open_rate < 10% for 14 days:
  → Deprecate brief (not valuable)
  → Ask user if they want to continue
  → Reallocate generation budget to higher-value briefs

If brief_open_rate > 80% AND positive_feedback > 70%:
  → This is a high-value brief
  → Consider increasing frequency (twice daily?)
  → Expand scope (add more detail)
  → Use as template for new briefs

If brief_opened_but_negative_feedback:
  → Brief is interesting but low quality
  → Improve prompt engineering
  → Add more specific instructions
  → Increase model temperature for creativity
```

### Prompt Evolution Example

**Iteration 1: Generic Prompt**
```
Generate a daily content brief for a photography business.
```
**Result:** Generic advice, low engagement (20% open rate)

**Iteration 2: Added Specificity**
```
Generate a daily content brief for Sullivan Creative Co., a photography
business in Greenville, SC. Focus on trending topics on social media.
```
**Result:** Better engagement (45% open rate), but still too generic

**Iteration 3: Added Structure + Examples**
```
You are a content strategist for Sullivan Creative Co...
[Current prompt with detailed output format]
```
**Result:** High engagement (75% open rate), positive feedback

**Iteration 4: Based on Feedback**
```
[Add after analyzing what users act on most]
- Focus more on TikTok trends (users prefer short-form video)
- Include specific audio tracks to use
- Add estimated ROI for each content idea
```

---

## 5. Suggested Build Order Based on Dependencies

### Phase 1: Foundation (Current State → Production Ready)

**Goal:** Make the current system reliable and observable

**Duration:** 1-2 weeks

**Components:**
1. **Replace cron with launchd** (macOS best practice)
   - Better logging
   - Auto-restart on failure
   - Environment variable handling

2. **Add structured logging**
   - Log to file (not just stdout)
   - Include timestamps, brief IDs, execution times
   - Separate logs by severity (info, warning, error)

3. **Implement basic error handling**
   - Timeout protection (120s per brief)
   - Retry logic (3 attempts with exponential backoff)
   - Graceful degradation (continue even if one brief fails)

4. **Add basic validation**
   - Check that output files are non-empty
   - Verify date in output matches today
   - Flag briefs shorter than expected

**Deliverables:**
- `~/Library/LaunchAgents/com.sullivancreative.daily-briefs.plist`
- `/usr/local/bin/daily-briefs` (improved bash script)
- `~/daily-briefs/logs/` directory with dated log files
- Basic monitoring dashboard (simple HTML page)

**Success Metrics:**
- Zero unhandled failures for 7 consecutive days
- All briefs complete within 5 minutes
- Logs capture all errors with actionable context

---

### Phase 2: Parallel Processing + Metadata

**Goal:** Speed up generation and track quality metrics

**Duration:** 1-2 weeks

**Components:**
1. **Parallel brief generation**
   - Use GNU parallel or background jobs
   - Reduce total execution time from 4× to ~1.2× single brief time

2. **Metadata collection**
   - Save JSON metadata alongside markdown
   - Track: generation time, quality score, validation results
   - Build manifest index for searchability

3. **Quality scoring system**
   - Implement validation rules
   - Calculate quality score (0-100)
   - Flag low-quality briefs for review

4. **Improved notification**
   - Include quality scores in notification
   - Add action buttons (Open Folder, View Dashboard)
   - Differentiate between warnings and errors

**Deliverables:**
- Parallel execution script
- Metadata JSON schema
- Quality validation module
- Enhanced macOS notification with actions

**Success Metrics:**
- Total execution time < 90 seconds (vs. 2+ minutes)
- Quality scores tracked for all briefs
- Metadata completeness: 100%

---

### Phase 3: Engagement Tracking + Feedback

**Goal:** Understand which briefs are valuable

**Duration:** 1-2 weeks

**Components:**
1. **File access tracking**
   - Monitor when briefs are opened
   - Calculate open rates per brief
   - Track time spent reading (file handle duration)

2. **Explicit feedback collection**
   - Add feedback buttons/commands to briefs
   - Create feedback log directory
   - Weekly feedback analytics report

3. **Analytics dashboard**
   - Web-based dashboard (simple HTML + JavaScript)
   - View trends over time (open rates, quality scores)
   - Identify high-value vs. low-value briefs

4. **Automated optimization**
   - Alert when open rate drops below threshold
   - Suggest deprecating low-engagement briefs
   - Recommend prompt improvements based on feedback

**Deliverables:**
- Engagement tracking module
- Feedback collection system
- Analytics dashboard (`~/daily-briefs/dashboard/index.html`)
- Weekly email report with insights

**Success Metrics:**
- Engagement data collected for 100% of briefs
- Dashboard loads in < 2 seconds
- At least one prompt improvement per week based on data

---

### Phase 4: Advanced Delivery + Storage

**Goal:** Multi-channel delivery and searchable archive

**Duration:** 2-3 weeks

**Components:**
1. **Email delivery**
   - HTML email template
   - Daily digest format
   - Attachment support (PDF exports)
   - Delivery confirmation tracking

2. **Slack integration**
   - Post briefs to dedicated channel
   - Interactive buttons (Was this useful?)
   - Thread replies for feedback
   - Archive links

3. **PDF export**
   - Convert markdown to styled PDF
   - Branded template (Sullivan Creative Co.)
   - Attachable to emails, shareable

4. **Searchable archive**
   - Full-text search across all briefs
   - Filter by date, brief type, quality score
   - Export capabilities (CSV, JSON)
   - Retention policies (auto-archive old briefs)

**Deliverables:**
- Email delivery system (SMTP or SendGrid)
- Slack bot integration
- PDF generation pipeline (Pandoc or Puppeteer)
- Search interface (web or CLI)

**Success Metrics:**
- Email delivery success rate > 99%
- Slack posts within 5 minutes of generation
- Search returns results in < 1 second
- PDF exports generated for 100% of briefs

---

### Phase 5: Intelligence + Automation

**Goal:** Self-improving system with predictive capabilities

**Duration:** 3-4 weeks

**Components:**
1. **Prompt A/B testing**
   - Test variations of prompts
   - Measure which generate higher engagement
   - Auto-adopt winning variations

2. **Predictive scheduling**
   - Learn when user typically reads briefs
   - Adjust delivery time to maximize engagement
   - Send reminders if briefs go unread

3. **Content personalization**
   - Track which topics user engages with most
   - Adjust prompt focus based on preferences
   - Generate "More like this" follow-ups

4. **Cross-brief insights**
   - Identify patterns across multiple briefs
   - Generate weekly meta-brief summarizing trends
   - Suggest new brief types based on gaps

**Deliverables:**
- A/B testing framework
- Predictive scheduling algorithm
- Personalization engine
- Weekly meta-brief generator

**Success Metrics:**
- A/B tests show statistically significant improvements (p < 0.05)
- Delivery time optimized to user's peak reading time
- Engagement increases by 20%+ via personalization
- Meta-brief provides actionable strategic insights

---

### Dependency Graph

```
Phase 1: Foundation
    │
    ├─── Phase 2: Parallel Processing + Metadata
    │        │
    │        ├─── Phase 3: Engagement Tracking + Feedback
    │        │        │
    │        │        └─── Phase 5: Intelligence + Automation
    │        │
    │        └─── Phase 4: Advanced Delivery + Storage
    │                 │
    │                 └─── Phase 5: Intelligence + Automation
    │
    └─── All phases depend on Phase 1
```

**Critical Path:** Phase 1 → Phase 2 → Phase 3 → Phase 5
**Parallel Track:** Phase 2 → Phase 4 → Phase 5

**Recommendation:** Build in order (1 → 2 → 3 → 4 → 5), but Phase 4 can be done in parallel with Phase 3 if resources allow.

---

## 6. Handling Failures Gracefully

### Failure Taxonomy

Daily intelligence systems face three categories of failures:

1. **Transient Failures** (retry will likely succeed)
   - Network hiccups
   - API rate limits (429 errors)
   - Temporary service outages

2. **Persistent Failures** (retry won't help immediately)
   - Invalid API key
   - Malformed prompts
   - Disk full

3. **Partial Failures** (some briefs succeed, others fail)
   - One prompt file missing
   - One brief times out
   - One validation fails

### Error Handling Strategy Matrix

| Error Type | Detection | Response | Notification | Retry Strategy |
|------------|-----------|----------|--------------|----------------|
| **API Timeout** | No response in 120s | Kill process, mark failed | Log warning | Retry 3× with backoff |
| **Rate Limit (429)** | HTTP 429 response | Wait for rate limit reset | Log info | Wait + retry once |
| **Network Error** | Connection refused | Try next brief | Log warning | Retry 3× with backoff |
| **Invalid API Key** | 401 Unauthorized | Stop all briefs | Alert immediately | No retry (manual fix) |
| **Prompt File Missing** | File not found | Skip brief | Log warning | No retry (add file) |
| **Validation Failure** | Quality score < 50 | Generate anyway | Log warning | No retry (review prompt) |
| **Disk Full** | Write error | Stop all briefs | Alert immediately | No retry (manual fix) |
| **Generation Partial** | Output truncated | Mark incomplete | Log warning | Retry 1× |

---

### Implementation: Retry Logic with Exponential Backoff

```bash
#!/bin/bash
# Function: generate_brief_with_retry
# Retries brief generation with exponential backoff

function generate_brief_with_retry() {
  local brief_id="$1"
  local prompt_file="$2"
  local output_file="$3"
  local max_attempts=3
  local timeout_seconds=120
  local attempt=1

  while [ $attempt -le $max_attempts ]; do
    echo "[Attempt $attempt/$max_attempts] Generating $brief_id..."

    # Run with timeout
    timeout $timeout_seconds "$CLAUDE" --print --dangerously-skip-permissions \
      "$(cat "$prompt_file")" > "$output_file" 2>&1

    local exit_code=$?

    # Success
    if [ $exit_code -eq 0 ] && [ -s "$output_file" ]; then
      echo "[SUCCESS] $brief_id generated successfully"
      return 0
    fi

    # Timeout
    if [ $exit_code -eq 124 ]; then
      echo "[TIMEOUT] $brief_id timed out after ${timeout_seconds}s"
    else
      echo "[ERROR] $brief_id failed with exit code $exit_code"
    fi

    # Don't retry on last attempt
    if [ $attempt -lt $max_attempts ]; then
      # Exponential backoff: 2^attempt seconds
      local backoff=$((2 ** attempt))
      echo "[RETRY] Waiting ${backoff}s before retry..."
      sleep $backoff
    fi

    attempt=$((attempt + 1))
  done

  echo "[FAILED] $brief_id failed after $max_attempts attempts"
  return 1
}
```

---

### Implementation: Rate Limit Handling

```bash
#!/bin/bash
# Function: handle_rate_limit
# Detects and handles API rate limits (429 errors)

function call_claude_with_rate_limit() {
  local prompt="$1"
  local output_file="$2"
  local max_rate_limit_retries=2
  local retry_count=0

  while [ $retry_count -le $max_rate_limit_retries ]; do
    # Call Claude, capture stderr
    local stderr_file=$(mktemp)
    "$CLAUDE" --print --dangerously-skip-permissions "$prompt" \
      > "$output_file" 2> "$stderr_file"

    local exit_code=$?
    local stderr_content=$(cat "$stderr_file")
    rm "$stderr_file"

    # Check for rate limit error
    if echo "$stderr_content" | grep -q "429\|rate limit\|too many requests"; then
      echo "[RATE LIMIT] Hit API rate limit"

      # Extract retry-after header if present (in seconds)
      local retry_after=60 # default 60s
      if echo "$stderr_content" | grep -q "retry-after"; then
        retry_after=$(echo "$stderr_content" | grep -oP 'retry-after: \K\d+')
      fi

      echo "[WAITING] Waiting ${retry_after}s for rate limit to reset..."
      sleep $retry_after

      retry_count=$((retry_count + 1))
      continue
    fi

    # Not a rate limit error, return
    return $exit_code
  done

  echo "[FAILED] Rate limit persists after $max_rate_limit_retries retries"
  return 1
}
```

---

### Implementation: Graceful Degradation

```bash
#!/bin/bash
# Continue generating even if some briefs fail

COMPLETED=0
FAILED=0
FAILED_BRIEFS=()

for brief_entry in "${BRIEFS[@]}"; do
  brief_id="${brief_entry%%:*}"
  brief_name="${brief_entry##*:}"

  echo "Processing: $brief_name"

  # Try to generate brief
  if generate_brief_with_retry "$brief_id" "$prompt_file" "$output_file"; then
    COMPLETED=$((COMPLETED + 1))
  else
    FAILED=$((FAILED + 1))
    FAILED_BRIEFS+=("$brief_name")
  fi
done

# Report results
echo ""
echo "=============================="
echo "Daily Briefs Complete"
echo "Completed: $COMPLETED / ${#BRIEFS[@]}"
echo "Failed: $FAILED"

if [ $FAILED -gt 0 ]; then
  echo ""
  echo "Failed briefs:"
  printf '  - %s\n' "${FAILED_BRIEFS[@]}"
fi

echo "=============================="

# Success if at least one brief completed
[ $COMPLETED -gt 0 ]
```

---

### Implementation: Circuit Breaker Pattern

Prevent cascading failures by stopping after too many consecutive errors:

```bash
#!/bin/bash
# Circuit breaker: stop all briefs if too many fail

MAX_CONSECUTIVE_FAILURES=2
consecutive_failures=0

for brief_entry in "${BRIEFS[@]}"; do
  # Try to generate
  if generate_brief "$brief_id"; then
    consecutive_failures=0  # Reset on success
  else
    consecutive_failures=$((consecutive_failures + 1))

    # Circuit breaker tripped
    if [ $consecutive_failures -ge $MAX_CONSECUTIVE_FAILURES ]; then
      echo "[CIRCUIT BREAKER] Too many failures ($consecutive_failures), stopping all briefs"
      echo "[ALERT] Possible systemic issue (API down, network offline, etc.)"

      # Send critical alert
      osascript -e 'display notification "Daily briefs system halted due to errors" with title "CRITICAL: Briefs Failed" sound name "Basso"'

      # Exit with error code
      exit 1
    fi
  fi
done
```

---

### Implementation: Fallback Content

If generation fails, deliver cached content from previous day:

```bash
#!/bin/bash
# Fallback to yesterday's brief if generation fails

function generate_or_fallback() {
  local brief_id="$1"
  local today=$(date +%Y-%m-%d)
  local yesterday=$(date -v-1d +%Y-%m-%d)
  local output_file="$OUTPUT_DIR/$today/$brief_id.md"
  local fallback_file="$OUTPUT_DIR/$yesterday/$brief_id.md"

  # Try to generate
  if generate_brief_with_retry "$brief_id"; then
    return 0
  fi

  # Generation failed, check for yesterday's brief
  if [ -f "$fallback_file" ]; then
    echo "[FALLBACK] Using yesterday's brief for $brief_id"

    # Copy with warning header
    cat > "$output_file" <<EOF
⚠️ WARNING: Generation Failed
This is yesterday's brief ($yesterday) because today's generation failed.
Please check system logs for errors.

---

EOF
    cat "$fallback_file" >> "$output_file"

    return 0
  fi

  echo "[FAILED] No fallback available for $brief_id"
  return 1
}
```

---

### Monitoring + Alerting

**Log Everything:**
```bash
# Structured logging function
function log() {
  local level="$1"    # INFO, WARNING, ERROR, CRITICAL
  local message="$2"
  local timestamp=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

  # Write to log file
  echo "{\"timestamp\":\"$timestamp\",\"level\":\"$level\",\"message\":\"$message\"}" \
    >> "$LOG_FILE"

  # Also write to stdout for debugging
  echo "[$level] $message"

  # Send critical alerts immediately
  if [ "$level" = "CRITICAL" ]; then
    osascript -e "display notification \"$message\" with title \"CRITICAL ERROR\" sound name \"Basso\""
    # Future: send email, Slack message, PagerDuty alert
  fi
}

# Usage
log "INFO" "Starting daily briefs generation"
log "WARNING" "Brief quality score below threshold (62)"
log "ERROR" "Brief generation failed after 3 retries"
log "CRITICAL" "System halted: API authentication failed"
```

**Daily Health Check:**
```bash
#!/bin/bash
# Send daily health report

# Check logs for errors
error_count=$(grep -c '"level":"ERROR"' "$LOG_FILE")
critical_count=$(grep -c '"level":"CRITICAL"' "$LOG_FILE")

# Calculate success rate
total_briefs=$((COMPLETED + FAILED))
success_rate=$((COMPLETED * 100 / total_briefs))

# Email health report
cat <<EOF | mail -s "Daily Briefs Health Report" user@example.com
Daily Briefs Health Report
Date: $(date +%Y-%m-%d)

Status: $COMPLETED/$total_briefs briefs completed ($success_rate% success rate)
Errors: $error_count
Critical: $critical_count

Recent errors:
$(grep '"level":"ERROR"' "$LOG_FILE" | tail -5 | jq -r '.message')

Full logs: $LOG_FILE
EOF
```

---

## Summary: Architectural Best Practices

### Key Takeaways

1. **Modularity:** Separate concerns (scheduler, generator, validator, delivery, storage)
2. **Idempotency:** Re-running for the same date produces same result
3. **Observability:** Log everything, track metrics, measure engagement
4. **Resilience:** Retry transient failures, fail gracefully, provide fallbacks
5. **Feedback:** Track which briefs drive action, optimize based on data
6. **Incremental:** Build in phases, validate each before moving forward

### Current State Assessment

**Strengths:**
- Simple, functional system (works daily)
- Minimal dependencies (bash + Claude Code CLI)
- Easy to debug (shell script is readable)

**Weaknesses:**
- No error handling (failures are silent)
- Sequential execution (slow)
- No validation (quality unchecked)
- No engagement tracking (don't know what's valuable)
- Brittle (one API error can break everything)

### Recommended Next Steps

**Week 1-2: Phase 1 (Foundation)**
1. Replace cron with launchd
2. Add structured logging to file
3. Implement retry logic with timeout
4. Add basic validation (non-empty, correct date)

**Week 3-4: Phase 2 (Speed + Quality)**
1. Parallel execution (reduce time by 70%)
2. Metadata collection (JSON alongside markdown)
3. Quality scoring (0-100 scale)
4. Enhanced notifications (include quality info)

**Week 5-6: Phase 3 (Feedback)**
1. Track file access (engagement metrics)
2. Add feedback buttons to briefs
3. Build analytics dashboard
4. Weekly feedback report

**Month 2-3: Phase 4 & 5 (Advanced)**
1. Email/Slack delivery
2. PDF exports
3. Searchable archive
4. A/B testing + personalization

---

## References

### Tools + Technologies

- **Scheduler:** launchd (macOS), systemd timers (Linux), node-cron (Node.js)
- **Parallel Execution:** GNU parallel, xargs -P, background jobs (&)
- **Logging:** JSON logs, jq for parsing, logrotate for retention
- **Validation:** custom scripts, JSON Schema validation
- **Delivery:** osascript (macOS), SendGrid (email), Slack API
- **Storage:** filesystem + JSON, SQLite (future), Elasticsearch (advanced)
- **Monitoring:** tail -f logs, web dashboard, Prometheus + Grafana (advanced)

### File Structure (Recommended)

```
~/daily-briefs/
├── prompts/                      # Input prompts
│   ├── sullivan-creative-brief.md
│   ├── local-business-scraper.md
│   ├── keegareaux-labs-brief.md
│   └── threatcaptain-msp-scanner.md
├── output/                       # Generated briefs
│   ├── 2026-02-15/
│   │   ├── sullivan-creative-brief.md
│   │   ├── sullivan-creative-brief.json (metadata)
│   │   └── manifest.json
│   └── 2026-02-14/
├── logs/                         # System logs
│   ├── 2026-02-15.log
│   └── errors.log
├── feedback/                     # User feedback
│   ├── sullivan-creative-brief.log
│   └── analytics.json
├── config/                       # Configuration
│   ├── briefs.yaml              # Brief definitions
│   └── settings.yaml            # System settings
├── scripts/                      # Executables
│   ├── run-briefs.sh            # Main runner
│   ├── validate.sh              # Validation
│   └── send-notification.sh     # Delivery
├── dashboard/                    # Web UI
│   ├── index.html
│   └── analytics.js
└── archive/                      # Old briefs (>30 days)
    └── 2026-01/
```

### Related Reading

- **Designing Data-Intensive Applications** (Martin Kleppmann) - Chapter 11: Stream Processing
- **Site Reliability Engineering** (Google) - Chapter 6: Monitoring Distributed Systems
- **The Twelve-Factor App** - Factor V: Build, release, run (separation of concerns)
- **AWS Well-Architected Framework** - Reliability Pillar (graceful degradation)

---

**End of ARCHITECTURE.md**
