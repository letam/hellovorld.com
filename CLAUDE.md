# CLAUDE.md

## Project Overview

**hellovorld.app** is a browser-based interactive programming console and educational course application. Users write JavaScript in a textarea editor and execute it against an HTML5 Canvas to create visual output. The project is a work-in-progress by Tam Le.

- **Language:** Pure JavaScript (ES modules, no TypeScript)
- **Framework:** None (vanilla JS, Tachyons CSS via CDN)
- **Bundler:** esbuild
- **Package manager:** Bun (preferred), npm also works
- **No test framework** is configured

## Repository Structure

```
src/
  index.html              # Main HTML entry point
  index.css               # Custom styles (dark mode, editor, rulers)
  scripts/
    index.js              # Main application logic (~1200 lines)
    util.js               # Utility functions (sleep, strSplice, $ DOM helper)
    gui/
      buttons.js          # Touch/click handlers for arrow control buttons
    polyfills/
      trimStart.js        # String.trimStart polyfill for older browsers
util/
  build.sh                # Production build script (esbuild + cache-busting hashes)
.github/
  workflows/
    deploy.yml            # GitHub Actions: build with Bun, deploy to GitHub Pages
```

## Commands

```bash
bun install               # Install dependencies
bun serve-src             # Dev server on http://localhost:3000 (serves src/ directly)
bun run build-dist        # Production build to dist/ (esbuild bundle + content hash)
bun serve-dist            # Serve production build on http://localhost:8000
```

### Linting and Formatting

```bash
npx eslint src/           # Lint JavaScript
npx prettier --check src/ # Check formatting
npx prettier --write src/ # Auto-format
```

## Code Style and Conventions

### Formatting (Prettier)
- No semicolons (`"semi": false`)
- Trailing commas everywhere (`"trailingComma": "all"`)
- 88 character print width

### Linting (ESLint)
- Extends `eslint:recommended` and `plugin:prettier/recommended`
- Browser environment, ES2021
- ECMAScript 12, ES module source type

### Naming Conventions
- Short aliases for frequently-used canvas functions: `c()`, `sc()` (setColor), `fr()` (fillRect)
- `w` = world canvas context, `f` = exposed functions, `v` = view objects
- Constants use UPPER_CASE: `WORLD_SIZE`, `RULE_THICKNESS`, `TAB`, `NEW_LINE`
- Regular functions use camelCase

### Module Patterns
- Named exports: `export function sleep()`, `export const $`
- Side-effect imports for modules that self-register: `import "./gui/buttons.js"`
- ES module `<script type="module">` in HTML

### Architecture
- Single main function scope in `src/scripts/index.js` containing most application logic
- State stored in module-scope variables (`bag`, `currentTheme`, event listener arrays)
- Global window properties exposed intentionally for user code (`window.w`, `window.f`, `window.v`)
- `eval()` used deliberately to execute user-entered code in an async wrapper
- Event listeners, animation frames, intervals, and timeouts are tracked in arrays and cleaned up before each code execution

### Data Persistence
- `localStorage` for theme preference and editor content

## Build Process

The `util/build.sh` script:
1. Creates `dist/` directory if missing
2. Bundles JS with esbuild (minified + sourcemap) to `dist/bundle.js`
3. Renames bundle with SHA1 content hash for cache busting (e.g., `bundle.<hash>.js`)
4. Updates sourcemap reference in the hashed bundle
5. Generates `dist/index.html` with the hashed bundle filename
6. Copies CSS to `dist/`

## CI/CD

GitHub Actions deploys to GitHub Pages on push to `main`:
- Uses Bun for install and build
- Uploads `dist/` as a Pages artifact

## Key Things to Know

- There are **no tests**. No test runner is configured.
- The project is **pure vanilla JavaScript** -- no React, Vue, or other component frameworks.
- The HTML uses **Tachyons CSS** utility classes from CDN for layout and styling.
- The codebase intentionally exposes globals (canvas context, helper functions) so users can call them from the editor.
- `dist/` and `tmp/` are gitignored; never commit build artifacts.
- Commit messages are descriptive lowercase, optionally prefixed with a scope (e.g., `refactor:`, `cleanup:`, `UX:`).
