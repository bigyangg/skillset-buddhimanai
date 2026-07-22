---
name: validate-idea
description: Run a structured validation sprint on a startup idea. Use when a user says "validate this idea", "is this worth building", "run a validation sprint", "help me test demand", "should I build this", or "stress-test my idea". Works for any product or startup concept.
---

# Validate Idea

## What This Does

Take an idea and stress-test it with a structured validation sprint. Produce a go/no-go recommendation backed by demand signals, risk analysis, and a concrete next-steps plan. Works for any product or startup concept — not limited to any specific industry.

## Workflow

1. Check for `idea-context.md` in the workspace. If found, load the chosen idea. If not, ask the user to describe their idea.
2. Read [references/validation-framework.md](references/validation-framework.md) for the sprint structure.
3. Evaluate demand signals using [references/customer-signal-rubric.md](references/customer-signal-rubric.md).
4. Run the technology necessity gut-check: "What breaks if you remove the [key technology/approach]?"
5. Map risks: technical, market, regulatory, team.
6. Apply [references/pivot-or-persist.md](references/pivot-or-persist.md) to reach a go/no-go decision.
7. Write a local HTML artifact with the validation report.

## Non-Negotiables

- Do not rubber-stamp. If the idea is weak, say so with specifics.
- Every "go" recommendation must cite at least 2 concrete demand signals (not vibes).
- Every "no-go" must include a pivot suggestion, not just rejection.
- If the user has no evidence of demand, the answer is "go validate" with a specific sprint plan, not "go build".
- Always check if there is already a live product solving the same problem for the same audience.
- Always write a local HTML artifact. Do not leave results only in chat.
- **Build-vs-integrate assessment**: As part of validation, identify whether the idea can be built by integrating existing tools/platforms rather than building from scratch. If integration is viable, note it as an advantage. If the idea requires novel technical work, custom development is the right call — don't penalize it.

## Phase Handoff

After completing validation:

1. Write/update `idea-context.md` (create if missing) with a `validation` field containing:
   - `demand_signals`: array of evidence items
   - `risks`: array of { category, description, severity }
   - `go_no_go`: "go" | "no-go" | "pivot"
   - `confidence`: 0.0 - 1.0
   - `next_steps`: array of concrete actions
2. Tell the user they can proceed to the Build phase if the verdict is "go".

## Decision Points

- **No idea yet?** Ask the user to describe their idea before starting.
- **Go vs. no-go threshold:** Score ≥ 8/15 across founder-fit + MVP-speed + distribution + market-pull + revenue = Go. Below 6 = strong No-go.
- **No demand evidence?** The sprint is "go validate" — give specific experiments targeting the weakest assumption.

## Resources

- [references/validation-framework.md](references/validation-framework.md)
- [references/customer-signal-rubric.md](references/customer-signal-rubric.md)
- [references/pivot-or-persist.md](references/pivot-or-persist.md)

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
