# AI Video Prompt Engineering - Complete Guide

## Universal Prompt Structure

```
[Shot type/Camera] + [Subject] + [Action] + [Setting] + [Lighting/Mood] + [Style/Quality] + [Camera Movement]
```

### Four Pillars
1. **Subject** - Primary focus (what is in frame)
2. **Action** - What the subject does (the motion)
3. **Context** - Environment (where it happens)
4. **Style** - Visual aesthetic (lighting, color, mood, quality)

### Key Principles
- Be specific, not conceptual. "Volumetric rim light in cyan" not "amazing lighting"
- Describe, do not command. "Camera slowly pushes in on..." not "Make the camera zoom in"
- Start simple, iterate. Add one element at a time
- 50-100 words sweet spot (Sora 2 recommendation)
- Separate concerns: image rules, identity rules, motion rules in distinct blocks
- Pair ONE camera move with ONE subject action

---

## Camera Movement Descriptors

### Basic Movements
| Movement | Prompt Phrasing |
|----------|----------------|
| Pan L/R | "Camera pans slowly to the right, revealing..." |
| Tilt Up/Down | "Camera tilts up from ground to reveal sky" |
| Dolly In/Out | "Slow dolly in on the subject's face" |
| Truck L/R | "Camera trucks right alongside the walking figure" |
| Zoom In/Out | "Smooth optical zoom into the subject's eyes" |
| Static | "Static shot, fixed camera, locked off frame" |

### Advanced Movements
| Movement | Prompt Phrasing |
|----------|----------------|
| Orbit/Arc | "Camera orbits 180 degrees around the subject" |
| 360 Orbit | "Fast 360 orbit around the figure, eye level" |
| Crane Up/Down | "Crane up shot soaring above the cityscape" |
| Tracking | "Side tracking shot following the runner" |
| Leading | "Leading shot, camera moves backward as subject walks forward" |
| Dolly Zoom | "Dolly zoom creating vertigo-like depth compression" |
| Dutch Angle | "Dutch angle tilted 15 degrees, creating unease" |
| Whip Pan | "Whip pan left to right with directional motion blur" |
| Handheld | "Handheld camera, documentary-style, subtle shake" |

### Cinematic Specialty
| Movement | Prompt Phrasing |
|----------|----------------|
| FPV Drone | "FPV drone dive down the side of the building" |
| Drone Fly Over | "Aerial drone fly-over across the mountain range" |
| Epic Drone Reveal | "Camera rises and tilts down to show full landscape" |
| Bullet Time | "Bullet time spin around the frozen subject" |
| Snorricam | "Face stays centered as background whirls" |
| Crash Zoom | "Crash zoom in on the subject's eye" |
| Worm's Eye | "Ultra-low angle tracking along ground, looking up" |
| Rack Focus | "Rack focus from blurred foreground to sharp background" |
| Fly Through | "Fly-through shot entering the corridor" |
| Hyperlapse | "Hyperlapse through city street at golden hour" |

### Pro Tips
- Use **active kinetic verbs**: glides, drifts, swirls, rushes, sweeps, crawls
- Specify speed: "slowly," "rapidly," "at moderate pace"
- Cinematic phrases like "35mm, slow push, golden hour, wet asphalt" outperform simpler descriptions by ~40%

---

## Motion Quality Descriptors

### Smooth/Gentle
```
smooth, fluid, gentle, graceful, effortless, floating, drifting,
gliding, languid, silky, flowing, undulating, elegant, weightless
```

### Dynamic/Energetic
```
dynamic, kinetic, explosive, rapid, vigorous, punchy, aggressive,
propulsive, accelerating, bursting, erupting, surging, snapping
```

### Temporal Modifiers
| Modifier | Example |
|----------|---------|
| Slow motion | "Slow-motion particles dispersing outward" |
| Time-lapse | "Time-lapse of clouds forming and dissolving" |
| Reverse | "Water droplets rising in reverse slow-motion" |
| Freeze frame | "Action freezes mid-frame before resuming" |
| Seamless loop | "Seamless looping animation, first and last frames identical" |

---

## Style Descriptors

| Style | Key Descriptors |
|-------|----------------|
| Minimalist | "Clean, sparse, white space, uncluttered, geometric simplicity" |
| Neon/Cyberpunk | "Neon-lit, electric glow, high contrast, saturated pink and cyan" |
| Glassmorphism | "Frosted glass, translucent, backdrop blur, glass refraction" |
| Liquid/Fluid | "Flowing organic shapes, soft gradients, morphing, viscous" |
| Geometric | "Mathematical precision, sharp edges, polygonal, tessellated" |
| Editorial | "High contrast, controlled lighting, film grain, authoritative" |
| Futuristic | "Holographic, hologram overlays, chrome, quantum particles" |

### Dark Mode Telemetry (HolisticOS Style)
```
"Dark mode telemetry dashboard, deep charcoal #121212 background,
neon green #00FF94 accent highlights, frosted glass panels with
backdrop blur, monospaced data readouts, volumetric ambient glow,
subtle grid overlay, floating UI elements with soft drop shadows"
```

---

## Lighting Descriptors

### Studio Lighting
| Type | Prompt Phrase |
|------|--------------|
| Rim Light | "Crisp rim lighting separating subject from background" |
| High-Key | "Bright and airy, minimal shadows" |
| Low-Key | "Deep shadows, dramatic contrast" |
| Chiaroscuro | "Bold light-shadow interplay" |

