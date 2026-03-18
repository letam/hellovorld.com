# Cyberpunk Hello Vorld Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Transform hellovorld.com into a cyberpunk-themed interactive coding education site with neon glows, glass-morphism panels, smooth animations, and a welcoming feel.

**Architecture:** CSS custom properties drive the entire color/glow system, toggled via `data-theme` attribute on `<html>`. HTML restructured into a two-panel layout (editor left, canvas right) with a collapsible lesson header. JS changes are moderate: theme toggle rewrite, ruler color updates, animation class triggers, and lesson panel toggle.

**Tech Stack:** Vanilla HTML/CSS/JS, Tachyons (layout utilities only), CSS custom properties, CSS keyframes, backdrop-filter.

**Spec:** `docs/superpowers/specs/2026-03-18-cyberpunk-redesign-design.md`

---

### Task 1: CSS Custom Properties and Base Theme

**Files:**
- Modify: `src/index.css` (full file — currently 39 lines)

This task establishes the entire color/glow system that everything else builds on.

- [ ] **Step 1: Add CSS custom properties for dark theme (default)**

Add at the top of `src/index.css`:

```css
:root,
[data-theme="dark"] {
  --bg-primary: #0a0a0f;
  --bg-panel: rgba(15, 15, 30, 0.85);
  --bg-panel-solid: rgb(15, 15, 30);
  --text-primary: #e0e0e8;
  --text-secondary: #8888aa;
  --neon-cyan: #00f0ff;
  --neon-magenta: #ff00aa;
  --neon-purple: #b44aff;
  --glow-cyan: 0 0 15px rgba(0, 240, 255, 0.4);
  --glow-magenta: 0 0 15px rgba(255, 0, 170, 0.4);
  --glow-purple: 0 0 15px rgba(180, 74, 255, 0.4);
  --ruler-color: #00f0ff;
  --canvas-bg: #000000;
  --border-color: rgba(0, 240, 255, 0.2);
  --link-color: var(--neon-cyan);
}

[data-theme="light"] {
  --bg-primary: #f0f0f5;
  --bg-panel: rgba(255, 255, 255, 0.85);
  --bg-panel-solid: rgb(240, 240, 245);
  --text-primary: #1a1a2e;
  --text-secondary: #555577;
  --neon-cyan: #0088aa;
  --neon-magenta: #cc0088;
  --neon-purple: #8822cc;
  --glow-cyan: 0 0 12px rgba(0, 136, 170, 0.3);
  --glow-magenta: 0 0 12px rgba(204, 0, 136, 0.3);
  --glow-purple: 0 0 12px rgba(136, 34, 204, 0.3);
  --ruler-color: #0088aa;
  --canvas-bg: #1a1a2e;
  --border-color: rgba(0, 136, 170, 0.3);
  --link-color: var(--neon-cyan);
}
```

- [ ] **Step 2: Add base body styles using custom properties**

Replace the existing `.darkmode`, `body[data-theme="dark"] a`, and `.o-01` rules with:

```css
body {
  background-color: var(--bg-primary);
  color: var(--text-primary);
  font-family: system-ui, -apple-system, sans-serif;
  margin: 0;
  min-height: 100vh;
  transition: background-color 0.3s, color 0.3s;
}

a {
  color: var(--link-color);
}
```

Remove `.o-01` (no longer needed — child element CSS animations handle the page reveal). Remove `.darkmode` class (replaced by custom properties). Remove `body[data-theme="dark"] a` (replaced by generic `a` rule).

- [ ] **Step 3: Verify in browser**

