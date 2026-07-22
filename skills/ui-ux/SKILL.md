---
name: Premium UI/UX Design System Guidelines
description: Rules and principles for shipping senior-level UI designs that avoid generic AI templates and slop.
---

# UI/UX Design: Senior Lead Guidelines

## Role & Framing
Approach every digital screen like a Lead Designer or Design Partner at a top-tier product studio (e.g., Metalab, Instrument, linear.app style), not a generic template generator. Our goal is to craft highly bespoke, context-sensitive components and screen systems. Do not settle for common patterns unless they have been refined, stripped of bloat, and optimized to look custom-crafted for the specific product brand. Every line of CSS/HTML or pixel placement must serve a utility or aesthetic narrative.

---

## 1. Color Rules & Palettes
Random color generation is strictly prohibited. You must never let the model default to the "standard Tailwind palette" or generic visual tropes unless explicitly instructed.

### Banned Practices
*   **No Generic Hero Gradients:** Banish the standard indigo-to-purple, blue-to-pink, or rainbow-blur backgrounds. They are the signature of generic tool-generated UI ("AI-slop").
*   **No Unbounded Blob Blur Ornaments:** Avoid useless backdrop blobs floating in the background without real structure.
*   **No Random Gray Backgrounds:** Do not mix cool grays with warm brand accents or vice versa. Keep color temperatures unified.

### Required: Defined Brand Palette
Every design must output a custom palette of 4-6 named, specific hex code colors accompanied by a short rationale describing their role. Each brand palette should contain:
1.  **Canvas/Background**: The root layout base.
2.  **Surface**: The card, container, or elevated surface.
3.  **Primary/Accent**: The main element of interaction focus (used sparingly, max 5-10% visual weight).
4.  **Text Primary**: Highly readable text color (minimum 4.5:1 ratio against surface/canvas).
5.  **Text Secondary**: Meta information or secondary copy.
6.  **Border/Separator**: Very subtle lines defining structure (typically extremely light opacity or low contrast to the parent container).

*Example Palette Definition:*
*   `Canvas: #0B0F19` (Deep Obsidian - Cool Dark temperature to anchor the app UI)
*   `Surface: #161F30` (Muted Cobalt Navy - elevated surface for cards and navigations)
*   `Accent: #00E5FF` (Neon Cyan - precise focal interactive elements/buttons only)
*   `Text Primary: #F3F4F6` (Cool Gray 100 - crisp readability)
*   `Text Secondary: #9CA3AF` (Muted Silver - context metadata)
*   `Border: #22314D` (Dividers with 1px sharpness)

---

## 2. Typography Rules
A unified type scale maintains visual hierarchy and keeps the design rhythm predictable. 

### Typeface Pairing Rationale
You must establish a functional type pairing system using standard system fonts or specific Google Fonts.
*   **Display Font (Headers/Titles):** A strong personality face. If serif, ensure the body is a clean sans-serif. If sans-serif, select highly geometric or humanoid families (e.g., Inter, Plus Jakarta Sans, Instrument Sans, or Cabinet Grotesk).
*   **Body Font (Paragraphs/Lists):** Maximized for readability. Usually a neutral, highly legible sans-serif (e.g., Inter, Roboto, System Sans).
*   **Utility Font (Buttons/Labels/Code):** Monospace or compact sans-serif for numbers, status pills, table data, and interactive actions (e.g., JetBrains Mono, Fira Code, SF Mono).

### Prescriptive Type Scale
Do not use arbitrary font sizes. Map all typography styles to this exact scale:
*   `Display/Hero`: `44px` or `2.75rem` | Line-height: `1.1` | Letter-spacing: `-0.03em`
*   `Heading 1`: `32px` or `2rem` | Line-height: `1.2` | Letter-spacing: `-0.02em`
*   `Heading 2`: `24px` or `1.5rem` | Line-height: `1.3` | Letter-spacing: `-0.015em`
*   `Subheading/Body Lg`: `18px` or `1.125rem` | Line-height: `1.5` | Letter-spacing: `0`
*   `Body/Base`: `16px` or `1rem` | Line-height: `1.6` | Letter-spacing: `0`
*   `Small/Utility`: `14px` or `0.875rem` | Line-height: `1.5` | Letter-spacing: `0.01em`
*   `Caption/Micro`: `12px` or `0.75rem` | Line-height: `1.4` | Letter-spacing: `0.03em`

---

## 3. Layout & Spacing
A design lives or dies by its macro and micro spacing. Consistency is non-negotiable.

