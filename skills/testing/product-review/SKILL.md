---
name: product-review
description: Product quality review — UX flows, onboarding, feature completeness, and user value. Use when a user says "product review", "review my product", "UX review", "is my product good", "product quality", "user experience review", "onboarding review", "feature audit", or "evaluate my app". Different from code review (code-review-and-quality) and product roast (roast-my-product) — this is structured, balanced evaluation.
---

# Product Review

## What This Does

Structured product quality evaluation. Walk through the product as a new user, evaluate every touchpoint, and produce an actionable improvement roadmap. Balanced — highlights strengths AND weaknesses.

This is NOT a code review and NOT a harsh roast (`roast-my-product`). This is a structured, balanced evaluation for understanding where the product stands and what to improve next.

## Workflow

### Step 1: Gather Context

**Always start by asking** — use a user-facing question prompt:

- What is the product? (URL, description, or demo)
- Who is the target user?
- What's the core use case — the ONE thing users should be able to do?
- What stage? (prototype, MVP, beta, launched)

Do NOT proceed without answers.

### Step 2: Read Existing Context

Read any project documentation, `README.md`, or product spec files that exist. These contain prior decisions about target market, tech stack, and build progress.

### Step 3: Load the Evaluation Framework

Read `references/product-quality-rubric.md` for the 8 quality dimensions. This is the scoring framework.

### Step 4: Walk Through as a First-Time User

Put yourself in the shoes of the target user. Use `references/onboarding-checklist.md` to systematically evaluate the first-time experience:

- What do you see first?
- How quickly do you understand what this product does?
- How many steps to the first meaningful action?
- Where do you get confused?
- Where do you get delighted?

Document every friction point and every moment of clarity.

### Step 5: Evaluate Each Quality Dimension

Go through each dimension from `references/product-quality-rubric.md`:

1. Onboarding Flow
2. Core Experience
3. Error Handling
4. Information Architecture
5. Visual Design & Polish
6. Performance
7. Accessibility
8. Feature Completeness

For each dimension:
- Score 1-10 with specific evidence
- Note what's working well
- Note what needs improvement
- Suggest a specific fix for the biggest issue in this dimension

### Step 6: Apply General UX Best Practices

Cross-reference the product against `references/ux-patterns.md` for general UX best practices. Note which patterns are implemented well and which are missing.

### Step 7: Synthesize Findings

Compile your evaluation into a structured report:

1. **Executive Summary** — 2-3 sentences on the product's overall quality
2. **Scorecard** — all 8 dimensions with scores
3. **Top 3 Strengths** — what's working well
4. **Top 3 Improvements** — highest-impact changes to make
5. **Detailed Dimension Reviews** — full analysis of each dimension
6. **Improvement Roadmap** — prioritized list of fixes by impact and effort

### Step 8: Prioritized Improvement Roadmap

Categorize improvements into:

- **Quick wins** (< 1 day, meaningful impact)
- **Medium effort** (1-3 days, significant improvement)
- **Major investment** (1+ week, transformative change)

Order by impact within each category.

## Non-Negotiables

- ALWAYS ask about the product before reviewing. Never assume.
- Be balanced — acknowledge what's working well, not just what's broken.
- Walk through as a REAL first-time user — document every friction point.
- Score with evidence — "onboarding: 6/10 because step 3 asks for email before showing value."
- Prioritize fixes by impact: what fixes will retain the most users?
- Distinguish between "nice to have" and "users are bouncing here."
- Don't confuse "I don't like the design" with "users can't complete the task." Focus on usability over aesthetics.

## Distinction from Other Review Skills

| Skill | Focus | Tone |
|-------|-------|------|
| `code-review-and-quality` | Code quality, security, production readiness | Technical, engineering-focused |
| `roast-my-product` | Find every weakness, deliberately harsh | Brutal, provocative |
| `product-review` | Balanced product quality evaluation | Structured, constructive |

## Output Format

```
## Executive Summary
[2-3 sentences on overall product quality]

## Scorecard
| Dimension | Score | Summary |
|-----------|-------|---------|
| Onboarding Flow | X/10 | ... |
| Core Experience | X/10 | ... |
| Error Handling | X/10 | ... |
| Information Architecture | X/10 | ... |
| Visual Design & Polish | X/10 | ... |
| Performance | X/10 | ... |
| Accessibility | X/10 | ... |
| Feature Completeness | X/10 | ... |
| **Overall** | **X/10** | |

## Top 3 Strengths
1. [Strength]: [Specific evidence]
2. [Strength]: [Specific evidence]
3. [Strength]: [Specific evidence]

## Top 3 Improvements
1. [Improvement]: [What to change and expected impact]
...

## Improvement Roadmap

### Quick Wins (< 1 day)
- [ ] [Fix]: [Expected impact]

### Medium Effort (1-3 days)
- [ ] [Fix]: [Expected impact]

### Major Investment (1+ week)
- [ ] [Fix]: [Expected impact]
```

## Tone

A thoughtful product manager doing a quarterly product review. Honest about problems, generous about strengths, always constructive about solutions.

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
