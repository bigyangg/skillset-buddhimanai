---
name: brand-design
description: Generate, preview, and apply a brand color palette (plus typography, gradients, and tone/voice) to a frontend project. Use when a user says "pick brand colors", "choose a color palette", "brand design", "generate a palette", "theme this project", "what colors should I use", "brand identity", "design my brand", "set up brand colors", "time to build the frontend", "let's start the UI", "make this look branded", or any time a project is about to start frontend work and has no brand.md yet. Presents 6 candidate palettes as a visual HTML preview opened in the user's browser, supports an infinite regenerate loop until the user is satisfied, then writes the chosen palette to CSS variables (light + dark), wires up typography, derives brand gradients, and writes brand.md for future reference.
---

# Brand Design

## What This Does

Runs the full brand pass — palette, typography, gradients, and tone/voice — for any web project. Takes an unthemed or generic-looking project and gives it a real visual identity. Pairs with `frontend-design-guidelines` (which reads the `brand.md` this skill writes).

## When to Use This Skill

- Starting frontend work on a newly scaffolded project with no `brand.md`
- Explicitly asking for brand colors, a palette, or a theme
- Asking what the app "should look like" before any UI code is written
- Unhappy with a stock neutral theme and wanting personality

If the user is already deep in component work, **do not interrupt** to run this skill unless they ask.

## Prerequisites Check

Before starting, read the project to ground the interview:

1. `brand.md` in project root — **three cases**:

   **Case A — `brand.md` does not exist**: first-time setup. Proceed normally.

   **Case B — `brand.md` exists with `Status: deferred`**: treat as first-time setup. Tell the user:

   > Picking up where we left off. You deferred brand setup earlier — I'll run the full interview now and replace the placeholder with your choices.

   **Case C — `brand.md` exists with a real palette**: show the current palette name and ask before overwriting.

## Workflow

### Step 1 — Context Interview

Ask four short questions. Do not skip. Do not combine. Wait for answers.

1. **Product name + one line of what it does**

2. **Category** — pick one or closest match:
   `saas/b2b` · `consumer/social` · `developer-tools` · `analytics/data` · `ai/tech` · `creative/agency` · `e-commerce` · `productivity`

3. **Mood** — pick 1 or 2:
   `minimal` · `bold` · `warm` · `calm` · `playful` · `serious` · `technical` · `premium`

4. **Reference brands** — "Any existing apps or sites you like the feel of? (Optional)"

### Step 2 — Generate 6 Candidate Palettes

Using the answers from Step 1:

- Read `references/palette-recipes.md` and select **3 curated palettes** that match the category × mood intersection.
- Read `references/palette-generation.md` and generate **3 algorithmic variants**.
- For each palette, compute the full CSS token set (both light and dark mode).
- Verify every palette against `references/contrast-rules.md`. If any token pair fails WCAG AA, auto-adjust lightness until it passes.

Each candidate palette has:
- A short memorable name (two words, e.g., "Midnight Signal", "Forest Green")
- A one-line vibe (e.g., "tech · serious · trustworthy")
- 5 seed colors in OKLCH + hex: `bg-base`, `bg-elevated`, `primary`, `primary-soft`, `fg-base`
- Derived full token set for light mode and dark mode

### Step 3 — Render the HTML Preview

Read `references/html-preview.md` for the exact HTML template and contextual mini-UI templates. The preview shows the SAME mini-UI rendered 6 times with 6 different palette tokens.

**Pick the contextual mini-UI template** based on the interview's category:
- `dashboard` — for analytics, data tools, observability
- `saas` — for SaaS apps, productivity tools, B2B
- `social` — for feeds, chat, community
- `ecommerce` — for shops, marketplaces, checkout
- `developer` — for developer tools, APIs, consoles
- `generic` — fallback

Write the filled template to `<project-root>/.brand-preview/index.html` and open it:

```bash
# macOS
open .brand-preview/index.html
# linux
xdg-open .brand-preview/index.html 2>/dev/null
# windows
start .brand-preview/index.html
```

Tell the user the options: pick one (1-6), ask for variations, or reject all.

### Step 4 — Regenerate Loop

Parse the user's next message:
- Number 1-6 or "I like N" → **Pick** that palette, go to Step 5
- "More like N" / "variations of N" → regenerate 6 variants around palette N
- "More [adjective]" → adjust mood, back to Step 2
- "None" / "start over" → go back to Step 1 question 3 (mood)

**There is no iteration limit.** If the user quits, stop cleanly.

### Step 5 — Apply the Chosen Palette

1. **Back up** `app/globals.css` to `app/globals.css.bak`.
2. **Write the new CSS tokens** under `:root` (light) and `.dark` (dark). Only replace the token block, preserve the rest.
3. **Run a sanity check**: print the 4 most important tokens so the user can see the change.

### Step 6 — Typography Pairing

1. Read `references/typography-pairings.md` and look up the 6 candidate font pairs for the chosen palette's mood.
2. Read `references/typography-preview.md` for the HTML template.
3. Write to `<project-root>/.brand-preview/typography.html` and open it.
4. Wait for the user's response: pick 1-6, or say "skip" to default to Inter + JetBrains Mono.
5. Wire the chosen pair into the project (via CSS variables or font imports).

### Step 7 — Gradient Derivation (Optional)

Ask: "Want brand gradients too? I'll derive 2 — a subtle background gradient and a brand accent gradient. (y/n)"

If yes, read `references/gradient-recipes.md` and derive both. Write them as CSS variables.

### Step 8 — Write brand.md

Read `references/brand-md-template.md` and write the filled template to the project root as `brand.md`. This file contains: palette, typography, gradients, tone/voice, and usage dos-and-don'ts.

### Step 9 — Close the Loop

Tell the user:
- What was applied (palette name, typography, gradients if any)
- Where the backup went
- How to run the dev server to see the result
- That `.brand-preview/` can be deleted once happy
- That `brand.md` has been written and will be read by `frontend-design-guidelines`

## Non-Negotiables

1. **Every palette passes WCAG AA.** Body text 4.5:1, large text/icons 3:1. Auto-adjust or drop.
2. **Light and dark from the same seeds.** Same brand at different times of day.
3. **OKLCH, not HSL.** All seed definitions and derivations use OKLCH. Hex is for display only.
4. **Back up before applying.** Always copy `globals.css` → `globals.css.bak`.
5. **No external scraping at runtime.** All palette sources are inside the skill's own references.
6. **The regenerate loop has no hard limit.** User decides when satisfied.
7. **Confirm before overwriting an existing `brand.md`.**

## Final Checklist

- [ ] User explicitly picked a palette
- [ ] All tokens derived for both light and dark mode
- [ ] Contrast verified on every foreground/background pair (AA pass)
- [ ] CSS backup exists
- [ ] `brand.md` written to project root

## References

- [references/palette-recipes.md](references/palette-recipes.md)
- [references/palette-generation.md](references/palette-generation.md)
- [references/contrast-rules.md](references/contrast-rules.md)
- [references/html-preview.md](references/html-preview.md)
- [references/typography-pairings.md](references/typography-pairings.md)
- [references/typography-preview.md](references/typography-preview.md)
- [references/gradient-recipes.md](references/gradient-recipes.md)
- [references/brand-md-template.md](references/brand-md-template.md)

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
