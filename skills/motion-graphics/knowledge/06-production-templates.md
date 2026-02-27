# Entropy Lifestyle — 5 Production Templates

These are the only five motion graphic templates you need. Once built as Remotion compositions, they are endlessly replicable with new data, new hooks, new scripts.

---

## Template 1 — STAT BOMB (12-18 seconds)

The workhorse. Fastest to produce. Highest frequency. Built for TikTok completion rate.

### Structure
```
[0.0-0.3s]  Black/off-white frame → Number enters (animated count-up, monospaced)
[0.3-3.0s]  Number lands at full size (50-60% screen width), holds
[3.0-5.0s]  Context line slides up from below (what this number means)
[5.0-8.0s]  Second stat or reframe appears (escalation)
[8.0-12.0s] Entropy truth line — color shift on background or text accent
[12.0-14.0s] Brand close (ENTROPY wordmark + entropylifestyle.com)
```

### Motion Notes
- Number MUST use counter animation (count-up), never simple fade-in
- Context line: slide-up from below, 0.3s entrance
- Truth line: accompanied by background color shift (canvas → deep teal, or text shifts to soft amber)
- Hard cuts between sections for fact delivery
- Nothing static for more than 1.5 seconds — add secondary animated element during holds

### Color
- Problem section: Off-white canvas, deep teal/graphite text
- Truth line: Introduce soft amber accent or shift background to deep teal with off-white text

### Sound Design
- Minimal beat. Single click/impact sound on number landing.
- Optional: one sound effect on truth line reveal.
- Content MUST work on mute — sound is enhancement only.

### Replication Guide
Swap: the number, the context text, the truth line. Everything else stays identical. 5 minutes per new execution once template is built.

### Hook Archetypes
Primary: Hook 1 (Confronting Stat), Hook 2 (Contradictory Truth)

### Platform
Primary: TikTok (15-25s sweet spot for completion). Secondary: IG Reel.

### Remotion Composition Pattern
```
Scenes: 4 (Hook Number → Context → Reframe → Brand Close)
Duration: 360-540 frames at 30fps (12-18s)
TransitionSeries with hard cuts (sceneFade: 3 frames)
Components: AnimatedCounter, SlideUpText, ColorShiftBackground, BrandClose
```

---

## Template 2 — LOOP NARRATIVE (20-30 seconds)

The emotional narrative format. Built for IG saves and DM shares. Highest engagement format.

### Structure
```
[0.0-3.0s]   DISRUPTION — Hook that breaks autopilot
[3.0-7.0s]   ESCALATION Beat 1 — Problem deepens
[7.0-11.0s]  ESCALATION Beat 2 — Problem feels universal
[11.0-14.0s] ROCK BOTTOM — The "this is where most people quit" moment
[14.0-18.0s] PIVOT — Reframe. "But here's what actually works."
[18.0-24.0s] RESOLUTION — The new world. The data that proves it.
[24.0-28.0s] ENTROPY CREDIT — Brand close with entropylifestyle.com
```

### Motion Notes
- Each beat gets a new screen/scene
- Problem progression: hard cuts between beats (fast, kinetic)
- The PIVOT: one smooth morph/crossfade transition (the only slow moment — signals something important changing)
- Resolution: faster pacing than problem section, energized feel
- Secondary animation during holds (subtle bar growing, dot pulsing, line drawing)

### Color Progression
- Disruption → Rock Bottom: Desaturated palette. Off-white with graphite. Muted.
- Pivot: Introduce soft amber accent. Energy shift.
- Resolution: Full brand palette. Sage green for positive metrics. Soft amber highlights.
- Brand close: Standard (off-white canvas, deep teal wordmark)

### Sound Design
- Begin with low-energy ambient. Music shifts energy at the PIVOT.
- This audio shift is the most important sound cue in the entire system.

### Replication Guide
Change the narrative beats while keeping the 6-act structure. The Loop, the Timeline, the Before/After hooks all use this template.

### Hook Archetypes
Primary: Hook 3 (Visual Timeline), Hook 6 (Before/After), Hook 7 (Loop Closer)

### Platform
Primary: Instagram Reels (25-45s sweet spot). Secondary: TikTok.

