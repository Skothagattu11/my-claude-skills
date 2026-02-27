# Motion Graphics Design Fundamentals

## The 12 Principles of Animation (Applied to Motion Graphics)

### 1. Squash and Stretch
Creates illusion of mass, weight, gravity. Subtle squash/stretch adds weight to flat graphics. Objects compress on impact, elongate in motion.

### 2. Anticipation
Preparatory movement before main action. A button pulls back before expanding. Primes viewer for what comes next.

### 3. Staging
Controls visual hierarchy so the viewer's eye goes to exactly the right element at the right time. Borrowed from theater.

### 4. Straight Ahead vs Pose to Pose
Pose-to-pose dominates motion graphics: define keyframes, software interpolates between them.

### 5. Follow-Through and Overlapping Action
When an object stops, parts don't stop simultaneously. Stagger animated parameters so they don't all end at the same time.

### 6. Slow In and Slow Out (Easing)
Objects need time to accelerate/decelerate. Linear = mechanical. Easing = natural.
- **Ease-out**: Elements enter at full velocity, slowly decelerate (entering screen)
- **Ease-in**: Elements slowly accelerate, exit quickly (leaving screen)
- **Ease-in-out**: Accelerate then decelerate (on-screen transitions)

### 7. Arcs
Natural movements follow curves, not straight lines. Adds fluidity and realism.

### 8. Secondary Action
Supporting movements that reinforce primary action without stealing attention. Card flips, shadow shifts underneath.

### 9. Timing
Backbone of motion design. Quick = urgency. Slow = gravity/calm. Duration range: 200-500ms for most UI/motion graphics.

### 10. Exaggeration
Push beyond literal realism for expression. Overshooting position before settling. 10-20% overshoot amplitude.

### 11. Solid Drawing
Volume and weight in 3D space. Shadow, perspective, dimensional animation give flat elements depth.

### 12. Appeal
Overall visual attractiveness. Design quality + color harmony + "feel."

---

## Easing Curves Reference

| Curve | Use Case | Bezier |
|-------|----------|--------|
| Ease-out (deceleration) | Elements entering screen | (0, 0, 0.2, 1) |
| Ease-in (acceleration) | Elements leaving screen | (0.4, 0, 1, 1) |
| Ease-in-out (standard) | On-screen transitions | (0.4, 0, 0.2, 1) |
| Sharp ease-out | Micro-interactions, snaps | (0, 0, 0, 1) |
| Custom overshoot | Playful entrances | (0.34, 1.56, 0.64, 1) |

---

## Duration Tokens

| Token | Duration | Use Case |
|-------|----------|----------|
| T1 (Extra Fast) | ~100ms | Micro-interactions, state changes |
| T2 (Fast) | ~200ms | Hover effects, small UI elements |
| T3 (Normal) | ~300ms | Standard transitions |
| T4 (Slow) | ~500ms | Larger element transitions |
| T5 (Extra Slow) | ~800ms | Full-screen transitions, dramatic reveals |

---

## Text Animation Techniques

### Slide-Ins
- Ease-out for entering, ease-in for exiting
- Direction communicates meaning: L-to-R = progress, bottom-to-top = uplift
- Duration: 200-500ms
- Slight overshoot (2-5%) for natural settle

### Typewriter Effect
- Characters appear one at a time with blinking cursor
- Uniform timing = mechanical; varied timing = human
- Monospaced fonts essential

### Glitch Text
- Duplicate layers with positional offsets, RGB channel separation
- Quick, intermittent (not sustained)
- Tech/gaming/high-energy content

### 3D Text Rotations
- Enable 3D layer mode, add Camera layer
- Cinema 4D renderer for extrusion/bevel
- Premium, dimensional feel

### Text Masking
- Text as Track Matte window for video/animation
- Visually striking for hero text

### Word-by-Word
- Each word animates individually, stagger 100-200ms
- Forces reading at your pace
- Most effective with bold, impactful copy

### Character-by-Character
- Individual letter animation, stagger 20-50ms
- Fluid, organic entrance

### Bounce/Overshoot
- 10-20% overshoot amplitude, 1-2 oscillations
- Playful, energetic feel

