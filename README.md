#  skillset-buddhimanai

A classroom-ready library of **94 AI agent skills** organized into 11 focused categories. Each skill teaches an AI coding agent (Claude Code, Cursor, GitHub Copilot, etc.) how to reliably execute a specific engineering, design, or operational task.

All skills are **generic and reusable** — . Each skill includes clear trigger phrases to support reliable agent routing.

The catalog is curated to be **managed, session-ready, and explicit**: category-level guidance is listed alongside task-specific skills, repository counts are accurate, and category boundaries are clearly defined.

---

## 📦 Skill Count by Category

| Folder | Skills | Purpose |
|--------|-------:|---------|
| `engineering/` | 13 | Core software engineering practices |
| `process/` | 10 | Development workflows & methodologies |
| `infra/` | 12 | Cloud, deployment & runtime infrastructure |
| `growth-ops/` | 10 | Analytics, marketing ops & developer tools |
| `design/` | 8 | Visual identity & design systems |
| `docs/` | 9 | Documentation, research & knowledge management |
| `media/` | 7 | AI-generated media & video production |
| `agent-dev/` | 8 | AI agent frameworks & SDK integration |
| `content/` | 5 | Pitch decks, copy & content creation |
| `ui-ux/` | 6 | Interface design & frontend implementation |
| `testing/` | 6 | Quality audits, E2E & performance testing |
| **Total** | **94** | |

---

## 🗂️ Skills Index

### `engineering/` — Software Engineering Practices
*Source: [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) (MIT)*

| Skill | Description |
|-------|-------------|
| `api-and-interface-design` | Design clean, consistent APIs, SDKs, and interface contracts |
| `browser-testing-with-devtools` | Browser automation and DevTools-based debugging patterns |
| `ci-cd-and-automation` | CI/CD pipeline design, GitHub Actions, deployment automation |
| `code-review-and-quality` | Structured code review checklists and quality gates |
| `code-simplification` | Refactoring patterns to reduce complexity without losing meaning |
| `database-design` | **NEW** — Relational schema modeling, normalization, FK indexes, and migrations |
| `debugging-and-error-recovery` | Systematic debugging methodology and error recovery playbooks |
| `deprecation-and-migration` | Safe deprecation strategies and migration guides |
| `observability-and-instrumentation` | Logging, tracing, metrics, and alerting patterns |
| `performance-optimization` | Frontend and backend performance profiling and optimization |
| `security-and-hardening` | Security best practices, hardening checklists, threat review |
| `test-driven-development` | TDD workflow: red → green → refactor with real examples |
| `typescript-patterns` | **NEW** — Advanced TypeScript: generics, discriminated unions, branded types, utility types |

---

### `process/` — Development Workflows
*Source: [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) (MIT)*

| Skill | Description |
|-------|-------------|
| `code-archaeology` | **NEW** — Navigate, map, and safely modify unfamiliar or legacy codebases |
| `context-engineering` | Structuring context and prompts for maximum AI agent effectiveness |
| `doubt-driven-development` | Surface and resolve hidden assumptions before building |
| `git-workflow-and-versioning` | Branch strategy, commit conventions, PR and merge best practices |
| `incremental-implementation` | Ship in small safe increments: plan → slice → build → verify |
| `interview-me` | Structured interview simulation and Q&A role-play |
| `planning-and-task-breakdown` | Break vague goals into concrete, estimable task lists |
| `shipping-and-launch` | Production launch checklist: readiness, monitoring, rollback |
| `source-driven-development` | Keep code in sync with its authoritative source of truth |
| `spec-driven-development` | Write the spec first, build to the spec, verify against the spec |

---

### `infra/` — Cloud, Deployment & Runtime Infrastructure
*Source: [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) (MIT)*

| Skill | Description |
|-------|-------------|
| `aws-account-management` | AWS account setup, IAM, billing, and multi-account strategy |
| `aws-harness` | AWS test harness patterns for integration and E2E testing |
| `bun` | Bun runtime: installation, bundling, test runner, and scripts |
| `cloudflare` | Cloudflare Workers, Pages, R2, D1, and edge deployment patterns |
| `docker` | **NEW** — Dockerfiles, multi-stage builds, Compose, and container security |
| `local-llm-router` | Route AI requests between local LLMs (Ollama, LM Studio) and cloud APIs |
| `mongodb` | MongoDB schema design, queries, aggregations, and Atlas setup |
| `postgres` | **NEW** — PostgreSQL schema design, indexing, queries, EXPLAIN ANALYZE, migrations |
| `railway` | Railway deployment: config, environment variables, and scaling |
| `redis` | **NEW** — Redis caching patterns, pub/sub, sessions, rate limiting, distributed locks |
| `supabase` | **NEW** — Supabase backend: auth, database, storage, realtime, edge functions |
| `vercel` | Vercel deployment: Next.js, Edge Functions, env config, and domains |

