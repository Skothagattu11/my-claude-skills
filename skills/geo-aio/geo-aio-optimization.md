# GEO/AIO: AI Search Optimization Skill

## Overview

This skill implements **Generative Engine Optimization (GEO)** and **AI Optimization (AIO)** to make websites visible and citable by AI systems like ChatGPT, Claude, Perplexity, Google AI Overviews, and other LLM-powered search engines.

**Key Insight**: LLMs only cite 2-7 domains per response on average. Your goal is to become one of those trusted sources.

---

## Quick Reference: The 9 GEO Tactics (Princeton Research)

Based on Princeton's GEO research, these tactics improve AI visibility by 15-40%:

| Tactic | Visibility Boost | Best For |
|--------|------------------|----------|
| **Cite Sources** | +115% (for lower-ranked sites) | All content |
| **Add Statistics** | +28-40% | Law, Government, Opinion content |
| **Add Quotations** | +30-40% | People & Society, History, Explanations |
| **Technical Terms** | +28% | Technical/specialized content |
| **Fluency Optimization** | +15-30% | All content |
| **Easy-to-Understand** | +15-30% | Complex topics |
| **Authoritative Tone** | Moderate | Expert content |
| **Unique Words** | Moderate | Differentiation |
| ~~Keyword Stuffing~~ | *Negative* | *Avoid - hurts visibility* |

---

## Part 1: Technical Requirements

### 1.1 Server-Side Rendering (CRITICAL)

**AI crawlers cannot execute JavaScript.** GPTBot, ClaudeBot, and PerplexityBot see only raw HTML.

```
REQUIREMENT: All critical content must be in the initial HTML response.
```

**Solutions by priority:**
1. **Static HTML** - Best for landing pages (already implemented in index.html)
2. **Server-Side Rendering (SSR)** - Next.js, Nuxt, Remix
3. **Static Site Generation (SSG)** - Build-time rendering
4. **Prerendering** - For existing SPAs (vite-plugin-ssr, react-snap)

### 1.2 Performance Requirements

```
Target Metrics:
- Time to First Byte (TTFB): < 200ms
- First Contentful Paint (FCP): < 1.5s
- Largest Contentful Paint (LCP): < 2.5s
```

AI crawlers abandon slow pages. They operate under time constraints and won't wait.

### 1.3 robots.txt Configuration

**Allow AI crawlers:**

```robots.txt
# AI Search Crawlers (allow for visibility)
User-agent: GPTBot
Allow: /

User-agent: ChatGPT-User
Allow: /

User-agent: OAI-SearchBot
Allow: /

User-agent: ClaudeBot
Allow: /

User-agent: Claude-User
Allow: /

User-agent: Claude-SearchBot
Allow: /

User-agent: PerplexityBot
Allow: /

User-agent: Perplexity-User
Allow: /

User-agent: Google-Extended
Allow: /

User-agent: YouBot
Allow: /

User-agent: PhindBot
Allow: /
```

**Mixed policy (search yes, training no):**

```robots.txt
# Allow AI search
User-agent: PerplexityBot
Allow: /

User-agent: OAI-SearchBot
Allow: /

# Block training crawlers
User-agent: GPTBot
Disallow: /

User-agent: ClaudeBot
Disallow: /

User-agent: Google-Extended
Disallow: /
```

### 1.4 llms.txt File

Create `/llms.txt` at your domain root to guide AI systems:

```markdown
# Your Site Name

> Brief description of what your site offers and its expertise.

## Main Content

- [Homepage](/): Overview of what we do
- [About](/about): Our expertise and background
- [Core Feature](/feature): Main value proposition

## Documentation

- [How It Works](/how-it-works): Step-by-step explanation
- [FAQ](/faq): Common questions answered

## Resources

- [Blog](/blog): Latest insights and articles
- [Research](/research): Original data and studies
```

**File requirements:**
- Must be at domain root: `yoursite.com/llms.txt`
- Use Markdown format
- Serve as `text/plain`
- Keep under 10KB for best compatibility

**Note:** As of 2025, AI crawlers don't actively fetch llms.txt yet, but it's a forward-looking best practice.

---

## Part 2: Content Optimization

### 2.1 Structure for AI Extraction

```
AI-Optimized Content Structure:

1. TL;DR / Summary Block (first 100 words)
   - Direct answer to the main question
   - Key takeaway in 1-2 sentences

2. Clear Hierarchy
   - H1: One main topic per page
   - H2: Major sections (question-based when possible)
   - H3: Subsections with specific details

3. Semantic HTML
   - <article> for main content
   - <header>, <footer>, <section> for structure
   - <nav> for navigation
   - <aside> for supplementary info

4. Answer-First Architecture
   - Lead with the answer
   - Then provide context, proof, examples
   - End with related questions or next steps
```

### 2.2 Content Elements That Get Cited

**Include these in your content:**