### Remotion Composition Pattern
```
Scenes: 6-8 (Disruption → Escalation x2-3 → Rock Bottom → Pivot → Resolution → Brand Close)
Duration: 600-900 frames at 30fps (20-30s)
TransitionSeries: hard cuts for problem, crossfade at pivot only
Components: KineticText, ProgressiveDesaturation, PivotTransition, DataReveal, BrandClose
```

---

## Template 3 — DATA JOURNEY (30-45 seconds)

The evidence-based narrative. Real user data animated as a story arc. Most powerful for credibility and conversion.

### Structure
```
[0.0-3.0s]   HOOK — End-state metric shown first (big number, "Here's how they got there")
[3.0-6.0s]   REWIND — Timeline scrolls back to Week 1
[6.0-10.0s]  Week 1-2 data panel slides in from right (early struggle, low metrics)
[10.0-15.0s] Week 3-4 data panel (first signs of improvement)
[15.0-20.0s] KEY STRUGGLE MOMENT — annotated (where most people quit)
[20.0-27.0s] Week 5-8 data (breakthrough, metrics climbing)
[27.0-33.0s] End state revealed again with full context (now it means something)
[33.0-38.0s] Key insight card + Entropy credit
[38.0-42.0s] Brand close with entropylifestyle.com
```

### Motion Notes
- ALL data uses animated counters and growing bars — never reveal data statically
- Each week's stats appear in a panel that slides in from the right (creates "turning pages" feel)
- Numbers: always monospaced (JetBrains Mono), count-up animation
- Charts: line charts draw left-to-right, bars grow from baseline, radials sweep-fill
- Annotations spring in with callout arrows
- Wearable device icons add credibility (heart rate, sleep, steps icons)

### Data Requirements
Minimum: 2 data points per week across 4+ weeks.
Best: HRV, sleep score, habit completion rate, recovery score.

### Color
- Early weeks: Muted palette (fogGrey backgrounds, graphite text)
- Improvement weeks: Gradual warm-up (soft amber accents appear)
- End state: Full brand palette with sage green for success metrics

### Replication Guide
Requires real user data. Build a pipeline: every beta user who consents to sharing becomes one execution. Swap the data, keep the structure.

### Hook Archetypes
Primary: Hook 5 (Data Story)

### Platform
Primary: Instagram Reels (30-45s). Secondary: TikTok (trim to 25s version).

### Remotion Composition Pattern
```
Scenes: 8-10 (Hook → Rewind → Week panels x4+ → Struggle annotation → End state → Insight → Brand Close)
Duration: 900-1350 frames at 30fps (30-45s)
TransitionSeries: slide-right panels for weeks, hard cuts for insights
Components: AnimatedCounter, DataPanel, LineChartDraw, BarGrow, AnnotationCallout, BrandClose
Props: weeklyData array, metrics config, user name (optional)
```

---

## Template 4 — SCIENCE EXPLAINER (15-25 seconds)

The authority-building format. One concept, one mechanism, one application. Built for TikTok SEO and IG saves.

### Structure
```
[0.0-3.0s]   Concept name — large, soft amber accent, kinetic entrance
[3.0-6.0s]   Definition — 1 sentence, medium text, white/off-white
[6.0-13.0s]  THE MECHANISM — visual diagram animation (the creative heart)
[13.0-18.0s] The data point or study citation
[18.0-22.0s] Entropy application — 1 line, connecting concept to product
[22.0-25.0s] Brand close
```

### Motion Notes
- Concept name: largest type in the video, enters with scale or slide
- The diagram animation is the creative centerpiece — can be simple (arrow, loop, scale tipping, cycle diagram) but must make the abstract visible
- Study citation: appears small but legibly — credibility without interrupting flow
- Application line: enters with soft amber accent to tie back to Entropy

### Typography
- Concept name: Display font, largest size (hookNumber scale)
- Definition: Body font, medium weight, off-white or graphite
- Data/study: Data font (monospaced), with source in caption-size below
- Application: Body font, medium, soft amber accent

### Color
- Concept introduction: Deep teal background, off-white text
- Mechanism diagram: Off-white canvas, deep teal/graphite elements with amber accents
- Application: Soft amber text highlight

