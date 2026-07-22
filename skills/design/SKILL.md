---
name: Premium Brand & Visual Design Guidelines
description: Rules and principles for shipping senior-level brand systems, assets, and visual materials that avoid stock clichés and generic templates.
---

# Brand & Visual Design: Senior Lead Guidelines

## Role & Framing
Approach every asset like the Brand Design Lead at a top-tier visual studio (e.g., Collins, Bruno, Koto, or Pentagram style). You are the custodian of a signature visual identity, not a generator of one-off graphics. Your responsibility is to build and protect a cohesive, instantly recognizable design system that unifies our application, marketing surfaces, decks, social graphics, and product screenshots. Ensure every element has intent, rigor, and fits into the overarching brand narrative.

---

## 1. Brand System Rules
A brand system is an ecosystem of rules that creates immediate recognition regardless of touchpoint. 

### Named Palette Roles
Do not define colors as arbitrary lists. Every color in our palette must have a designated, functional name mapping back to its job in the brand system:
*   **Brand Canvas**: The primary background surface (dark or light theme anchor).
*   **Brand Elevation**: The card or elevated container background.
*   **Canvas Base Layer**: Backdrop level beneath the canvas for sectioning.
*   **Primary Action**: Color containing the highest visual weight reserved exclusively for primary buttons and high-priority states.
*   **Accent Signal**: Tone utilized only for drawing eyes to key branding, logos, or primary value propositions (max 5% surface area).
*   **Danger / Warning**: Universal accent indicating critical user action requirement or error conditions.
*   **Muted Anchor**: Colors used for boundaries, thin strokes, and inactive states.

### Typographic Voice
The font pairing should act as the brand's vocal expression. Define and enforce:
1.  **Brand Display Face**: Set the brand tone. E.g., a bold sans-serif with geometric properties (like Outfit or Cl clash) for a modern tech feel, or a high-contrast serif (like Ogg or Instrument Serif) for humanistic or editorially driven platforms.
2.  **Brand Body Face**: High-legibility sans-serif optimized for long-form reading on screen formats (like Inter, Plus Jakarta Sans, or DM Sans).
3.  **Brand Monospace Face**: For secondary metadata, counts, tables, or utility labels.

### Visual "Signature" Element
Every cohesive brand has a signature elements—a recurring motif that ties everything together. This signature must be applied systematically across all touchpoints (marketing hero, app dashboard headers, deck slides, social graphics). Choose one signature and apply it consistently:
*   *Example 1 (Geometric Grid):* The use of a subtle background blueprint grid mesh (`1px` width line grids spaced exactly at `48px`).
*   *Example 2 (Skeuomorphic Detail):* Sharp glass key caps, micro-textured borders, or subtle paper textures.
*   *Example 3 (Chambered Border Framing):* Double borders separated by a `4px` gap, or nested boxes with absolute corner offsets.

---

## 2. Iconography Rules
Never mix icon families. Visual inconsistency in icons cheapens a product instantly.

### Banned Practices
*   **No Mixed Fill States:** Do not combine outline icons and filled icons in the same section or viewport.
*   **No Mixed Corner Radii:** Do not mix sharp-edged geometric icons with warm, rounded face icons.
*   **No Mixed Stroke Weights:** Never mix icons built with `1.5px` lines alongside icons built with `2.5px` lines.

### Icon Design Specification
Every icon must be constructed under a set structural specification definition:
*   **Bounding Box Grid:** Standardized at `24x24px` (or `20x20px` for dense lists) with a `2px` internal padding safety margin.
*   **Stroke Weight:** Kept uniform at `1.5px` or `2.0px`. Do not scale icons arbitrarily; scale the vector path, maintaining stroke settings.
*   **Corner Treatment:** Defined rounding (e.g., `2px` inner corners, `4px` outer corners) to preserve brand warmth.
*   **End Caps:** Uniform stroke caps (either strictly `round` or `square`).

---

## 3. Illustration & Imagery Guidelines
Illustrations must feel bespoke and directly illustrate the product's underlying concept. Do not default to "AI-art" tropes.

### Style Selection
Select **ONE** consistent illustration style for the entire brand lifecycle and stick to it verbatim:
*   *Option A (Flat Geometric):* Bold shapes, zero gradients, minimal color palette, stark silhouette vectors.
*   *Option B (Technical Line Art):* Mono-line isometric schematics showing hardware, data paths, or process flow blueprints.
*   *Option C (Bespoke 3D Matte):* Highly modeled, custom-textured surfaces with studio-light setups.
*   *Option D (Editorial Photographic/Collage):* High contrast silhouettes mixed with physical print textures and real photo overlays.