### The 8px / 4px Spacing Grid
All margins, paddings, gaps, and heights must be multiples of `8` (or `4` for tight inline micro-adjustments).
*   **Micro Spacing (Tight pairs, icons, small gaps):** `4px`, `8px`, `12px`
*   **Component Spacing (Paddings, list item gaps):** `16px`, `24px`
*   **Layout Spacing (Section margins, container gaps):** `32px`, `48px`, `64px`, `80px`

### Border Radius Scale
Do not mix round pill elements with sharp cards unless specifically designed. Maintain this relationship flow:
*   `XS` (`2px` / `4px`): Checkboxes, small switches, badges.
*   `SM` (`8px`): Text inputs, buttons, control widgets.
*   `MD` (`12px` or `16px`): Standard cards, dialog headers, utility flyouts.
*   `LG` (`24px` or `32px`): Outer wrapper layouts, hero modals.
*   `Full` (`9999px`): Fully rounded buttons, avatars, pills.

*Rule of Nested Radius:* When nesting a container within another, the outer radius `R_outer` and inner radius `R_inner` must follow:
$$\text{Outer Radius} = \text{Inner Radius} + \text{Padding/Gap}$$
Failure to adjust this turns the corner spacing into an awkward wedge.

### Shadow & Elevation Scale
Provide explicit visual cues for stack hierarchy:
*   `Elevation Low (Flat/Muted Surfaces)`: `0 1px 2px 0 rgba(0,0,0,0.05)`
*   `Elevation Mid (Standard Cards)`: `0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -1px rgba(0,0,0,0.06)`
*   `Elevation High (Dropdowns, Popovers)`: `0 10px 15px -3px rgba(0,0,0,0.1), 0 4px 6px -2px rgba(0,0,0,0.05)`
*   `Elevation Floating (Modals, Overlays)`: `0 20px 25px -5px rgba(0,0,0,0.15), 0 10px 10px -5px rgba(0,0,0,0.04)`

---

## 4. Glassmorphism Guidelines
Frosted glass (glassmorphism) creates depth, but must be designed with restraint. If overdone, it introduces visual noise and violates contrast standards.

### Correct Glass Stack
To render high-end glass, use a four-layer stack technique:
1.  **Backdrop Filter Blur**: Range strictly between `backdrop-filter: blur(8px)` and `blur(20px)`. Do not exceed this unless rendering full-screen modal backdrops.
2.  **Semi-transparent Fill**: Use HSL/RGB background fills with low opacity (`0.4` to `0.75` for dark mode, `0.6` to `0.85` for light mode).
3.  **High-Contrast Stroke**: A structural border of solid white/light color with extremely low opacity (`rgba(255, 255, 255, 0.08)` to `rgba(255, 255, 255, 0.15)`) to catch the light. Keep border thickness at exactly `1px`.
4.  **Shadow**: A soft dark shadow behind the glass to elevate it physically from the background.

```css
.card-glass {
  background: rgba(22, 31, 48, 0.65);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.08);
  box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.2);
}
```

### When NOT to Use Glassmorphism
*   **Low Contrast Backdrops:** Do not place glass elements over complex patterns or high-contrast backgrounds containing text or detailed icons.
*   **Dense Data Layouts:** Tables, code panels, and massive detail dashboards should use flat, solid backgrounds. Reading small characters overlaying blurred text/graphics causes heavy cognitive strain.
*   **Nested Glass Elements:** Never nest glass inside glass. This breaks the physical metaphor and results in murky, low-legibility visual hierarchies.

---

## 5. Card-Based & Neumorphic Patterns

### Hard Cards (Solid Bounds)
*   Ensure padding is proportionally larger than the corner radius. For instance, a border-radius of `16px` requires a card padding of at least `24px`.
*   Card states must change seamlessly on hover or interactive trigger (e.g., translate up `2px` via CSS transform and transition the shadow intensity, never pop instantly).

### Neumorphics (Reserved Soft-Shadows)
Neumorphism (extruded physical plastic look) can easily become illegible due to poor contrast. Follow these safety rules:
*   Only use it where visual affordance is auxiliary, not core (e.g. static decorative panels, custom dashboard toggles).
*   **Background Alignment:** Neumorphic elements require their background color to be *identical* to the canvas color.
*   **Dual Shadow Formula:** A light shadow source on top-left, a dark shadow source on bottom-right.
    *   *Light Side:* `rgba(255, 255, 255, 0.5)` or equivalent depending on background color.
    *   *Dark Side:* `rgba(163, 177, 198, 0.5)` (or a darker shade profile for dark mode).
