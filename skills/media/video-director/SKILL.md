---
name: video-director
description: All-in-one AI video pipeline. Turns an idea or script into a finished MP4 by orchestrating HyperFrames (HTML→deterministic video render), Lottie (branded motion graphics), ManimCE (math/concept animations), GSAP (kinetic text/transitions), and a transcribe→approve caption flow. Use whenever asked to make, edit, or explain something as a video: promo, explainer, launch, social reel. Triggers on "make a video", "explain X as a video", "product explainer", "promo video", "neural network animation", "turn this into a video", captioned tutorial, 16:9 or 9:16 social cuts.
---

# Video Director

## What This Does

The conductor for AI video production. You (the agent) decide **what each beat needs**, route it to the right **rendering engine**, compose everything into **one HyperFrames composition**, enforce brand consistency via a `frame.md`, self-verify, and render to MP4.

This skill is the router + working reference for multi-engine video composition. Load a reference file only when that engine is in play.

## The One Law (Decides Everything)

HyperFrames renders by **seeking each frame in headless Chrome → FFmpeg** (`frameIndex = floor(t·fps)`, same input → same output). So every visual is one of two kinds:

| Pattern | Runs… | Engines | Rule |
|---|---|---|---|
| **Live seekable adapter** | *inside* the render, driven to time `t` per frame | GSAP, Lottie (`window.__hfLottie`), Three.js, canvas driven by GSAP proxy `onUpdate` | must be **clock-driven** — no `Date.now()`, `Math.random()` (seed a mulberry32), `setTimeout`, or `.play()` |
| **Pre-rendered asset** | *offline*, outputs a file, imported as a clip | **ManimCE** (Python→MP4/alpha), TTS audio, background-removal | render first, then drop in as `<video>`/asset clip |

**If it can be seeked, it's an adapter. If it can't, pre-render it.** Manim is *always* a pre-rendered clip — it runs in Python and can never be a live adapter.

## Route Each Beat to an Engine

```
"explain X as a teaser/promo (fast, punchy)"         → references/teaser-explainer.md
"explain a concept / math / algorithm / animation"   → ManimCE   (pre-rendered clip)
"branded motion: logo sting, stat reveal, icon pop"  → Lottie    (live, lottie-web)
"kinetic captions, titles, reveals, data callouts"   → GSAP      (live) ← default
"3D / spatial"                                        → Three.js  (live)
speech → captions                                     → transcribe + approve webapp
no voiceover provided                                 → TTS (Kokoro: npx hyperframes tts)
brand colors/fonts/motifs                             → frame.md  (picked up front)
```

GSAP is the reliable default for text/motion. Reach for Lottie for *designed* branded graphics, Manim for *explaining* an idea with animation.

## Workflow

1. **Plan the beats.** Narrative arc + which engine each beat needs. Pick 16:9 and/or 9:16.
2. **Set the brand.** Ensure `FRAME.md` is in the project root. All colors/fonts/motifs come from it — never invent. Every video needs three brand anchors: logo at reveal + end card, a featured Lottie animation, and a link end-card (logo + CTA + URL).
3. **Pre-render the asset beats first** (so they exist as clips):
   - **Manim** → `py -m manim render -qh --fps 30 scene.py SceneName`, copy the MP4 into `assets/`.
   - **Captions/TTS** → transcribe → approve webapp → sync, then `npx hyperframes tts`.
4. **Scaffold + compose.** `npx hyperframes init <slug> --non-interactive`. Author `index.html`. Use:
   - A seekable animated background (canvas driven by GSAP proxy)
   - Lottie for branded motion graphics
   - Manim render as a `<video class="clip" muted playsinline>` body clip
   - GSAP entrances per scene, a flash transition on each boundary, fade-out only on the final scene
   - For teaser/social pacing (fast cuts, kinetic word-slams, hook montage) cut like a producer, not a slideshow
5. **Self-verify (gates):** `npx hyperframes lint` (0 errors) → `validate` (0 console errors, WCAG AA) → render → spot-check 5 frames across the timeline. Fix → re-run.
6. **Render.** `npx hyperframes render --fps 30 --output renders/<name>_FINAL.mp4`. For vertical, clone with a 1080×1920 layout.

## Prerequisites (Check First; Degrade Gracefully)

Need **Node 22+**, **FFmpeg**, **Python 3.11+ with pip** (Manim/captions). ManimCE installs via `py -m pip install manim` (no LaTeX needed if you author with `Text()`/`MarkupText()` instead of `Tex`/`MathTex()`). If Manim isn't available → skip math beats or offer to install; never hard-fail the whole video.

## Companion Skills

`video-edit` (transcribe + approve webapp), `page-load-animations` (spring/framer-motion patterns), `marketing-video` (Remotion alternative pipeline).

## Resources

- [references/teaser-explainer.md](references/teaser-explainer.md) — Cinematic teaser-explainer formula
- [references/composition-pattern.md](references/composition-pattern.md) — Full multi-engine `index.html` pattern
- [references/manim.md](references/manim.md) — ManimCE scene authoring guide
- [references/lottie.md](references/lottie.md) — Lottie integration for HyperFrames
- [references/editing.md](references/editing.md) — Social pacing and cutting rules
- [references/cinematic.md](references/cinematic.md) — Psychological/FOMO cuts, cliffhanger structure

---

_Adapted from: https://github.com/hoodini/ai-agents-skills yuv-video-director (MIT License) — shot routing logic and engine decision matrix extracted and genericized; all personal brand references (YUV.AI, Neon Phoenix, Yuval Avidani) removed._