### Atmospheric
| Type | Prompt Phrase |
|------|--------------|
| Volumetric | "Volumetric light rays cutting through atmospheric haze" |
| Neon Glow | "Neon glow in electric blue casting colored shadows" |
| Golden Hour | "Warm orange hues, long shadows" |
| Bioluminescent | "Organic glowing emanating from surfaces" |

---

## Color Control

### Techniques
1. **Named colors**: "Electric blue and deep charcoal palette"
2. **Light context**: "Warm peach highlights fading into cool teal shadows"
3. **Known palettes**: "Synthwave palette - neon pink, cyan, purple on dark"
4. **Hex codes**: "Palette centered on #00FF94 neon green and #121212 charcoal"

### Color Anchoring (5-color system)
```
"Color palette: Deep obsidian black (#121212) background,
electric blue (#4E8FE8) primary accents,
soft gray (#B0B0B0) secondary text,
neon green (#00FF94) status indicators,
amber (#FFD600) warning highlights"
```

---

## Composition & Framing

### Shot Types
| Shot | Prompt Phrase |
|------|--------------|
| Extreme Wide | "Vast landscape with figure barely visible" |
| Wide | "Full room and its occupant" |
| Medium | "Waist up" |
| Close-Up | "Face fills frame, shallow DOF" |
| Extreme Close-Up | "Detail of the eye reflecting light" |
| Top-Down | "Directly overhead looking down" |
| Low Angle | "Looking up at towering structure" |

### Composition Rules
- Rule of Thirds: "Subject along right third of frame"
- Centered: "Perfectly centered and symmetrical"
- Negative Space: "Large negative space surrounding small isolated subject"
- Depth Layers: "Foreground, midground, background layers creating depth"

### Lens Specification
```
"35mm" - Standard cinematic
"85mm portrait" - Compressed background, flattering
"24mm wide angle" - Environmental, slight distortion
"Macro lens" - Product close-ups
"Anamorphic lens" - Cinematic widescreen, horizontal flares
```

---

## Prompt Templates

### Template 1: Abstract Motion Background
```
Abstract fluid simulation of [elements] mixing and swirling,
vibrant [color palette] against [background color],
smooth flowing movements, [aesthetic] aesthetic,
hypnotic patterns forming and dissolving,
seamless looping motion, 4K quality.
```

### Template 2: Product/UI Reveal
```
[Camera movement] of [subject] [action] in [environment].
[Lighting description]. [Surface/material details].
[Color/style treatment]. [Quality modifiers].
```

### Template 3: Data Visualization
```
Cinematic [camera type] shot of [visualization type]
processing [data] in real-time. [Aesthetic reference].
Camera [movement] with [DOF]. [Color palette]. [Lighting].
```

### Template 4: Glassmorphism UI
```
[Camera] of translucent glass panels floating in [environment].
Frosted glass effect with [blur level] backdrop blur.
[Content through glass]. [Edge treatment]. [Background].
[Lighting from glass]. [Color palette]. Premium, modern, [quality].
```

### Template 5: Particle Effect
```
Geometric [shape] [action] from [origin] in slow motion,
[material] catching light, [background] emphasizing bright [elements],
modern motion graphics, [mood] and [composition],
digital art, [theme].
```

---

## Negative Prompting

### By Platform
- **Kling, Wan 2.2**: Dedicated negative prompt field (no "no" prefix needed)
- **Runway Gen-4**: Does NOT support negative phrasing - describe only what SHOULD be present
- **Sora 2**: Describe what should happen, not what to avoid

### Organized Negative Categories
```
Technical: blurry, low resolution, pixelated, grainy, compression artifacts, watermark
Motion: morphing, warping, flickering, stuttering, jittering, frame tearing
Subject: deformed faces, extra fingers, extra limbs, distorted anatomy
Composition: cluttered background, cropped subject, oversaturated, overexposed
```

---

## Duration & Pacing

### Duration Guidelines
| Duration | Best For |
|----------|----------|
| 3-5s | Single action, product shot, transition |
| 5-8s | Scene establishment, moderate action |
| 8-10s | Complex sequences, reveals |
| 10+s | Stitch shorter clips together instead |

### Beat-Based Prompting
```
"Dashboard materializes [beat 1], data points illuminate
sequentially [beat 2], entire display pulses with energy [beat 3]"
```

### Timestamp Prompting (Runway)
```
"[0-2s] Camera holds static on dark scene.
[2-4s] Glass panel fades in from particles.
[4-7s] Camera begins slow dolly in as data animates.
[7-10s] Final pulse of light, camera holds."
```

---

## The Master Formula (Quick Reference)

```
1. SHOT TYPE + FRAMING    "Medium close-up, centered"
2. LENS / CAMERA          "50mm, shallow DOF"
3. SUBJECT + ACTION       "Glass panel materializes, data animates"
4. ENVIRONMENT            "Dark void with faint particle grid"
5. CAMERA MOVEMENT        "Slow dolly in"
6. LIGHTING               "Volumetric glow, neon blue rim light"
7. COLOR PALETTE           "Electric blue, deep charcoal, white"
8. STYLE / QUALITY        "Cinematic, 4K, modern motion graphics"
9. PACING / DURATION      "Smooth, deliberate, 6 seconds"
10. NEGATIVE              "No flickering, no morphing, no text"
```
