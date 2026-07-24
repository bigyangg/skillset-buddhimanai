---
name: dark-mode
description: Implement dark mode in any web application with correct CSS variable patterns. Use when a user says "add dark mode", "dark theme", "light/dark toggle", "system theme detection", "prefers-color-scheme", "theme switcher", "dark mode flash", or "CSS variables for theming".
---

# Dark Mode

## What This Does

Implement dark mode using CSS custom properties (variables) as the foundation. Covers system preference detection, manual toggle with persistence, preventing flash-of-wrong-theme (FOUT), and accessibility requirements.

## Non-Negotiables

- **CSS variables, not class duplications.** All color values reference tokens — never hardcode `#fff` in components.
- **System preference is the default.** Respect `prefers-color-scheme` before the user sets a preference.
- **Prevent flash of wrong theme.** The blocking script technique must be used in SSR/static apps.
- **Never toggle by swapping stylesheets.** CSS variable swapping via a data attribute is the correct approach.
- **Test WCAG contrast in both modes.** Both light and dark variants must meet 4.5:1 for normal text.

## CSS Variable Architecture

```css
/* index.css — Design Tokens */

:root {
  /* Semantic tokens — always reference these in components */
  --bg-primary:      #ffffff;
  --bg-secondary:    #f8f9fa;
  --bg-elevated:     #ffffff;
  --surface-default: #f1f3f5;
  
  --text-primary:    #111827;
  --text-secondary:  #6b7280;
  --text-muted:      #9ca3af;
  
  --border-subtle:   #e5e7eb;
  --border-default:  #d1d5db;
  
  --accent-primary:  #6366f1;
  --accent-hover:    #4f46e5;
  --accent-subtle:   #eef2ff;
  
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,0.1);
}

/* Dark mode token overrides */
[data-theme="dark"] {
  --bg-primary:      #0f172a;
  --bg-secondary:    #1e293b;
  --bg-elevated:     #1e293b;
  --surface-default: #334155;
  
  --text-primary:    #f1f5f9;
  --text-secondary:  #94a3b8;
  --text-muted:      #64748b;
  
  --border-subtle:   #1e293b;
  --border-default:  #334155;
  
  --accent-primary:  #818cf8;
  --accent-hover:    #6366f1;
  --accent-subtle:   #1e1b4b;
  
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.3);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,0.4);
}

/* Component usage — always use tokens, never raw colors */
.card {
  background:   var(--bg-elevated);
  border:       1px solid var(--border-subtle);
  color:        var(--text-primary);
  box-shadow:   var(--shadow-md);
}
```

## Theme Hook (React)

```typescript
// hooks/useTheme.ts
import { useState, useEffect } from 'react'

type Theme = 'light' | 'dark' | 'system'

export function useTheme() {
  const [theme, setThemeState] = useState<Theme>('system')
  const [resolvedTheme, setResolvedTheme] = useState<'light' | 'dark'>('light')

  // On mount, read saved preference
  useEffect(() => {
    const saved = localStorage.getItem('theme') as Theme | null
    if (saved) setThemeState(saved)
  }, [])

  // Apply theme to document and resolve system preference
  useEffect(() => {
    const systemDark = window.matchMedia('(prefers-color-scheme: dark)')

    const apply = (t: Theme) => {
      const isDark = t === 'dark' || (t === 'system' && systemDark.matches)
      document.documentElement.setAttribute('data-theme', isDark ? 'dark' : 'light')
      setResolvedTheme(isDark ? 'dark' : 'light')
    }

    apply(theme)

    // Listen for system preference changes (when theme === 'system')
    const handler = () => { if (theme === 'system') apply('system') }
    systemDark.addEventListener('change', handler)
    return () => systemDark.removeEventListener('change', handler)
  }, [theme])

  const setTheme = (t: Theme) => {
    setThemeState(t)
    localStorage.setItem('theme', t)
  }

  return { theme, resolvedTheme, setTheme }
}
```

## Flash Prevention (Critical for SSR/Next.js)

```typescript
// app/layout.tsx — blocking script prevents flash
export default function RootLayout({ children }) {
  return (
    <html lang="en" suppressHydrationWarning>
      <head>
        {/* This MUST be a blocking inline script — no defer/async */}
        <script
          dangerouslySetInnerHTML={{
            __html: `
              (function() {
                try {
                  var theme = localStorage.getItem('theme') || 'system';
                  var isDark = theme === 'dark' || 
                    (theme === 'system' && window.matchMedia('(prefers-color-scheme: dark)').matches);
                  document.documentElement.setAttribute('data-theme', isDark ? 'dark' : 'light');
                } catch (e) {}
              })();
            `,
          }}
        />
      </head>
      <body>{children}</body>
    </html>
  )
}
```

## Toggle Component

```typescript
// components/ThemeToggle.tsx
import { useTheme } from '@/hooks/useTheme'

export function ThemeToggle() {
  const { resolvedTheme, setTheme } = useTheme()

  return (
    <button
      onClick={() => setTheme(resolvedTheme === 'dark' ? 'light' : 'dark')}
      aria-label={`Switch to ${resolvedTheme === 'dark' ? 'light' : 'dark'} mode`}
      className="theme-toggle"
    >
      {resolvedTheme === 'dark' ? '☀️' : '🌙'}
    </button>
  )
}
```

## System Preference Only (No Toggle)

```css
/* Simplest approach — automatically follows system preference */
@media (prefers-color-scheme: dark) {
  :root {
    --bg-primary:   #0f172a;
    --text-primary: #f1f5f9;
    /* ... remaining dark tokens */
  }
}
```

## Images in Dark Mode

```css
/* Reduce image brightness in dark mode */
[data-theme="dark"] img:not([src*=".svg"]) {
  filter: brightness(0.85) contrast(1.05);
}

/* SVG icons that need color inversion */
[data-theme="dark"] .icon-invert {
  filter: invert(1);
}
```

## Checklist

- [ ] All component colors use CSS variables (no hardcoded hex in components)
- [ ] Flash prevention script in `<head>` (before any CSS)
- [ ] `data-theme` attribute on `<html>`, not `<body>`
- [ ] `suppressHydrationWarning` on `<html>` in React
- [ ] System preference respected on first visit (no saved preference)
- [ ] Toggle saves preference to `localStorage`
- [ ] WCAG 4.5:1 contrast verified in both modes
- [ ] Images slightly dimmed in dark mode
- [ ] `prefers-color-scheme` media query tested in browser DevTools