---

## Infographic Animation Guidelines

### Chart Types
- **Line Charts**: Stroke reveal L-to-R, highlight data points
- **Bar Charts**: Grow from baseline with ease-out, 50-100ms stagger between bars
- **Pie/Donut**: Segments rotate/expand sequentially
- **Counters**: Count up 1-3s, monospaced fonts mandatory

### Timing Rules
- 200-500ms per element animation
- Stagger entry: 50-100ms offset between elements
- Ease-out curves for data entering
- Brief pauses between sections for absorption

### Process Flows
- Sequential steps, one at a time
- Connecting lines/arrows draw between them
- Color changes indicate progression
- Consistent timing between steps
- L-to-R or top-to-bottom directional flow

---

## Social Media Specifications

### Platform Specs
| Platform | Aspect | Resolution | Sweet Spot Duration |
|----------|--------|-----------|-------------------|
| Instagram Reels | 9:16 | 1080x1920 | 15-30s viral, 60-90s engagement |
| TikTok | 9:16 | 1080x1920 | 60-180s substantive |
| Instagram Feed | 4:5 | 1080x1350 | Carousel (static) |
| YouTube Shorts | 9:16 | 1080x1920 | Under 60s |
| Stories | 9:16 | 1080x1920 | 15s per frame |

### First 3 Seconds (Hook Techniques)
- 65% of viewers who watch 3s will watch 10s+
- 45% will continue 30s+
- Reels with >60% 3s hold rate outperform weak holds by 5-10x

**Hook Types:**
1. Pattern Interrupt - unexpected visual element
2. Dynamic Camera Action - fast zoom, quick pan
3. Kinetic Typography - large bold animated text
4. Big Type + Motion - oversized text with dynamic animation
5. Curiosity Gap - partial result, incomplete reveal
6. Before/After Tease - flash end result, cut to beginning
7. Speed Ramps - start fast then suddenly slow (or vice versa)

### Export Settings
- Frame Rate: 30fps min, 60fps for premium
- Codec: H.264 for compatibility
- Bitrate: 3,500-5,000 kbps
- Format: MP4 for video, Lottie JSON for web/app

---

## Advanced Techniques

### Parallax
- 3-5 depth layers, background slowest, foreground fastest
- Creates dimensionality in flat designs
- Looping parallax: loop frames must divide into total frame count

### Morph Transitions
- Shape morph, container morph, text morph, liquid morph, particle morph
- Best for storytelling, product showcases, brand reveals

### Seamless Loops
- Last frame flows naturally into first frame
- Match keyframes between first and last
- Types: Perfect (invisible repetition) and Cyclical (natural variations)

### Camera in 2D
- Pan (horizontal), Tilt (vertical), Zoom (scale), Dolly Zoom (simultaneous zoom + counter-move)
- Truck (lateral), Parallax Camera (through depth layers)

### Stagger/Cascade
- Delay each element by ≤20ms
- Apply easing to both individual animations AND delay distribution
- Perfect for lists, grids, cards, data points

### Overshoot and Settle
- Elements move beyond final position then spring back
- 1-3 oscillations, decaying amplitude
- Creates energy and liveliness

---

## 2025-2026 Trends

1. **Kinetic Typography Explosion** - Text as primary storytelling device
2. **3D/2D Hybrid** - 3D assets in 2D compositions
3. **Deep Glow Aesthetic** - Neon gradients, retrofuturistic geometry
4. **Minimalist Maximalism** - Clean layouts + vibrant colors + dramatic transitions
5. **Liquid Morphing** - Organic flowing transitions
6. **Gradient Animations** - Mesh gradients, aurora-style flows
7. **Particle Effects** - Dissolve/reform/flow systems
8. **Micro-Interactions** - Subtle hover effects, animated icons
9. **Short-Form Optimization** - Under 15s micro-content
10. **Sound-Synced Motion** - Movement synced to custom sound design
11. **Imperfect by Design** - Handmade textures, slight irregularities, organic imperfections
12. **AI + Craft Hybrid** - AI generation with human refinement
