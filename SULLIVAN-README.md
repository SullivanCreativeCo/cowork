# Sullivan Creative Content Tools

This repository contains custom Claude Code skills and prompts for Sullivan Creative's content workflow.

## Available Skills

### 1. Engagement Scanner (`/engagement-scanner`)
**Purpose**: Weekly lead identification from LinkedIn and Instagram engagement

**Usage**: Type `/engagement-scanner` in Claude Code to start the engagement analysis workflow.

**What it does**:
- Analyzes LinkedIn and Instagram engagement from the past 7 days
- Qualifies leads as Hot, Warm, or Cool based on engagement patterns
- Drafts personalized DM follow-ups for each warm lead
- Generates a scannable report with copy-paste ready messages

**Best for**: Monday morning lead follow-up routine

---

### 2. Content Batch Processor (`/content-batch`)
**Purpose**: Transform video/audio recordings into multi-platform content

**Usage**: Type `/content-batch` in Claude Code to start the content repurposing workflow.

**What it does**:
- Processes raw video or audio files
- Generates platform-specific content for LinkedIn, TikTok, Instagram, Twitter, blog, and email
- Creates 6+ unique assets from each source file
- Outputs copy-paste ready content in organized markdown files

**Best for**: Monthly content batch recording sessions

---

## Prompt Files

The `/prompts` directory contains the full workflow documentation for each tool:
- `engagement-scanner.md` - Detailed engagement scanner workflow
- `content-batch-processor.md` - Detailed content batch processor workflow

## Setup

These skills are automatically available in Claude Code when this repository is opened. Simply invoke them using slash commands:

```
/engagement-scanner
/content-batch
```

## Brand Voice Guidelines

All outputs follow Sullivan Creative brand voice:
- Warm but professional
- Short sentences
- No emojis
- Confidence-focused (help clients feel comfortable)
- Non-cookie-cutter approach
