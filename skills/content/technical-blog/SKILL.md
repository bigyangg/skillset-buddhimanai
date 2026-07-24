---
name: technical-blog
description: Write technical blog posts, developer tutorials, and engineering articles. Use when a user says "write a blog post", "write a tutorial", "explain this concept in a post", "engineering article", "developer post", "write for dev.to", "technical writing", "explain like I'm a senior dev", or "post about X technology".
---

# Technical Blog

## What This Does

Write high-quality technical blog posts and tutorials that engineers actually read and share. Covers structure, hook writing, code example formatting, SEO basics, and the common mistakes that make technical posts unreadable.

## Non-Negotiables

- **Never start with "In this tutorial, we will..."** It's the most overused opener in technical writing. Start with the problem or a hook.
- **Code examples must be copy-pasteable and runnable.** Never show partial snippets without context.
- **One idea per post.** Trying to cover everything produces content nobody finishes.
- **Show the why, not just the how.** Readers can find documentation. They come to blogs for judgment and context.
- **Test code examples before publishing.** Untested code in tech posts destroys credibility.

## Post Types

| Type | Structure | Best For |
|------|-----------|---------|
| **Tutorial** | Problem → Setup → Steps → Result | New tools, frameworks |
| **Deep dive** | Concept → Internals → Trade-offs | Architecture, algorithms |
| **Comparison** | Option A vs B vs C → Verdict | Library/tool selection |
| **Lessons learned** | What I tried → What broke → What works | War stories, post-mortems |
| **Opinion/take** | Thesis → Arguments → Counter-arguments → Stance | Controversial topics |

## Workflow

### Step 1 — Interview the Author (3 Questions)

Before writing, ask:
1. "Who is the reader? Junior dev, senior engineer, or tech-curious? What do they already know?"
2. "What one thing should the reader be able to do after reading this?"
3. "What's the surprising or counterintuitive part of this topic that most posts miss?"

### Step 2 — Write the Hook First

The first 3 sentences determine if the reader stays. Write these last, but place them first.

**Hook patterns:**
```
Problem hook:    "Every Node.js app I've worked on cached data wrong for the first 6 months."
Surprising fact: "PostgreSQL can replace your search infrastructure for most use cases."
Bold claim:      "Microservices are the wrong choice for 90% of startups."
Story hook:      "On a Friday at 4pm, our API stopped responding to 40% of requests."
```

**Openers to avoid:**
- "In this post, I will..."
- "Today we're going to learn..."
- "As a developer, you've probably..."
- "Welcome to this tutorial..."

### Step 3 — Outline Before Writing

```markdown
## Outline Template

**Hook:** [1-3 engaging opening sentences]
**Thesis:** [One sentence: what the reader will know/be able to do]

**Section 1:** [The problem / why this matters]
**Section 2:** [The concept / the solution]
**Section 3:** [Step-by-step implementation OR deep dive]
**Section 4:** [Real-world examples / trade-offs / when NOT to use this]
**Conclusion:** [What to do next + key takeaways]
```

### Step 4 — Write Code Examples

```markdown
<!-- Always: context comment → code → explanation -->

<!-- ❌ Bad: No context, unexplained -->
```js
const cache = new Map()
```

<!-- ✅ Good: Context → Code → Why it matters -->
We use a `Map` instead of a plain object because `Map` preserves insertion order and 
provides O(1) lookup without prototype chain issues:

```typescript
// store/cache.ts
const cache = new Map<string, { data: unknown; expiresAt: number }>()

function get<T>(key: string): T | null {
  const entry = cache.get(key)
  if (!entry || entry.expiresAt < Date.now()) {
    cache.delete(key)
    return null
  }
  return entry.data as T
}
```

The `expiresAt` check in `get()` means stale entries are lazily evicted — 
no background timer needed for simple cases.
```

### Step 5 — Structure Rules

**Headings:**
- H1: Post title (one only)
- H2: Major sections (3–6 max)
- H3: Subsections within major sections only

**Paragraphs:**
- Max 3–5 sentences per paragraph
- One idea per paragraph
- Short paragraphs for listicles, longer for deep dives

**Lists:**
- Use bullet lists for 3+ parallel items
- Use numbered lists for steps that must be done in order
- Never start a bullet with "Note that..." — just say the thing

**Code blocks:**
- Always specify the language for syntax highlighting
- Add a filename comment as the first line for files: `// src/cache.ts`
- Never show `> 50 lines` without breaking into numbered steps

### Step 6 — Write the Conclusion

Good conclusions do three things:
1. **Summarize without copy-pasting.** Restate the takeaway in fresh words, not the same sentences.
2. **Point to the next step.** "Try it with..." or "The next thing to explore is..."
3. **Invite discussion.** A question or "What approach do you use?" drives comments.

## SEO Basics for Tech Posts

- **Title:** Include the primary keyword. "How I..." and "Why..." outperform "Introduction to..."
- **Meta description:** 150–160 characters, one sentence, includes the keyword and a benefit.
- **Keyword in first paragraph:** Appear naturally within the first 100 words.
- **H2 headings:** Include secondary keywords naturally.
- **Images:** `alt` text on every image, descriptive filename (`redis-cache-miss.png`, not `image1.png`).

## Review Checklist

Before publishing:
- [ ] Hook does not start with "In this post..."
- [ ] Code examples are tested and runnable
- [ ] All code blocks have language identifier (```typescript, ```bash, etc.)
- [ ] Paragraphs are ≤ 5 sentences
- [ ] Post has clear takeaways (what can the reader do now?)
- [ ] Title is specific: "How Zustand Beats Redux for 90% of React Apps" not "State Management"
- [ ] No passive voice in first paragraph
- [ ] Conclusion points to a next action
- [ ] Read time estimate: target 5–8 minutes (800–1500 words) for tutorials, up to 15 min for deep dives