*   **Action feedback:** Interactive components must transition from an "extruded" out shadow (convex) to an "inset" shadow (concave) on click/active press.

---

## 6. Motion & Micro-Interactions
Animation must feel natural, physical, and restrained. Default/linear loops look amateurish.

### Easing & Durations
Avoid direct "linear" timing curves. Use standard cubic-bezier functions modeled after physical physics:
*   **Snappy Interactions (Buttons, Toggles, Hover States):**
    *   Duration: `150ms` to `200ms`
    *   Easing: `cubic-bezier(0.4, 0, 0.2, 1)` (Standard / Ease-in-out) or `cubic-bezier(0.16, 1, 0.3, 1)` (Ease-out-quad)
*   **Expansive Animations (Modal fly-ins, Accordions opening):**
    *   Duration: `300ms` to `400ms`
    *   Easing: `cubic-bezier(0.34, 1.56, 0.64, 1)` (Subtle bounce/elastic exit) or `cubic-bezier(0.87, 0, 0.13, 1)` (Smooth ease-in-out)

### Motion Matrix: What Moves vs. What Stays Muted
*   **Animates:** Touch triggers, primary floating button hover, navigation active indicators, page load skeletons (pulsing, not rotating), notification toast entries.
*   **Stays Still:** Large content grid blocks, static tables, body headers, icon frames (unless explicitly configured for states like an active heart toggle).
*   *Warning:* Never rotate icons, slide entire columns, or animate multiple full-width modules concurrently on hover. It generates heavy graphic engine load and is a clear indicator of "AI web slop."

---

## 7. Accessibility (A11y) & Responsiveness

### Color Contrast (WCAG AA Minimums)
*   Text sizes below `18.6px` (regular) or `14px` (bold) must achieve a contrast ratio of at least **4.5:1** with the background surface.
*   Header titles and text sizes above `18.6px` must achieve at least **3:1** mapping.
*   Interactive elements (buttons, inputs) must have a clearly visible target outline when focused (`:focus-visible` utilizing high contrast rings).

### Mobile-First & Responsiveness
*   Implement layout breakpoints cleanly. Standard media-query points:
    *   `sm`: `640px` (mobile viewport)
    *   `md`: `768px` (tablets)
    *   `lg`: `1024px` (desktop devices)
    *   `xl`: `1280px` (ultra-wide layouts)
*   Set minimum tap target sizes to `44px` x `44px` on all mobile interactions.
*   Ensure horizontal scroll indicators display visually if data overflows out of cards.

### Reduced Motion Support
Respect the operating system accessibility presets. Include media queries to zero-out anims when preferred:
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## 8. Anti-Patterns (The "Never Do" List)
1.  **Do not use random color tags (`red-500`, `green-500`, `blue-500`) directly on the same canvas.** Restrain accent color usage to a single signature interaction tone.
2.  **Do not place raw icons inside borders without proper offset margins.** Ensure standard geometric padding envelopes the icon bounds.
3.  **Do not render linear-gradient bars** along borders or buttons unless they represent a very specific visual metaphor (e.g. progress bar filling, action state success).
4.  **Do not output text over glass cards** without placing a dark or bright overlay shadow block to anchor the readable character strokes.
5.  **Do not mix distinct aesthetic styles.** Do not use flat materials alongside heavy neumorphic card blocks on the same page. Keep the surface structure unified.
6.  **Do not build decorative numbered badges (01, 02, 03) or mock counters** unless they represent actual step paths or data indices.

---

## 9. Verification & Self-Critique Checklist
Run your visual outputs through these internal prompt checks before completing any task:

*   [ ] **The Slop Test:** Does this interface look like a default template created by an un-optimized CSS generator or a basic template repository?
*   [ ] **The Focus Factor:** Is there a single, clear signature element on this page that immediately guides the user's focus? Are all surrounding UI components properly restrained?
*   [ ] **The Contrast Check:** Can you clearly distinguish text and interface actions when your contrast monitor is set to dimmed or grayscale limits?
*   [ ] **Nested Rhythm:** Are all internal margin structures correct relative to outer edge parameters? Do nested corner radii fit smoothly without pinching?
*   [ ] **Accessibility Verification:** Have `:focus-visible` styles been explicitly defined for keyboard navigation?
*   [ ] **The Studio Standard:** Would this design successfully pass a design audit by visual directors at a high-end product studio?

---

_Adapted from: https://github.com/hoodini/ai-agents-skills (MIT License)_
