# Brand Asset Pre-Ship Checklist

Run all graphics, icons, illustrations, and marketing assets through this checklist before integration.

---

## 1. Visual Alignment & Consistency
*   [ ] **Style Enforcement:** Does the asset match the single selected illustration framework (e.g. isometric line art vs. minimal flat geometry)?
*   [ ] **Palette Match:** Does the asset use color hex codes directly from our brand semantic color definitions (e.g. `--brand-canvas` and `--brand-primary-action`)? 
*   [ ] **Stroke & Weight Uniformity:** For vector icons/illustrations, are stroke weights kept at the default value (e.g. `2px`)?
*   [ ] **Perspective Alignment:** If the system is isometric, is the asset drawn on the exact axonometric coordinates? If flat, is the viewpoint directly orthogonal?
*   [ ] **Text Alignment:** Does the visual typography pair correctly with standard display and body faces?

---

## 2. Contrast & Accessibility (A11y)
*   [ ] **Foreground Contrast:** Is the element silhouette easily distinguishable against its target layout canvas color? (Achieve WCAG AA minimum 3:1 contrast for graphical shapes).
*   [ ] **Copy Support:** If the graphic contains overlay letters, is the readability verified on normal and low-luminance screen displays?
*   [ ] **Alt Annotation:** Has a clear, descriptive alt text attribute been authored for display to screen-readers?
*   [ ] **Reduced Motion Check:** If the asset features animations (SVG/WebM/Lottie), does it respect user OS motion requests?

---

## 3. Duplication & Code Maintenance
*   [ ] **Duplication Check:** Does this asset overlap with or duplicate an existing graphic pattern in our product index? (Can you reuse an existing component rather than adding code bloat?)
*   [ ] **Vector Optimization:** Have all unused paths, anchor points, metadata, and editor classes been cleaned out of SVG definitions?
*   [ ] **Modular Architecture:** Is the graphic reusable across contexts via component properties?

---

## 4. Export & File Format Requirements

Compile and package assets using these format benchmarks:

| Asset Type | Primary Format | Resolution / Size Guidelines | Optimization / Tooling |
| :--- | :--- | :--- | :--- |
| **Icons & Line Tech graphics** | SVG (`.svg`) | Raw code embedded directly, viewbox size exact | Minified using SVGO, attributes cleaned |
| **Complex Illustrations** | WebP (`.webp`) | Render at 2x screen dimensions for high-DPI | Compressed to at least 75-80% quality |
| **Moving Graphics / Lottie** | JSON (`.json`) / WebM | Native vector JSON or highly compressed video | Loop states checked, framing fixed |
| **Photographic Assets** | WebP (`.webp`) | Width boundary max `1920px` for hero banners | WebP output format, metadata stripped |
