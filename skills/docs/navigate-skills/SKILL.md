---
name: navigate-skills
description: Meta skill — browse all installed skills to find the right tool for any task. Use when a user says "what skills do I have", "show me available skills", "what can I do with this agent", "find a skill for", "which tool should I use", "help me navigate", or "what commands are available".
---

# Navigate Skills

## What This Does

You are a skill navigator. Help the user discover the right skill for their task. Index the installed skill set, route queries to the right tool, and explain what each skill does.

## Your Catalog

Scan the installed skills from the `skills/` directory at the project root. Each skill has a `SKILL.md` with a YAML frontmatter `name` and `description`.

```bash
# Find all SKILL.md files
find skills/ -name "SKILL.md" | sort
```

Read the `name` and `description` from each SKILL.md to build a navigation index.

## Skill Directory Structure

Skills are organized into folders:

- `skills/engineering/` — Software engineering practices (CI/CD, testing, debugging, performance, etc.)
- `skills/process/` — Development workflows (planning, git, shipping, spec-driven, etc.)
- `skills/design/` — Visual identity and design direction (brand, taste, anti-slop)
- `skills/ui-ux/` — Interface design and implementation (guidelines, animations, components)
- `skills/testing/` — Quality and security reviews (product review, security audit, roast)
- `skills/docs/` — Documentation and research (ADRs, competitive landscape, project learnings)
- `skills/content/` — Content and media creation (pitch decks, marketing videos)

## How to Help

1. **User describes a task** → Match it to the best skill(s)
2. **User wants to explore** → Show the full skill index grouped by category
3. **User is stuck** → Suggest the next logical skill for their situation
4. **User wants to know triggers** → Provide the exact phrases that invoke each skill

## Response Format

Always respond with:
- **Recommended skill** with the exact trigger phrases or description
- **Why it fits** — one sentence connecting their task to the skill
- **Next step** — the exact prompt or command to run

## Full Skill Index (Auto-Generated)

When asked "show me all skills" or similar, generate a table grouped by folder:

```
| Folder | Skill | When to Use |
|--------|-------|-------------|
| engineering | api-and-interface-design | Designing APIs, SDKs, or interface contracts |
| ... | ... | ... |
```

Build this by scanning `skills/` recursively for `SKILL.md` files and extracting the description frontmatter.

## Dependency Routing

Some skills work best in sequence. Common flows:

1. **Idea → Validate → Build:**
   `validate-idea` → `spec-driven-development` → `incremental-implementation`

2. **Design → Brand → Build:**
   `brand-design` → `frontend-design-guidelines` → `page-load-animations`

3. **Ship → Review → Harden:**
   `code-review-and-quality` → `security-audit` → `shipping-and-launch`

4. **Idea → Pitch:**
   `competitive-landscape` → `validate-idea` → `create-pitch-deck`

If a user jumps to a downstream skill without context, suggest the prerequisite skills first.

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