Run: `bun serve-src` and open `http://localhost:3000`. The page will look broken (theme toggle won't work yet) but the custom property definitions should be loadable without CSS errors. Check browser dev tools for any parse errors in the CSS.

- [ ] **Step 4: Commit**

```bash
git add src/index.css
git commit -m "feat: add CSS custom properties for cyberpunk color system"
```

---

### Task 2: Animated Background

**Files:**
- Modify: `src/index.css`

- [ ] **Step 1: Add grid background pattern and animated gradient sweep**

Add after the body styles:

```css
body::before {
  content: "";
  position: fixed;
  inset: 0;
  z-index: -1;
  background:
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 49px,
      rgba(0, 240, 255, 0.03) 49px,
      rgba(0, 240, 255, 0.03) 50px
    ),
    repeating-linear-gradient(
      90deg,
      transparent,
      transparent 49px,
      rgba(0, 240, 255, 0.03) 49px,
      rgba(0, 240, 255, 0.03) 50px
    );
  pointer-events: none;
}

body::after {
  content: "";
  position: fixed;
  inset: 0;
  z-index: -1;
  background: linear-gradient(
    135deg,
    rgba(0, 240, 255, 0.05) 0%,
    transparent 40%,
    rgba(255, 0, 170, 0.05) 60%,
    transparent 100%
  );
  background-size: 200% 200%;
  animation: none;
  pointer-events: none;
}

@media (prefers-reduced-motion: no-preference) {
  body::after {
    animation: gradient-sweep 20s ease-in-out infinite;
  }
}

@keyframes gradient-sweep {
  0%, 100% { background-position: 0% 0%; }
  50% { background-position: 100% 100%; }
}
```

- [ ] **Step 2: Verify grid and sweep are visible**

Reload `http://localhost:3000`. You should see a faint grid pattern and a slow-moving gradient on the dark background.

- [ ] **Step 3: Commit**

```bash
git add src/index.css
git commit -m "feat: add animated cyberpunk grid background"
```

---

### Task 3: Glass-Morphism Panel Styles

**Files:**
- Modify: `src/index.css`

- [ ] **Step 1: Add panel base class**

```css
.cyber-panel {
  background: var(--bg-panel);
  border: 1px solid var(--border-color);
  border-radius: 12px;
  box-shadow: var(--glow-cyan);
  padding: 1rem;
}

@supports (backdrop-filter: blur(10px)) {
  .cyber-panel {
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }
}
```

- [ ] **Step 2: Add button styles**

```css
.cyber-btn {
  background: var(--bg-panel);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  color: var(--text-primary);
  padding: 0.5rem 1rem;
  cursor: pointer;
  font-family: inherit;
  font-size: 0.875rem;
  transition: box-shadow 0.2s ease, transform 0.2s ease, border-color 0.2s ease;
}

.cyber-btn:hover {
  box-shadow: var(--glow-cyan);
  border-color: var(--neon-cyan);
  transform: scale(1.03);
}

.cyber-btn:active {
  transform: scale(0.97);
}

.cyber-btn--execute {
  border-color: rgba(0, 240, 255, 0.4);
  box-shadow: var(--glow-cyan);
}

.cyber-btn--magic {
  border-color: rgba(255, 0, 170, 0.4);
  box-shadow: var(--glow-magenta);
}

.cyber-btn--magic:hover {
  box-shadow: var(--glow-magenta);
  border-color: var(--neon-magenta);
}

.cyber-btn--purple {
  border-color: rgba(180, 74, 255, 0.4);
  box-shadow: var(--glow-purple);
}

.cyber-btn--purple:hover {
  box-shadow: var(--glow-purple);
  border-color: var(--neon-purple);
}

@media (prefers-reduced-motion: no-preference) {
  .cyber-btn--magic {
    animation: pulse-magenta 2s ease-in-out infinite;
  }
}

@keyframes pulse-magenta {
  0%, 100% { box-shadow: var(--glow-magenta); }
  50% { box-shadow: 0 0 25px rgba(255, 0, 170, 0.6); }
}
```

- [ ] **Step 3: Add editor textarea styles**

Replace the existing `#editor` rule with:

```css
#editor {
  font-family: Consolas, Monaco, "Lucida Console", "Liberation Mono",
    "DejaVu Sans Mono", "Bitstream Vera Sans Mono", "Courier New", monospace;
  resize: vertical;
  background: var(--bg-panel);
  color: var(--text-primary);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  padding: 1rem;
  caret-color: var(--neon-cyan);
  outline: none;
  transition: box-shadow 0.3s ease, border-color 0.3s ease;
  width: 100%;
  box-sizing: border-box;
}

#editor:focus {
  box-shadow: var(--glow-cyan);
  border-color: var(--neon-cyan);
}

@supports (backdrop-filter: blur(10px)) {
  #editor {
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }
}
```

- [ ] **Step 4: Add canvas wrapper styles**

Replace the existing `#world-wrapper` rule with:

```css
#world-wrapper {
  background-color: var(--canvas-bg);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  box-shadow: var(--glow-cyan);
  overflow: hidden;
  transition: box-shadow 0.3s ease;
}
```

- [ ] **Step 5: Add execution flash animation**

```css
@keyframes flash-execute {
  0% { box-shadow: var(--glow-cyan); }
  30% { box-shadow: 0 0 30px rgba(0, 240, 255, 0.8); }
  100% { box-shadow: var(--glow-cyan); }
}

.flash-execute {
  animation: flash-execute 0.5s ease-out;
}
```

- [ ] **Step 6: Commit**

```bash
git add src/index.css
git commit -m "feat: add glass-morphism panels, buttons, editor, and canvas styles"
```

---

### Task 4: HTML Layout Restructure

**Files:**
- Modify: `src/index.html`
- Modify: `src/index.css` (layout additions)

Restructure from the current nested layout to a clean two-panel design. The key changes:
1. Header becomes its own section with collapsible lesson
2. Editor and canvas sit side-by-side in a flex container
3. All buttons get `cyber-btn` classes
4. `data-theme="dark"` on `<html>` element

- [ ] **Step 1: Rewrite `src/index.html`**

```html
<!DOCTYPE html>
<html lang="en" data-theme="dark">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Hello Vorld — A Course on Computer Programming</title>
  <link rel="stylesheet" href="https://unpkg.com/tachyons@4.12.0/css/tachyons.css"/>
  <link rel="stylesheet" href="./index.css" />
</head>

<body>
  <div class="site-container">

    <!-- Header -->
    <header class="site-header ph3 pv2 flex items-center justify-between">
      <div class="flex items-center">
        <h2 class="mv0 f5 cyber-title">A Course on Computer Programming</h2>
        <button type="button" id="toggle-lesson" class="cyber-btn ml3 f7">Lesson</button>
      </div>
      <button id="toggle-theme" class="cyber-btn">Toggle Theme</button>
    </header>

    <!-- Collapsible Lesson Panel -->
    <div id="lesson-panel" class="lesson-panel ph3 pv2">
      <div class="cyber-panel">
        <h1 class="mv0 f4 cyber-title">Lesson 1 — Hello World</h1>
        <p class="mv2">Hello. Let's create the world.</p>
        <p class="mv0">
          A work in progress.<br/>
          Have any questions? Feel free to ask me anything at
          <a href="mailto:tam@hellovorld.com">tam@hellovorld.com</a>.
        </p>
      </div>
    </div>

    <!-- Main Two-Panel Layout -->
    <main class="main-panels ph3 pb3 flex">

      <!-- Left: Editor Panel -->
      <div id="editor-panel" class="editor-panel cyber-panel mr3 flex flex-column">
        <form id="editor-form" class="flex flex-column flex-grow-1">
          <textarea name="editor" id="editor" rows="20" autocorrect="off"
            autocapitalize="off" spellcheck="false" class="flex-grow-1"></textarea>
          <div class="flex justify-between items-center mt2">
            <button type="button" id="expand-editor-v" class="cyber-btn">&#x2B06;</button>
            <div class="flex">
              <button type="button" id="magic" class="cyber-btn cyber-btn--magic mr2">Magic</button>
              <button id="execute" class="cyber-btn cyber-btn--execute">Execute</button>
            </div>
          </div>
        </form>
      </div>

      <!-- Right: Canvas Panel -->
      <div id="console" class="console-panel cyber-panel flex flex-column">
        <div id="output">
          <div data-level="top" data-hidden="1" class="flex">
            <canvas id="orule"></canvas>
            <canvas id="xrule"></canvas>
          </div>
          <div data-level="middle" class="flex">
            <div data-level="middle-left" data-hidden="1">
              <canvas id="yrule"></canvas>
            </div>
            <div class="flex">
              <div id="world-wrapper">
                <canvas id="world"></canvas>
              </div>
              <div data-hidden="1">
                <canvas id="yrule2"></canvas>
              </div>
            </div>
          </div>
          <div data-level="bottom" data-hidden="1" class="flex">
            <canvas id="orule2"></canvas>
            <canvas id="xrule2"></canvas>
          </div>
        </div>
        <div class="flex justify-between items-center mt2">
          <button type="button" id="console-toggle-rules" class="cyber-btn cyber-btn--purple">Toggle Rules</button>
          <div class="flex arrow-buttons">
            <button type="button" id="key-arrow-left" class="cyber-btn">&loarr;</button>
            <div class="w1"></div>
            <button type="button" id="key-arrow-up" class="cyber-btn rotate-90">&loarr;</button>
            <div class="w1"></div>
            <button type="button" id="key-arrow-right" class="cyber-btn">&roarr;</button>
          </div>
        </div>
      </div>

    </main>
  </div>

  <script type="module" src="./scripts/index.js"></script>
</body>

</html>
```

Key differences from the old HTML:
- `data-theme="dark"` on `<html>` (not body)
- Removed `bg-black` and `o-01` from body — child element CSS animations handle the reveal
- Removed `vh-100` wrapper div, replaced with `.site-container`
- Header is its own `<header>` element with lesson toggle button
- Lesson content is in a separate collapsible `#lesson-panel` div
- Editor form is directly in `.editor-panel` (not nested inside `#lesson`)
- Removed `class="stuff"` elements
- All buttons have `cyber-btn` class
- Removed inline `style="color: white;"` from email link
- Removed Tachyons color classes — custom properties handle colors

- [ ] **Step 2: Add layout CSS to `src/index.css`**

```css
.site-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.site-header {
  border-bottom: 1px solid var(--border-color);
}

.cyber-title {
  color: var(--neon-cyan);
  text-shadow: var(--glow-cyan);
}

@media (prefers-reduced-motion: no-preference) {
  .site-header .cyber-title {
    animation: title-pulse 3s ease-in-out infinite;
  }
}

@keyframes title-pulse {
  0%, 100% { text-shadow: var(--glow-cyan); }
  50% { text-shadow: 0 0 20px rgba(0, 240, 255, 0.6), 0 0 40px rgba(0, 240, 255, 0.2); }
}

.lesson-panel {
  border-bottom: 1px solid var(--border-color);
}

.main-panels {
  flex: 1;
  min-height: 0;
  padding-top: 0.75rem;
}

.editor-panel {
  flex: 1;
  min-width: 0;
}

.console-panel {
  flex-shrink: 0;
}

#editor {
  min-height: 300px;
}
```

- [ ] **Step 3: Add page-load animations**

```css
.site-header,
.editor-panel,
.console-panel {
  opacity: 0;
  transform: translateY(10px);
}

@media (prefers-reduced-motion: no-preference) {
  .site-header {
    animation: fade-slide-in 0.6s ease-out 0.1s forwards;
  }
  .editor-panel {
    animation: fade-slide-in 0.6s ease-out 0.3s forwards;
  }
  .console-panel {
    animation: fade-slide-in 0.6s ease-out 0.5s forwards;
  }
}

@media (prefers-reduced-motion: reduce) {
  .site-header,
  .editor-panel,
  .console-panel {
    opacity: 1;
    transform: none;
  }
}

@keyframes fade-slide-in {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

- [ ] **Step 4: Add mobile responsive styles**

```css
@media (max-width: 768px) {
  .main-panels {
    flex-direction: column;
  }

  .editor-panel {
    margin-right: 0;
    margin-bottom: 0.75rem;
  }

  #world-wrapper canvas {
    width: 100%;
    height: auto;
    max-width: 450px;
  }

  .arrow-buttons .cyber-btn {
    min-height: 48px;
    min-width: 48px;
  }
}

