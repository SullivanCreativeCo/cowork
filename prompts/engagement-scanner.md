# Engagement Scanner - Weekly Lead Identification

## Objective
Help the user identify warm leads from LinkedIn and Instagram engagement and draft personalized DM follow-ups every Monday morning.

## Execution Steps

### Step 1: Prompt for Engagement Data
Ask the user to provide engagement data from the past 7 days:
* Which LinkedIn posts did they publish?
* Who liked, commented, or shared each post?
* Which Instagram posts/reels did they publish?
* Who engaged with Instagram content?

Use a conversational, guided approach. Don't overwhelm with a giant form.

### Step 2: Analyze Engagement Patterns
For each person who engaged, identify:
* How many times did they engage? (multiple engagements = warmer lead)
* What type of engagement? (comment with question = hottest, like = coldest)
* What content did they engage with? (senior photos, web design, general tips?)
* Any buying signals? (asked about pricing, services, availability, booking)

### Step 3: Qualify Leads
Score each lead as:
* Hot: Commented with questions, asked about services, engaged multiple times
* Warm: Commented positively, engaged 2+ times
* Cool: Single like or generic comment

Focus only on Hot and Warm leads for DM follow-ups.

### Step 4: Draft Personalized DMs
For each Hot/Warm lead, draft a DM template that:
* References the specific content they engaged with
* Feels personal and conversational (not templated)
* Offers value (not pushy sales)
* Includes a soft CTA (question, invitation to chat, link to resource)

Follow Sullivan Creative brand voice:
* Warm but professional
* Short sentences
* No emojis
* Confidence-focused (help them feel comfortable)

### Step 5: Output Report
Create a markdown report in `/sessions/confident-sharp-maxwell/mnt/outputs/` with:
* Summary metrics (total engagement, # of warm leads identified)
* Table of Hot/Warm leads with engagement patterns
* Draft DM for each lead (ready to copy/paste)
* Suggested next actions

## Success Criteria
* 5-10 qualified warm leads identified
* Each DM feels personal and specific
* Report is scannable and actionable
* User can copy/paste DMs directly into LinkedIn/Instagram

## Constraints
* Quality over quantity (better to have 5 real warm leads than 20 lukewarm)
* Never use generic templates (each DM should reference specific engagement)
* Align with Sullivan Creative positioning (non-cookie-cutter, comfort-first)
