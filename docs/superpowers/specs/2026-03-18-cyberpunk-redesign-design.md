# Cyberpunk Hello Vorld — Full UI Redesign

## Overview

Redesign hellovorld.com with a cyberpunk/hacker aesthetic that feels animated, alive, and welcoming to beginners. Dark backgrounds, neon accents (cyan, magenta, purple), smooth animations, glass-morphism UI elements. The vibe is "your cool friend's hacker setup" — not intimidating.

All existing functionality is preserved: code editor, eval execution, canvas output, rulers, localStorage persistence, keyboard shortcuts, theme toggle, mobile touch controls.

## Layout

Restructure the current layout (lesson+editor left, canvas right) into a cleaner two-panel design:

```
┌──────────────────────────────────────────────────┐
│  ✦ Hello Vorld — A Course on Computer Programming │  ← header (slim, glowing)
│  [▼ Lesson] [theme toggle]                        │
├────────────────────────┬─────────────────────────┤
│                        │                         │
│   Code Editor          │   Canvas (#world)       │
│   (textarea)           │   450x450               │
│                        │                         │
│                        │   [Rulers]              │
│                        │                         │
├────────────────────────┤   [Toggle Rules]        │
│ [⬆ Expand] [Magic] [Execute] │   [← ↑ →]       │
└────────────────────────┴─────────────────────────┘
```

- **Header**: Slim bar with glowing title + theme toggle. Lesson intro text toggles via a JS click handler on a "Lesson" button — collapsed by default after first visit (state in localStorage). When expanded, lesson text appears as a panel below the header.
- **Main area**: Side-by-side panels — **editor (left)** and **canvas + console (right)** — filling the viewport.
- **Controls**: Execute, Magic, and Expand buttons in the editor panel footer. Arrow controls + Toggle Rules remain below the canvas.
- **Expand editor feature**: Preserved. When active, hides header/lesson and expands editor vertically, same as current behavior but restyled to match cyberpunk theme.

### Mobile (breakpoint: `max-width: 768px`)
- Editor stacks above canvas, full-width panels
- Canvas scales: CSS `width: 100%`, `height: auto`, with `max-width: 450px` to cap size. Canvas element `width`/`height` attributes stay at 450 for drawing resolution; CSS handles display scaling.
- Lesson auto-collapsed on mobile
- Arrow buttons get larger touch targets (`min-height: 48px`)
- Page-load stagger animation reduced to 0.1s delay (faster feel on mobile)

## Color System

CSS custom properties for theming. Theme is toggled by setting `data-theme="dark"|"light"` on `<html>`, which switches all custom property values.

### Dark mode (default — cyberpunk)
- `--bg-primary`: `#0a0a0f` (near-black with blue tint)
- `--bg-panel`: `rgba(15, 15, 30, 0.85)` (translucent dark panel)
- `--text-primary`: `#e0e0e8`
- `--text-secondary`: `#8888aa`
- `--neon-cyan`: `#00f0ff`
- `--neon-magenta`: `#ff00aa`
- `--neon-purple`: `#b44aff`
- `--glow-cyan`: `0 0 15px rgba(0, 240, 255, 0.4)`
- `--glow-magenta`: `0 0 15px rgba(255, 0, 170, 0.4)`
- `--ruler-color`: `#00f0ff` (cyan tick marks and labels)
- `--canvas-bg`: `#000000`

### Light mode (daylight cyberpunk)
- `--bg-primary`: `#f0f0f5`
- `--bg-panel`: `rgba(255, 255, 255, 0.85)`
- `--text-primary`: `#1a1a2e`
- `--text-secondary`: `#555577`
- `--neon-cyan`: `#0088aa` (deeper cyan for light bg contrast)
- `--neon-magenta`: `#cc0088` (deeper magenta)
- `--neon-purple`: `#8822cc` (deeper purple)
- `--glow-cyan`: `0 0 12px rgba(0, 136, 170, 0.3)`
- `--glow-magenta`: `0 0 12px rgba(204, 0, 136, 0.3)`
- `--ruler-color`: `#0088aa`
- `--canvas-bg`: `#1a1a2e` (dark canvas even in light mode — code output looks better on dark)

## Visual Elements

### Background
Subtle dark CSS grid pattern (repeating linear gradients forming a grid). A slow-moving diagonal gradient sweep animates across the page using CSS keyframes (~20s loop). Purely decorative, no performance impact.

### Panels (editor, canvas, lesson)
- `backdrop-filter: blur(10px)` for glass-morphism. Fallback: solid `--bg-panel` with full opacity — works fine without blur, just not translucent.
- Rounded corners (`border-radius: 12px`)
- 1px border in `--neon-cyan` at low opacity
- Subtle `box-shadow` glow in cyan