1. **Statistics with sources**
   ```
   "According to a 2024 Princeton study, GEO tactics can improve
   AI visibility by up to 40%."
   ```

2. **Expert quotations**
   ```
   "As Dr. Jane Smith, Stanford researcher, explains: 'AI systems
   prioritize content that demonstrates clear expertise.'"
   ```

3. **Specific technical terminology**
   - Use precise industry terms
   - Define acronyms on first use

4. **Structured lists and tables**
   - Bullet points for features/benefits
   - Tables for comparisons
   - Numbered steps for processes

5. **FAQ sections**
   ```html
   <h2>Frequently Asked Questions</h2>
   <h3>What is functional biological age?</h3>
   <p>Functional biological age reflects how your body is actually
   aging based on physical performance markers...</p>
   ```

### 2.3 Conversational Keywords

**Shift from keywords to questions:**

| Traditional SEO | Conversational AI |
|-----------------|-------------------|
| "biological age test" | "How can I find out my biological age?" |
| "fitness assessment" | "What's the best way to measure my fitness?" |
| "at-home health test" | "How do I test my health without going to a doctor?" |

**Implementation:**
- Use question-based H2/H3 headings
- Include natural language variations
- Answer the question directly after the heading

---

## Part 3: Structured Data (Schema Markup)

### 3.1 Essential Schema Types

**Organization (required for brand recognition):**

```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Entropy Age",
  "url": "https://www.entropyage.com",
  "logo": "https://www.entropyage.com/logo.png",
  "description": "Functional biological age assessment through at-home tests",
  "sameAs": [
    "https://twitter.com/entropyage",
    "https://linkedin.com/company/entropyage"
  ]
}
```

**WebApplication (for tools/apps):**

```json
{
  "@context": "https://schema.org",
  "@type": "WebApplication",
  "name": "Entropy Age Assessment",
  "url": "https://www.entropyage.com",
  "description": "Free functional biological age assessment through 5 simple at-home tests",
  "applicationCategory": "HealthApplication",
  "operatingSystem": "Web",
  "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "USD"
  }
}
```

**FAQPage (high impact for AI visibility):**

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is functional biological age?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Functional biological age reflects how your body is actually aging based on physical performance markers like strength, balance, endurance, and mobility."
      }
    },
    {
      "@type": "Question",
      "name": "How long does the assessment take?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The complete assessment takes approximately 10 minutes and includes 5 simple tests you can do at home."
      }
    }
  ]
}
```

**HowTo (for step-by-step processes):**

```json
{
  "@context": "https://schema.org",
  "@type": "HowTo",
  "name": "How to Complete the Entropy Age Assessment",
  "description": "Step-by-step guide to measuring your functional biological age",
  "step": [
    {
      "@type": "HowToStep",
      "name": "Enter your details",
      "text": "Provide your age, gender, and basic health information"
    },
    {
      "@type": "HowToStep",
      "name": "Complete the sit-to-stand test",
      "text": "Measure your lower-body strength by counting chair stands in 30 seconds"
    }
  ]
}
```

### 3.2 Schema Best Practices

1. **Match schema to visible content** - Never include schema data not on the page
2. **Start with 2-3 essential types** - Organization, FAQPage, Article/WebApplication
3. **Use sameAs for entity linking** - Connect to Wikipedia, LinkedIn, Crunchbase
4. **Validate with Google's Rich Results Test**
5. **Update schema when content changes**

---

## Part 4: E-E-A-T Implementation

### 4.1 The Four Pillars

```
E-E-A-T Framework:
- Experience: You've done it (case studies, personal data)
- Expertise: You know it (credentials, specialized content)
- Authoritativeness: Others recognize it (citations, mentions)
- Trustworthiness: It's accurate and transparent (sources, updates)

TRUST is the most important factor.
```

### 4.2 Implementation Tactics

**Author Signals:**
- Add author bios with credentials
- Link to author's social profiles
- Show author's other published work
- Use Person schema for authors

**Authority Signals:**
- Include press mentions and awards
- Display certifications and partnerships
- Link to original research
- Reference peer-reviewed sources

**Trust Signals:**
- Clear disclaimers where appropriate
- Transparent methodology
- Last updated dates
- Contact information
- Privacy policy

**Experience Signals:**
- Case studies with real data
- User testimonials
- Before/after comparisons
- Original research and findings

### 4.3 External Validation (Critical)

```
The Consensus Rule:
If your website says X, but 5 external sources confirm X,
AI models cite you with confidence.

