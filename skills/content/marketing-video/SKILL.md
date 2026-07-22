---
name: marketing-video
description: Create marketing videos for any product using Remotion (code-driven) and AI video generation. Use when a user says "marketing video", "product video", "promo video", "create a video", "demo video", "Remotion project", "product demo animation", or "promotional content".
---

# Marketing Video — Remotion + AI Video

## What This Does

Create marketing videos for your product using two complementary approaches:
- **Remotion** (code-driven): Programmatic React videos — full control, reproducible, version-controlled.
- **AI video generation** (Renoise or similar): AI video generation — fast, creative, no code needed.

**Goal: Professional quality that doesn't look AI-generated.** Read [references/professional-quality-guide.md](references/professional-quality-guide.md) before starting any Remotion work.

## Non-Negotiables

- **Never skip the interview.** Don't guess the product, the audience, or the visual direction.
- **Never ask about animation technicalities.** The user doesn't care about spring configs. Ask about FEEL, not technique.
- **Stay opinionated.** If the user's idea for a video is weak (too long, wrong audience, no hook), say so.
- **Visual taste first, code second.** Establish what the video should FEEL like before writing a single line of code.
- **Gather elements proactively.** Ask for screenshots, logos, brand colors. If they don't have them, source SVG icons and suggest color palettes.

## Workflow

### Step 1: Context Gathering (Before Asking Anything)

Silently check for existing context:
- `idea-context.md` or `product-spec.md` → Product concept, audience, value prop
- `brand.md` → Colors, typography, tone
- `build-context.md` → Tech stack, features, deployment status

Auto-detect product assets:
```bash
find . -maxdepth 3 -type f \( -name "*.png" -o -name "*.jpg" -o -name "*.jpeg" -o -name "*.webp" \) ! -path "*/node_modules/*" ! -path "*/.git/*" 2>/dev/null | head -20
find . -maxdepth 3 -type f -name "*.svg" ! -path "*/node_modules/*" ! -path "*/.git/*" 2>/dev/null | head -20
```

If you find assets, mention them during the interview.

### Step 2: The Interview (Deep, Product-Focused)

**Round 1 — The Product (mandatory):**

1. "Explain your product to me like I'm a potential user, not a developer."
2. "Who specifically is going to watch this video? Not 'everyone' — give me one person."
3. "What's the single thing you want the viewer to DO after watching?"

**Round 2 — The Story (mandatory):**

4. "What problem does your product solve? Describe the pain — make me feel it."
5. "What's the 'aha moment' in your product? The thing that makes people go 'oh wait, that's actually cool.'"
6. "Do you have a number that would make someone stop scrolling?" (users, speed, savings, etc.)

**Round 3 — The Taste (mandatory):**

7. "Show me a video you love — from any brand, any industry."
8. "What vibe should this video have? Pick one: confident, urgent, playful, mysterious, authoritative, rebellious."
9. "Where will people see this? What platform?"

**Round 4 — The Assets (mandatory):**

10. "Do you have: a logo, brand colors, and a screenshot of your product? Share what you have."
11. "Do you have a preferred writing tone for text overlays? Lowercase and casual, or polished and proper?"
12. "Any specific text that MUST appear? Tagline, URL, metric?"

### Step 3: Creative Direction (You Decide, User Approves)

After the interview, produce the creative direction. Present a single confident recommendation.

**Output a Creative Brief that includes:**

1. **Video Concept** — One sentence
2. **Storytelling Framework** (from [references/video-storytelling.md](references/video-storytelling.md)):

   | Framework | Best For | Structure |
   |-----------|----------|-----------| 
   | **Hook-Proof-CTA** | Twitter, TikTok (15-30s) | Shocking metric → Demo → "Try it" |
   | **Problem-Demo-Result** | Product demos (30-90s) | Pain point → Solution → Outcome |
   | **Before/After** | Landing pages (30-60s) | Old way → New way |
   | **Speedrun** | Demo days (1-3min) | Full product walkthrough |
   | **Data Story** | Analytics/infra products | Animated metrics telling growth story |

3. **Visual Direction** — Color palette, typography, animation feel, frame archetype per scene
4. **Scene-by-Scene Storyboard** — Written out, frame by frame
5. **Approach Decision** — Remotion or AI generation, with reasoning

### Step 4: Element Gathering

- **Brand Assets:** Logo (SVG preferred), brand colors, brand fonts
- **Product Assets:** Screenshots at 2x resolution, screen recordings
- **SVG Icons:** Use Lucide React or Heroicons for animatable icons

### Step 5: Build (Remotion)

Read [references/remotion-quickstart.md](references/remotion-quickstart.md) for the full technical reference.

```bash
# Scaffold
npx create-video@latest marketing-video
cd marketing-video && npm install
npx remotion add @remotion/transitions @remotion/google-fonts @remotion/noise @remotion/captions
```

**Composition types by platform:**

| Composition | Use Case | Dimensions | Duration |
|------------|----------|------------|----------|
| `ProductDemo.tsx` | Full product walkthrough | 1920x1080 | 30-90s |
| `SocialClip.tsx` | TikTok/Reels vertical | 1080x1920 | 15-60s |
| `TwitterClip.tsx` | Twitter/X landscape | 1920x1080 | 15-30s |
| `SquareClip.tsx` | Instagram feed | 1080x1080 | 15-30s |

**Key animation rules:**
- All animation via `useCurrentFrame()` + `interpolate()` or `spring()`
- Default spring: `{damping: 200}` — no bounce
- Always clamp: `extrapolateRight: "clamp"` on every `interpolate()`
- Use `<Sequence premountFor={1 * fps}>` to preload content

**Render:**
```bash
npx remotion studio  # Preview
npx remotion render ProductDemo out/product-demo.mp4 --codec h264 --crf 18 --color-space bt709
```

### Step 6: Platform Optimization

| Platform | Format | Duration | Hook Window |
|----------|--------|----------|-------------|
| Twitter/X | 16:9 | 15-60s | First 1.5s |
| TikTok | 9:16 | 15-60s | First 1s |
| YouTube | 16:9 | 1-5min | First 5s |
| Instagram Reels | 9:16 | 15-90s | First 1s |
| Landing page | 16:9 | 30-90s | Immediate |
| Pitch meeting | 16:9 | 1-2min | First 5s |

## Output Deliverables

1. **Video files** in `out/` (MP4, platform-specific formats)
2. **Source code** (Remotion project) for future edits
3. **Platform-specific cuts** — landscape, portrait, square
4. **Caption file** (.srt) for accessibility
5. **Post copy** — suggested caption text for each platform

## Resources

- [references/scene-templates.md](references/scene-templates.md)
- [references/remotion-quickstart.md](references/remotion-quickstart.md)
- [references/professional-quality-guide.md](references/professional-quality-guide.md)
- [references/video-storytelling.md](references/video-storytelling.md)
- [references/audio-library.md](references/audio-library.md)

## Framework Credits

- **[remotion-dev/skills](https://github.com/remotion-dev/skills)** — Official Remotion agent skill with 38 rule modules
- **Remotion** ([remotion.dev](https://remotion.dev)) — Programmatic video framework for React
- **Disney's 12 Principles of Animation** — Applied to motion graphics
- Platform-specific best practices from Twitter, TikTok, YouTube creator guidelines

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
