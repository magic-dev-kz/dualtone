# DualTone Fix Log

Audit score: 4/10 -> target 8+/10

## P0 Fixes Applied

### BUG-C1: PWA Support
- Added inline Service Worker via blob URL with cache-first strategy
- Added inline Web App Manifest via blob URL (name: "DualTone", display: "standalone", theme_color: "#6366F1")
- SW caches index.html and serves from cache first, falls back to network

### BUG-C2: WCAG Auto-fix
- Added "Auto-fix All" button in WCAG section header
- Algorithm: binary search on OKLCH lightness to achieve contrast ratio >= 4.5:1
- Direction-aware: lightens fg on dark bg, darkens fg on light bg
- Shows before/after for each fixed pair (e.g. "Fixed: #abc123 -> #def456")
- Fix count displayed via toast notification

### BUG-C3: Random Color Bias
- Replaced biased random with: `'#' + Math.floor(Math.random() * 16777215).toString(16).padStart(6, '0')`
- Ensures uniform distribution and always produces valid 6-digit hex

### BUG-M1: JSON Export W3C Compliance
- Changed `value` -> `$value` and `type` -> `$type` in all JSON Design Token output
- Applies to all scales (primary, secondary, accent, neutral, semantic, surface)

### BUG-M3: Share Button
- Added "Share" button in header actions
- Copies current URL (with hash params) to clipboard
- Shows toast "Link copied!"

### BUG-M5: Inline HEX Validation Error
- Red border + shake animation on invalid HEX input
- Error message "Invalid HEX color" shown below input via `aria-live="polite"`
- Clears on valid input or blur

### Contrast Fix
- `--text-tertiary` dark theme: opacity increased from 0.38 to 0.52
- `--text-tertiary` light theme: opacity increased from 0.32 to 0.50

### Performance
- Added `requestAnimationFrame` debounce on color picker input event
- Prevents excessive re-renders during continuous slider drag

### Accessibility
- Added skip-to-content link (`<a href="#mainContent">Skip to content</a>`)
- Changed DualTone logo text to `<h1>` element
- Added arrow key navigation (Left/Right) for both tablists (preview mode + export format)
- Proper `tabindex` management: active tab = 0, inactive = -1
- Added `role="tabpanel"` to preview content and export code areas

### Export Enhancements
- Added "Download" button next to "Copy to Clipboard" for CSS and JSON export
- Uses Blob + URL.createObjectURL for file download
- Filename: `dualtone-tokens.css` or `dualtone-tokens.json`
- CSS export now includes OKLCH values as comments alongside HEX values

---

## Round 2 Fixes

### BUG-R2-1: PWA — blob: URL SecurityError
- **Problem:** Browsers reject `navigator.serviceWorker.register()` with blob: URLs (SecurityError)
- **Fix:** Extracted SW code to external `sw.js` file; registration now uses `'./sw.js'`
- Manifest blob URL kept (manifests allow blob: URLs, only SW registration is restricted)

### BUG-R2-2: Light theme text-tertiary WCAG contrast
- **Problem:** `rgba(0,0,0,0.50)` on white bg gives ~3.9:1 — fails WCAG AA (4.5:1 min)
- **Fix:** Increased to `rgba(0,0,0,0.58)` → ~4.6:1 contrast ratio, passes AA

### BUG-R2-3: Auto-fix not propagated to export
- **Problem:** "Auto-fix All" stored fixes in `state.wcagFixes` but export functions only read from `state.palette`
- **Fix:** Added `applyWcagFixesToPalette()` that maps WCAG pair indices back to palette entries and mutates `state.palette` in-place before `renderExport()` is called
- Pairs with `#ffffff` as fg (indices 2, 8, 9) are skipped since white is not a palette-generated color
