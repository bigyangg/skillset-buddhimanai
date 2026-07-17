# Design System Tokens Reference

> **IMPORTANT:** This tokens sheet serves as a baseline boilerplate. 
> Do not copy these tokens verbatim into every project. Always adapt, modify, and tweak parameters to fit the context, theme, and product requirements.

---

## 1. Color Palette Tokens

Adapt these variables to shape the brand tone (e.g., Obsidian Dark, Warm Cream, Tech Slate, Cyberpunk Neon).

### Dark Mode System Boilerplate (Obsidian Theme)
```css
:root {
  /* Brand Canvas Backdrops */
  --canvas-bg: #090d16;             /* Adapt per brief - root app background */
  --canvas-floor: #06090f;          /* Ground level behind layout blocks */
  
  /* Component Surfaces */
  --surface-default: #121926;       /* Card default surface */
  --surface-raised: #1c2638;        /* Elevated status (dropdown, popover) */
  --surface-sunken: #0b111e;        /* Input blocks, dark wells */
  
  /* Brand Accents */
  --accent-primary: #3b82f6;        /* Adapt per brief - key action color */
  --accent-hover: #2563eb;          /* Focused hover state color */
  --accent-glow: rgba(59, 130, 246, 0.45);
  
  /* Typography Scale */
  --text-primary: #f8fafc;          /* High contrast readability */
  --text-secondary: #94a3b8;        /* Medium contrast metadata */
  --text-muted: #64748b;            /* Disabled state, placeholders */
  
  /* Borders and Dividers */
  --border-subtle: rgba(255, 255, 255, 0.06);
  --border-default: rgba(255, 255, 255, 0.1);
  --border-strong: rgba(255, 255, 255, 0.2);
}
```

### Light Mode System Boilerplate (Warm Cream Theme)
```css
:root {
  /* Brand Canvas Backdrops */
  --canvas-bg: #fafafa;
  --canvas-floor: #f4f4f5;
  
  /* Component Surfaces */
  --surface-default: #ffffff;
  --surface-raised: #ffffff;
  --surface-sunken: #f1f1f2;
  
  /* Brand Accents */
  --accent-primary: #18181b;
  --accent-hover: #27272a;
  --accent-glow: rgba(24, 24, 27, 0.15);
  
  /* Typography Scale */
  --text-primary: #09090b;
  --text-secondary: #52525b;
  --text-muted: #71717a;
  
  /* Borders and Dividers */
  --border-subtle: rgba(0, 0, 0, 0.04);
  --border-default: rgba(0, 0, 0, 0.08);
  --border-strong: rgba(0, 0, 0, 0.15);
}
```

---

## 2. Spacing Scale (4px/8px Grid)

```css
:root {
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;
  --space-20: 80px;
}
```

---

## 3. Border Radius Tokens

```css
:root {
  --radius-xs: 4px;     /* Small badges, interactive toggles */
  --radius-sm: 8px;     /* Button layers, text-inputs */
  --radius-md: 12px;    /* Normal card structures, small overlays */
  --radius-lg: 16px;    /* Large display cards, modal drawers */
  --radius-xl: 24px;    /* Landing page feature blocks */
  --radius-full: 9999px;/* Circle avatars, pill buttons */
}
```

---

## 4. Shadow & Elevation Tokens

```css
:root {
  /* Flat level */
  --shadow-none: none;
  
  /* Subtly raised elements (e.g. flat card outlines) */
  --shadow-low: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  
  /* Standard modules/nav elements */
  --shadow-mid: 0 4px 6px -1px rgba(0, 0, 0, 0.08), 
                0 2px 4px -1px rgba(0, 0, 0, 0.04);
  
  /* Tooltips, notifications, popup modals */
  --shadow-high: 0 12px 20px -8px rgba(0, 0, 0, 0.15), 
                 0 4px 8px -2px rgba(0, 0, 0, 0.08);
                 
  /* Full page overlays / immersive modals */
  --shadow-float: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  
  /* Glass card bottom depth elevation */
  --shadow-glass: 0 8px 32px 0 rgba(0, 0, 0, 0.24);
}
```

---

## 5. Motion (Duration & Easing)

```css
:root {
  /* Standard Micro Actions */
  --transition-fast: 150ms cubic-bezier(0.4, 0, 0.2, 1);
  
  /* Hover Exit/Transforms */
  --transition-normal: 250ms cubic-bezier(0.16, 1, 0.3, 1);
  
  /* Multi Step / Complex UI Drawer slide-in */
  --transition-slow: 350ms cubic-bezier(0.34, 1.56, 0.64, 1);
  
  /* Easing curves alone for dynamic interpolation */
  --ease-snappy: cubic-bezier(0.16, 1, 0.3, 1);
  --ease-bounce: cubic-bezier(0.34, 1.56, 0.64, 1);
  --ease-standard: cubic-bezier(0.4, 0, 0.2, 1);
}
```
