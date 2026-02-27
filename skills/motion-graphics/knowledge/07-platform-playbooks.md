# Entropy Lifestyle — Platform-Specific Playbooks

Each platform serves a different role in the content ecosystem. The same topic generates different content per platform.

---

## TikTok — The Viral Engine

### Platform Role
Discovery. Reach strangers. Build awareness at scale.

### Algorithm Priority
**Completion rate** is king (70%+ = viral threshold).
Signal ranking: Shares > Saves > Comments > Likes.

### Specs
| Setting | Value |
|---|---|
| Aspect ratio | 9:16 (1080x1920) |
| Optimal duration | 15-25 seconds (max completion) |
| Max before drop-off | Under 30 seconds |
| Safe zone | 15% from all edges |
| FPS | 30 |

### Posting Frequency
5x per week. Consistency over volume.

### Posting Times
7-9 AM or 7-9 PM audience active times. Never post within 2 hours of a previous post.

### Primary Hook Types
- Hook 1 (Confronting Stat) — numbers stop scrolls
- Hook 2 (Contradictory Truth) — disagreement drives comments
- Hook 4 (You Call-Out) — direct address bypasses passive mode

### Caption Strategy
- First line is a SECOND hook (shown as text overlay)
- Short and conversational
- 2-4 hashtags maximum
- Include keyword phrases for TikTok SEO
- End with engagement prompt or question

### Comment Strategy
- Reply to every comment in the first 2 hours
- Pin one comment that extends the story
- Controversy-inviting captions drive comment velocity

### Primary Templates
1. **Stat Bomb** (T1) — highest frequency, fastest production
2. **Science Explainer** (T4) — niche authority builder
3. **Loop Narrative** (T2) — trimmed to 20-25s

### Content Series

#### Series 1: "The Real Numbers" (Weekly)
One stat per week. Fully animated, 12-18 seconds. No story — just the number, the context, and one Entropy truth line.
- Template: Stat Bomb (T1)
- Frequency: Every week, no exceptions
- This is your volume content

#### Series 2: "The Loop" (Bi-Weekly)
The animated habit failure cycle. 20-25 second narrative loop that mirrors the viewer's experience so precisely they share it.
- Template: Loop Narrative (T2)
- Frequency: Every 2 weeks
- Highest share potential

#### Series 3: "Science In 15" (Weekly)
One behavioral science concept, fully animated in 15 seconds or less. Builds niche authority and attracts the health-optimization audience.
- Template: Science Explainer (T4)
- Frequency: Weekly
- Best for follower quality over quantity

### Remotion Config for TikTok
```typescript
const TIKTOK_CONFIG = {
  width: 1080,
  height: 1920,
  fps: 30,
  maxDuration: 25 * 30, // 750 frames (25s)
  targetDuration: 18 * 30, // 540 frames (18s)
  safeZone: { top: 0.15, bottom: 0.15, sides: 0.05 },
  motionStyle: 'kinetic', // fast entrances, hard cuts
  maxStaticHold: 1.5 * 30, // 45 frames
};
```

---

## Instagram Reels — The Brand Home

### Platform Role
Brand identity, community depth, saves & DM shares.

### Algorithm Priority
**Watch time** is primary. DM Sends = top signal for new audience reach. Saves = existing audience deepening.

### Specs
| Setting | Value |
|---|---|
| Aspect ratio | 9:16 (1080x1920) for Reels |
| Carousel | 1:1 (1080x1080) or 4:5 (1080x1350) |
| Optimal Reel duration | 25-45 seconds |
| Safe zone | 15% from all edges |
| FPS | 30 |

### Posting Frequency
5x per week: 3 Reels + 1 Carousel + 1 Static/Story highlight.

### Posting Times
6-8 AM, 11 AM-1 PM, or 7-9 PM. Never post within 2 hours of previous post.

### Primary Hook Types
- Hook 3 (Visual Timeline) — highest save-driving hook
- Hook 5 (Data Story) — establishes evidence-based credibility
- Hook 6 (Before/After) — product awareness driver
- Hook 7 (Loop Closer) — high rewatch rate

