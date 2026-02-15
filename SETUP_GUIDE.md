# Shortcut Setup Guide

## What Was Created

### 1. Custom Claude Code Skills
Two custom skills were installed in your Claude Code configuration (`~/.claude/skills/`):

#### `/engagement-scanner`
- **Location**: `~/.claude/skills/engagement-scanner/SKILL.md`
- **Purpose**: Weekly lead identification from social media engagement
- **Usage**: Type `/engagement-scanner` in any Claude Code session

#### `/content-batch`
- **Location**: `~/.claude/skills/content-batch/SKILL.md`
- **Purpose**: Transform video/audio into multi-platform content
- **Usage**: Type `/content-batch` in any Claude Code session

### 2. Prompt Documentation
The full workflow documentation is stored in this repository:

- `prompts/engagement-scanner.md`
- `prompts/content-batch-processor.md`

These files serve as reference documentation and can be used independently if needed.

### 3. Repository Files
All files have been committed to branch `claude/shortcut-setup-guide-xKYWe` and pushed to the remote repository.

---

## How to Use

### Engagement Scanner Workflow

1. Open Claude Code in this repository
2. Type `/engagement-scanner` and press Enter
3. Follow the guided prompts to provide:
   - LinkedIn posts from the past 7 days
   - Engagement data (likes, comments, shares)
   - Instagram content and engagement
4. Claude will generate a report with:
   - Qualified warm leads (Hot/Warm scoring)
   - Personalized DM drafts for each lead
   - Suggested next actions

**Output**: Markdown report in `/sessions/confident-sharp-maxwell/mnt/outputs/`

### Content Batch Processor Workflow

1. Open Claude Code in this repository
2. Type `/content-batch` and press Enter
3. Provide video or audio files for processing
4. Specify target platforms (LinkedIn, TikTok, Instagram, Twitter, blog, email)
5. Claude will generate:
   - Platform-specific content variations
   - Copy-paste ready captions and posts
   - Organized output with metadata

**Output**: `content_batch_[date].md` in `/sessions/confident-sharp-maxwell/mnt/outputs/`

---

## Tips for Efficient Use

### Engagement Scanner
- Run every Monday morning for consistency
- Keep engagement data organized (screenshots or export from platforms)
- Focus on quality over quantity (5 real warm leads > 20 lukewarm)

### Content Batch Processor
- Batch record 12-15 videos/audio clips in one session
- Each source piece will generate 6+ platform-specific assets
- Keep a swipe file of high-performing hooks and CTAs
- Test and track engagement to refine over time

---

## Brand Voice Consistency

All outputs follow Sullivan Creative guidelines:
- ✅ Warm but professional tone
- ✅ Short, scannable sentences
- ✅ Confidence-focused messaging
- ✅ No emojis
- ✅ Non-cookie-cutter approach

---

## Next Steps

1. Merge `claude/shortcut-setup-guide-xKYWe` into your main branch
2. Test both skills with sample data
3. Integrate into your weekly (Engagement Scanner) and monthly (Content Batch) workflows
4. Refine prompts based on actual usage and results

---

## Troubleshooting

**Skills not showing up?**
- Restart Claude Code
- Check `~/.claude/skills/` directory for the skill folders
- Ensure SKILL.md files have proper frontmatter

**Want to modify a skill?**
- Edit the SKILL.md file in `~/.claude/skills/[skill-name]/`
- Changes take effect immediately (no restart needed)

**Need keyboard shortcuts instead?**
- You can add custom keybindings in `~/.claude/keybindings.json`
- Use the `/keybindings-help` skill for assistance