@media (max-width: 768px) and (prefers-reduced-motion: no-preference) {
  .site-header {
    animation-delay: 0.05s;
  }
  .editor-panel {
    animation-delay: 0.15s;
  }
  .console-panel {
    animation-delay: 0.25s;
  }
}
```

- [ ] **Step 5: Update arrow-buttons and utility styles**

```css
.arrow-buttons {
  user-select: none;
  -webkit-touch-callout: none;
  gap: 0.5rem;
}

.rotate-90 {
  transform: rotate(90deg);
}
```

- [ ] **Step 6: Verify layout in browser**

Reload `http://localhost:3000`. The page will look partially broken (JS theme logic hasn't been updated yet), but the two-panel layout should be visible.

- [ ] **Step 7: Commit**

```bash
git add src/index.html src/index.css
git commit -m "feat: restructure HTML to two-panel cyberpunk layout"
```

---

### Task 5: JavaScript Theme System Rewrite

**Files:**
- Modify: `src/scripts/index.js`

This is the critical JS task. Rewrite theme system to use `data-theme` on `<html>`, update ruler colors, replace old reveal animation, add flash-execute trigger, and add lesson panel toggle.

- [ ] **Step 1: Add `getRulerColor()` helper inside `main()`**

Add after the variable declarations (around line 19, after `let debugFunction`):