### Replication Guide
Each behavioral science concept = one execution. Library of concepts:
- Habit loops, Social facilitation, Implementation intentions, Loss aversion
- Commitment devices, Social norms, Identity-based behavior, Variable reward schedules
- Zeigarnik Effect, Fresh start effect, Planning fallacy, Temptation bundling

### Hook Archetypes
Primary: Hook 2 (Contradictory Truth), Hook 4 (You Call-Out)

### Platform
Primary: TikTok (15-25s). Secondary: IG Reel.

### Remotion Composition Pattern
```
Scenes: 5 (Concept → Definition → Mechanism Diagram → Data Point → Application + Brand Close)
Duration: 450-750 frames at 30fps (15-25s)
TransitionSeries: hard cuts
Components: ConceptTitle, DiagramAnimation (custom per concept), StudyCitation, BrandClose
Props: conceptName, definition, mechanism (custom), studyText, applicationLine
```

---

## Template 5 — THE COMPARISON (20-30 seconds)

The positioning format. Places Entropy's approach against the status quo. Built for comments and DM shares.

### Structure
```
[0.0-3.0s]   Comparison frame established (two columns or split screen)
[3.0-8.0s]   Problem scenario plays out on BOTH sides simultaneously
[8.0-13.0s]  DIVERGENCE POINT — where the approaches split
[13.0-20.0s] Outcome contrast — left side fails, right side succeeds
[20.0-25.0s] Entropy side result with key metric
[25.0-30.0s] Brand close
```

### Motion Notes
- Left side (status quo): greyed/desaturated palette, slower animation timing
- Right side (Entropy): full colour, faster, more energetic animation
- The visual language alone communicates the argument before a word is read
- Divergence point: sharp visual split (line draw, color change, or panel separation)
- Both sides animate simultaneously — this is the one template where concurrent animation is intentional (because comparison requires it)

### Color
- Left column: Desaturated (fogGrey, muted graphite, no accents)
- Right column: Full brand palette (off-white canvas, deep teal headers, amber accents, sage green metrics)
- Divergence: Left stays muted, right brightens

### Tone
Never mock the competitor approach. Show, don't tell. Factual, not mean.
"This is what happens" not "this is why that sucks."

### Caption Engineering
Always end the caption with a question that invites pushback. Comment volume drives distribution.

### Replication Guide
Each comparison is a new scenario:
- Wearable tracking alone vs with Entropy
- Gym friend accountability vs systematic accountability
- Motivation vs commitment devices
- Solo habit tracking vs community accountability
- Generic wellness app vs energy-first approach

### Hook Archetypes
Primary: Hook 6 (Before/After), Hook 2 (Contradictory Truth)

### Platform
Primary: Instagram Reels. Secondary: TikTok.

### Remotion Composition Pattern
```
Scenes: 5 (Setup → Problem → Divergence → Outcome Contrast → Brand Close)
Duration: 600-900 frames at 30fps (20-30s)
Layout: SplitScreen component with independent left/right animation
Components: SplitScreen, DesaturatedPanel, FullColorPanel, DivergenceLine, MetricCompare, BrandClose
Props: leftScenario, rightScenario, divergenceText, leftOutcome, rightOutcome
```

---

## Template Selection Matrix

| Content Goal | Template | Duration | Primary Platform |
|---|---|---|---|
| Quick stat punch | Stat Bomb | 12-18s | TikTok |
| Emotional narrative | Loop Narrative | 20-30s | IG Reels |
| Evidence/credibility | Data Journey | 30-45s | IG Reels |
| Authority/education | Science Explainer | 15-25s | TikTok |
| Positioning/contrast | Comparison | 20-30s | IG Reels |

## Hook × Template Compatibility

| Hook | Stat Bomb | Loop Narrative | Data Journey | Science Explainer | Comparison |
|---|---|---|---|---|---|
| 1. Confronting Stat | **Primary** | - | Secondary | - | - |
| 2. Contradictory Truth | **Primary** | - | - | **Primary** | **Primary** |
| 3. Visual Timeline | - | **Primary** | - | - | - |
| 4. You Call-Out | Secondary | - | - | **Primary** | - |
| 5. Data Story | - | - | **Primary** | - | - |
| 6. Before/After | - | **Primary** | - | - | **Primary** |
| 7. Loop Closer | - | **Primary** | - | - | - |