If your claims conflict with external sources,
AI models skip citing you or default to external consensus.
```

**Build external validation:**
- Get mentioned in industry publications
- Contribute to Wikipedia (where appropriate)
- Maintain consistent info across Crunchbase, LinkedIn, G2
- Earn links from high-DA sites (70+ DA dominates AI citations)
- Aim for ~250 external documents mentioning your brand

---

## Part 5: Platform-Specific Optimization

### 5.1 ChatGPT

**Citation patterns:**
- Favors Wikipedia (7.8% of citations)
- Prefers high-authority, factual sources
- Relies on Domain Rating
- Favors good Flesch readability scores
- Avoids user-generated content

**Optimization focus:**
- Encyclopedic, factual tone
- High domain authority
- Clear, readable content
- Expert-sourced information

### 5.2 Perplexity

**Citation patterns:**
- Reddit is top source (6.6% of citations)
- Cites more sources per answer (~13 brands)
- Favors industry-specific expert sites
- Includes niche players more often

**Optimization focus:**
- Comprehensive, listicle-style content
- Industry expert positioning
- Participation in relevant forums
- Niche expertise demonstration

### 5.3 Google AI Overviews

**Citation patterns:**
- Heavy Reddit and Quora inclusion
- Wider net including blogs and social
- Video content (especially YouTube)
- Community-sourced content

**Optimization focus:**
- Community engagement
- Video content creation
- Blog posts with depth
- User-generated content strategy

### 5.4 Claude

**Citation patterns:**
- Only cites when browsing is active
- Won't cite from training data alone
- Requires high-quality source material

**Optimization focus:**
- Clear, well-structured content
- Easy to extract and reference
- Factual accuracy

---

## Part 6: Implementation Checklist

### Pre-Launch Checklist

```
Technical:
[ ] Static HTML for landing page (no JS required for content)
[ ] TTFB < 200ms
[ ] All AI crawlers allowed in robots.txt
[ ] HTTPS enabled
[ ] Mobile responsive
[ ] Semantic HTML structure
[ ] No critical content behind JavaScript

Content:
[ ] TL;DR or summary at top of key pages
[ ] Question-based headings (H2, H3)
[ ] Statistics with citations
[ ] Expert quotes where relevant
[ ] FAQ section with common questions
[ ] Clear, readable language (Flesch score 60+)

Structured Data:
[ ] Organization schema
[ ] FAQPage schema (if applicable)
[ ] WebApplication schema (if tool/app)
[ ] All schema validated

E-E-A-T:
[ ] Author information visible
[ ] Sources cited for claims
[ ] Last updated date shown
[ ] Contact information accessible
[ ] Disclaimers where appropriate

Meta Tags:
[ ] Descriptive title (50-60 chars)
[ ] Meta description (150-160 chars)
[ ] Open Graph tags for social sharing
[ ] Twitter Card tags
```

### Post-Launch Monitoring

```
Track These Metrics:
[ ] AI crawler access in server logs
[ ] Brand mentions in AI responses (use Otterly.ai, Profound, etc.)
[ ] Citation frequency in Perplexity, ChatGPT
[ ] Organic traffic from AI referrals
[ ] Conversion rate from AI traffic (expect 4.4x higher than organic)
```

---

## Part 7: Quick Wins for Existing Sites

### Immediate Actions (< 1 hour)

1. **Update robots.txt** - Allow AI crawlers
2. **Add FAQ schema** - To existing FAQ content
3. **Add Organization schema** - To homepage

### Short-term Actions (1 day)

1. **Enhance index.html** - Static content for SPA
2. **Add TL;DR blocks** - To top 5 pages
3. **Convert headings** - To question format
4. **Add citations** - To key claims

### Medium-term Actions (1 week)

1. **Create llms.txt** - Content roadmap for AI
2. **Build FAQ page** - Comprehensive Q&A
3. **Implement full schema** - All relevant types
4. **Optimize TTFB** - Server/CDN improvements

---

## Part 8: Common Mistakes to Avoid

1. **JavaScript-only content** - AI crawlers can't see it
2. **Keyword stuffing** - Hurts visibility (Princeton study)
3. **Schema without matching content** - Gets flagged
4. **Blocking AI crawlers** - Default robots.txt may block
5. **Thin content** - AI prefers 2900+ word content
6. **Missing citations** - Reduces trust signals
7. **Inconsistent brand info** - Confuses entity recognition
8. **Slow pages** - AI crawlers abandon slow sites

---

## Resources

### Research & Studies
- [Princeton GEO Paper](https://arxiv.org/abs/2311.09735)
- [llms.txt Specification](https://llmstxt.org/)

### Monitoring Tools
- [Otterly.ai](https://otterly.ai/) - AI visibility tracking
- [Profound](https://www.tryprofound.com/) - AI crawler analytics
- [Am I Cited](https://www.amicited.com/) - Citation monitoring

### Validation Tools
- [Google Rich Results Test](https://search.google.com/test/rich-results)
- [Schema.org Validator](https://validator.schema.org/)
- [PageSpeed Insights](https://pagespeed.web.dev/)

---

*Last updated: February 2026*
*Based on research from Princeton, Walker Sands, Search Engine Land, and analysis of 100M+ AI citations*
