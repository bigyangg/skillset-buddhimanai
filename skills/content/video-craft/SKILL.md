---
name: video-craft
description: Frame-level visual composition and product demo presentation for Remotion videos. Use when the user says "video looks generic", "make video frames look better", "video frame design", "device frame", "product demo video craft", "video CTA", "end card", "video composition", "screenshot in video", "frame quality", or when reviewing Remotion compositions for visual quality. Sits on top of marketing-video — adds the visual design layer for each frame.
---

# Video Craft

## What This Does

Frame-level visual composition and product demo presentation for Remotion videos. This skill makes each frame of a video look intentionally designed, not just correctly animated.

`marketing-video` handles the production pipeline (interview, storytelling, animation mechanics, rendering). **This skill handles how each static frame looks** — composition, device framing, backgrounds, effect choices, and end-card design.

## Authority Model

- `marketing-video` → production pipeline, animation mechanics, baseline quality
- `design-taste` → aesthetic direction. This skill CONSUMES it, does not redefine it.
- **This skill** → frame composition for time-bounded viewing, product demo presentation, end-card design, effect families

## When to Fire

- Reviewing Remotion compositions for visual quality
- Building product demo videos with screenshots
- Designing end cards / CTA frames
- User says "the video frames look generic" or "make this look more polished"
- `marketing-video` invokes this skill during creative direction or build phases

Do NOT fire on "create a video" or "marketing video" — those route to `marketing-video`.

## Workflow

1. Check `design-taste` for aesthetic direction (or ask user to pick one)
2. Identify which reference is relevant:
   - Composing video frames or end cards → [references/frame-composition.md](references/frame-composition.md)
   - Building product demo scenes → [references/product-demo-patterns.md](references/product-demo-patterns.md)
3. Produce a **frame brief** per scene
4. Run preflight checklist before rendering

## Frame Brief (Per Scene)

```
Scene: [name]
Duration: [seconds / frames]
Aspect: [16:9 / 9:16 / 1:1]
Focal point: [what the eye hits first]
Archetype: [hero / product / split / data / comparison / end-card]
Device: [laptop / phone / browser / none]
Assets needed: [screenshot path, logo, icon names]
Background: [radial gradient + noise / screenshot blur / mesh / solid]
Effect family: [glow / kinetic / liquid / depth / none]
Animation: [entrance spring config, hold frames, exit]
```

## Preflight Checklist

- [ ] Every frame has one clear focal point
- [ ] Screenshots are in device frames, not floating raw
- [ ] Backgrounds have depth (gradient + noise or vignette), not flat solid
- [ ] Max 8 words per text block on screen
- [ ] Hold time >= `(word_count x 0.3) + 1` seconds per text frame
- [ ] End card has minimum 3 seconds and one action only
- [ ] Effect families limited to 1-2 per video
- [ ] Aesthetic direction chosen before any frame work

## Resources

- [references/frame-composition.md](references/frame-composition.md) — Frame archetypes (16:9 and 9:16), time-bounded readability, background depth, effect families, end-card archetypes
- [references/product-demo-patterns.md](references/product-demo-patterns.md) — Device frames (laptop/phone/browser), cursor choreography, zoom recipes, screenshot prep, annotation patterns

---

_Adapted from: https://github.com/sendaifun/solana-new (MIT License)_
