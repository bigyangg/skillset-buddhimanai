---
name: seo-optimization
description: Audit and improve technical SEO for any website. Use when a user says "improve SEO", "fix SEO", "technical SEO audit", "meta tags", "page speed SEO", "Core Web Vitals", "structured data", "schema markup", "sitemap", "robots.txt", "canonical URLs", "keyword rankings", or "search engine optimization".
---

# SEO Optimization

## What This Does

Perform systematic technical SEO audits and implement fixes. Covers on-page SEO, technical infrastructure, Core Web Vitals, structured data, and content optimization — with concrete code implementations for each area.

## Non-Negotiables

- **One `<h1>` per page.** Exactly one, matching the page's primary topic.
- **Every page has a unique `<title>` and `<meta description>`.** Duplicates across pages hurt rankings.
- **All images have `alt` text.** Missing alt text is an accessibility and SEO failure.
- **Canonical URLs are explicit.** Always set `<link rel="canonical">` even on the canonical page itself.
- **Core Web Vitals are measured in the field.** Use PageSpeed Insights / CrUX — lab data alone isn't enough.

## Audit Checklist

### 1. Title & Meta Tags

```html
<!-- ✅ Correct title: Primary keyword first, brand last, 50–60 chars -->
<title>Open Source Task Manager for Developers | Taskly</title>

<!-- ✅ Meta description: 150–160 chars, includes keyword, has CTA -->
<meta name="description" content="Taskly is a free, open source task manager built for developers. CLI-first, Git-integrated, and fully offline. Download in 30 seconds.">

<!-- ✅ Canonical (always explicit, even on canonical page) -->
<link rel="canonical" href="https://www.example.com/features">

<!-- ✅ OG / Social cards -->
<meta property="og:title"       content="Open Source Task Manager for Developers | Taskly">
<meta property="og:description" content="CLI-first task management for developers.">
<meta property="og:image"       content="https://www.example.com/og/features.png">
<meta property="og:url"         content="https://www.example.com/features">
<meta property="og:type"        content="website">
<meta name="twitter:card"       content="summary_large_image">
```

### 2. Heading Structure

```html
<!-- ✅ Correct: One H1, logical hierarchy -->
<h1>Open Source Task Manager for Developers</h1>
  <h2>Key Features</h2>
    <h3>CLI Integration</h3>
    <h3>Git Sync</h3>
  <h2>Getting Started</h2>
    <h3>Installation</h3>
    <h3>Your First Task</h3>

<!-- ❌ Wrong: Multiple H1s, skipped levels -->
<h1>Features</h1>
<h1>Pricing</h1>
<h4>FAQ</h4>  <!-- jumped from h1 to h4 -->
```

### 3. URL Structure

```
✅ Good URLs:
/blog/react-state-management-guide
/products/keyboard-wireless
/docs/api/authentication

❌ Bad URLs:
/blog/post?id=12345
/p/xyz-abc-123
/docs/page1
```

### 4. Image Optimization

```html
<!-- ✅ All images: descriptive alt, proper sizing, lazy loading -->
<img
  src="/images/dashboard-dark-mode.webp"
  alt="Taskly dashboard in dark mode showing project kanban board"
  width="1200"
  height="630"
  loading="lazy"
  decoding="async"
>

<!-- ✅ Hero/LCP image: eager load, no lazy -->
<img
  src="/images/hero.webp"
  alt="Developer using Taskly terminal interface"
  width="1440"
  height="800"
  loading="eager"
  fetchpriority="high"
>
```

## Structured Data (Schema.org)

```html
<!-- Article schema (blog posts) -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "How to Build an MCP Server in 10 Minutes",
  "author": {
    "@type": "Person",
    "name": "Jane Smith",
    "url": "https://example.com/authors/jane"
  },
  "datePublished": "2026-07-24",
  "dateModified": "2026-07-24",
  "image": "https://example.com/blog/mcp-server-tutorial.webp",
  "publisher": {
    "@type": "Organization",
    "name": "Example Dev Blog",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.png"
    }
  }
}
</script>

<!-- FAQ schema (documentation / LLM-friendly) -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is an MCP server?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "An MCP server exposes tools, resources, and prompts to AI models via the Model Context Protocol."
      }
    }
  ]
}
</script>
```

## Core Web Vitals

| Metric | What It Measures | Good | Poor |
|--------|-----------------|------|------|
| LCP | Largest Contentful Paint (load speed) | < 2.5s | > 4s |
| INP | Interaction to Next Paint (responsiveness) | < 200ms | > 500ms |
| CLS | Cumulative Layout Shift (visual stability) | < 0.1 | > 0.25 |

### Fix LCP

```html
<!-- 1. Preload the LCP image -->
<link rel="preload" as="image" href="/images/hero.webp" fetchpriority="high">

<!-- 2. Use modern formats -->
<picture>
  <source srcset="/images/hero.avif" type="image/avif">
  <source srcset="/images/hero.webp" type="image/webp">
  <img src="/images/hero.jpg" alt="Hero" width="1440" height="800" loading="eager">
</picture>
```

```typescript
// Next.js: priority prop on LCP image
import Image from 'next/image'
<Image src="/hero.webp" alt="Hero" width={1440} height={800} priority />
```

### Fix CLS

```css
/* Always declare width and height on images */
img { aspect-ratio: attr(width) / attr(height); }

/* Reserve space for dynamic content */
.ad-placeholder { min-height: 250px; }

/* Avoid inserting content above existing content */
.notification-banner { position: fixed; top: 0; }
```

### Fix INP

```typescript
// Defer non-critical interactions
import { startTransition } from 'react'

function handleFilter(value: string) {
  startTransition(() => {
    setFilter(value)   // Mark as non-urgent — React yields to user input
  })
}
```

## Sitemap & Robots.txt

```xml
<!-- public/sitemap.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2026-07-24</lastmod>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/blog/mcp-server-guide</loc>
    <lastmod>2026-07-24</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

```
# public/robots.txt
User-agent: *
Allow: /

Disallow: /api/
Disallow: /admin/
Disallow: /_next/

Sitemap: https://example.com/sitemap.xml
```

## Next.js SEO Setup

```typescript
// app/layout.tsx — base metadata
import type { Metadata } from 'next'

export const metadata: Metadata = {
  metadataBase: new URL('https://example.com'),
  title: {
    default:  'Taskly — Developer Task Manager',
    template: '%s | Taskly',
  },
  description: 'CLI-first task manager built for developers.',
  openGraph: {
    type: 'website',
    locale: 'en_US',
    url: 'https://example.com',
    siteName: 'Taskly',
  },
  robots: {
    index:  true,
    follow: true,
  },
}

// app/blog/[slug]/page.tsx — page-level override
export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPost(params.slug)
  return {
    title:       post.title,
    description: post.excerpt,
    openGraph: {
      title:       post.title,
      description: post.excerpt,
      images:      [{ url: post.ogImage, width: 1200, height: 630 }],
    },
  }
}
```

## SEO Audit Command

```bash
# Run Lighthouse audit
npx lighthouse https://example.com --view

# Check for broken links
npx broken-link-checker https://example.com --recursive

# Validate structured data
# → https://search.google.com/test/rich-results
# → https://validator.schema.org
```
