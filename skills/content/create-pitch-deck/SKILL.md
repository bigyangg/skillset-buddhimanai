---
name: create-pitch-deck
description: Create a structured pitch deck for any product or startup. Use when a user says "create a pitch deck", "help me pitch", "I need slides", "prepare for a demo", "investor presentation", "build slides for my product", or "grant application". Reads idea-context.md and product context files from prior phases if available.
---

# Create Pitch Deck

## What This Does

Generate a compelling, investor-ready pitch deck using proven frameworks from Sequoia, YC, a16z, and Guy Kawasaki — adapted for any product or startup. Produces a polished HTML artifact with slide content, speaking notes, narrative arc, and audience-specific tailoring.

This skill doesn't just generate slides — it coaches you through building a narrative that lands.

## Core Principles

Judges and investors review hundreds of submissions quickly and often skim for the essentials. Capture attention in the first 30 seconds with a punchy one-liner using a sharp, relatable analogy that instantly clarifies the idea. Follow immediately with a crisp demo or proof of value. Structure the deck as a simple narrative: clearly explain the problem, the innovative solution, and the business model. Keep everything short, visually polished, aesthetic, and easy to scan. Clarity and impact win every time.

## Non-Negotiables

- **Never skip the interview.** Don't auto-generate from context alone. Every pitch needs a human conversation to find the real story.
- **Stay blunt.** If their "problem" is vague, push back. If their "why now" is weak, say so.
- **Problem first, always.**
- **One idea per slide.** (YC rule)
- **Real numbers only.** Mark unknowns as "TBD".
- **The ask must be specific.** Amount + instrument + use + timeline + milestone.
- **Always generate the HTML artifact.**
- **3 questions max per message.** Conversational rounds, not a questionnaire dump.

## Workflow

### Phase 1: Context Gathering (Silent)

Before asking anything, read what exists:
- `idea-context.md`
- `build-context.md` or `product-spec.md`
- `brand.md` for visual direction

### Phase 2: Deep Interview

**Round 1 — The Story:**

1. "Explain what you do to me like I'm someone you met at a coffee shop — no jargon."
2. "What personal experience made you want to build this? Or who told you the problem existed?"
3. "What's the most surprising thing a user or potential customer has told you?"

**Round 2 — The Evidence:**

4. "What's your single strongest proof that this works?" (users, revenue, pilots, waitlists, testimonials)
5. "What changed in the last 12 months that makes this possible NOW? What's your Why Now?"
6. "What breaks in your product if you remove the [core technology/unique approach]?"

**Round 3 — The Audience & Ask:**

7. "Who exactly will you be presenting this to?" (investors / accelerator / grant committee / demo day)
8. "What's the specific outcome you want? Be precise." (raise amount + instrument + milestone)
9. "What's the question you're most afraid someone will ask?"

**Round 4 — Taste & Positioning:**

10. "Do you have a preferred writing tone? Lowercase and casual, or polished and corporate?"
11. "Have you seen a pitch or deck you loved? Any industry."
12. "Do you have brand colors? If not, what's the vibe — corporate and trustworthy, or bold and consumer?"

### Phase 3: Select Narrative Framework

Based on the interview, choose the strongest storytelling approach. Read [references/storytelling-frameworks.md](references/storytelling-frameworks.md).

| Framework | Best When |
|-----------|-----------|
| **PAS** (Problem-Agitate-Solve) | Strong problem, weak traction |
| **6-Part Investor Arc** | Raising funding, strong "why now" |
| **Before-After-Bridge** | Strong demo, visual transformation |
| **Hero's Journey** | User-centric product, strong testimonials |
| **Pixar Framework** | Complex technical product |

**Default:** 6-Part Investor Arc for VCs/accelerators, PAS for demo days, Before-After-Bridge for product-led pitches.

### Phase 4: Build the Deck

Read ALL reference files:
- [references/pitch-structure.md](references/pitch-structure.md) — Slide-by-slide framework
- [references/pitch-mistakes.md](references/pitch-mistakes.md) — Common antipatterns that kill pitches
- [references/investor-audience-guide.md](references/investor-audience-guide.md) — Audience-specific tailoring
- [references/storytelling-frameworks.md](references/storytelling-frameworks.md)

For each slide: write content, add speaking notes, add objection prep for the 3 hardest slides (Problem, Traction, Ask).

### Phase 5: Score & Strengthen

Self-score the deck against the audience rubric. Flag 🟢 Strong / 🟡 Moderate / 🔴 Weak areas with specific improvements.

### Phase 6: Generate HTML Artifact

Write a polished HTML pitch deck to `pitch-deck-YYYYMMDD-HHMMSS.html` in the project root.

**Audience-specific slide selection:**
- Demo day: Title → Problem → Solution → Demo → Traction → Ask → Contact (7-8 slides)
- VC: All 13 slides + optional Roadmap/Financials
- Grant: Title → Problem → Solution → Demo → Impact → Traction → Ask → Contact
- Accelerator: Title → Problem → Solution → Demo → Traction → Team → Ask → Contact

**Design rules (enforced):**
- One idea per slide
- 30pt minimum font equivalent
- Max 6 words per bullet, max 6 bullets per slide
- Visuals > text
- Whitespace is confidence

### Phase 7: Objection Prep

After generating, provide a Q&A Prep Sheet covering:
1. The user's self-identified "hardest question"
2. Standard investor objections for their category
3. Product-specific challenges: "Why not just use [existing solution]?", "What happens when [market condition changes]?", "Why are you the right team?"

## Framework Credits

- **Sequoia Capital** — The 12-slide standard
- **Y Combinator** — Radical simplicity, one idea per slide
- **Guy Kawasaki** — 10/20/30 rule
- **a16z** — Pitch guidance and frameworks
- **Pixar** — Narrative structure for complex products

## Resources

- [references/pitch-structure.md](references/pitch-structure.md)
- [references/storytelling-frameworks.md](references/storytelling-frameworks.md)
- [references/pitch-mistakes.md](references/pitch-mistakes.md)
- [references/investor-audience-guide.md](references/investor-audience-guide.md)
- [references/deck-design-system.md](references/deck-design-system.md)
- [references/slide-templates.md](references/slide-templates.md)

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