---

### `growth-ops/` — Analytics, Marketing Ops & Developer Tools
*Source: [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) (MIT)*

| Skill | Description |
|-------|-------------|
| `analytics-metrics` | Build KPI dashboards and data visualization with Recharts |
| `copilot-docs` | Structure and generate AI-readable project documentation |
| `github-trending` | Monitor and act on GitHub trending repositories and developers |
| `google-workspace-cli` | Automate Google Docs, Sheets, and Drive via CLI and APIs |
| `mermaid-diagrams` | Generate architecture diagrams and flowcharts with Mermaid |
| `meta-ads` | Meta (Facebook/Instagram) Ads campaign structure and automation |
| `owasp-security` | OWASP Top 10 security checklist for web applications |
| `product-hunt` | **NEW** — Product Hunt launch: 30-day plan, assets, maker comment, outreach strategy |
| `seo-optimization` | **NEW** — Technical SEO: meta tags, Core Web Vitals, structured data, sitemaps |
| `x-twitter-scraper` | X/Twitter data extraction — provided as a teaching example; review platform terms before use |

---

### `design/` — Visual Identity & Design Systems
*Sources: [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) · [sendaifun/solana-new](https://github.com/sendaifun/solana-new) · [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) (all MIT)*

| Skill | Description |
|-------|-------------|
| `brand-design` | Full brand pass — palette, typography, gradients, and tone/voice |
| `cinematic-scrub-landing` | Scroll-driven cinematic landing pages with video scrub effects |
| `design-taste` | Design judgment, generic-output review, and visual direction |
| `figma` | Figma workflows: component building, auto-layout, and handoff |
| `mobile-responsiveness` | Mobile-first responsive design patterns and breakpoint strategy |
| `parallax-landing-page` | Parallax scroll effects for high-impact landing pages |
| `ux-design-systems` | Design system architecture: tokens, components, and governance |
| *(category-guidelines)* `Premium Brand & Visual Design Guidelines` | Brand system direction for palette, icons, motion, and layout consistency |

---

### `docs/` — Documentation, Research & Knowledge Management
*Sources: [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) · [sendaifun/solana-new](https://github.com/sendaifun/solana-new) (both MIT)*

| Skill | Description |
|-------|-------------|
| `competitive-landscape` | Map competitors, substitutes, and dead projects for any idea |
| *(category-guidelines)* `Formal Document Writing & Editing Guidelines` | Executive-grade document structure, citation, formatting, and editorial consistency |
| `documentation-and-adrs` | Write ADRs, READMEs, and technical documentation |
| `idea-refine` | Sharpen rough ideas into well-defined problem statements |
| `navigate-skills` | Meta-skill: browse this skill catalog to find the right tool |
| `project-learnings` | Capture, search, prune, and export cross-session project learnings |
| `user-research` | **NEW** — User interviews, usability testing, synthesis, and research reports |
| `using-agent-skills` | How to install, invoke, and compose agent skills |
| `validate-idea` | Structured validation sprint: go/no-go recommendation with evidence |

---

### `media/` — AI-Generated Media & Video Production
*Sources: [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) · [sendaifun/solana-new](https://github.com/sendaifun/solana-new) (both MIT)*

| Skill | Description |
|-------|-------------|
| `director` | Coordinate scripts, scenes, and renders for AI-assisted video production |
| `fal-ai` | fal.ai image and video generation API integration |
| `image-master` | Advanced image generation, editing, and batch processing |
| `nano-banana-pro` | Lightweight AI media pipeline for quick content generation |
| `video-director` | Multi-engine video pipeline: GSAP + Lottie + ManimCE + HyperFrames |
| `video-edit` | Video editing automation: transcription, captions, and cuts |
| `video-to-landing-page` | Convert product demo videos into structured landing pages |

---

### `agent-dev/` — AI Agent Frameworks & SDK Integration
*Source: [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) (MIT)*

| Skill | Description |
|-------|-------------|
| `aws-strands` | AWS Strands agent framework: build, deploy, and chain agents |
| `copilot-sdk` | GitHub Copilot Extensions SDK and agent development |
| `crewai` | **NEW** — CrewAI multi-agent orchestration: roles, tasks, tools, and process flows |
| `honest-agent` | Build agents with strict honesty constraints and uncertainty disclosure |
| `langchain` | LangChain: chains, agents, memory, and tool integration |
| `mcp-server` | **NEW** — Build MCP servers: tools, resources, and prompts for AI models |
| `openai-sdk` | **NEW** — OpenAI API: completions, streaming, structured outputs, tool calling, embeddings |
| `pydantic-ai` | **NEW** — PydanticAI: type-safe agents, structured outputs, and dependency injection |

---

### `content/` — Pitch Decks, Copy & Content Creation
*Sources: [sendaifun/solana-new](https://github.com/sendaifun/solana-new) · [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) (both MIT)*

| Skill | Description |
|-------|-------------|
| `create-pitch-deck` | Build investor-ready pitch decks using Sequoia/YC/a16z frameworks |
| `marketing-video` | Product marketing videos with Remotion (code-driven) |
| `technical-blog` | **NEW** — Write engineering posts: structure, hooks, code examples, SEO basics |
| `video-craft` | Frame-level visual composition for Remotion video scenes |
| *(category-guidelines)* `Premium UX Writing & Product Copy Guidelines` | Product copy standards for microcopy, onboarding, error states, and tone control |

---

### `ui-ux/` — Interface Design & Frontend Implementation
*Sources: [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) · [sendaifun/solana-new](https://github.com/sendaifun/solana-new) · [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) (all MIT)*

| Skill | Description |
|-------|-------------|
| `dark-mode` | **NEW** — CSS variable theming, system preference detection, SSR flash prevention |
| `frontend-design-guidelines` | Comprehensive UI rules: a11y, states, forms, dark mode, tokens |
| `frontend-ui-engineering` | React/Next.js component patterns, Tailwind, and shadcn/ui |
| `page-load-animations` | Framer-motion recipes: stagger, spring, page choreography |
| `state-management` | **NEW** — TanStack Query, Zustand, URL state, and Context decision tree |
| `web-accessibility` | WCAG 2.1 checklist, ARIA patterns, and accessible component recipes |

---

### `testing/` — Quality Audits & Security Reviews
*Source: [sendaifun/solana-new](https://github.com/sendaifun/solana-new) (MIT)*

| Skill | Description |
|-------|-------------|
| `e2e-testing` | **NEW** — Playwright E2E: page objects, auth setup, CI integration, visual regression |
| `load-testing` | **NEW** — k6 and Locust: load scenarios, p95/p99 metrics, capacity planning |
| *(category-guidelines)* `Application Security & Auth QA Testing Guidelines` | Security-focused QA coverage for auth, sessions, and critical application flows |
| `product-review` | Structured product quality evaluation across 8 UX dimensions |
| `roast-my-product` | Direct product critique to surface weaknesses before release |
| `security-audit` | CSO-mode: secrets archaeology, OWASP Top 10, STRIDE threat model |

---

## 🔗 Common Skill Flows

```
Idea → Pitch:
  validate-idea → competitive-landscape → create-pitch-deck

Design → Build:
  brand-design → frontend-design-guidelines → page-load-animations

Ship → Harden:
  code-review-and-quality → security-audit → shipping-and-launch

Plan → Code → Review:
  planning-and-task-breakdown → spec-driven-development → incremental-implementation → code-review-and-quality

Video Production:
  marketing-video → video-craft → video-director (advanced)

AI Agent Pipeline:
  openai-sdk → langchain → crewai (multi-agent) → mcp-server (expose tools)

Full-Stack App:
  database-design → postgres → supabase → docker → vercel

Launch:
  product-review → seo-optimization → product-hunt

New Codebase:
  code-archaeology → typescript-patterns → e2e-testing
```

---

## ⚠️ Deliberate Exclusions

The following items were **intentionally excluded**:

| Excluded | Reason |
|----------|--------|
| All `yuv-*` skills (yuv-decks, yuv-design-system, yuv-pilot, yuv-reel-covers, yuv-video-director, yuv-viral-video) | Individually branded and not suitable for a general-purpose public catalog |
| `shabbat-times` | Too narrow in scope for a general-purpose skill library |
| All blockchain/DeFi/crypto skills from solana-new | Domain-specific and outside the generic-library scope |

The engine-routing logic from `yuv-video-director` (GSAP/Lottie/ManimCE/HyperFrames) was generalized into `media/video-director`, with personal branding removed.

---

## 📋 How to Use a Skill

1. **Install** — Copy the desired skill folder into your project's `.claude/skills/` or `.github/skills/`
2. **Trigger** — Invoke the skill using any of the trigger phrases listed in its `description` frontmatter
3. **Compose** — Combine skills to support larger multi-step workflows

```bash
# Copy a skill to your project
cp -r skills/engineering/test-driven-development .claude/skills/

# Or install all skills at once (for a comprehensive agent workspace)
cp -r skills/ .claude/skills/
```

---

## 📜 License & Attribution

All skills are MIT-licensed. Original sources:
- [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) — generic engineering skills
- [sendaifun/solana-new](https://github.com/sendaifun/solana-new) — product/design skills (adapted, crypto content removed)
- [hoodini/ai-agents-skills](https://github.com/hoodini/ai-agents-skills) — infra, media, and growth ops skills
