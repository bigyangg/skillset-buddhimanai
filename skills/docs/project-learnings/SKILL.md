---
name: project-learnings
description: |
  Manage project learnings across sessions. Review, search, prune, and export what
  the agent has learned about this project. Use when asked "what have we learned",
  "show learnings", "prune stale learnings", "export learnings", "remember this",
  or "what patterns have we found". Proactively suggest when the user wonders
  "didn't we fix this before?" or "what did we decide about X?".
---

# Project Learnings

## What This Does

Manage the project's knowledge base across sessions. Learnings are stored in a single human-readable markdown file at `.learnings.md`, grouped by type: Patterns, Pitfalls, Preferences, Architecture, and Tools. Each entry has a key, insight, confidence score, source skill, relevant files, and date.

This skill is read/write for the learnings file ONLY. It never modifies project code.

## Commands

| Command | Action |
|---------|--------|
| `/learn` | Show recent learnings (last 20 entries across all types) |
| `/learn search <query>` | Search learnings by keyword |
| `/learn prune` | Check for stale or contradictory entries |
| `/learn export` | Format learnings for project documentation |
| `/learn stats` | Summary statistics |
| `/learn add` | Manually add a learning |

---

## Learnings File Format

File: `.learnings.md`

```markdown
# Project Learnings

> Append-only — latest entry wins on conflicts.

## Patterns

### auth-middleware-placement
- **Insight:** Auth middleware must be applied before route handlers, not after
- **Confidence:** 9/10
- **Source:** debugging-and-error-recovery
- **Files:** src/middleware/auth.ts
- **Date:** 2026-07-15

## Pitfalls

### test-db-isolation
- **Insight:** Tests must reset the database between runs or state bleeds across test suites
- **Confidence:** 8/10
- **Source:** test-driven-development
- **Files:** tests/setup.ts
- **Date:** 2026-07-15

## Preferences

(entries here)

## Architecture

(entries here)

## Tools

(entries here)
```

---

## Command Implementations

### Show Recent (`/learn`)

1. Read `.learnings.md`
2. If the file does not exist, respond: "No learnings yet. As you use skills, the agent captures patterns and insights automatically. Use `/learn add` to manually record something."
3. Parse all entries across all type sections
4. Display the 20 most recent entries (sorted by Date descending)
5. Show total count: "Showing 20 of N total learnings."

### Search (`/learn search <query>`)

1. Read `.learnings.md`
2. Search across all fields (key, insight, source, files) for the query string (case-insensitive)
3. Display all matching entries, grouped by type
4. If no matches: "No learnings match '{query}'. Try a broader search."

### Prune (`/learn prune`)

1. Read all entries from `.learnings.md`
2. For each entry with a **Files** field: check if the referenced files still exist. If not, flag as **stale**.
3. Check for contradictions: entries with the same key but different insights
4. For each flagged entry, ask the user:
   - **A) Remove** — delete the entry
   - **B) Keep** — mark as still valid (update date)
   - **C) Update** — provide a new insight
5. Apply changes and save

### Export (`/learn export`)

1. Read all entries from `.learnings.md`
2. Format as a clean markdown section for inclusion in CLAUDE.md or project docs
3. Ask the user: "Where should I put the exported learnings?" (append to CLAUDE.md / save to a standalone file / print only)

### Stats (`/learn stats`)

Compute and display a summary table: total entries by type, top source skill, average confidence, oldest/newest entry.

### Manual Add (`/learn add`)

1. Ask the user for each field one at a time: type, key, insight, confidence, files
2. Set source to `manual` and date to today
3. Append to `.learnings.md` under the correct type section
4. Confirm: "Added {type} learning '{key}' with {confidence}/10 confidence."

---

## How Other Skills Write Learnings

Other skills should append learnings by following this protocol:

1. **Read** `.learnings.md` — create with template header if missing
2. **Find the right section** — `## Patterns`, `## Pitfalls`, etc.
3. **Append a new entry** using the standard format
4. **De-duplication:** If the same key exists, append below it (latest wins)

**When to write a learning:**
- A non-obvious pattern was discovered
- A pitfall was hit and resolved (especially if it cost > 10 minutes)
- The user expressed a preference
- An architectural decision was made with trade-offs

**When NOT to write a learning:**
- Trivial or obvious information
- Temporary workarounds that will be removed
- Information already in the project's README

## Non-Negotiables

- **NEVER modify project code.** This skill manages learnings only.
- **Always confirm** before removing or modifying existing entries.
- **Append-only by default** — latest entry for a given key wins. Old entries are history.
- **No silent writes** — always confirm with the user what was added or changed.

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License). Original adapted from [gstack](https://github.com/garrytan/gstack) learn skill._
