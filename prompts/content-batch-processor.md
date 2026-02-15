# Content Batch Processor

## Objective
Transform raw video or audio recordings into multiple repurposed content assets optimized for different platforms (LinkedIn, TikTok, Instagram, Twitter, blog, email).

## Execution Steps

### Step 1: Gather Source Content
Ask the user to provide:
* Raw video or audio files (from monthly batch recording session)
* Topic or theme for each piece
* Target platforms (LinkedIn, TikTok, Instagram, Twitter, blog, newsletter)

Accept files via upload or from `/sessions/confident-sharp-maxwell/mnt/uploads/` or user's workspace folder.

### Step 2: Extract Transcripts
For each video/audio file:
* Generate full transcript using available transcription tools
* Clean up filler words (um, uh, like) for written content
* Keep natural speech patterns for video captions (more authentic)

### Step 3: Identify Key Moments
Analyze each transcript to identify:
* Hook moments (first 3-5 seconds, attention-grabbing)
* Quotable lines (1-2 sentence insights)
* Tactical tips (actionable advice)
* Story moments (personal anecdotes or examples)
* CTA opportunities (natural points to invite engagement)

### Step 4: Create Platform-Specific Content
For each core piece, generate:

**LinkedIn Post (Professional, thought leadership)**:
* 1,300-1,800 characters
* Hook in first line
* 2-3 paragraphs with line breaks
* Ends with question or CTA
* No emojis
* Professional but conversational tone

**TikTok Caption (Casual, authentic)**:
* 100-150 characters
* Direct and punchy
* Uses trending sounds/formats if applicable
* Casual language
* Hook references video content

**Instagram Caption (Visual storytelling)**:
* 150-300 characters
* Complements visual
* Encourages saves/shares
* Can include hashtags (3-5 relevant)

**Twitter/X Thread (Concise, tactical)**:
* 3-5 tweets
* Each tweet 200-280 characters
* Thread starts with hook
* Each tweet is self-contained but flows
* Ends with summary or CTA

**Blog Excerpt (SEO-optimized, detailed)**:
* 300-500 words
* Includes subheadings
* Expanded explanations of key points
* Keyword-optimized for Sullivan Creative services
* Links to portfolio or booking page

**Email Newsletter Snippet (Personal, valuable)**:
* 150-250 words
* Conversational tone
* Includes actionable tip
* Soft CTA (reply, book call, check out resource)

### Step 5: Output Repurposed Content
Create a markdown file in `/sessions/confident-sharp-maxwell/mnt/outputs/` with:
* File name: `content_batch_[date].md`
* Organized by source video/audio
* All platform variations clearly labeled
* Copy-paste ready (no extra formatting)
* Metadata: word counts, suggested posting times, hashtags

## Example Output Structure

```markdown
# Content Batch - [Date]

## Source 1: [Video Title/Topic]
**Core Message**: [1-sentence summary]

### LinkedIn Post
[Copy-paste ready LinkedIn post]

### TikTok Caption
[Copy-paste ready TikTok caption]

### Instagram Caption
[Copy-paste ready Instagram caption]

### Twitter Thread
1/5: [Tweet 1]
2/5: [Tweet 2]
...

### Blog Excerpt
[Copy-paste ready blog section with subheadings]

### Email Snippet
[Copy-paste ready newsletter section]

---

## Source 2: [Next Video Title/Topic]
[Repeat structure]
```

## Success Criteria
* 1 source video/audio produces 6+ platform-specific assets
* Each asset feels native to its platform (not copy-pasted across)
* Captions align with Sullivan Creative brand voice (warm, professional, no emojis, confidence-focused)
* All content is copy-paste ready
* Total output: 12-15 source pieces → 60+ repurposed assets

## Constraints
* Maintain consistent brand voice across platforms (adapt tone, not personality)
* No emojis (Sullivan Creative style)
* Short sentences, scannable structure
* Each asset must provide value (not just promotion)
* ThreatCaptain content should establish thought leadership
* Sullivan Creative content should showcase expertise and build trust

## Tips for Efficiency
* Use AI tools for transcription (Whisper, etc.)
* Batch similar platforms together (all LinkedIn posts, then all TikTok captions)
* Keep a swipe file of high-performing hooks and CTAs
* Test different formats and track engagement to refine over time
