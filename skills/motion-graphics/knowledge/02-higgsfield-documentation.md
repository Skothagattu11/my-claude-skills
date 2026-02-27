# Higgsfield AI - Complete Documentation

## Overview

Higgsfield AI is a generative AI video and image platform aggregating 15+ AI models (Sora 2, Kling 2.6, Veo 3.1, WAN 2.5/2.6, Minimax Hailuo) into a single creative environment. Founded 2023 in Kazakhstan by Yerzat Dulat, CEO Alex Mashrabov. $200M ARR, $1.3B valuation (Jan 2026).

---

## Complete Tool Inventory

| Tool | Purpose |
|------|---------|
| **Text-to-Video** | Generate video from text prompts |
| **Image-to-Video** | Animate static images |
| **Draw-to-Video** | Animate hand-drawn sketches |
| **Cinema Studio** | Virtual production with camera lens/sensor simulation |
| **Camera Controls** | 70+ cinematic camera presets |
| **Higgsfield Mix** | Combine 2+ camera movements in one shot |
| **Effects Mix** | Combine multiple VFX effects in one shot |
| **Mixed Media** | Creator-first editing workflow |
| **Popcorn** | AI storyboard engine with scene continuity |
| **Recast** | One-click character swap preserving motion/lighting |
| **Soul** | Photorealistic image generation model |
| **Soul ID** | Character identity preservation (digital double) |
| **Nano Banana Pro** | Google Gemini 3 Pro image gen (4K, text rendering) |
| **Flux Kontext** | Image generation model |
| **GPT Image** | OpenAI image generation |
| **Topaz** | Image enhancement |
| **Lipsync Studio** | Multi-engine lip-sync |
| **UGC Builder** | AI-generated user-style video ads |
| **Ads 2.0** | Product placement and commercial video generation |
| **Vibe Motion** | Claude-powered motion graphics from text |
| **Higgsfield Assist** | GPT-5 creative copilot |
| **Video Upscale** | Up to 8K resolution |
| **Diffuse App** | iOS/Android mobile app |
| **Cloud API** | Programmatic access via Python SDK |

---

## Vibe Motion (Key Feature for Motion Graphics)

### What It Is
No-code motion graphics creation powered by Anthropic's Claude AI. Generates production-ready, editable motion graphics and underlying animation code from plain-English prompts.

### How It Works
1. Claude's reasoning engine interprets natural language intent
2. Output is structured, editable (not flat video) - individual elements tweakable
3. Chat-based interface for describing motion ideas
4. Upload assets (images, videos, logos, PDFs) - builds around YOUR files
5. Motion presets library for enter/exit/transition behaviors
6. Real-time adjustments to text, colors, layout, speed
7. Production-ready export

### Best Prompts for Vibe Motion
- Describe visual content AND behavior: "bar chart animating quarterly revenue with bars growing sequentially left-to-right with slight bounce"
- Include brand elements: "Use logo in top-left, brand blue (#4E8FE8) as primary"
- Specify timing/hierarchy: "Title fades in first, then data points appear one-by-one with 0.5s delay"

### Ideal Output Types
- Animated infographics and data visualizations
- Brand presentations
- Social media motion graphics
- Product showcases with animated elements
- Educational content with animated diagrams
- Protocol breakdown animations

---

## Nano Banana Pro (Image Generation)

### Capabilities
- Resolution: Up to native 2K, upscalable to 4K
- **Best-in-class text rendering** - legible text directly in images
- Multi-image context: Upload up to 14 reference images
- Identity preservation across up to 5 subjects
- Physics control: lighting, camera, focus, color grading, composition
- Multilingual text generation
- SynthID watermarks embedded

### Prompt Structure
```
[Subject] + [Composition] + [Camera/Perspective] + [Action] + [Style/Mood] + [Text if needed]
```

### Example Prompts
```
Product: "A sleek wireless earbud case in matte white on light grey concrete.
Soft directional lighting upper left. Shallow DOF. Minimalist studio.
Text on case reads 'ECHO PRO' in clean sans-serif."

Portrait: "Confident woman 30s in tailored navy blazer, modern glass office.
Golden hour through floor-to-ceiling windows. 85mm, f/1.8, shallow DOF."

Data card: "Dark charcoal card with neon blue border glow showing '94%'
in large JetBrains Mono font, subtitle 'Recovery Score' below in Inter.
Glassmorphism effect, subtle grid background."
```

### Creating Source Images for Video Pipeline
- Produces sharp, well-lit images ideal for image-to-video
- Character consistency via Soul ID for multiple angles
- Text rendered in image carries through to video
- Physics-accurate lighting ensures smooth video transitions

---

## Image-to-Video

### Workflow
1. Upload reference image (aspect ratio matches output)
2. Select model (Sora 2, WAN 2.5, Kling 2.5 Turbo, Veo 3.1, etc.)
3. Add prompt describing desired motion, camera, atmosphere
4. Select camera presets (optional, 50+ available)
5. Generate

