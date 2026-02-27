# GEO/AIO Implementation Templates

Quick-copy templates for implementing AI search optimization.

---

## 1. robots.txt Template (Full AI Access)

```txt
# Allow all AI crawlers for maximum visibility

# OpenAI
User-agent: GPTBot
Allow: /

User-agent: ChatGPT-User
Allow: /

User-agent: OAI-SearchBot
Allow: /

# Anthropic (Claude)
User-agent: ClaudeBot
Allow: /

User-agent: Claude-User
Allow: /

User-agent: Claude-SearchBot
Allow: /

# Perplexity
User-agent: PerplexityBot
Allow: /

User-agent: Perplexity-User
Allow: /

# Google AI
User-agent: Google-Extended
Allow: /

# Other AI crawlers
User-agent: YouBot
Allow: /

User-agent: PhindBot
Allow: /

User-agent: Applebot-Extended
Allow: /

# Traditional crawlers
User-agent: Googlebot
Allow: /

User-agent: Bingbot
Allow: /

# Sitemap
Sitemap: https://www.yoursite.com/sitemap.xml
```

---

## 2. llms.txt Template

```markdown
# [Your Site Name]

> [One sentence describing what your site does and its core value proposition]

## Overview

- [Main Page](/): [Brief description of what visitors find here]
- [About Us](/about): [What makes you authoritative on this topic]

## Core Content

- [Feature 1](/feature-1): [Description]
- [Feature 2](/feature-2): [Description]
- [How It Works](/how-it-works): [Description]

## Resources

- [FAQ](/faq): Common questions and answers
- [Blog](/blog): Latest insights and updates
- [Documentation](/docs): Technical details and guides

## Contact

- [Contact](/contact): How to reach us
- [Support](/support): Get help
```

---

## 3. Schema Templates

### Organization Schema

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "[Your Company Name]",
  "url": "https://www.[yourdomain].com",
  "logo": "https://www.[yourdomain].com/logo.png",
  "description": "[One sentence about what you do]",
  "foundingDate": "[YYYY]",
  "sameAs": [
    "https://twitter.com/[handle]",
    "https://linkedin.com/company/[company]",
    "https://www.crunchbase.com/organization/[org]"
  ],
  "contactPoint": {
    "@type": "ContactPoint",
    "email": "contact@[yourdomain].com",
    "contactType": "customer service"
  }
}
</script>
```

### WebApplication Schema

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebApplication",
  "name": "[App Name]",
  "url": "https://www.[yourdomain].com",
  "description": "[What your app does in one sentence]",
  "applicationCategory": "[Category]Application",
  "operatingSystem": "Web",
  "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "USD"
  },
  "creator": {
    "@type": "Organization",
    "name": "[Your Company Name]"
  }
}
</script>
```

### FAQPage Schema

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "[Question 1]?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "[Answer to question 1]"
      }
    },
    {
      "@type": "Question",
      "name": "[Question 2]?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "[Answer to question 2]"
      }
    },
    {
      "@type": "Question",
      "name": "[Question 3]?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "[Answer to question 3]"
      }
    }
  ]
}
</script>
```

### Article Schema (for blog posts)

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "[Article Title]",
  "description": "[Article summary in 150-160 characters]",
  "image": "https://www.[yourdomain].com/images/[article-image].jpg",
  "author": {
    "@type": "Person",
    "name": "[Author Name]",
    "url": "https://www.[yourdomain].com/author/[author-slug]"
  },
  "publisher": {
    "@type": "Organization",
    "name": "[Your Company Name]",
    "logo": {
      "@type": "ImageObject",
      "url": "https://www.[yourdomain].com/logo.png"
    }
  },
  "datePublished": "[YYYY-MM-DD]",
  "dateModified": "[YYYY-MM-DD]"
}
</script>
```

### HowTo Schema

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "HowTo",
  "name": "[How to do X]",
  "description": "[Brief description of what this guide covers]",
  "totalTime": "PT[X]M",
  "step": [
    {
      "@type": "HowToStep",
      "name": "[Step 1 Title]",
      "text": "[Step 1 detailed instructions]",
      "position": 1
    },
    {
      "@type": "HowToStep",
      "name": "[Step 2 Title]",
      "text": "[Step 2 detailed instructions]",
      "position": 2
    },
    {
      "@type": "HowToStep",
      "name": "[Step 3 Title]",
      "text": "[Step 3 detailed instructions]",
      "position": 3
    }
  ]
}
</script>
```

---

## 4. Meta Tags Template

```html
<head>
  <!-- Primary Meta Tags -->
  <title>[Page Title] – [Brand Name]</title>
  <meta name="title" content="[Page Title] – [Brand Name]" />
  <meta name="description" content="[150-160 character description with key value proposition]" />
  <meta name="author" content="[Brand Name]" />
  <meta name="robots" content="index, follow" />

  <!-- Canonical -->
  <link rel="canonical" href="https://www.[yourdomain].com/[page-path]" />

  <!-- Open Graph / Facebook / LinkedIn -->
  <meta property="og:type" content="website" />
  <meta property="og:url" content="https://www.[yourdomain].com/[page-path]" />
  <meta property="og:title" content="[Page Title] – [Brand Name]" />
  <meta property="og:description" content="[Compelling description for social sharing]" />
  <meta property="og:image" content="https://www.[yourdomain].com/og-image.png" />
  <meta property="og:image:width" content="1200" />
  <meta property="og:image:height" content="630" />
  <meta property="og:site_name" content="[Brand Name]" />

  <!-- Twitter -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:url" content="https://www.[yourdomain].com/[page-path]" />
  <meta name="twitter:title" content="[Page Title] – [Brand Name]" />
  <meta name="twitter:description" content="[Description for Twitter]" />
  <meta name="twitter:image" content="https://www.[yourdomain].com/og-image.png" />
