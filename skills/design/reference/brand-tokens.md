# Brand Tokens Quick Reference

> **IMPORTANT:** This tokens sheet is a starter template. 
> Do not copy these tokens verbatim into every brand system. Adapt parameters to suit the brand voice, product style sheet, and company identity.

---

## 1. Palette Roles (Semantic mapping)

Use these semantic roles directly to map Hex variables. Do not reference raw hues like `red` or `blue` directly in application files.

```css
:root {
  /* 1. Canvas / Backgrounds */
  --brand-canvas: #08080a;           /* The main backdrop for the brand */
  --brand-surface: #101014;          /* Card container surfaces */
  --brand-floor: #040406;            /* Background layout blocks dividers */

  /* 2. Primary Systems */
  --brand-primary-action: #eb5e28;   /* Primary user input button accent */
  --brand-primary-hover: #d04d1d;    /* Active state mouse hover color */
  --brand-primary-glow: rgba(235, 94, 40, 0.2);

  /* 3. Accents and Indicators */
  --brand-accent-signal: #80ed99;    /* Strategic branding highlights (e.g. logos) */
  --brand-signal-hover: #57cc99;
  
  --brand-danger: #ea3546;           /* Destructive action warning colors */
  --brand-muted-anchor: #25252b;     /* Thin borders, dividers, disabled values */
}
```

---

## 2. Typography Voice Profiles

Select and customize the typography scales depending on the category of product:

### Option A: Editorial / Human-centric Voice
```css
:root {
  --font-display: "Instrument Serif", Georgia, serif;
  --font-body: "Inter", system-ui, sans-serif;
  --font-utility: "JetBrains Mono", monospace;
  
  --letter-spacing-display: -0.01em;
  --letter-spacing-body: 0;
  --letter-spacing-utility: 0.05em;
}
```

### Option B: Tech-focused / Precise Developer Voice
```css
:root {
  --font-display: "Clash Display", -apple-system, sans-serif;
  --font-body: "Plus Jakarta Sans", system-ui, sans-serif;
  --font-utility: "Fira Code", monospace;

  --letter-spacing-display: -0.03em;
  --letter-spacing-body: -0.01em;
  --letter-spacing-utility: 0.02em;
}
```

---

## 3. Icon Grid Specifications

Enforce these strict grid proportions when setting up vector icons:

```css
:root {
  /* Grid sizes */
  --icon-grid-default: 24px;
  --icon-grid-dense: 20px;
  
  /* Path properties */
  --icon-stroke-default: 2px;
  --icon-stroke-thin: 1.5px;
  --icon-radius-default: 4px;
  --icon-radius-sharp: 1px;
}
```

*Alignment Checklist:*
*   All icon nodes must be absolute centered on the viewport.
*   Path endings (`stroke-linecap`) should always use `round`.
*   Corners (`stroke-linejoin`) should match the border-radius specification style.

---

## 4. Brand-Level Motion Values

Use these parameters for entry loads, splash cards, hero illustration transitions and presentations.

```css
:root {
  /* Splash transitions (Long, majestic overlays) */
  --brand-transition-splash: 800ms cubic-bezier(0.16, 1, 0.3, 1); /* Custom ease-out */

  /* Section Reveals (Synchronized delay chains) */
  --brand-transition-reveal: 450ms cubic-bezier(0.25, 1, 0.5, 1);
  --brand-reveal-delay-1: 150ms;
  --brand-reveal-delay-2: 300ms;
  --brand-reveal-delay-3: 450ms;

  /* Custom ease profiles */
  --brand-ease-fluid: cubic-bezier(0.85, 0, 0.15, 1);  /* Heavy acceleration, long settling curve */
  --brand-ease-elastic: cubic-bezier(0.34, 1.56, 0.64, 1); /* Quick acceleration with overshoot */
}
```