```javascript
function getRulerColor() {
  return currentTheme === "dark" ? "#00f0ff" : "#0088aa"
}
```

- [ ] **Step 2: Add canvas clearing before ruler redraws**

Add a `clearCanvasRules()` function right after `bag.createCanvasRule` (around line 310):

```javascript
bag.clearCanvasRules = function () {
  orule.clearRect(0, 0, oruleCanvas.width, oruleCanvas.height)
  xrule.clearRect(0, 0, xruleCanvas.width, xruleCanvas.height)
  yrule.clearRect(0, 0, yruleCanvas.width, yruleCanvas.height)
  yrule2.clearRect(0, 0, yruleCanvas2.width, yruleCanvas2.height)
  orule2.clearRect(0, 0, oruleCanvas2.width, oruleCanvas2.height)
  xrule2.clearRect(0, 0, xruleCanvas2.width, xruleCanvas2.height)
}
```

Then in the `setTheme()` function (Task 5, Step 6), call `bag.clearCanvasRules()` before `bag.createCanvasRule()`. Without this, switching themes layers new colored strokes over old ones.

- [ ] **Step 3: Update all 6 ruler functions to use `getRulerColor()`**

Replace every occurrence of `currentTheme === "dark" ? "white" : "black"` with `getRulerColor()`. These appear in:
- `createORule()` (line 137) — `orule.strokeStyle = getRulerColor()`
- `createXRule()` (lines 155-156) — both `xrule.strokeStyle` and `xrule.fillStyle`
- `createYRule()` (lines 202-203) — both `yrule.strokeStyle` and `yrule.fillStyle`
- `createYRule2()` (line 234) — `yrule2.strokeStyle`
- `createORule2()` (lines 253-254) — both `orule2.strokeStyle` and `orule2.fillStyle`
- `createXrule2()` (line 290) — `xrule2.strokeStyle`

