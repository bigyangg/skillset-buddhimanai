---
name: page-load-animations
description: Fix janky page loads where everything appears at once. Production framer-motion recipes for choreographed page entrances, staggered lists, modal transitions, filter cross-fades, live data animations, and micro-interactions. Use when building or reviewing any page that loads content, when animations feel broken or janky, when framer-motion code needs production patterns, or when the user says "page load animation", "entrance choreography", "stagger animation", "framer-motion recipe", "page feels janky", "everything appears at once", "spring animation", "modal animation", "dropdown animation", "rolling numbers", "chart morph", "donut reveal", "filter transition", "tab animation", "micro-interaction", "hover animation", "button feedback", "AnimatePresence", or "framer-motion pattern". Use proactively whenever writing page-level components or reviewing animation code.
---

# Page Load Animations

## What This Does

Production framer-motion recipes for making page loads feel intentional instead of chaotic. The most common animation problem in web UIs is not missing animation — it's everything appearing at once with no choreography, stagger, or spring physics. This skill provides the implementation recipes.

For animation theory (when to animate, duration tiers, easing curves, reduced-motion), see `frontend-design-guidelines/references/animation.md`. **This skill inherits all those baseline constraints.**

## When to Fire This Skill

Apply these recipes any time you are:

- Building a page that loads multiple sections (dashboard, detail screen, landing page)
- Staggering a list, card grid, or table on mount
- Animating a modal, dropdown, or popover
- Switching tabs or filters where content changes and height shifts
- Displaying live-updating numbers, charts, or graphs
- Adding hover feedback, chevron rotation, or tooltip entrance
- Reviewing existing framer-motion code for anti-patterns
- A user says "the page feels janky" or "everything appears at once"

## Setup

```bash
npm install framer-motion
```

CSS transitions are the default for simple state changes. Reach for framer-motion when you need: spring physics, orchestrated sequences, `AnimatePresence` exit animations, gesture/drag, or shared-layout transitions.

## Workflow

### Before Writing Animation Code, Identify Which Reference File is Relevant:

- Orchestrating a page load entrance → [references/page-choreography.md](references/page-choreography.md)
- Staggering a list or card grid → [references/list-stagger.md](references/list-stagger.md)
- Animating a modal, dropdown, or popover → [references/popups-modals.md](references/popups-modals.md)
- Switching tabs/filters with height changes → [references/filter-transitions.md](references/filter-transitions.md)
- Rolling numbers, chart morphs, donut reveals → [references/live-data.md](references/live-data.md)
- Button feedback, chevron rotation, tooltips → [references/hover-micro.md](references/hover-micro.md)
- Looking up a spring preset or easing curve → [references/spring-presets.md](references/spring-presets.md)

Load only what you need.

### Mode 1: Building New Animations

1. Read the relevant reference file(s).
2. Start with the ASCII storyboard comment — plan the full sequence before writing code.
3. Define a TIMING object with named constants for every delay.
4. Implement using the patterns from the reference. Use spring physics by default.
5. Verify against non-negotiables before reporting complete.

### Mode 2: Reviewing Existing Animation Code

1. Scan for common anti-patterns:
   ```bash
   grep -rn "layout" --include="*.tsx" | head -20
   grep -rn "staggerChildren" --include="*.tsx" | head -10
   grep -rn "transition-all\|transition: all" --include="*.tsx" --include="*.css" | head -10
   ```
2. For each issue, reference the specific anti-pattern rule from the relevant reference file.
3. Provide exact code patches.

### Mode 3: Quick Lookup

If the user asks "what spring config for X?" or "how do I animate Y?", answer directly from the reference files.

## Non-Negotiables

1. **ASCII storyboard comment.** Every animated page starts with a timeline comment showing the full sequence.
2. **Named timing constants.** Every delay goes in a TIMING object. No magic numbers.
3. **Spring-first.** Prefer spring physics over duration-based easing. Only use duration for fades and tooltips.
4. **Stage-driven sequencing.** A single integer state drives multi-section sequences.
5. **`stage >= N` pattern.** Sections stay visible once they appear. Never `stage === N`.
6. **Never `layout` on both parent AND children.** Use `AnimatedHeight` with `ResizeObserver` instead.
7. **Never `staggerChildren` with `AnimatePresence`.** Use manual `delay: i * stagger`.
8. **Respect `prefers-reduced-motion`.**
9. **Mount vs update distinction.** Mount animations are dramatic (600ms). Update animations are subtle (150ms).

## Design Principles

1. **Storyboard-first** — plan the sequence before writing code
2. **Spring-first** — spring physics over duration-based easing
3. **Named constants** — every timing value is declared, not inline
4. **Stage-driven** — single integer state orchestrates complex sequences
5. **Data-driven** — repeated elements use `.map()` with index-based delays
6. **Measure, don't guess** — `ResizeObserver` for dynamic heights

## Resources

- [references/page-choreography.md](references/page-choreography.md)
- [references/list-stagger.md](references/list-stagger.md)
- [references/popups-modals.md](references/popups-modals.md)
- [references/filter-transitions.md](references/filter-transitions.md)
- [references/live-data.md](references/live-data.md)
- [references/hover-micro.md](references/hover-micro.md)
- [references/spring-presets.md](references/spring-presets.md)

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
