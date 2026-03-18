# Cyberpunk Hello Vorld — Full UI Redesign

## Overview

Redesign hellovorld.com with a cyberpunk/hacker aesthetic that feels animated, alive, and welcoming to beginners. Dark backgrounds, neon accents (cyan, magenta, purple), smooth animations, glass-morphism UI elements. The vibe is "your cool friend's hacker setup" — not intimidating.

All existing functionality is preserved: code editor, eval execution, canvas output, rulers, localStorage persistence, keyboard shortcuts, theme toggle, mobile touch controls.

## Layout

Replace the current three-column layout (lesson / editor / canvas) with a two-panel design:

- **Header**: Slim, glowing title bar with "A Course on Computer Programming" and theme toggle. Lesson intro text is collapsible beneath the header.
- **Main area**: Side-by-side panels — **editor (left)** and **canvas + console (right)** — filling the viewport.
- **Controls**: Execute and Magic buttons integrated into the editor panel footer. Arrow controls remain below the canvas.
- **Mobile**: Editor stacks above canvas. Full-width panels.

## Color System

CSS custom properties for theming:

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

### Light mode (daylight cyberpunk)
- `--bg-primary`: `#f0f0f5`
- `--bg-panel`: `rgba(255, 255, 255, 0.85)`
- `--text-primary`: `#1a1a2e`
- `--text-secondary`: `#555577`
- Neon colors shift to deeper/more saturated variants for contrast on light backgrounds.

## Visual Elements

### Background
Subtle dark CSS grid pattern (repeating linear gradients forming a grid). A slow-moving diagonal gradient sweep animates across the page using CSS keyframes (~20s loop). Purely decorative, no performance impact.

### Panels (editor, canvas, lesson)
- `backdrop-filter: blur(10px)` for glass-morphism
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

### Editor textarea
- Dark translucent background matching panel style
- Neon cyan `caret-color`
- On focus: border glow brightens (transition)
- Monospace font, same sizing as current

### Canvas (#world)
- Neon border glow (cyan by default)
- On code execution: border flash animation (glow brightens then fades, ~0.5s)
- Slight `border-radius: 8px`

### Rulers
- Restyle with neon cyan tick marks and labels on dark backgrounds
- Match the overall panel aesthetic

### Theme toggle
- Styled as a glowing icon/button
- Smooth CSS transition between dark and light modes (~0.3s)

## Animations

All CSS-only (keyframes + transitions). No JS animation libraries.

### Page load
- Staggered fade-in + slide-up for header, then editor, then canvas (`animation-delay` offsets)
- Duration: ~0.6s per element, ~0.2s stagger

### Title
- Subtle `text-shadow` pulse: glow intensity oscillates slowly (~3s loop)

### Background grid
- Slow diagonal gradient sweep (~20s loop, `background-position` animation)

### Code execution
- Canvas border glow flash (cyan intensifies, then returns to normal over 0.5s)
- Editor gets a brief subtle flash too

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
- Use `backdrop-filter` for glass-morphism (widely supported).
- Keep Tachyons for layout utilities; add custom classes for cyberpunk styling.
- One potential lightweight addition: if animated gradient needs it, CSS `@property` for Houdini-based gradient animation (progressive enhancement, falls back gracefully).

## Files Modified

- `src/index.css` — Major expansion: custom properties, animations, glass-morphism, glows, responsive layout overhaul
- `src/index.html` — Layout restructure (two-panel), class changes, possible minor structural edits
- `src/scripts/index.js` — Minor: add/remove CSS classes on execution for animation triggers, update theme toggle logic for new custom properties

## Files NOT Modified

- `src/scripts/util.js` — No changes
- `src/scripts/gui/buttons.js` — No changes (touch handlers stay the same)
- `src/scripts/polyfills/trimStart.js` — No changes
- `package.json` — No new dependencies
- Build pipeline — No changes

## Out of Scope

- Code syntax highlighting (would require a library like CodeMirror/Monaco — future enhancement)
- Sound effects
- User accounts or backend changes
- New lessons or content changes