- [ ] **Step 4: Add flash-execute trigger in `evalCode()`**

After `worldCanvas.style.opacity = 1` (around line 99), add:

```javascript
// Trigger canvas flash animation
worldWrapper.classList.add("flash-execute")
document.getElementById("editor-panel").classList.add("flash-execute")
worldWrapper.addEventListener("animationend", () => {
  worldWrapper.classList.remove("flash-execute")
}, { once: true })
document.getElementById("editor-panel").addEventListener("animationend", () => {
  document.getElementById("editor-panel").classList.remove("flash-execute")
}, { once: true })
```

- [ ] **Step 5: Add lesson panel toggle after expand-editor-v handler**

Add after the expand-editor-v event listener block (around line 359):

```javascript
/* Lesson panel toggle */
let lessonPanel = document.getElementById("lesson-panel")
let lessonState = localStorage.getItem("lessonVisible")
// First visit: show lesson. Subsequent visits: respect saved state. Mobile: auto-collapse.
let isMobile = window.innerWidth <= 768
let lessonVisible = lessonState === null ? !isMobile : lessonState === "true"
lessonPanel.style.display = lessonVisible ? "" : "none"
document.getElementById("toggle-lesson").addEventListener("click", () => {
  lessonVisible = !lessonVisible
  lessonPanel.style.display = lessonVisible ? "" : "none"
  localStorage.setItem("lessonVisible", lessonVisible)
})
```

- [ ] **Step 6: Update expand-editor-v handler to target new elements**

Replace the handler (lines 340-359) — change `document.getElementsByClassName("stuff")` references to target `.site-header` and `#lesson-panel`:

```javascript
document.getElementById("expand-editor-v").addEventListener("click", (e) => {
  if (!isExpandedV) {
    document.querySelector(".site-header").style.display = "none"
    document.getElementById("lesson-panel").style.display = "none"
    e.target.innerText = ICON_ARROW_DOWN
    document.querySelector("#editor").style.height = isIOS
      ? `calc(100vh - ${2 * 44}px - 50px)`
      : `calc(100vh - ${2 * 44}px)`
  } else {
    document.querySelector(".site-header").style.display = ""
    let shouldShowLesson = localStorage.getItem("lessonVisible") !== "false"
    document.getElementById("lesson-panel").style.display = shouldShowLesson ? "" : "none"
    e.target.innerText = ICON_ARROW_UP
    document.querySelector("#editor").style.height = ``
  }
  isExpandedV = !isExpandedV
})
```