### Typography
- **Code**: Keep existing monospace stack (Consolas, Monaco, monospace)
- **UI text**: `system-ui, -apple-system, sans-serif` (no external font load)
- **Headings**: Neon-colored with `text-shadow` glow
- **Lesson text**: `--text-secondary` color, clean and readable

### Buttons
- Glass-morphism background (`rgba` + `backdrop-filter`)
- 1px neon border
- On hover: glow intensifies (`box-shadow` transition), slight `scale(1.03)`
- On active/click: `scale(0.97)` press-down
- Execute button: cyan glow accent
- Magic button: magenta glow accent with subtle pulse animation
- Toggle Rules button: styled same as other buttons, neon purple accent

### Editor textarea
- Dark translucent background matching panel style
- Neon cyan `caret-color`
- On focus: border glow brightens (transition)
- Monospace font, same sizing as current

### Canvas (#world)
- Neon border glow (cyan by default)
- On code execution: border flash animation (glow brightens then fades, ~0.5s). Triggered by adding class `.flash-execute`, removed on `animationend` event.
- `border-radius: 8px` on the wrapper. Canvas wrapper background matches `--canvas-bg` so rounded corners don't show page background bleed.

### Rulers
- Ruler draw functions read `--ruler-color` (via JS `getComputedStyle` or a JS variable synced from theme state) instead of hardcoded `"white"/"black"`
- Tick marks and labels in `--ruler-color` on dark backgrounds matching panel style

### Theme toggle
- Styled as a glowing icon/button matching the button system
- Smooth CSS transition between dark and light modes (~0.3s)

## Animations

All CSS-only (keyframes + transitions). No JS animation libraries.

**Replaces** the existing page-load reveal animation (`opacity: 0.01` → `1` transition). The old `revealDocumentBody()` and `revealEditor()` JS functions are replaced with the new CSS animation system.

The existing **typing effect** (welcome message typed character-by-character on first visit) is preserved as-is — it runs inside the new animation timing.

### `prefers-reduced-motion`
All animations and transitions are wrapped in `@media (prefers-reduced-motion: no-preference)`. Users who prefer reduced motion get instant display with no glow pulses or sweeps.

### Page load
- Staggered fade-in + slide-up for header, then editor, then canvas (`animation-delay` offsets)
- Duration: ~0.6s per element, ~0.2s stagger (0.1s on mobile)

### Title
- Subtle `text-shadow` pulse: glow intensity oscillates slowly (~3s loop)

### Background grid
- Slow diagonal gradient sweep (~20s loop, `background-position` animation)

### Code execution
- Canvas border glow flash: class `.flash-execute` added by JS, removed on `animationend`
- Editor panel gets class `.flash-execute` briefly too

### Button interactions
- Hover: glow + scale transition (~0.2s ease)
- Active: scale down (~0.1s)

### Editor focus
- Border glow transition on focus/blur (~0.3s)

### Magic button
- Idle: subtle magenta pulse animation (~2s loop)

## Tech Approach

- **No new frameworks**. Pure CSS additions on top of existing Tachyons.
- Use CSS custom properties for the entire color/glow system.
- Use CSS `@keyframes` for all animations.
- Use `backdrop-filter` for glass-morphism (solid fallback for unsupported browsers).
- Keep Tachyons for layout utilities; add custom classes for cyberpunk styling.
- Remove Tachyons color utility classes (`bg-black-90`, `near-white`, etc.) from theme-toggled elements — replaced entirely by custom properties.
- One potential lightweight addition: CSS `@property` for Houdini-based gradient animation (progressive enhancement, falls back to static gradient).

## Files Modified

- `src/index.css` — Major expansion: custom properties, animations, glass-morphism, glows, responsive layout overhaul, `prefers-reduced-motion` support
- `src/index.html` — Layout restructure (two-panel), class changes, add lesson toggle button, update element structure
- `src/scripts/index.js` — **Moderate changes**:
  - Rewrite `setDarkmodeTheme()` / `setLightmodeTheme()` to toggle `data-theme` attribute instead of swapping Tachyons classes
  - Update ruler draw functions (6 functions) to use theme-aware colors instead of hardcoded black/white
  - Replace `revealDocumentBody()` / `revealEditor()` with CSS animation triggers
  - Add `.flash-execute` class toggle on code execution (add class, remove on `animationend`)
  - Add lesson panel toggle handler (JS click → toggle class, persist state to localStorage)

## Files NOT Modified

- `src/scripts/util.js` — No changes
- `src/scripts/gui/buttons.js` — No changes (touch handlers stay the same; button restyling is CSS-only)
- `src/scripts/polyfills/trimStart.js` — No changes
- `package.json` — No new dependencies
- Build pipeline — No changes

## Out of Scope

- Code syntax highlighting (would require a library like CodeMirror/Monaco — future enhancement)
- Sound effects
- User accounts or backend changes
- New lessons or content changes
