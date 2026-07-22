---
name: design-taste
description: Design direction, judgment calls, and anti-generic-AI review for product UIs. Use when the user says "this looks generic", "this looks AI-generated", "anti-slop", "design judgment", "premium feel", "design direction", "what direction should this take", "make this feel more premium", "review for taste", "theme reference", "warm monochrome", "stark minimal", "gradient trust", "workstation dense", "soft consumer", "gallery editorial", "density", "page archetype", "design brief", "pitch deck style", "deck visual direction". Also use when building any new page-level component that needs aesthetic direction before implementation. Does NOT claim "make this look good" or "polish this" — those belong to frontend-design-guidelines.
---

# Design Taste

## What This Does

The taste layer that sits between brand selection and mechanical implementation. Answers: "Given the constraints, what should this actually look like?" Catches generic AI output, makes judgment calls about density and layout, and defines what polished, intentional interfaces feel like.

`frontend-design-guidelines` tells you how to build it right. `brand-design` tells you how to brand it. **This skill tells you what design direction to take.**

## Authority Model

- `brand-design` → palette/typography selection → writes `brand.md`
- **This skill** → design direction, judgment calls, anti-slop review
- `frontend-design-guidelines` → mechanical correctness (a11y, forms, states) → invokes this skill when direction is needed
- `page-load-animations` → framer-motion implementation recipes

`frontend-design-guidelines` delegates direction questions TO this skill. This skill does NOT invoke `frontend-design-guidelines`.

## When to Fire This Skill

- The user says "this looks generic" or "this feels AI-generated"
- The user asks what direction a page or component should take
- The user wants something to feel "more premium" or "less generic"
- Building a new page and the aesthetic direction is unclear
- Reviewing existing UI for generic AI patterns
- The user references a design direction ("make it warm and premium", "dense dashboard feel")

Do NOT fire on "make this look good", "polish this", or "design this page" — those belong to `frontend-design-guidelines`.

## Workflow

### Step 0: Read brand.md if Present
Understand the existing aesthetic direction. Respect it — don't override palette or typeface choices.

### Step 1: Identify Which Reference is Relevant
- Checking for generic AI patterns → [references/anti-ai-slop.md](references/anti-ai-slop.md)
- Making layout/density/component decisions → [references/design-judgment.md](references/design-judgment.md)
- Looking for style direction and inspiration → [references/theme-references.md](references/theme-references.md)
- Choosing visual direction for a pitch deck → [references/theme-references.md](references/theme-references.md) § "Pitch Deck Styles"

Load only what you need. Don't read all references upfront.

### Step 2: Apply Direction and Produce a Design Brief

```
Direction: [archetype — e.g., "dark dashboard, warm-monochrome style"]
Density:   [spacious / comfortable / compact]
Surface:   [cards on dark bg / flat sections / panels]
Type mood: [3 descriptors — e.g., "tight, technical, mono-heavy"]
Motion:    [1 descriptor — e.g., "crisp springs, no bounce"]
Do:        [3-5 specific direction choices]
Don't:     [3-5 specific anti-patterns to avoid]
```

### Step 3: Run Anti-Slop Check Before Reporting Complete
Scan for the detection signals in `anti-ai-slop.md`. Flag any that match.

## 3 Modes

### Direction Mode
Choosing what the page/component should look like before building. Produces a design brief. Used at the start of UI work.

### Review Mode
Auditing existing UI for generic/AI-slop patterns. Diagnostic only — detects problems and names them.

### Quick Lookup
"What layout for a dashboard?" → page archetype from `design-judgment.md`.
"What density for a data-heavy surface?" → density guidance.

## Enforceable Rules (Code-Scannable)

1. **No pure black `#000` or `#000000` in product UI** — unless a deliberate brand token.
2. **No `transition: all` or `transition-all`.**
3. **No arbitrary Tailwind values in spacing** — grep for `p-[`, `m-[`, `gap-[` patterns.

## Judgment Heuristics

1. Single accent + grayscale preferred — multiple competing colors is a smell
2. Content inside cards/panels, not bare page background
3. Entry takes longer than exit (asymmetric timing)
4. Convergence test: "If someone said AI made this, would they believe immediately?"
5. Typography hierarchy: 3 weights max, 4-5 sizes max
6. Whitespace signals confidence — cramped signals cheap
7. Custom easing curves preferred over CSS defaults
8. Shadow opacity should be low (4-8% for cards, 12% max for overlays)

## State Transition Quick Reference

| Transition | Feel | Timing |
|---|---|---|
| Skeleton → loaded | Cross-fade, 300-500ms | Show skeleton within 300ms of action |
| Success → idle | Toast auto-dismiss 5s, green flash then fade | Check animation 300ms |
| Error → recovery | Error persists until action, retry button | Shake 200ms (subtle) |
| Filter/sort change | Cross-fade content 120ms, height spring 350ms | Stagger new rows 40ms |
| Mount (first appearance) | Dramatic, expressive | 300-600ms |
| Update (subsequent) | Subtle, smooth | 100-150ms |
| Entry vs exit | Entry takes longer | Enter 300ms, exit 200ms |

## Resources

- [references/anti-ai-slop.md](references/anti-ai-slop.md) — Detection checklist for generic AI output + fix rules + 11 aesthetic directions
- [references/design-judgment.md](references/design-judgment.md) — Decision trees, page archetypes, mobile collapse rules
- [references/theme-references.md](references/theme-references.md) — 6 style references + trait index + composition guide

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
