---
name: competitive-landscape
description: Map the competitive landscape for any product idea. Use when a user says "who are my competitors", "map the competitive landscape", "what exists in this space", "show me similar products", "competitive analysis", or "find comparable tools". Works for any industry or product category.
---

# Competitive Landscape

## What This Does

Map every relevant competitor, substitute, and adjacent product for a given idea. Produce a landscape matrix showing where the opportunities and dangers are. Works for any product idea — software, SaaS, mobile, developer tools, consumer apps, etc.

## Workflow

1. Ask the user what space or product idea to analyze (or read from any existing product spec/idea document in the project).
2. Read [references/landscape-mapping.md](references/landscape-mapping.md) for the mapping methodology.
3. Assess defensibility using [references/moat-analysis.md](references/moat-analysis.md).
4. Do fresh web research: industry blogs, GitHub, product review sites, app directories.
5. Produce a landscape HTML artifact with the full competitive matrix.

## Non-Negotiables

- Always search broadly first — if a tool already exists, the user needs to know.
- Include dead/failed projects, not just live ones. Failures reveal landmines.
- Distinguish between "competitors" (same problem, same user) and "substitutes" (same problem, different approach).
- Include at least one non-technical/manual substitute if applicable (e.g., "spreadsheet" or "email" as a substitute for a SaaS tool).
- Do not declare "no competition" unless you've exhausted all search paths.
- Rate crowdedness honestly: empty / sparse / moderate / crowded / saturated.
- Always write a local HTML artifact.

## Phase Handoff

After mapping the landscape:

1. Write/update `idea-context.md` (create if missing) with a `landscape` field containing:
   - `direct_competitors`: array of { name, url, status, strength, weakness }
   - `substitutes`: array of { name, approach, why_users_stay }
   - `dead_projects`: array of { name, why_failed }
   - `crowdedness`: "empty" | "sparse" | "moderate" | "crowded" | "saturated"
   - `moat_type`: identified moat category
   - `differentiation`: recommended angle

## Decision Points

- **Saturated market?** If crowdedness = "saturated" (>5 direct competitors), look for underserved niche.
- **Which data sources?** Use Product Hunt, GitHub, App Store, G2, Capterra, or web search for product data. Use SimilarWeb or Ahrefs for traffic data if available.
- **Dead project found?** Check WHY it died — technical failure, market timing, or team issues. The idea might still be valid.

## Resources

- [references/landscape-mapping.md](references/landscape-mapping.md)
- [references/moat-analysis.md](references/moat-analysis.md)

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