### Banned AI Clichés & Stock Aesthetics
*   **No Random Floating 3D Blobs:** Banish meaningless plastic capsules, spheres, toruses, and wavy organic shapes hovering around screenshots.
*   **No Diverse Cartoon Handshakes:** Avoid generic illustrations of vector people pointing at a laptop, high-fiving across desks, or sitting beside oversized plants.
*   **No Gradient Bubble Blurs:** Do not use randomly placed blobs of soft pink and purple blurs in the background to hide empty layout space.

---

## 4. Motion at the Brand Level
Brand motion refers to macro-animations that define personality and pace. It is distinct from utility micro-interactions.

### Brand Motion Philosophy
*   **Restraint Over Novelty:** Restraint is the benchmark of premium brand design. Introduce one signature motion moment on page load (e.g., a crisp logo reveal sequence, or an elegant landing hero element assembly). Make sure all other elements follow standard document layout reveals without sliding in from all angles of the view block.
*   **Physics-Based Easing:** Never use standard CSS `linear` or basic `ease` keywords. Use custom bezier physics mimicking real-world gravity and momentum (highly energetic acceleration with long, smooth deceleration tails).
*   **Consistent Motion Signature:** If the brand voice is premium and editorial, motion must be slow and sliding. If the brand voice is cutting-edge developer tool, motion should be ultra-snappy and mechanical.

---

## 5. Layout for Marketing & Brand Surfaces
Brand layouts differ from standard UI structures; they prioritize storytelling, visual breathing room, and dynamic rhythm over data density.

### Design Principles for Brand layout
*   **Whitespace Discipline:** Whitespace is not empty space; it is a visual framing device. Ensure clear asymmetry exists. Keep hero sections framed with at least `96px` or `6rem` vertical padding on desktop viewports.
*   **The Grid Anchor:** Align all marketing visual panels, testimonial blocks, copy columns, and visual assets to a strict 12-column grid. Let elements span unequal grid lengths (e.g., copy spans 5 columns, visual asset spans 7 columns) to create architectural flow.
*   **Visual Hierarchy Flow:** Maintain a clear structure:
    1.  *Eyebrow label:* Tiny, uppercase, tracked text (12px, letter-spacing: 0.1em) framing the context.
    2.  *Hero Statement / H1:* High weight density, tight line height, large font size.
    3.  *Support Paragraph:* Softened font weight, larger reading size (18px), high contrast.
    4.  *Call-to-Action Pair:* Primary button (filled/accented) paired with a secondary text action (arrow/underline).

---

## 6. Voice-to-Visual Consistency
A mismatch between copy tone and visual styles breaks user trust instantly. Ensure style parameters sync with brand positioning:

### Case Study 1: "Playful / Modern B2C" Brand System
*   **Brand Voice:** Friendly, energetic, human, approachable.
*   **Visual System:**
    *   *Palette:* Warm pastel base canvas (`#FCFAF7`), rich warm charcoal text (`#1E1C1A`), and neon primary orange (`#FF6B35`).
    *   *Shapes:* Soft corners (`--radius-lg: 20px`), heavy drop shadows, rounded pill forms.
    *   *Typography:* Soft, humanist Grotesk (like *Plus Jakarta Sans* or *Outfit*).

### Case Study 2: "Sophisticated Enterprise / DevTool" Brand System
*   **Brand Voice:** Technical, highly precise, high-performance, robust.
*   **Visual System:**
    *   *Palette:* Deep Obsidian canvas (`#0A0A0A`), crisp white text (`#FFFFFF`), muted slate accents (`#475569`), neon turquoise for precision highlights (`#00F2FE`).
    *   *Shapes:* Sharp corners (`--radius-sm: 6px` or `8px`), zero shadow offsets, raw borders (`1px` width line grids).
    *   *Typography:* Precise geometric monospace or clean neo-grotesk (like *Inter* or *JetBrains Mono*).

---

## 7. Verification & Self-Critique Checklist
Execute these critical checks before shipping any visual brand asset:

*   [ ] **The Logo Test:** If you remove our logotype from the header, does this page, graphic, or layout still feel like it belongs to our company? Or does it look like a template for 100 other startups?
*   [ ] **The Anchor Check:** Is there a single, prominent visual signature element anchoring the user's attention? Or are they distracted by multiple illustrations, glowing blobs, and typography treatments competing at once?
*   [ ] **Icon Uniformity:** Are all vector icon styles (shapes, sizes, stroke widths, corner radii) completely synchronized across headers, lists, and buttons?
*   [ ] **Motion Constraint:** Is the page load smooth and fast? Have we restricted animation to only one key sequence while everything else loads inline?
*   [ ] **Studio Review Quality:** Would this graphic, deck slide, or page hold up under visual scrutiny from Design Directors at a top-tier brand agency?
```,Complexity:3,Description:

---

_Adapted from: https://github.com/hoodini/ai-agents-skills (MIT License)_
