# Entropy Motion Graphics - Prompt Archive

> Living record of all prompts that worked. Use these as foundations for iteration.
> Each entry includes the concept, what worked, what to improve, and the full prompt.

---

## PIECE #1: Brand Manifesto - "Your Energy is the Bottleneck"

**Type**: TOFU - Brand Awareness / Manifesto
**Format**: 9:16 (1080x1920) | 22s | Instagram Reels / TikTok / Shorts
**Tool**: Vibe Motion (primary)
**Status**: v2 - Concept worked, transitions still slightly quick

### What Worked
- Counter hook (62 energy score) grabs attention immediately
- Clean off-white canvas stands out in dark-heavy feeds
- Chaos-to-order fragment animation IS the brand moment
- Data labels among chaos (sleep: 4.2h, stress: HIGH) add credibility
- Line chart with +47% callout makes the promise tangible
- "ENTROPY" wordmark assembly from fragments as brand lock-up

### What to Improve for Next Iteration
- Transitions between scenes STILL too quick -- need even more breathing room
- Push minimum transition time to 800-1000ms between scenes
- Element position changes need 1000ms+ not 800ms
- Add more deliberate pauses between text reveals (400ms+ between lines)
- Consider 25-28s total to give everything proper space

### Full Prompt (v2)

```
=== VIBE MOTION PROMPT ===
[Title]: ENTROPY Brand Manifesto - "Your Energy is the Bottleneck"
[Format]: 1080x1920 (9:16) | 22 seconds | Vertical Reel

[Description]:
Create a 22-second vertical motion graphic (1080x1920, 9:16) for a premium
wellness brand called ENTROPY.

OVERALL STYLE: Ultra-minimal, premium, high-whitespace design on an off-white
(#FAFAF8) canvas. Think Apple keynote aesthetics meets health data
visualization. Typography-driven with thin-line geometric accents. No dark
backgrounds. Clean, calm, scientific, premium. ALL text must be large and
bold -- designed to be read at arm's length on a phone. No emojis anywhere.

SCENE 1 (0-1.2s): HOOK - THE NUMBER
- Off-white (#FAFAF8) canvas, completely clean
- A large "62" counts up from 0, center-screen
  Font: JetBrains Mono Bold, color #1A3A3A (deep teal)
  Size: MASSIVE -- fills 55% of screen width
- Below the number: a thin horizontal rule (#F2F2EF), full width with 10% margin
- Below the line: "Your energy score today." in Inter SemiBold, #2D2D2D
  Size: LARGE -- fills 60% of screen width, clearly readable
- A subtle underline beneath "62" in #C4A35A (muted amber = caution)
- Count-up animation: fast start, decelerating to settle on 62 (800ms)

SCENE 2 (1.2-3.5s): SMOOTH TRANSITION + PROBLEM
- SLOW, SMOOTH transition: "62" gracefully eases up and to the top-left
  corner over 1000ms (ease-in-out curve, deliberate, not rushed)
  It shrinks to about 30% of its original size, stays visible as context
  The subtitle and line fade out during this movement (600ms fade)
- After the 62 settles (300ms pause for breathing room)...
- Center screen: TWO LINES appear ONE AT A TIME:
  Line 1: "You didn't fail today."
    General Sans Bold, #2D2D2D, fills 80% of screen width
    Slides in from right, 500ms, holds 500ms before line 2
  Line 2: "Your energy did."
    General Sans Bold, "energy" in #E8A86B, fills 80% of screen width
    Slides in from right, 500ms
- Background: #FAFAF8 with barely visible geometric grid (#E8E8E4 at 20%)

SCENE 3 (3.5-5.5s): CHAOS MOMENT
- Text shatters into scattered geometric fragments
  Animation: 500ms scatter, fragment colors: #1A3A3A, #2D2D2D, #D4D4CF
- Three LARGE data labels visible among fragments:
  "sleep: 4.2h" (upper-left), "stress: HIGH" (center-right),
  "focus: scattered" (lower-center)
  JetBrains Mono SemiBold, #6B6B6B, each fills 45-50% screen width
- Grain overlay at 15% opacity

SCENE 4 (5.5-8.0s): REFRAME
- Fragments pause 300ms, then converge to center (600ms)
- Grain fades, canvas cleans to pure #FAFAF8
- Text assembles LINE BY LINE from fragments:
  Line 1: "The bottleneck isn't time."
    General Sans Bold, #2D2D2D, 80% screen width
    "time" in #D4D4CF with thin strikethrough
    Assembles 600ms, HOLDS 600ms
  Line 2: "It's energy."
    "energy" in #E8A86B, 65% screen width
    Assembles 600ms

SCENE 5 (8.0-11.5s): FOUR PILLARS
- Previous fades (400ms), upward wipe (600ms)
- Four cards, vertical stack, staggered 150ms:
  Each: #F2F2EF rounded rect, thin #E8A86B left border
  Abstract geometric icons (no emojis): sine waveform, overlapping circles,
  concentric arcs, ascending data points
  Text: Inter SemiBold, #2D2D2D, 85% screen width
  "Energy Score" / "Smart Schedule" / "Rituals" / "Proof"

SCENE 6 (11.5-14.5s): ENERGY CURVE
- Cards compress up (800ms)
- Line chart draws L-to-R (1500ms)
  Low/wobbly in #C4A35A zone, rises to stable in #6B9B7A zone
  X-axis: "Day 1"/"Day 15"/"Day 30" in JetBrains Mono, readable
  Callout: "+47% energy stability" JetBrains Mono Bold, #6B9B7A, 55% width

SCENE 7 (14.5-17s): PROMISE
- Chart fades (400ms)
- Four lines, sequential fade-in, 350ms stagger, ALL SAME SIZE:
  "Managed energy." / "Usable time." / "Sustainable lifestyle." /
  "Measurable progress."
  General Sans SemiBold, #1A3A3A, 75% screen width each
  Small #E8A86B dot bullets

SCENE 8 (17-22s): BRAND LOCK-UP
- "ENTROPY" assembles from fragments (800ms), ALL CAPS
  General Sans Bold, #1A3A3A, 60% screen width
- "Your lifestyle, redesigned" Inter Regular, #2D2D2D
- Gradient line #E8A86B → #D4955A
- "entropylifestyle.com" Inter Medium, #1A3A3A
- "Follow for the protocol →" Inter SemiBold, #E8A86B
- HOLD 1.5 seconds

PALETTE: #FAFAF8 canvas, #1A3A3A primary, #2D2D2D text, #E8A86B accent,
#6B9B7A success, #C4A35A caution, #F2F2EF cards, #D4D4CF muted, #E8E8E4 grid

NO dark backgrounds. NO emojis. NO tiny text. NO rushed transitions.
NO bouncy animations. Premium, minimal, calm.
```