### Caption Strategy
- Captions can be LONGER than TikTok
- First 2 lines must hook (shown before "more" truncation)
- Caption extends the story — contains information NOT in the video
- End with a question that invites response
- Hashtags: 5-10, mix of niche and broad

### Primary Templates
1. **Loop Narrative** (T2) — emotional depth for saves
2. **Data Journey** (T3) — credibility builder
3. **Comparison** (T5) — positioning for DM shares

### Content Series

#### Series 1: "What Week [X] Actually Looks Like" (Weekly Reel)
Data-animated narrative following a real (or prototype) Entropy user's journey week by week. Highest-save content category.
- Template: Data Journey (T3)
- Frequency: Weekly
- Duration: 30-45 seconds
- Data: HRV, sleep, habit completion, recovery scores

#### Series 2: "The Accountability Equation" (Bi-Weekly Carousel)
Designed carousel functioning as a mini-guide. Each slide is a motion graphic still — designed to screenshot and save. 6-8 slides with cliffhanger to next slide.
- Format: 1080x1350 carousel (4:5)
- Frequency: Every 2 weeks
- Each slide: one concept, one visual, one data point
- Last slide: CTA to save or DM

#### Series 3: "Entropy vs The World" (Monthly Comparison Reel)
Motion graphic comparison positioning Entropy against common alternatives. Designed for DM shares and comments.
- Template: Comparison (T5)
- Frequency: Monthly
- Duration: 25-30 seconds
- Tone: Factual, never mocking

### Remotion Config for IG Reels
```typescript
const IG_REELS_CONFIG = {
  width: 1080,
  height: 1920,
  fps: 30,
  maxDuration: 45 * 30, // 1350 frames (45s)
  targetDuration: 30 * 30, // 900 frames (30s)
  safeZone: { top: 0.15, bottom: 0.15, sides: 0.05 },
  motionStyle: 'kinetic', // same kinetic base
  maxStaticHold: 1.5 * 30,
};

const IG_CAROUSEL_CONFIG = {
  width: 1080,
  height: 1350, // 4:5
  fps: 30,
  slideDuration: 0, // static exports
  slideCount: 8,
};
```

---

## Instagram Stories — The Retention Engine

### Platform Role
Daily intimacy tool. Keep existing audience warm between Reels. NOT promotional content.

### Frequency
3-5 stories per day. Always interactive.

### The 5 Story Types

#### Type 1: POLL (Every Tuesday)
"Be honest: how many habit streaks did you break last week?" 0-1 / 2-4 / "I don't count anymore."
- Results become content for next Reel
- Template: Static graphic with poll overlay

#### Type 2: QUESTION BOX (Every Thursday)
"What habit are you trying to build right now?"
- Answers become raw brief for next week's content
- Template: Branded question graphic

#### Type 3: DATA DROP
Animated 15-second mini-Reel posted to Stories first, then as a Reel.
- Gives followers "first access" energy
- Template: Stat Bomb (T1) shortened to 10-15s
- Tap-through: 2-3 story slides

#### Type 4: BUILD IN PUBLIC (Weekly)
One frame showing a real app metric, user streak, or feature being built.
- No polish. Stories CAN be rough. This is humanizing content.
- Screenshot or screen recording format

#### Type 5: RESHARE
When a user tags Entropy, reshare immediately with a personalized note.
- Highest-leverage content — social proof without production budget.
- Always add a response/note, never bare reshare.

### Remotion Config for Stories
```typescript
const IG_STORIES_CONFIG = {
  width: 1080,
  height: 1920,
  fps: 30,
  maxDuration: 15 * 30, // 450 frames (15s max per story)
  safeZone: { top: 0.20, bottom: 0.20, sides: 0.08 }, // extra safe for story UI
};
```

---

## LinkedIn — The Authority Platform

### Platform Role
Professional credibility. Long-form data stories. B2B and thought leadership.

### Specs
| Setting | Value |
|---|---|
| Aspect ratio | 16:9 (1920x1080) or 1:1 (1080x1080) |
| Optimal duration | 30-120 seconds |
| Safe zone | Standard (no overlay UI) |
| FPS | 30 |