</head>
```

---

## 5. HTML Content Structure Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Meta tags here -->
</head>
<body>
  <header>
    <nav aria-label="Main navigation">
      <!-- Navigation links as proper <a> tags -->
    </nav>
  </header>

  <main>
    <article>
      <!-- TL;DR / Summary Block (CRITICAL for AI) -->
      <section aria-label="Summary">
        <p><strong>TL;DR:</strong> [One paragraph summary of the page's main point]</p>
      </section>

      <!-- Main Heading -->
      <h1>[Main Topic - Only One H1 Per Page]</h1>

      <!-- Section with Question Heading -->
      <section>
        <h2>What is [Topic]?</h2>
        <p>[Direct answer to the question in the first sentence.]
        [Supporting details and context follow.]</p>
      </section>

      <!-- Section with Statistics -->
      <section>
        <h2>Why does [Topic] matter?</h2>
        <p>According to [Source], [statistic or data point].
        [Explanation of significance.]</p>
      </section>

      <!-- Section with List -->
      <section>
        <h2>How does [Topic] work?</h2>
        <ol>
          <li><strong>[Step 1]:</strong> [Description]</li>
          <li><strong>[Step 2]:</strong> [Description]</li>
          <li><strong>[Step 3]:</strong> [Description]</li>
        </ol>
      </section>

      <!-- FAQ Section -->
      <section aria-label="Frequently Asked Questions">
        <h2>Frequently Asked Questions</h2>

        <h3>What is [common question 1]?</h3>
        <p>[Direct answer]</p>

        <h3>How do I [common question 2]?</h3>
        <p>[Direct answer]</p>

        <h3>Why should I [common question 3]?</h3>
        <p>[Direct answer]</p>
      </section>
    </article>
  </main>

  <footer>
    <p>&copy; [Year] [Brand Name]. All rights reserved.</p>
    <p>[Disclaimer if applicable]</p>
  </footer>

  <!-- Schema markup scripts -->
</body>
</html>
```

---

## 6. Static Landing Page Template (for SPAs)

For React/Vue/Angular apps, include this in index.html:

```html
<div id="root">
  <!-- Static content for crawlers - replaced when JS loads -->
  <div id="static-content">
    <header>
      <h1>[Brand Name]</h1>
      <p>[Tagline]</p>
    </header>

    <main>
      <section>
        <h2>[Main Value Proposition]</h2>
        <p>[Detailed description of what you offer - 100+ words]</p>
        <a href="/[cta-path]">[Call to Action] &rarr;</a>
      </section>

      <section>
        <h2>How It Works</h2>
        <ol>
          <li><strong>[Step 1]</strong> - [Description]</li>
          <li><strong>[Step 2]</strong> - [Description]</li>
          <li><strong>[Step 3]</strong> - [Description]</li>
        </ol>
      </section>

      <section>
        <h2>Key Features</h2>
        <ul>
          <li><strong>[Feature 1]</strong> - [Benefit]</li>
          <li><strong>[Feature 2]</strong> - [Benefit]</li>
          <li><strong>[Feature 3]</strong> - [Benefit]</li>
        </ul>
      </section>

      <section>
        <h2>Frequently Asked Questions</h2>
        <h3>[Question 1]?</h3>
        <p>[Answer 1]</p>
        <h3>[Question 2]?</h3>
        <p>[Answer 2]</p>
        <h3>[Question 3]?</h3>
        <p>[Answer 3]</p>
      </section>
    </main>

    <footer>
      <p>[Disclaimer/legal text]</p>
      <p>&copy; [Year] [Brand Name]. All rights reserved.</p>
    </footer>
  </div>

  <!-- Noscript fallback -->
  <noscript>
    <p>JavaScript is required for the interactive features.
    Please enable JavaScript to continue.</p>
  </noscript>
</div>

<script type="module" src="/src/main.tsx"></script>
```

---

## 7. Verification Checklist

Run these checks after implementation:

```bash
# Check robots.txt is accessible
curl -I https://www.yoursite.com/robots.txt

# Verify HTML content (no JavaScript)
curl -s https://www.yoursite.com | head -100

# Test TTFB
curl -w "TTFB: %{time_starttransfer}s\n" -o /dev/null -s https://www.yoursite.com

# Validate schema
# Use: https://search.google.com/test/rich-results

# Check mobile rendering
# Use: https://search.google.com/test/mobile-friendly
```

---

*Templates last updated: February 2026*