### Iteration Notes
- v1: Had dark mode colors (#121212), wrong brand palette. Scrapped.
- v2: Correct brand palette. Transitions improved but user says still slightly quick.
  For v3: push all transitions to 1000ms+, total duration to 25s+.

---

## PIECE #2: Energy Waveform - "Your Day Has a Shape"

**Type**: TOFU - Awareness / Visual Education
**Format**: 9:16 (1080x1920) | 30s | Instagram Reels / TikTok / Shorts
**Tool**: Vibe Motion (primary)
**Status**: v1 produced, worked well. One sequencing fix needed (see below).

### What Worked
- Waveform drawing across white canvas is a strong visual hook
- Zone color coding (green/ochre/blue) is clear and intuitive
- Annotation callouts with colored borders match zones well
- Contrast section (erratic vs smooth waveform) lands the message
- "Energy isn't random. It's architecture." is a strong thesis line
- Waveform callback behind brand lock-up ties the close to the concept

### What to Fix
- **Sequencing issue**: When "Your day has a shape" slides to top, the waveform
  partially drew simultaneously. Then the full waveform drawing animation restarted,
  causing the pre-drawn section to jump/skip. FIX: The waveform must NOT begin
  drawing until AFTER the title has fully settled at the top AND a pause has passed.
  Strict sequential order: title moves → title settles → pause → THEN waveform starts.

### Key Learning: Strict Sequencing Rule
When an element moves/transitions AND a new element is about to animate, they must
be STRICTLY SEQUENTIAL -- never overlapping. The rule:
1. Previous element completes its animation fully
2. Pause (400ms minimum)
3. THEN next element begins
Never allow two animations to run concurrently unless they are part of the SAME
visual moment (e.g., staggered card entries are one moment, but title-move +
waveform-draw are two separate moments).

### Design Elements Introduced
- Energy waveform as core visual (reusable in all future data content)
- Zone color coding: sage green (high), muted ochre (low), twilight blue (evening)
- Annotation callout cards with colored left borders matching zones
- Contrast waveforms (erratic vs. smooth) as before/after visual
- Subtle background waveform in brand lock-up (visual callback)

### Full Prompt (v1 - with sequencing fix)

```
=== VIBE MOTION PROMPT ===
[Title]: ENTROPY TOFU #2 - "Your Day Has a Shape"
[Format]: 1080x1920 (9:16) | 30 seconds | Vertical Reel

[Description]:
Create a 30-second vertical motion graphic (1080x1920, 9:16) for a premium
wellness brand called ENTROPY.

OVERALL STYLE: Ultra-minimal, premium, high-whitespace design on an off-white
(#FAFAF8) canvas. Apple keynote aesthetics meets elegant health data
visualization. This piece is VISUAL -- centered on an animated energy
waveform/curve. Typography supports the visual, not the other way around.
Clean, calm, scientific. ALL text large and readable on mobile. No emojis.

CRITICAL SEQUENCING RULE: Animations must be STRICTLY SEQUENTIAL. When one
element is moving/transitioning, NO other new element should begin animating
until the first is fully complete AND a pause has passed. Never run two
independent animations concurrently. Order: element completes → 400ms pause
→ next element begins.

CRITICAL PACING RULE: Every transition must be SLOW, SMOOTH, and DELIBERATE.
Nothing should feel rushed. Premium pacing throughout. When in doubt, go slower.

==========================================================
SCENE 1 (0-3.0s): HOOK - THE QUESTION
==========================================================
- Clean off-white (#FAFAF8) canvas
- 500ms of pure stillness (let the white canvas breathe)

- Centered text fades in SLOWLY (800ms fade, ease-out):
  "Your day has a shape."
  Font: General Sans Bold, #1A3A3A (deep teal)
  Size: LARGE -- fills 75% of screen width

- 400ms hold after text is fully visible

- Below the text, a thin horizontal baseline (#D4D4CF) fades in across
  full width (1000ms, drawing left to right)

- Below the line, time markers fade in simultaneously:
  "6AM"  "12PM"  "6PM"  "12AM"
  Font: JetBrains Mono Regular, #6B6B6B, clearly readable
  Spaced evenly. Fade in: 600ms

- HOLD for 800ms. Let the viewer read and understand.

IMPORTANT: The baseline and time markers are STATIC SETUP elements.
NO waveform drawing happens in this scene. The waveform does NOT exist yet.

==========================================================
SCENE 2 (3.0-8.0s): THE WAVEFORM DRAWS
==========================================================
STRICT SEQUENCE -- three phases, never overlapping:

PHASE A - TITLE MOVES (3.0-4.4s):
- "Your day has a shape." smoothly slides up to the top of the screen
  Duration: 1000ms, ease-in-out, deliberate
  Shrinks to 60% of original size, stays visible as title
- The baseline and time markers stay in their positions (they do NOT move)
- 400ms pause after title is fully settled at top

PHASE B - PAUSE (4.4-4.8s):
- 400ms of stillness. Title at top. Baseline visible. No animation.
  The screen is waiting. This pause is MANDATORY.

PHASE C - WAVEFORM DRAWS (4.8-8.0s):
- ONLY NOW does the waveform begin drawing.
- From the LEFT edge of the baseline, the energy waveform draws LEFT to RIGHT
  continuously over 2500ms. The waveform starts FRESH from the left edge --
  there is NO pre-drawn portion, it begins from zero.

  Waveform shape:
  - Starts low at 6AM (energy building)
  - Rises through morning, PEAKS around 10AM
  - Descends through midday
  - DIPS to valley around 2-3PM (afternoon crash)
  - Rises slightly 5-6PM (second wind)
  - Descends through evening, settles low by 10PM

  Line: 3px stroke, smooth, #1A3A3A (deep teal)
  A small bright dot (#1A3A3A) leads the drawing tip

  Soft gradient fill appears beneath the line AS it draws (progressive):
  - Under peaks: #6B9B7A at 15% opacity (sage green)
  - Under valleys: #C4A35A at 15% opacity (muted ochre)
  - Under evening: #5A7A8C at 15% opacity (twilight blue)

  Drawing pace: DELIBERATE, steady, not racing

- 800ms hold after waveform finishes. Let the full shape land.

==========================================================
SCENE 3 (8.0-14.0s): THE ANNOTATIONS
==========================================================
Three callout annotations appear ONE AT A TIME. Each is fully complete
before the next begins. No overlapping animations.

CALLOUT 1 (8.0-10.0s): THE PEAK
- At the 10AM peak on the curve:
  Thin vertical line (#1A3A3A, 1px) draws upward from curve point (400ms)
- 200ms pause
- Rounded rectangle card appears above:
  Background: #F2F2EF. Left border: 3px #6B9B7A (sage green)
  "Peak focus" Inter SemiBold, #2D2D2D
  "9-11 AM" JetBrains Mono, #6B6B6B below
  Card: 40% screen width, fade in + slide (500ms)
- HOLD 800ms

CALLOUT 2 (10.0-12.0s): THE CRASH
- At the 2-3PM valley:
  Thin vertical line draws downward (400ms)
- 200ms pause
- Card below curve:
  Left border: 3px #C4A35A (muted ochre)
  "The crash" / "2-3 PM"
  Same animation (500ms)
- HOLD 800ms

CALLOUT 3 (12.0-14.0s): RECOVERY
- At 5-6PM secondary rise:
  Thin vertical line draws upward (400ms)
- 200ms pause
- Card:
  Left border: 3px #5A7A8C (twilight blue)
  "Second wind" / "5-6 PM"
  Same animation (500ms)
- HOLD 800ms

==========================================================
SCENE 4 (14.0-19.0s): THE CONTRAST
==========================================================
- Everything fades to 20% opacity (1000ms)
- 400ms pause on dimmed state

- Two mini-waveforms, stacked vertically:

  TOP (appears first):
  "Fighting the curve" Inter SemiBold, #2D2D2D, 60% width
  Label fades in (500ms), then 300ms pause, THEN:
  Jagged erratic waveform draws L-to-R (1200ms)
  Line: #C4A35A, fill beneath at 10%
  600ms hold

  BOTTOM (appears after top completes):
  "Designing around it" Inter SemiBold, #2D2D2D, 60% width
  Label fades in (500ms), then 300ms pause, THEN:
  Smooth stable waveform draws L-to-R (1200ms)
  Line: #6B9B7A, fill beneath at 10%
  600ms hold

- Below both, text fades in (600ms):
  "Same person. Same 24 hours."
  Inter Regular, #6B6B6B, 65% width

- HOLD 1000ms

==========================================================
SCENE 5 (19.0-23.5s): THE THESIS
==========================================================
- Everything fades out (1000ms)
- 600ms clean white canvas (breathing room)

- Line 1: "Energy isn't random."
  General Sans Bold, #1A3A3A, 75% width
  Fade in + slide up 80px (600ms)
  HOLD 800ms

- Line 2: "It's architecture."
  General Sans Bold, #E8A86B (soft amber)
  65% width, fade in + slide up (600ms)
  HOLD 1000ms

==========================================================
SCENE 6 (23.5-30.0s): BRAND LOCK-UP
==========================================================
- Thesis fades (800ms)
- 500ms clean canvas

- Subtle waveform line (#1A3A3A at 30%) draws across center (1000ms)
- 400ms pause

- "ENTROPY" assembles from geometric fragments above line (1000ms)
  General Sans Bold, #1A3A3A, ALL CAPS, 55% width
- 400ms pause

- "Your lifestyle, redesigned" fades in below line (600ms)
  Inter Regular, #2D2D2D, 50% width
- 400ms pause

- "entropylifestyle.com" fades in (600ms)
  Inter Medium, #1A3A3A, 45% width
- 300ms pause

- "Follow for the protocol →" fades in (600ms)
  Inter SemiBold, #E8A86B, 50% width

- HOLD 2 full seconds

==========================================================

PALETTE: #FAFAF8 canvas, #1A3A3A primary, #2D2D2D text, #E8A86B accent,
#6B9B7A success, #C4A35A caution, #5A7A8C calm, #F2F2EF cards, #D4D4CF muted

TYPOGRAPHY: JetBrains Mono (numbers/data), General Sans Bold (headlines/brand),
Inter (body/tags/CTA/website)

ABSOLUTE RULES:
- Animations are STRICTLY SEQUENTIAL. Never two independent animations at once.
- Order: element completes → 400ms pause → next element begins.
- Every transition 1000ms minimum. Every hold 800ms minimum.
- Final frame 2 second hold. No tiny text. No emojis. No dark backgrounds.
- No bouncy animations. Mechanical, damped, ease-out only.
- The waveform MUST NOT begin drawing until the title has fully settled AND
  a 400ms pause has passed. This is NON-NEGOTIABLE.
```

### Iteration Notes
- v1: Waveform partially drew while title was still moving up. Sequencing issue.
  Fixed by adding STRICT SEQUENTIAL rule and explicit PHASE A/B/C breakdown
  in Scene 2 to prevent concurrent animations.

### Content Strategy Notes
- Piece #1 (Manifesto) + Piece #2 (Energy Shape) establish two visual pillars:
  1. Fragment assembly/disassembly motif
  2. Energy waveform motif
- All future content can reference either or both
- Together they answer: "What?" (energy matters) and "How?" (your day has a shape)

---

## PIECE #3: Discipline Battery - "Discipline Has a Battery Life"

**Type**: TOFU - Contrarian Hook / Myth-Busting
**Format**: 9:16 (1080x1920) | 30s | Instagram Reels / TikTok / Shorts
**Tool**: Vibe Motion (primary)
**Status**: v1 - New concept

### What's Different From Pieces #1 and #2
- Provocative/contrarian hook vs. informative (drives comments + shares)
- Interactive visual element: a battery/progress bar that depletes in real-time
- Narrative structure: scenario-by-scenario storytelling (6AM, 9AM, 1PM, 3PM)
- Emotional arc: recognition → empathy → reframe → hope
- Callbacks to Piece #2's waveform (visual continuity across content system)
- Delivers "Systems over Willpower" pillar directly

### Design Elements Introduced
- Depletion bar (horizontal battery that drains) -- third visual motif
- Color-shifting fill (green → ochre → red as energy depletes)
- Scenario-based storytelling with time stamps
- Callback waveform (sage green, stable, from Piece #2)

### Full Prompt (v1)

```
=== VIBE MOTION PROMPT ===
[Title]: ENTROPY TOFU #3 - "Discipline Has a Battery Life"
[Format]: 1080x1920 (9:16) | 30 seconds | Vertical Reel

OVERALL STYLE: Ultra-minimal, premium, high-whitespace on off-white (#FAFAF8)
canvas. Apple keynote meets health data viz. ALL text large, readable at
arm's length. No emojis. Geometric elements only.

CRITICAL SEQUENCING RULE: Animations STRICTLY SEQUENTIAL. When one element
is animating, NO independent element begins until the first is FULLY COMPLETE
and a 400ms pause has passed. A completes → 400ms pause → B begins.

CRITICAL PACING RULE: Every transition 1000ms+. Every hold 800ms+. Final
frame 2 seconds. Premium = deliberate, never rushed.

==========================================================
SCENE 1 (0-3.5s): HOOK - THE BATTERY
==========================================================
- Clean off-white (#FAFAF8) canvas
- 500ms of pure stillness

- A large horizontal rounded rectangle appears center-screen:
  The "discipline battery" -- a progress/energy bar
  Outer shell: 2px border, #D4D4CF, rounded corners
  Inner fill: solid #6B9B7A (sage green), completely full (100%)
  Size: fills 75% screen width, about 40px tall
  Bar fades in (600ms, ease-out)

- 400ms pause AFTER bar fully visible

- ONLY THEN: Text appears ABOVE the bar (fade in, 600ms):
  "Discipline has a battery life."
  General Sans Bold, #1A3A3A, fills 80% screen width

- "100%" label appears RIGHT of bar:
  JetBrains Mono Bold, #6B9B7A, fades in with text (600ms)

- HOLD 1000ms

==========================================================
SCENE 2 (3.5-12.5s): THE DRAIN
==========================================================
Headline slides to top (1000ms, ease-in-out), shrinks to 60%.
400ms pause AFTER title settles.

Four drain moments, STRICTLY SEQUENTIAL. Each completes fully
before next begins. Scenario text fades OUT before next fades IN.

DRAIN 1 (~4.9-6.5s):
- Text fades in LEFT side, above bar (500ms):
  "6 AM — Alarm rings." Inter SemiBold, #2D2D2D, 60% width
  "Discipline kicks in." Inter Regular, #6B6B6B, 50% width
- 300ms pause
- THEN bar: 100% → 85% (600ms, ease-out). Fill: #6B9B7A. Label: "85%"
- HOLD 600ms

DRAIN 2 (~6.5-8.1s):
- Previous text fades OUT (400ms). 300ms pause.
- New text fades in (500ms):
  "9 AM — Deep work sprint." / "Pushing through back-to-backs."
- 300ms pause
- THEN bar: 85% → 55% (600ms). Fill transitions to #C4A35A. Label: "55%"
- HOLD 600ms

DRAIN 3 (~8.1-9.7s):
- Previous text fades OUT (400ms). 300ms pause.
- New text fades in (500ms):
  "1 PM — Skip lunch. Power through." / "Still grinding."
- 300ms pause
- THEN bar: 55% → 25% (600ms). Fill stays #C4A35A. Label: "25%"
- HOLD 600ms

DRAIN 4 (~9.7-12.5s):
- Previous text fades OUT (400ms). 300ms pause.
- New text fades in (500ms):
  "3 PM — Decisions feel impossible." / "Everything takes twice the effort."
- 300ms pause
- THEN bar: 25% → 4% (800ms, slower -- dragging). Fill transitions to #C45A5A.
  Nearly empty, thin sliver. Label: "4%" in #C45A5A
- HOLD 800ms

==========================================================
SCENE 3 (12.5-15.5s): THE CRASH
==========================================================
- All scenario text fades out (400ms). 400ms pause.
- Bar drains 4% → 0% (600ms, ease-in -- accelerating collapse)
  Fill disappears. Empty shell. Label: "0%" in #C45A5A
- 400ms pause
- Battery outline dims to 30% opacity (400ms)
- 400ms pause. THEN:
- Text fades in below dimmed bar (600ms):
  "This is where your day falls apart."
  General Sans SemiBold, #2D2D2D, fills 80% screen width
- HOLD 1000ms

==========================================================
SCENE 4 (15.5-20.5s): THE REFRAME
==========================================================
- Everything fades out (1000ms). 600ms clean canvas.
- Line 1 (600ms fade + slide up 60px):
  "What if the answer isn't more discipline?"
  General Sans Bold, #2D2D2D, fills 85% width
- HOLD 1000ms
- Line 2 (600ms, same animation):
  "What if it's better energy?"
  General Sans Bold, #1A3A3A. "energy" in #E8A86B.
  Fills 75% width
- HOLD 1000ms

==========================================================
SCENE 5 (20.5-24.5s): SYSTEMS OVER WILLPOWER
==========================================================
- Both lines fade out (800ms). 500ms clean canvas.
- Small clean energy waveform draws center-screen (L-to-R, 1500ms)
  2px, #6B9B7A, smooth stable curves. Fill: #6B9B7A at 10%
  Occupies middle third, 40% height. Visual callback to Piece #2.
- 400ms pause AFTER waveform complete
- Text fades in below (600ms):
  "Systems over willpower."
  General Sans Bold, #1A3A3A, fills 70% width
- HOLD 1000ms

==========================================================
SCENE 6 (24.5-30.0s): BRAND LOCK-UP
==========================================================
- Everything fades (800ms). 500ms clean canvas.
- "ENTROPY" assembles from fragments (1000ms). ALL CAPS.
  General Sans Bold, #1A3A3A, 55% width
- 400ms pause
- "Your lifestyle, redesigned" fades in (600ms)
  Inter Regular, #2D2D2D, 50% width
- 400ms pause
- Gradient line draws: #E8A86B → #D4955A, 40% width (400ms)
- 300ms pause
- "entropylifestyle.com" fades in (600ms). Inter Medium, #1A3A3A, 45% width
- 300ms pause
- "Follow for the protocol →" fades in (600ms). Inter SemiBold, #E8A86B, 50% width
- HOLD 2 full seconds

==========================================================

PALETTE: #FAFAF8 canvas, #1A3A3A primary, #2D2D2D text, #E8A86B accent,
#6B9B7A healthy, #C4A35A caution, #C45A5A depleted, #F2F2EF cards,
#D4D4CF borders, #6B6B6B captions

TYPOGRAPHY: JetBrains Mono Bold (%, numbers), General Sans Bold (headlines/brand),
Inter SemiBold/Regular (scenarios/tagline/CTA/website)

ABSOLUTE RULES:
- STRICTLY SEQUENTIAL animations. A complete → 400ms pause → B begins.
- Scenario text fades OUT fully before next scenario fades IN.
- Bar animations ONLY begin after scenario text is visible + 300ms pause.
- Every transition 1000ms+. Every hold 800ms+. Final frame 2 seconds.
- No emojis. No dark backgrounds. No tiny text. No bouncy animations.
```

### Content Strategy Notes
- Three TOFU pieces now cover three pillars:
  1. Manifesto → "Energy > time" (Time as Currency + Energy as North Star)
  2. Energy Shape → "Your day has a pattern" (Energy as North Star)
  3. Discipline Battery → "Systems > willpower" (Systems over Willpower)
- Remaining TOFU pillars to cover: "Measurable Proof" and "Lifelong Operating System"

---

## PIECE #4: Metric Dashboard - "This is 90 Days of Proof"

**Type**: TOFU - Aspirational Data / Measurable Proof
**Format**: 9:16 (1080x1920) | 30s | Instagram Reels / TikTok / Shorts
**Tool**: Vibe Motion (primary)
**Status**: v1 - New concept

### What's Different From Pieces #1-3
- Most DATA-DENSE piece yet (5 animated metrics with inline visualizations)
- Introduces the "metric card" visual motif (reusable shareable format)
- Aspirational: shows the RESULT, not just the concept
- Before → after transformation animation is the core content
- Visual satisfaction of watching numbers climb is inherently engaging
- Each metric has a small geometric indicator (arc, bar, ring) that animates

### Design Elements Introduced
- Metric cards: fog grey rounded rectangles with colored left borders
- Inline geometric indicators: partial arcs, horizontal bars, vertical bars, circular rings
- Color-state shifting: ochre (before/poor) → sage green (after/good)
- "Day 1" → "Day 90" header transition as transformation trigger
- Count-up animations across multiple metrics sequentially

### The 5 Metrics
1. Sleep Score: 52 → 87 (partial arc indicator)
2. Energy Stability: 34% → 81% (horizontal bar indicator)
3. Deep Work: 1.2h → 4.1h (3 vertical bars indicator)
4. Recovery Rate: 45% → 88% (circular ring indicator)
5. Decision Fatigue: HIGH → LOW (stress lines → calm line indicator)

### Full Prompt (v1)

```
[Title]: ENTROPY TOFU #4 - "This is 90 Days of Proof"
[Format]: 1080x1920 (9:16) | 30 seconds | Vertical Reel

STYLE: Ultra-minimal, premium, high-whitespace on #FAFAF8 canvas.
Apple Health yearly report meets WHOOP monthly review meets Strava shareables.
Data-dense with 5 metric cards. ALL text large. No emojis.

SEQUENCING: STRICTLY SEQUENTIAL. A completes → 400ms pause → B begins.
PACING: Every transition 1000ms+. Every hold 800ms+. Final frame 2s.

SCENE 1 (0-3.5s): HOOK
- 500ms stillness. Single metric card fades in center (600ms):
  #F2F2EF card, 4px #6B9B7A left border, 80% screen width
  "Sleep Score" Inter SemiBold, #2D2D2D (left)
  "87" JetBrains Mono Bold, #6B9B7A (right) -- counts from 0 (800ms)
  Small partial-circle arc, 95% filled, #6B9B7A
- 400ms pause. THEN: "After 90 days." fades in below (600ms)
  Inter Regular, #6B6B6B, 40% width
- HOLD 1000ms

SCENE 2 (3.5-9.0s): DAY 1 STATE
- Hook card + subtitle fade out (800ms). 600ms clean canvas.
- "Day 1" appears top-center (500ms): General Sans Bold, #C4A35A, 35% width
  Thin line draws beneath (400ms). 400ms pause.
- 5 cards appear ONE AT A TIME (400ms each, slide up + fade, 200ms between):
  All: #F2F2EF bg, 4px #C4A35A border, 85% width
  Values in JetBrains Mono Bold, #C4A35A
  1. Sleep Score: "52" + 60% arc
  2. Energy Stability: "34%" + 34% horizontal bar
  3. Deep Work: "1.2h" + three short vertical bars
  4. Recovery Rate: "45%" + ~45% circular ring
  5. Decision Fatigue: "HIGH" + three descending stress lines
- HOLD 800ms

SCENE 3 (9.0-16.0s): TRANSFORMATION
- "Day 1" cross-fades to "Day 90" (600ms), color #C4A35A → #6B9B7A
- 400ms pause (tension: header changed, cards haven't)
- Cards transform ONE AT A TIME, each complete before next:
  Per card: number counts to new value (600ms) + border/color shifts
  ochre → green + indicator animates to new state (400ms). 400ms hold.
  1. 52→87, arc fills 95%
  2. 34%→81%, bar fills 81%
  3. 1.2h→4.1h, bars grow tall
  4. 45%→88%, ring fills 88%
  5. HIGH→LOW, stress lines smooth to one calm line
- HOLD 800ms after final card

SCENE 4 (16.0-20.0s): THESIS
- All fades (1000ms). 600ms clean canvas.
- "This is what managed energy looks like." (600ms fade + slide up)
  General Sans Bold, #1A3A3A, 85% width. "managed" in #E8A86B.
- HOLD 1200ms

SCENE 5 (20.0-24.0s): CLOSER
- Fade out (800ms). 500ms canvas.
- "Measurable progress." General Sans Bold, #1A3A3A, 70% width (600ms)
- HOLD 800ms
- "Not motivation." Same style, same size, below (600ms)
- HOLD 1000ms

SCENE 6 (24.0-30.0s): BRAND
- Fade out (800ms). 500ms canvas.
- "ENTROPY" fragment assembly (1000ms). ALL CAPS. #1A3A3A. 55% width.
- 400ms. "Your lifestyle, redesigned" (600ms). #2D2D2D. 50% width.
- 400ms. Gradient line #E8A86B→#D4955A (400ms).
- 300ms. "entropylifestyle.com" (600ms). #1A3A3A. 45% width.
- 300ms. "Follow for the protocol →" (600ms). #E8A86B. 50% width.
- HOLD 2 seconds.

PALETTE: #FAFAF8 canvas, #1A3A3A primary, #2D2D2D text, #E8A86B accent,
#C4A35A before/ochre, #6B9B7A after/green, #F2F2EF cards, #D4D4CF borders,
#E8E8E4 track/empty, #6B6B6B captions

RULES: Strictly sequential. Cards one at a time. Transforms one at a time.
No emojis. No dark bg. No tiny text. No bouncy. 1000ms+ transitions.
Card indicators animate DURING their card's transform (same moment).
```

### Content Strategy Notes
- Four TOFU pieces now cover four of five pillars:
  1. Manifesto → Energy as North Star + Time as Currency
  2. Energy Shape → Energy as North Star (visual proof)
  3. Discipline Battery → Systems over Willpower
  4. 90 Days of Proof → Measurable Proof
- Final TOFU pillar: "Lifelong Operating System" (not a 30-day challenge)
- After TOFU #5, shift to MOFU content (protocol breakdowns, how-to, deeper education)

---

## PIECE #5: App Graveyard - "Every System You've Tried"

**Type**: TOFU - Identity / Lifelong Operating System
**Format**: 9:16 (1080x1920) | 32s | Instagram Reels / TikTok / Shorts
**Tool**: Vibe Motion (primary)
**Status**: v1 - Template-breaking concept

### What's Different From ALL Previous Pieces
- **BREAKS THE TEMPLATE** intentionally -- every piece before followed the same skeleton
- Hook is a personal question (confrontational), not data or statements
- Content is relatable narrative (app graveyard), not data viz or philosophy
- Brand moment IS the content (tiles converging = the OS concept)
- Brand lock-up: ENTROPY fades in cleanly -- NO fragment assembly (done it 4 times already)
- CTA: "This is the last system you'll need" (identity close, not "follow for the protocol")
- Horizontal line is solid #1A3A3A (not gradient amber→gold)
- Tone: intimate, self-aware, slightly confrontational then hopeful

### Design Elements Introduced
- Scattered tiles that dim/die (the "graveyard" motif)
- Tile convergence animation: scattered → aligned → unified structure
- Organic positioning with slight rotations (not rigid grid)
- Clean brand fade (no assembly) -- confidence through simplicity
- Identity-level CTA vs. action CTA

### Visual Innovation
- Tiles positioned organically (rotated 1-3 degrees, scattered like tossed sticky notes)
- Each tile "dies" individually (dims to 25%, drains to grey)
- The convergence is gravitational -- slow then purposeful
- Tiles straighten, resize uniformly, align into a system structure
- Left borders appear simultaneously on all cards (the "system" snapping into place)

### The 6 Tiles (Relatable Archetypes)
1. "The 30-day challenge"
2. "The habit tracker"
3. "The meditation app"
4. "The morning routine PDF"
5. "The gratitude journal"
6. "The accountability partner"

### Full Prompt (v1)

```
[Saved as delivered above -- full 32s Vibe Motion prompt]
```

### Content Strategy Notes
- ALL FIVE TOFU pillars now covered:
  1. Manifesto → Energy as North Star + Time as Currency
  2. Energy Shape → Energy as North Star (visual)
  3. Discipline Battery → Systems over Willpower
  4. 90 Days of Proof → Measurable Proof
  5. App Graveyard → Lifelong Operating System
- TOFU foundation COMPLETE. Piece #6 bridges TOFU→MOFU.
- MOFU candidates: Protocol breakdowns, "How the system works" deep dives,
  user journey walkthroughs, specific ritual/protocol education content

### Creative Notes for Future Reference
- Breaking the template after 4 consistent pieces felt RIGHT. The consistency
  built brand recognition; the break creates surprise and re-engagement.
- The "clean fade-in" for the brand name (no assembly) only works BECAUSE
  we established the assembly pattern first. Use sparingly.
- Personal questions as hooks drive higher comment engagement than statements.
- The convergence animation (scattered → organized) is the purest expression
  of the entropy→order brand principle. Could become the signature animation.
- "This is the last system you'll need" is a stronger identity-lock CTA than
  "Follow for the protocol" -- use for closing/capstone content.

---

## PIECE #6: Dual-Layer Data Story - "What's Actually Running Your Best Day"

**Type**: TOFU→MOFU Bridge - Energy Architecture Visualization
**Format**: 9:16 (1080x1920) | 34s | Instagram Reels / TikTok / Shorts
**Tool**: Vibe Motion (primary)
**Status**: v1 - Most complex piece yet. Split-screen dual-layer format.

### Concept Overview
A split-screen format that shows a typical high-performer's day from TWO perspectives simultaneously: the visible schedule (top half) and the invisible energy data running beneath it (bottom half). The piece reveals that every "great day" has a hidden architecture — your energy data — and that ENTROPY makes it visible.

### What's New (Creative Firsts)
- **Split-screen dual-layer format** — first time using divided canvas
- **Synchronized beats** — paired activity + energy data, not sequential scenes
- **Color-coded zone progression** — sage green → muted ochre → twilight blue
- **Insight annotations** — text overlaying the divider line at key moments
- **Reveal climax** — data layer expands, schedule fades (role reversal)
- **Architecture metaphor** — positions energy data as the structural foundation of a day

### Visual Motif
Horizontal divider splitting the screen into schedule (top) and energy stream (bottom). The divider itself becomes a surface for insight annotations. At the climax, the bottom layer (energy) expands to fill the screen — revealing that the data was always the real story.

### Hook Strategy
- Type: Visual question + split reveal
- Opening: "What's actually running your best day?" appears center-screen
- The split opens like a curtain — schedule slides up, energy data slides down
- Mute-effective: Yes — text-driven, color-coded zones are self-explanatory

### The 9 Beats (Activity + Energy Pairs)

| Beat | Top (Schedule) | Bottom (Energy) | Zone Color |
|------|---------------|-----------------|------------|
| 1 | 6:00 AM — Wake | Recovery Score: 84 | Sage Green #6B9B7A |
| 2 | 6:30 AM — Cold Exposure Protocol | HRV: 68ms ↑ | Sage Green #6B9B7A |
| 3 | 7:15 AM — Deep Work Block | Focus Index: 92 | Sage Green #6B9B7A |
| 4 | 9:30 AM — Back-to-back meetings | Energy: declining → 61 | Muted Ochre #C4A35A |
| 5 | 11:00 AM — "Quick lunch at desk" | Strain: 14.2 (overtrained) | Muted Ochre #C4A35A |
| 6 | 1:30 PM — Strategy session | Cognitive load: HIGH | Muted Ochre #C4A35A |
| 7 | 3:00 PM — Scheduled recovery | Energy: recovering → 72 | Sage Green #6B9B7A |
| 8 | 5:30 PM — Evening wind-down | Parasympathetic shift detected | Twilight Blue #5A7A8C |
| 9 | 9:00 PM — Sleep protocol | Sleep readiness: 91 | Twilight Blue #5A7A8C |

### Insight Annotations (Overlay the Divider)
- After Beat 3: "This is where most people stop looking."
- After Beat 5: "The schedule said fine. The data said otherwise."
- After Beat 7: "Recovery isn't a break. It's architecture."

### Full Prompt (v1)

```
=== VIBE MOTION PROMPT ===
[Title]: ENTROPY Dual-Layer Data Story - "What's Actually Running Your Best Day"
[Format]: 1080x1920 (9:16) | 34 seconds | Vertical Reel

[Description]:
Create a 34-second vertical motion graphic (1080x1920, 9:16) for a premium
wellness brand called ENTROPY.

OVERALL STYLE: Ultra-minimal, premium, high-whitespace design on an off-white
(#FAFAF8) canvas. Think Apple keynote aesthetics meets health data
visualization. Typography-driven with thin-line geometric accents. No dark
backgrounds. Clean, calm, scientific, premium. ALL text must be large and
bold -- designed to be read at arm's length on a phone. No emojis anywhere.
Geometric shapes and thin-line icons only.

CRITICAL ANIMATION RULE: Animations are STRICTLY SEQUENTIAL. No two independent
animations may run at the same time. When one element finishes animating, wait
400ms, THEN start the next animation. Use "ONLY THEN", "AFTER", "WAIT UNTIL"
language throughout. Previous text must fade OUT before new text fades IN.

---

SCENE 1 (0.0–3.0s): HOOK — THE QUESTION

[Phase A: 0.0–1.5s]
- Off-white (#FAFAF8) canvas, completely clean
- Text fades in, center-screen, one line at a time:
  Line 1: "What's actually running"
  Font: General Sans Bold, color #2D2D2D (graphite), 80% screen width
  Fade in over 600ms. Hold 400ms.
  ONLY THEN Line 2: "your best day?"
  Same style, same size. Fade in over 600ms. Hold 600ms.

[Phase B: 1.5–3.0s]
- Both lines hold for 400ms.
- ONLY THEN: A thin horizontal line (#D4D4CF, 1px) draws from left edge to
  right edge across the exact vertical center of the screen. Drawing animation
  takes 800ms, ease-in-out.
- ONLY THEN: The question text fades out over 500ms.
- The divider line remains. This line will persist through Scenes 2–6.

---

SCENE 2 (3.0–5.5s): THE SPLIT OPENS

[Phase A: 3.0–4.0s]
- The divider line is at vertical center.
- ABOVE the line: Label fades in at top-left corner:
  "YOUR SCHEDULE" — General Sans Medium, #1A3A3A, 12px caps, letter-spacing 3px
  Fade in 500ms.

[Phase B: 4.0–5.0s]
- ONLY THEN, BELOW the line: Label fades in at bottom-left corner:
  "YOUR ENERGY DATA" — General Sans Medium, #1A3A3A, 12px caps, letter-spacing 3px
  Fade in 500ms.

[Phase C: 5.0–5.5s]
- Hold both labels visible. 500ms breathing pause.
- The stage is set: two layers, one screen.

---

SCENE 3 (5.5–14.0s): BEATS 1–5 — THE MORNING ARC

Each beat follows this STRICT pattern:
1. Activity card appears in TOP half (fade in 500ms)
2. WAIT 400ms
3. Energy data appears in BOTTOM half (fade in 500ms)
4. WAIT 400ms
5. Previous beat's elements fade out 400ms
6. WAIT 400ms
7. Next beat begins

BEAT 1 (5.5–7.0s):
- TOP: "6:00 AM" in JetBrains Mono Bold #1A3A3A (55% width), below it
  "Wake" in General Sans Medium #2D2D2D (60% width of top half)
- WAIT 400ms
- BOTTOM: "Recovery Score" label in General Sans Medium #6B6B6B (40% width),
  below it "84" counting up from 0 in JetBrains Mono Bold #6B9B7A (sage green),
  55% width of bottom half. Small filled circle (#6B9B7A) to left of score.
- Hold 600ms.

BEAT 2 (7.0–8.5s):
- Beat 1 fades out 400ms. WAIT 400ms.
- TOP: "6:30 AM" + "Cold Exposure Protocol"
- WAIT 400ms
- BOTTOM: "HRV" label, "68ms ↑" in JetBrains Mono Bold #6B9B7A.
  Thin upward arrow drawn as a 2px line stroke animation (300ms).
- Hold 600ms.

BEAT 3 (8.5–10.0s):
- Beat 2 fades. WAIT 400ms.
- TOP: "7:15 AM" + "Deep Work Block"
- WAIT 400ms
- BOTTOM: "Focus Index" label, "92" in JetBrains Mono Bold #6B9B7A.
  Small thin-line bar chart (3 bars, ascending) draws left to right (400ms).
- Hold 600ms.

INSIGHT ANNOTATION 1 (10.0–11.5s):
- All beat elements fade out 400ms. WAIT 400ms.
- ON the divider line itself, centered: text appears
  "This is where most people stop looking."
  General Sans Medium Italic, #1A3A3A, 65% width.
  Fade in 600ms. Hold 800ms. Fade out 400ms. WAIT 400ms.

BEAT 4 (11.5–12.5s):
- TOP: "9:30 AM" + "Back-to-back meetings"
- WAIT 400ms
- BOTTOM: "Energy" label, "declining → 61" in JetBrains Mono Bold #C4A35A
  (muted ochre). Small downward trend line draws (300ms). Circle changes to
  #C4A35A.
- Hold 500ms.

BEAT 5 (12.5–14.0s):
- Beat 4 fades. WAIT 400ms.
- TOP: "11:00 AM" + "Quick lunch at desk" (in quotes)
- WAIT 400ms
- BOTTOM: "Strain" label, "14.2" in JetBrains Mono Bold #C4A35A,
  "(overtrained)" in General Sans Regular #C4A35A below.
- Hold 600ms.

---

SCENE 4 (14.0–20.0s): BEATS 6–7 + INSIGHT 2 — THE MID-DAY TRUTH

INSIGHT ANNOTATION 2 (14.0–15.5s):
- All elements fade out 400ms. WAIT 400ms.
- ON the divider line: "The schedule said fine. The data said otherwise."
  General Sans Medium Italic, #1A3A3A, 70% width.
  Fade in 600ms. Hold 800ms. Fade out 400ms.

BEAT 6 (15.5–17.0s):
- WAIT 400ms after insight fades.
- TOP: "1:30 PM" + "Strategy session"
- WAIT 400ms
- BOTTOM: "Cognitive load" label, "HIGH" in JetBrains Mono Bold #C4A35A.
  Three thin horizontal lines at different lengths (stress visualization),
  drawing sequentially, 200ms each.
- Hold 500ms.

BEAT 7 (17.0–18.5s):
- Beat 6 fades. WAIT 400ms.
- TOP: "3:00 PM" + "Scheduled recovery"
- WAIT 400ms
- BOTTOM: "Energy" label, "recovering → 72" in JetBrains Mono Bold #6B9B7A
  (back to sage green). Small upward trend line draws (300ms). Circle returns
  to #6B9B7A.
- Hold 500ms.

INSIGHT ANNOTATION 3 (18.5–20.0s):
- All elements fade out 400ms. WAIT 400ms.
- ON the divider line: "Recovery isn't a break. It's architecture."
  General Sans Medium Italic, #1A3A3A, 65% width.
  Fade in 600ms. Hold 800ms. Fade out 400ms.

---

SCENE 5 (20.0–23.5s): BEATS 8–9 — THE WIND-DOWN

BEAT 8 (20.0–21.5s):
- WAIT 400ms.
- TOP: "5:30 PM" + "Evening wind-down"
- WAIT 400ms
- BOTTOM: "Parasympathetic shift detected" in General Sans Medium #5A7A8C
  (twilight blue). A soft sine wave draws slowly across bottom half (600ms),
  color #5A7A8C.
- Hold 600ms.

BEAT 9 (21.5–23.5s):
- Beat 8 fades. WAIT 400ms.
- TOP: "9:00 PM" + "Sleep protocol"
- WAIT 400ms
- BOTTOM: "Sleep Readiness" label, "91" in JetBrains Mono Bold #5A7A8C.
  Circle indicator: #5A7A8C. Three thin concentric arcs draw around the number
  (500ms, staggered 150ms each).
- Hold 800ms.

---

SCENE 6 (23.5–28.0s): THE REVEAL — DATA EXPANDS

[Phase A: 23.5–24.5s]
- All beat elements fade out 400ms. WAIT 400ms.
- The divider line begins to move UPWARD — slowly, deliberately.
  Over 1200ms (ease-in-out), the line moves from center to about 15% from top.
  The "YOUR SCHEDULE" label fades out as the space shrinks.
  The "YOUR ENERGY DATA" label grows slightly (fade to larger size).

[Phase B: 24.5–26.0s]
- ONLY THEN: In the now-expanded bottom area (85% of screen), a simplified
  24-hour energy curve draws from left to right.
  The curve uses three color segments:
  - Morning peak: #6B9B7A (sage green)
  - Mid-day dip: #C4A35A (muted ochre)
  - Evening wind-down: #5A7A8C (twilight blue)
  Curve draws over 1500ms, smooth, continuous.
  Thin vertical dotted lines mark the key beats (6AM, 7:15, 9:30, 3PM, 9PM).

[Phase C: 26.0–28.0s]
- ONLY THEN: Below the curve, centered:
  "Every great day has architecture."
  General Sans Bold, #1A3A3A (deep teal), 75% screen width.
  Fade in 600ms. Hold 1200ms.
  "You just couldn't see it. Until now."
  General Sans Regular, #2D2D2D (graphite), 70% width.
  Fade in 600ms. Hold 800ms.

---

SCENE 7 (28.0–34.0s): BRAND LOCK-UP + CTA

[Phase A: 28.0–29.5s]
- All previous content fades out 500ms. WAIT 400ms.
- Off-white (#FAFAF8) canvas, clean.
- "ENTROPY" fades in, center-screen, upper third.
  General Sans Bold, #1A3A3A (deep teal), 60% screen width, letter-spacing 8px.
  Fade in 800ms. Hold 400ms.

[Phase B: 29.5–31.0s]
- ONLY THEN: Below ENTROPY:
  "Your lifestyle, redesigned"
  General Sans Regular, #2D2D2D, 55% screen width.
  Fade in 600ms. Hold 400ms.

[Phase C: 31.0–32.0s]
- ONLY THEN: A thin horizontal line (same as the divider, callback) draws
  beneath the tagline. 40% width, centered. Drawing 500ms.
- WAIT 400ms.
- Below the line: "Your architecture starts here."
  General Sans Medium, #E8A86B (soft amber), 50% screen width.
  Fade in 500ms.

[Phase D: 32.0–34.0s]
- ONLY THEN: At bottom of screen:
  "entropylifestyle.com"
  General Sans Regular, #6B6B6B, 45% screen width.
  Fade in 400ms.
- Hold ALL elements for full 2 seconds.
- End frame.

---

[Brand Assets to Upload]:
- ENTROPY wordmark (or use text generation)
- JetBrains Mono Bold font file

[Post-Generation Adjustments]:
- Verify all beat transitions have visible 400ms pauses
- Check that no two animations overlap in any frame
- Verify zone colors match exact hex codes
- Confirm the divider-to-curve reveal transition is smooth
- Ensure insight annotations are centered ON the divider line
- Test at 720p first, upscale winner to 1080p
```

### What Worked
- Split-screen format is a genuine creative first — nothing in the series looks like this
- The "schedule vs. data" parallel creates an immediate "aha" moment
- Insight annotations ON the divider line are a strong narrative device
- The reveal (data expanding, schedule shrinking) is the entropy→order moment
- Color-coded zone progression (green → ochre → blue) is instantly readable
- "Recovery isn't a break. It's architecture." is the strongest single line yet
- "Your architecture starts here" is a CTA that sells identity, not features

### What to Improve for Next Iteration
- 34 seconds is long — test whether attention holds through all 9 beats
- The beat pattern (activity → data → fade → repeat) could feel repetitive —
  vary the animation style between beats if possible
- Consider whether 7 beats + 2 cut would be tighter without losing impact
- The reveal transition needs to feel magical, not mechanical — test carefully

### Content Strategy Notes
- This is the FIRST piece that bridges TOFU→MOFU territory
- It's still awareness-level (doesn't explain HOW the system works) but
  teases the depth of data ENTROPY provides
- The schedule-vs-data framing sets up future MOFU content perfectly:
  "Here's what happened on a great day → here's how to engineer one"
- MOFU follow-up candidates:
  - "The anatomy of a recovery block" (protocol deep dive)
  - "How your morning protocol builds compound energy" (mechanism education)
  - "Your first week in ENTROPY" (onboarding walkthrough)

### Creative Notes for Future Reference
- Split-screen formats create natural visual tension — use for comparison content
- Synchronized beats (parallel layers) are more demanding to produce but more
  rewarding to watch than sequential scenes
- Insight annotations as "interrupts" (appearing on the divider) break the
  pattern rhythm and create micro-hooks within the piece
- The "reveal" (one layer expanding, other shrinking) is a new transition type
  that could become a signature move for data-centric content
- "Architecture" as a metaphor for energy management landed strongly —
  worth building an entire vocabulary around (foundation, blueprints, load-bearing)
- Zone color progression (green→ochre→blue) across time creates a visual
  shorthand that should be consistent across all future pieces

---

## Design Rules Consolidated (From All Iterations)

### Timing Minimums
| Element | Minimum Duration |
|---------|-----------------|
| Scene-to-scene transition | 1000ms |
| Element position change | 1000ms |
| Text line reveal | 500-600ms |
| Breathing pause between lines | 400ms |
| Breathing pause between independent animations | 400ms |
| Hold after key statement | 800ms+ |
| Hold after recognition/low moment | 1000ms |
| Final frame hold | 2s |
| Total piece for 6-8 scenes | 28-30s |

### Sizing Minimums (1080x1920)
| Element | Screen Width % |
|---------|---------------|
| Hook number | 50-60% |
| Headlines | 70-85% |
| Body/statement | 75-90% |
| Data labels | 45-55% |
| Chart callouts | 50-60% |
| Chart axis labels | Clearly readable (28px+ equivalent) |
| CTA text | 50-60% |
| Website URL | 45-50% |

### Sequencing Rules
- Animations are STRICTLY SEQUENTIAL. Never two independent animations at once.
- Order: A completes → 400ms pause → B begins.
- Previous text fades OUT before next text fades IN (never overlap).
- Bar/chart animations begin ONLY AFTER their label text is visible + pause.
- Waveforms/drawing animations begin ONLY AFTER previous elements are settled + pause.
- Use explicit PHASE labels (A, B, C) in prompts for complex scenes.

### Absolute Rules
- No emojis. Geometric shapes and thin-line icons only.
- No dark backgrounds. Off-white #FAFAF8 canvas.
- No bouncy animations. Mechanical, damped, ease-out.
- No simultaneous text reveals. Always sequential, line by line.
- No decreasing sizes for equal-weight statements.
- Brand name: ENTROPY (all caps)
- Tagline: "Your lifestyle, redesigned"
- Website: entropylifestyle.com (every closing frame)
- All numbers in JetBrains Mono
- Premium = deliberate. When in doubt, add more time.

### Creative Evolution Rules
- After 3-4 consistent pieces, BREAK the template to surprise and re-engage
- Vary hook types: data → visual → narrative → personal question → provocation
- Vary brand lock-up: fragment assembly is default, but clean fade-in works
  for capstone/identity content (only after assembly pattern is established)
- Vary CTAs: "Follow for the protocol" for standard, identity-level closes
  ("This is the last system you'll need") for capstone content
- Each piece should introduce at least one NEW visual motif or animation
- Callbacks to previous pieces' visuals create series continuity
- Tone can range: philosophical → educational → provocative → intimate
- Don't repeat the same emotional register twice in a row
