# Component Implementation Patterns

This document details implementation principles and pseudo-code/CSS recipes for core interactive patterns. Use these blueprints to structure styling layers.

---

## 1. Glassmorphism Card

### Architectural Concept
A premium glass surface relies on layering rules. A light overlay border must outline the element to separate it visually from backdrop visuals, paired with a solid text background filter.

### Pseudo-HTML & Stylesheet Recipe
```html
<!-- Outer glass card wrapper -->
<div class="glass-card">
  <!-- Content must have high contrast text levels -->
  <h3 class="glass-card-title">Glass Element</h3>
  <p class="glass-card-body">Readable body offset with high contrast values.</p>
</div>
```

```css
.glass-card {
  /* 1. Backdrop Blur creates frosted effect */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);

  /* 2. Semi-translucent surface fill (Dark-mode optimized example) */
  background: rgba(18, 25, 38, 0.65);

  /* 3. Outer boundary outline to simulate light refraction */
  border: 1px solid rgba(255, 255, 255, 0.08);

  /* 4. Smooth corner radius nested appropriately */
  border-radius: var(--radius-md);

  /* 5. Deep background block shadows */
  box-shadow: 
    0 4px 6px -1px rgba(0, 0, 0, 0.1),
    0 2px 4px -2px rgba(0, 0, 0, 0.06),
    inset 0 1px 0 rgba(255, 255, 255, 0.05); /* Optional inner top reflection */

  padding: var(--space-6);
}
```

---

## 2. Neumorphic Action Elements

### Architectural Concept
Neumorphism creates a 3D interface extruded from the base material. The element and its background *must* share the same matte hex tone. Interacting (pressing) toggles the surface state.

### Pseudo-HTML & Stylesheet Recipe
```html
<button class="neumorphic-button" aria-pressed="false">
  Interactive Plate
</button>
```

```css
/* Base Canvas needs to match component color */
body {
  background-color: #e0e8f6; /* Matte light blue-gray */
}

.neumorphic-button {
  background: #e0e8f6;
  border: none;
  border-radius: var(--radius-sm);
  padding: var(--space-3) var(--space-6);
  color: var(--text-primary);
  font-weight: 500;
  cursor: pointer;
  outline: none;

  /* Dual shadow system: Top-left light reflection + Bottom-right deep occlusion */
  box-shadow: 
    -6px -6px 16px rgba(255, 255, 255, 0.8),
     6px  6px 16px rgba(163, 177, 198, 0.6);

  transition: box-shadow var(--transition-fast), transform var(--transition-fast);
}

/* Focused elements must show outlines for accessibility */
.neumorphic-button:focus-visible {
  outline: 2px solid var(--accent-primary);
  outline-offset: 4px;
}

/* Pressed/Active Inset Transition */
.neumorphic-button:active,
.neumorphic-button[aria-pressed="true"] {
  /* Shadows flip inside the boundaries */
  box-shadow: 
    inset -4px -4px 10px rgba(255, 255, 255, 0.8),
    inset  4px  4px 10px rgba(163, 177, 198, 0.6);
  transform: scale(0.98);
}
```

---

## 3. Micro-Interaction: Hover & Press States

### Architectural Concept
Never snap hover transformations instantly. Use custom transitions that scale and translate elements along the Z-axis, with shadows broadening asynchronously as the surface lifts.

### CSS Animation Blueprint
```css
.card-interactive {
  background: var(--surface-default);
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-md);
  padding: var(--space-5);
  cursor: pointer;

  /* Explicitly transition the specific parameters to prevent paint layout shifts */
  transition: 
    transform var(--transition-normal), 
    box-shadow var(--transition-normal), 
    border-color var(--transition-normal);
}

/* Elevation Lift on Hover */
.card-interactive:hover {
  border-color: var(--border-strong);
  
  /* 1. Subtle elevation lift upward */
  transform: translateY(-4px);
  
  /* 2. Wider, softer shadow dispersion to match the height lift */
  box-shadow: 
    0 12px 24px -10px rgba(0, 0, 0, 0.15),
    0 4px 6px -4px rgba(0, 0, 0, 0.08);
}

/* Compression on Active Press */
.card-interactive:active {
  /* Compression downwards below baseline */
  transform: translateY(-1px);
  
  /* Shadow compresses back to near-surface levels */
  box-shadow: 
    0 2px 4px rgba(0, 0, 0, 0.06);
    
  transition-duration: var(--transition-fast); /* Faster snappy response for direct physical feedback */
}
```

---

## 4. Basic Responsive Card Grid

### Architectural Concept
A resilient responsive design uses fluid grid metrics with fallback boundaries, avoiding hardcoded fixed desktop layouts. 

### CSS Grid Blueprint
```css
.card-grid {
  display: grid;
  
  /* 1. Auto-fit columns dynamically based on minimum card width boundaries */
  /* Fallback: Cards wrap naturally if width declines below 280px */
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  
  /* 2. Dynamic gaps mapped to standard spacing grid scales */
  gap: var(--space-6); /* 24px gap */
  width: 100%;
}

@media (max-width: 640px) {
  .card-grid {
    /* 3. Muted tighter layout sizing for mobile canvases */
    gap: var(--space-4); /* 16px gap */
    grid-template-columns: 1fr; /* Single column layout enforcement if viewport is very tight */
  }
}
```
