---
name: code-archaeology
description: Navigate, understand, and safely modify unfamiliar or legacy codebases. Use when a user says "understand this codebase", "legacy code", "inherited project", "I don't know this code", "where does X happen", "trace this flow", "read someone else's code", "code archaeology", "explore this repo", or "understand before I change".
---

# Code Archaeology

## What This Does

Systematically explore, map, and safely modify an unfamiliar or legacy codebase. Provides a structured approach for understanding code you didn't write — without breaking it.

## Non-Negotiables

- **Read before you write.** Never modify code you don't understand. Always map first.
- **Follow the data, not the files.** Trace how data transforms from input to output — not directory structure.
- **Form hypotheses, test them.** Before assuming you understand a function, make a prediction and verify it.
- **Tests are documentation.** If tests exist, read them before the source code.
- **Leave breadcrumbs.** As you explore, add `// AG: [understanding]` comments to track what you've learned.

## Workflow

### Phase 1 — Orient (30 minutes)

Get the big picture before reading any code:

```bash
# 1. Read the README, CONTRIBUTING, and any ADRs
cat README.md
ls docs/ .adr/ decisions/ 2>/dev/null

# 2. Understand the directory structure
find . -type f -name "*.md" | head -20      # Documentation
ls -la                                        # Top-level layout

# 3. Read the package manifest for dependencies
cat package.json          # Node.js
cat pyproject.toml        # Python
cat go.mod                # Go
cat Cargo.toml            # Rust

# 4. Find entry points
grep -r "listen\|app\.run\|start\|main" --include="*.ts" -l | head -10
grep -r "export default\|module.exports" --include="*.ts" -l | head -20

# 5. Check git history for important context
git log --oneline -20                          # Recent changes
git log --oneline --all --graph | head -30     # Branch structure
git log --stat --oneline -5                    # What changed recently
```

**Deliverable:** A mental map of: entrypoints → major modules → data stores

### Phase 2 — Map the Data Flow

Follow data from the outside in:

```bash
# Trace an HTTP request
grep -r "router\|Route\|app.get\|app.post" --include="*.ts" -l | head -10
grep -r "@Get\|@Post\|@Controller" --include="*.ts" -l | head -10

# Find database access
grep -r "prisma\.\|db\.\|query\|findOne\|findMany" --include="*.ts" -l | head -10

# Find external API calls
grep -r "fetch\|axios\|httpClient\|request(" --include="*.ts" -l | head -10

# Find where secrets/config are used
grep -r "process\.env\|config\." --include="*.ts" -l | head -10
```

**Draw this diagram for the key flows:**
```
HTTP Request
    ↓
Router (routes/api.ts)
    ↓
Middleware (auth check, validation)
    ↓
Controller (controllers/userController.ts)
    ↓
Service (services/userService.ts)
    ↓
Repository (repositories/userRepository.ts)
    ↓
Database (Prisma → PostgreSQL)
```

### Phase 3 — Read the Tests First

```bash
# Find all test files
find . -name "*.test.ts" -o -name "*.spec.ts" | head -20
find . -name "*_test.go" | head -20
find . -name "test_*.py" | head -20

# Run tests to see what passes
npm test 2>&1 | tail -30
pytest -v 2>&1 | tail -30
go test ./... 2>&1 | tail -30
```

Tests reveal:
- **What the code is supposed to do** (intent)
- **What edge cases matter** (fragile paths)
- **What's NOT tested** (risky areas)

### Phase 4 — Trace One Complete Flow

Pick the most important user action and trace it completely:

```typescript
// Example: "User creates an order"

// Step 1: Find the API endpoint
// → grep for "order" in routes/controllers

// Step 2: Read the controller
// → understand: what does it validate? what does it call?

// Step 3: Read the service it calls
// → understand: what business logic runs? what can fail?

// Step 4: Read the repository/database layer
// → understand: what gets persisted? what are the transactions?

// Step 5: Read the tests for this flow
// → understand: what edge cases are covered? what's missing?
```

### Phase 5 — Identify Risk Zones

Before any modification, classify the code:

| Risk Level | Characteristics | Action |
|-----------|----------------|--------|
| 🔴 **High** | No tests, many callers, auth/payment/data logic, last touched 3+ years ago | Full test coverage before touching |
| 🟡 **Medium** | Some tests, few callers, recently touched | Add targeted tests, then modify |
| 🟢 **Low** | Good test coverage, 1–2 callers, utility/helper | Modify with care, verify tests pass |

```bash
# Find high-risk: heavily used functions
grep -r "functionName" --include="*.ts" -l | wc -l   # many callers = high risk

# Find untested code
npx c8 npm test   # Coverage report — find the red zones
```

### Phase 6 — Safe Modification

```
1. Write a test for the CURRENT behavior (even if the behavior is wrong)
2. Verify the test passes
3. Make the smallest possible change
4. Verify existing tests still pass
5. Add/update tests for your change
6. Code review with focus: "what did I miss?"
```

## Investigation Toolkit

```bash
# Who last changed this file?
git log --oneline -10 -- src/api/orders.ts

# What changed in a specific commit?
git show abc1234 --stat

# When was a function introduced?
git log -S "functionName" --source --all --oneline

# Who do I ask about this module?
git log --format="%ae" -- src/payments/ | sort | uniq -c | sort -rn | head -5

# Find all TODOs and FIXMEs
grep -r "TODO\|FIXME\|HACK\|XXX\|DEPRECATED" --include="*.ts" | head -20

# Find dead code candidates (rarely imported)
grep -r "export function" --include="*.ts" | wc -l   # total exports
# then check import counts for each

# Find circular dependencies
npx depcruise --include-only "^src" --output-type err src/
```

## Documentation as You Go

As you explore, maintain a running `ARCHAEOLOGY.md`:

```markdown
## Code Archaeology Notes — [Date]

### System Overview
- Entry: `src/server.ts` → Express app on port 3000
- Config: `src/config/index.ts` reads from env vars
- DB: Prisma ORM → PostgreSQL (dev: SQLite for tests)

### Key Flows
- Auth: JWT in `Authorization` header → `middleware/auth.ts` → `services/authService.ts`
- Orders: `POST /api/orders` → `OrderController.create` → `OrderService.createOrder` (validation here) → `OrderRepository.save`

### Surprises Found
- `UserService.getUser()` is called from 14 different places but only has 2 tests
- All payment logic is in `utils/stripe.ts` (not a service layer — risky)
- `config/database.ts` uses a singleton pattern that won't work in tests

### Risk Zones
- 🔴 `src/payments/` — no tests, business-critical
- 🔴 `src/auth/tokens.ts` — no tests, security-critical
- 🟢 `src/utils/formatters.ts` — well tested, pure functions

### Open Questions
- [ ] Why does `OrderService` have a `notify: boolean` param? What does it notify?
- [ ] Is `legacyUserMigration.ts` still needed?
```

## Red Flags in Legacy Code

| Pattern | Risk | Action |
|---------|------|--------|
| `// TODO: fix this` from 5+ years ago | Accepted technical debt | Understand why before touching |
| `// HACK:` comments | Workaround for something broken | Find the root cause |
| Long functions (100+ lines) | Complex, tangled logic | Understand completely before splitting |
| `any` TypeScript everywhere | Missing type safety | Don't add more `any` |
| No error handling on DB calls | Silent failures | Add before modifying |
| Circular imports | Architectural smell | Map dependencies before touching |