- [ ] **Step 7: Rewrite `themeConfiguration()` (lines 952-991)**

Replace the entire function with:

```javascript
function themeConfiguration() {
  function setTheme(theme) {
    document.documentElement.setAttribute("data-theme", theme)
    currentTheme = theme
    localStorage.setItem("theme", currentTheme)
    bag.clearCanvasRules()
    bag.createCanvasRule()
  }

  document.getElementById("toggle-theme").addEventListener("click", () => {
    setTheme(currentTheme === "dark" ? "light" : "dark")
  })

  setTheme(currentTheme)
  revealDocumentBody()
}
```

Note: removed `revealEditor()` call — editor reveal is handled by CSS panel animation.

- [ ] **Step 8: Simplify `revealDocumentBody()` (lines 993-1005)**

Replace with:

```javascript
function revealDocumentBody() {
  // Body no longer uses o-01 class — child element animations handle the reveal.
  // This function is kept as a no-op for compatibility with the call in themeConfiguration().
}
```

- [ ] **Step 9: Remove `revealEditor()` function (lines 1007-1021)**

Delete the entire `revealEditor()` function. The editor's appearance is handled by the CSS panel animation.

- [ ] **Step 10: Remove the `#lesson` element reference**

In `main()`, line 13: `let lesson = document.getElementById("lesson")` — this element no longer exists. Remove this line.

- [ ] **Step 11: Verify full functionality in browser**

Reload `http://localhost:3000`. Test:
- Dark theme loads correctly (neon cyan title, dark background, glowing panels)
- Theme toggle switches to light mode and back
- Rulers draw with cyan/teal colors
- Code execution triggers canvas flash
- Lesson panel toggles open/closed
- Expand editor works
- Magic button loads platformer
- Welcome typing effect still works
- Arrow buttons work
- localStorage persists theme and lesson state

- [ ] **Step 12: Commit**

```bash
git add src/scripts/index.js
git commit -m "feat: rewrite theme system, add flash animation and lesson toggle"
```

---

### Task 6: Polish and Final Touches

**Files:**
- Modify: `src/index.css` (minor)
- Modify: `src/scripts/index.js` (minor cleanup if needed)

- [ ] **Step 1: Clean up any remaining old CSS from `src/index.css`**

Remove if still present:
- `.darkmode` block
- `body[data-theme="dark"] a`
- Old `#lesson {}` empty rule
- Any duplicate rules

Keep `[data-hidden="1"]` rule (still used by rulers).

- [ ] **Step 2: Test mobile layout**

Open browser dev tools, toggle device toolbar, test at 375px and 768px width:
- Editor stacks above canvas
- Canvas scales to fit width
- Arrow buttons have adequate touch targets
- No horizontal overflow

- [ ] **Step 3: Test `prefers-reduced-motion`**

In browser dev tools → Rendering → Emulate `prefers-reduced-motion: reduce`:
- No pulsing title glow
- No background gradient sweep
- No magic button pulse
- Panels appear instantly

- [ ] **Step 4: Test light mode thoroughly**

Toggle to light mode and verify:
- Light background
- Readable text contrast
- Deeper neon accent colors
- Canvas stays dark
- Rulers use teal color

- [ ] **Step 5: Final commit**

```bash
git add src/index.css src/index.html src/scripts/index.js
git commit -m "feat: polish cyberpunk theme — cleanup, mobile, reduced-motion"
```

---

### Task Summary

| Task | Description | Files | Complexity |
|------|------------|-------|------------|
| 1 | CSS custom properties + base theme | `index.css` | Low |
| 2 | Animated grid background | `index.css` | Low |
| 3 | Glass-morphism panels, buttons, editor, canvas | `index.css` | Medium |
| 4 | HTML layout restructure + layout CSS + animations + mobile | `index.html`, `index.css` | Medium |
| 5 | JS theme rewrite, rulers, flash, lesson toggle, expand | `index.js` | Medium-High |
| 6 | Polish: cleanup, mobile test, reduced-motion, light mode | All 3 files | Low |