### Duration by Model
| Model | Max Duration | Best For |
|-------|-------------|----------|
| WAN 2.5 | 10s | Rapid iteration |
| WAN 2.6 | 15s | Longest duration, cinematic continuity |
| Veo 3.1 | 4/6/8s | Scale, lighting, dialogue |
| Kling 2.5 Turbo | 5/10s | Human expression, lip-sync |
| Sora 2 | 10s | Maximum photorealism |

### Tips
- Clean source images: sharp, well-lit, clear subject, simple background
- Don't crop hands, face, or product labels
- Test at 480p/720p first, upscale best outputs
- Pair one camera move + one subject action

---

## Text-to-Video

### Key Features
- Native audio generation (dialogue, ambience, music) - no post-production
- Automatic lip-sync
- Character consistency across frames
- Multi-aspect ratio: 16:9, 9:16, 1:1

### Quality Tiers
- Base: 720p @ 30fps, 3-5s, MP4 (~20-25MB)
- Enhanced: 1080p, up to 10-15s
- Upscaled: Up to 4K/8K via Video Upscale

---

## Camera Presets (70+)

### Named Presets
Bullet Time, Aerial Pullback, Arc Left/Right, Car Chasing, Car Grip, Crane Over The Head, Crash Zoom In/Out, Double Dolly, Eating Zoom, Fisheye, Flying Cam Transition, Focus Change, Glam, Head Tracking, Hero Cam, Incline, Lazy Susan, Low Shutter, Mouth In, Object POV, Overhead, Road Rush, Robo Arm, Super Dolly In/Out, Through Object In/Out, Timelapse Glam, Timelapse Human, Timelapse Landscape, Wiggle, YoYo Zoom, 360 Rotation, FPV Drone, Snorricam, Whip Pan, Tracking Shot, and more.

### Mix Feature
Combine 2+ camera moves in one shot: arc + zoom, dolly + punch. Create movements impossible with real cameras.

---

## Prompt Engineering for Higgsfield

### Core Structure
```
[SHOT FRAMING] + [SUBJECT] + [ACTION/MOVEMENT] + [CAMERA MOVE] + [LIGHTING/MOOD] + [STYLE]
```

### Camera Movement Vocabulary
| Term | Effect |
|------|--------|
| dolly in/out | Camera closer/farther |
| orbit around subject | Circles without changing distance |
| handheld shake | Natural tension |
| FPV / drone view | Floating/fast directional |
| crash zoom | Rapid zoom-in for drama |
| crane shot | Vertical movement |
| whip pan | Fast horizontal swing |
| bullet time | Frozen-moment multi-angle |
| Snorricam | Camera on subject, environment moves |
| tracking shot | Camera follows laterally |

### Rules
- Use active verbs: "darts through," "leaps across," "slowly rises"
- ONE camera move + ONE subject action per shot
- Use real cinematography terms, NOT "cinematic" or "dynamic"
- End with atmosphere: lighting, emotion, pacing

---

## Pricing (Early 2026)

| Plan | Monthly | Credits/Month |
|------|---------|--------------|
| Free | $0 | Limited daily |
| Basic | $9 | 150 |
| Pro | $29 | 600 |
| Ultimate | $49 | 1,200 |
| Creator | $249 | 6,000 |
| Enterprise | Custom | Custom |

### Credit Costs
| Action | ~Credits |
|--------|---------|
| Basic text-to-video | 15-20 |
| Image-to-video | 15-25 |
| Cinema Studio + camera | 30-50 |
| Cinema Studio + Upscale | 80+ |

---

## API Access

- **Endpoint**: cloud.higgsfield.ai
- **API Keys**: cloud.higgsfield.ai/api-keys
- **Python SDK**: github.com/higgsfield-ai/higgsfield-client
- Sync and async support
- Methods: `generate_video()`, `subscribe()`, `subscribe_async()`
- Parameters: `image_url`, `motion_id`, `prompt`, `quality`, `custom_reference_id`, `custom_reference_strength`
- **MCP Server**: github.com/geopopos/higgsfield_ai_mcp
- **Make.com integration** available

---

## Model Selection Guide

| Goal | Model |
|------|-------|
| Cinematic continuity | WAN 2.6 |
| Fast iteration | Minimax Hailuo 02 |
| Strong lip-sync | Kling 2.6 |
| Max photorealism | Sora 2 |
| Scale and lighting | Veo 3.1 |
| Longest duration (15s) | WAN 2.6 |
| Native audio + video | WAN 2.5/2.6, Veo 3.1 |
| Motion graphics | Vibe Motion |

---

## Cost Optimization
- Start with WAN 2.5 (often unlimited on paid plans) for rapid iteration
- Use 720p for testing; upscale only best outputs
- Generate several takes at lower cost before selecting best for upscale
- Credit packs expire in 90 days
- Watch for promotional discounts (up to 85% off observed)

---

## Known Limitations
- Complex actions can produce artifacts; simplify movement
- Hand/facial expression artifacts in fast movements
- Character drift possible despite Soul ID; may need multiple takes
- Generation speed varies by model and server load
- Some prompts may be censored; rephrase if blocked