### Content Strategy
- Data-heavy content performs best
- Longer narratives acceptable (60-120s)
- Professional tone — lean into SCIENTIFIC and DIRECT traits
- No trendy hooks — lead with insight
- Written captions should be article-length mini-essays

### Primary Templates
1. **Data Journey** (T3) — adapted for horizontal format
2. **Science Explainer** (T4) — extended with deeper mechanism explanation
3. **Comparison** (T5) — professional positioning

### Primary Hook Types
- Hook 5 (Data Story) — evidence-based authority
- Hook 2 (Contradictory Truth) — thought leadership
- Hook 1 (Confronting Stat) — industry data

### Posting Frequency
2-3x per week. Quality over quantity.

### Remotion Config for LinkedIn
```typescript
const LINKEDIN_CONFIG = {
  width: 1920,
  height: 1080, // 16:9 landscape
  fps: 30,
  maxDuration: 120 * 30, // 3600 frames (120s)
  targetDuration: 60 * 30, // 1800 frames (60s)
  safeZone: { top: 0.05, bottom: 0.05, sides: 0.05 },
  motionStyle: 'deliberate', // slightly slower than social, more professional
};
```

---

## Weekly Batch Production Workflow

### Step 1: INSIGHT MINING (30 min)
Pick one core insight for the week from: behavioral science literature, user feedback, app data, counterintuitive wellness take. Write the one-sentence truth.

### Step 2: ANGLE MAPPING (20 min)
Map that one insight to the 5 templates. Not every insight maps to all five — map to three minimum. Write the script for each.

### Step 3: SCRIPT REVIEW (10 min)
Each script must pass three tests:
1. Does frame 1 contain the audience's pain, not the product?
2. Can someone watch it muted and understand the argument?
3. Would someone DM this to a friend?

### Step 4: GENERATION (Variable)
Generate using Remotion pipeline. One template, multiple executions. Platform-specific adjustments.

### Step 5: CAPTION WRITING (20 min)
Write captions LAST, after the video is done. TikTok: short, conversational. IG: extend the story. Both end with a hook or question.

### Step 6: SCHEDULING
TikTok: 7-9 AM or 7-9 PM. IG: 6-8 AM, 11 AM-1 PM, or 7-9 PM. Never post within 2 hours of previous post on same platform.

---

## Platform Comparison at a Glance

| | TikTok | IG Reels | IG Stories | LinkedIn |
|---|---|---|---|---|
| **Role** | Discovery | Brand home | Retention | Authority |
| **Duration** | 15-25s | 25-45s | 10-15s | 30-120s |
| **Aspect** | 9:16 | 9:16 | 9:16 | 16:9 or 1:1 |
| **Frequency** | 5x/week | 3 Reels + 2 other | 3-5/day | 2-3x/week |
| **Top metric** | Completion rate | Saves + DM sends | Tap-through | Engagement rate |
| **Hooks** | 1, 2, 4 | 3, 5, 6, 7 | N/A | 1, 2, 5 |
| **Templates** | T1, T4, T2 | T2, T3, T5 | T1 (short) | T3, T4, T5 |
| **Motion style** | Fast, kinetic | Kinetic + depth | Quick, interactive | Deliberate, data-rich |
| **Caption** | Short + SEO | Long + story | Minimal | Essay-length |

---

## Metrics That Matter (Month 1)

Vanity metrics (follower counts, likes) are NOT the north star.

| Metric | Platform | Target | Why |
|---|---|---|---|
| Completion rate | TikTok | 60%+ | Below 40% = content structure failed |
| Shares & Saves / Views | TikTok | 4%+ | Drives distribution more than any other signal |
| Saves per Reel | Instagram | 2%+ of views | "This is important" signal |
| DM Sends / Reach | Instagram | Track week-on-week | Strongest IG distribution signal |
| Rewatch rate | Both | High completion + strong like:view | Indicates rewatches |
| Waitlist signups / week | Both | Track which content type drives clicks | The only metric that matters to the business |
