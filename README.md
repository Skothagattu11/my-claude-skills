# Claude Skills Repository

A collection of custom skills and templates for Claude Code to enhance AI-assisted development workflows.

## Skills

### GEO/AIO Optimization
**Location:** `skills/geo-aio/`

Comprehensive skill for Generative Engine Optimization (GEO) and AI Optimization (AIO) to make websites visible and citable by AI systems like ChatGPT, Claude, Perplexity, and Google AI.

**Files:**
- `geo-aio-optimization.md` - Complete skill with principles, tactics, and checklists
- `templates/geo-implementation-templates.md` - Ready-to-use templates

**Use Cases:**
- Optimizing websites for AI search visibility
- Adding schema markup for LLM understanding
- Configuring robots.txt and llms.txt
- Implementing E-E-A-T signals

---

### Motion Graphics Production
**Location:** `skills/motion-graphics/`

Comprehensive skill for creating AI-generated motion graphics and video content using platforms like Higgsfield AI, Runway Gen-4, Kling, and Sora 2.

**Files:**
- `motion-graphics-production.md` - Complete skill with prompt engineering, camera movements, templates

**Use Cases:**
- Creating AI-generated video content for social media (Instagram Reels, TikTok, LinkedIn)
- Developing brand motion identity and animation systems
- Writing prompts for AI video generation platforms
- Producing data visualizations and protocol animations
- Creating before/after transformation videos
- Building consistent visual content for lifestyle/health/tech brands

**Covers:**
- Universal prompt structure (Subject + Action + Context + Style)
- Camera movement library (30+ movement types with prompt phrasing)
- Motion quality descriptors (smooth, dynamic, temporal)
- Style and lighting libraries
- Platform-specific guidelines (durations, aspect ratios, negative prompts)
- Mobile-first production rules (text sizing, transition timing)
- Content templates (Quote cards, Data stories, Transformations)

## How to Use

### Option 1: Copy to Claude Commands (Local)
```bash
cp skills/geo-aio/geo-aio-optimization.md ~/.claude/commands/
```

### Option 2: Reference in Conversation
Simply tell Claude: "Use the GEO/AIO skill from my skills repository" and paste the relevant content.

### Option 3: Include in Project CLAUDE.md
Add to your project's CLAUDE.md:
```markdown
## Skills Reference
- GEO/AIO Optimization: See /path/to/claude-skills/skills/geo-aio/
```

## Repository Structure

```
claude-skills/
├── README.md
├── skills/
│   ├── geo-aio/
│   │   └── geo-aio-optimization.md
│   ├── motion-graphics/
│   │   └── motion-graphics-production.md
│   └── [future-skills]/
├── templates/
│   └── geo-implementation-templates.md
└── examples/
    └── [implementation-examples]/
```

## Adding New Skills

1. Create a folder in `skills/` with your skill name
2. Add a markdown file following the skill format:
   ```markdown
   # Skill Name

   ## Overview
   [What this skill does]

   ## When to Use
   [Use cases]

   ## Instructions
   [Detailed guidance]

   ## Examples
   [Practical examples]
   ```
3. Add any templates to `templates/`
4. Update this README

## Skills Index

| Skill | Description | Status |
|-------|-------------|--------|
| GEO/AIO Optimization | AI search visibility optimization | ✅ Ready |
| Motion Graphics Production | AI video generation & prompt engineering | ✅ Ready |

---

*Created: February 2026*
