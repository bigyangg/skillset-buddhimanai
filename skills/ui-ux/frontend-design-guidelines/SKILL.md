---
name: frontend-design-guidelines
description: Apply high-quality web interface design rules when building, reviewing, or styling frontend code. Use when the user says "build a frontend", "create a component", "style this", "review my UI", "build a landing page", "design this page", "make this look good", "add animation", "build a form", "improve the UI", "polish this", "make this feel right", "review for craft", "the interaction feels off", "make this look polished", or when generating any React/Next.js component. Defaults to Tailwind CSS and shadcn/ui. Reads brand.md at the project root (if present) and uses it as the source of truth for colors, typography, and voice. Covers interactions, layout, typography, forms, animation, states, accessibility, and a dedicated craft-and-polish layer for taste-level review. Use proactively whenever frontend code is being written — do not wait to be asked.
---

# Frontend Design Guidelines

## What This Does

A practical, enforceable ruleset for building frontend UIs that look and feel high-quality instead of generic. Catches hardcoded colors, broken keyboard nav, missing empty states, and janky animations before the user sees them.

## When to Fire This Skill

Apply these rules any time you are:

- Creating a new React / Next.js component or page
- Building a form, layout, dialog, or navigation
- Reviewing frontend code for quality
- Adding animations, transitions, or micro-interactions
- Styling anything with CSS, Tailwind, or CSS-in-JS
- A user asks you to "make it look good", "clean this up", "polish the UI"

If generating frontend code and this skill has *not* been triggered, trigger it yourself.

## Default Stack (Use Unless the User Says Otherwise)

- **Framework:** Next.js (App Router) + TypeScript
- **Styling:** Tailwind CSS
- **Components:** shadcn/ui — install per component via `npx shadcn@latest add <name>`
- **Icons:** `lucide-react`
- **Animation:** CSS transitions for micro-interactions. Use `framer-motion` only when you need shared-layout, gesture, or orchestrated sequences.
- **Fonts:** `next/font` with a sans (Inter or Geist) for UI and a mono (JetBrains Mono or Geist Mono) for code/numbers.
- **Theming:** shadcn's CSS variables so light/dark work automatically.

If the repo already has a different stack, match it.

## Workflow

0. **Check for `brand.md` at the project root.** Three cases:

   **Case A — `brand.md` exists with a real palette**: Read it. Use as source of truth for every color, font, and copy decision.

   **Case B — `brand.md` exists with `Status: deferred`**: Use stock shadcn neutral tokens. Do not prompt again. Continue to step 1.

   **Case C — `brand.md` does not exist**: Ask the user once this session:

   > **Set up brand guidelines for this project?**
   >
   > I can run `brand-design` now — it walks you through picking a palette, typography, and tone. Takes 2–3 minutes.
   >
   > - **Yes** → I'll run `brand-design` now.
   > - **No** → I'll use shadcn's default neutral theme. You can set up brand guidelines anytime by saying "pick brand colors".

   If No: write a deferred `brand.md` and continue.

1. **Before writing code**, identify which reference files are relevant:
   - Building a form → `references/forms.md` + `references/interactions.md`
   - Building a page/layout → `references/layout-and-design.md`
   - Adding motion → `references/animation.md`
   - Building framer-motion page entrances, list staggers, modals → invoke `page-load-animations` skill
   - Making design direction decisions → invoke `design-taste` skill
   - Any interactive component → `references/interactions.md` + `references/states.md`
   - **Polishing, reviewing for craft** → `references/craft-and-polish.md`

   Load only what you need.

2. **While writing code**, apply the rules. If a rule conflicts with an explicit user request, surface the tradeoff out loud.

3. **Before reporting the task complete**, run the final review checklist.

## Non-Negotiables (Apply to Every Component)

1. **Interactive elements are real elements.** Use `<button>` or `<a href>`, never `<div onClick>`.
2. **Every interactive element has a visible focus ring.** Never remove focus rings with `outline-none` alone.
3. **Hit targets are at least 40×40 px on touch.**
4. **Loading, empty, and error states exist.**
5. **Animations respect `prefers-reduced-motion`.**
6. **Color and spacing come from tokens, not magic numbers.**
7. **Contrast passes WCAG AA.** 4.5:1 for body text, 3:1 for large text/icons.
8. **Forms have labels, correct `type`, and `autocomplete`.**
9. **Dark mode works.** Never hardcode `bg-white` or `text-black`.
10. **Copy is concise, active voice, and specific.**

## Final Review Checklist

- [ ] Keyboard navigation works: Tab moves in logical order, Enter/Space activates, Escape closes overlays
- [ ] Visible focus ring on all interactive elements
- [ ] No `<div onClick>` — real `<button>` / `<a>` everywhere
- [ ] Hit targets ≥ 40×40 px on mobile
- [ ] Loading state implemented (skeleton preferred over spinner)
- [ ] Empty state implemented with a clear next action
- [ ] Error state implemented with recovery action
- [ ] Contrast passes AA for all text and meaningful icons
- [ ] Respects `prefers-reduced-motion` for every animation
- [ ] Dark mode renders correctly
- [ ] No hardcoded colors or spacing outside design tokens
- [ ] Form fields have labels, proper `type`, `autocomplete`, and visible inline errors
- [ ] Copy is concise, active voice, and human
- [ ] Images have `alt`, icons have `aria-label` or are marked `aria-hidden`
- [ ] Tested at mobile (375 px), tablet (768 px), and desktop (1280 px) widths

## References

- [references/stack-defaults.md](references/stack-defaults.md)
- [references/interactions.md](references/interactions.md)
- [references/forms.md](references/forms.md)
- [references/animation.md](references/animation.md)
- [references/layout-and-design.md](references/layout-and-design.md)
- [references/states.md](references/states.md)
- [references/craft-and-polish.md](references/craft-and-polish.md) — Inspired by Emil Kowalski's design engineering philosophy — credit to [emilkowal.ski](https://emilkowal.ski)

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
