# DualTone Changelog

## v7.0.0 (2026-03-29) — Retention Features by Mario

### URL Hash State
- Color saved to URL hash in simple format: `#ff5733`
- Loading a URL with hash auto-generates the palette for that color
- Enables sharing palettes via URL (e.g. `dualtone/#ff5733`)
- Backward compatible with legacy `#c=hex&h=harmony` format
- Hero collapses automatically when loading from any hash

### Keyboard Shortcuts
- `Space` generates a random color
- `C` copies CSS variables to clipboard
- `T` copies Tailwind config to clipboard
- `H` toggles harmony suggestions visibility
- `1` switches to light theme, `2` switches to dark theme
- Shortcuts disabled when typing in input fields
- Modifier keys (Ctrl/Cmd/Alt) do not trigger shortcuts
- Persistent keyboard hint overlay in bottom-right corner (hidden on mobile)

### Recently Used Colors
- Last 5 manually picked colors stored in localStorage (`dt_recent_colors`)
- Displayed as small clickable circles below the input bar
- Click any circle to restore that color and regenerate the palette
- Random colors (button, Space key, hero random) excluded from history
- Hover reveals hex code tooltip below each circle

### Share Card
- Version badge updated to v7.0

### Service Worker
- Cache version bumped to `dualtone-v7.0`

### Print
- Recently used colors and keyboard hint hidden in print mode

---

## v6.0.0 (2026-03-29) — Quality Polish by Mario

### Hero Visual Strips Update on Color Change
- Hero visual strips now update to reflect the current palette when color changes
- Previously only cycled random palettes on a 3s timer
- New `updateHeroStrips()` function called from `updateAll()` pipeline

### Swatch Click Feedback
- Pulse animation on swatch click (scale up with glow, then settle)
- Toast now shows the copied HEX value: "Copied #6366F1!" instead of generic "Copied!"
- Applied to both palette swatches and semantic color cards
- CSS `@keyframes swatchPulse` with spring easing

### Harmony Swatch Labels
- Labels (Compl., Analog 1, Analog 2, Triad 1, Triad 2, Current) now always visible
- Previously only showed on hover via `::after` pseudo-element
- Increased font-size to 0.6rem and weight 600 for readability

### WCAG Badge Pulse on Auto-fix
- When "Auto-fix All" resolves contrast issues, the WCAG badge pulses green
- `@keyframes wcagFixPulse` animates from accent to green with scale
- Pulse lasts 1.2s then returns to normal state
- Only triggers when fixes were actually applied

### Share Card
- Version badge updated to v6.0

### Service Worker
- Cache version bumped to `dualtone-v6.0`

---

## v5.0.0 (2026-03-29) — Feature Pack v2 by Mario

### Gradient Generator
- New collapsible "Gradient Generator" section with CSS gradient from current palette
- 3 gradient types: linear-gradient (135deg), radial-gradient (circle), conic-gradient
- Configurable color count (2, 3, or 4 colors from palette scales)
- Live preview strip with gradient rendering
- "Shuffle" button to randomize color selection from palette
- "Copy CSS" button for one-click gradient code copying

### Color Blind Simulation (enhanced)
- Added Achromatopsia (complete color blindness / monochromacy) as 4th simulation type
- Full set now: Normal, Protanopia, Deuteranopia, Tritanopia, Achromatopsia
- New SVG feColorMatrix filter using luminance coefficients (0.2126, 0.7152, 0.0722)
- Gradient preview also responds to color blindness simulation filters

### Export as Image (enhanced share card)
- Redesigned canvas card (1200x630) with 6 color strips instead of 4 palette rows
- Each strip shows color label at top and HEX code at bottom
- Added gradient accent strip below color bars
- Subtle radial glow effect from primary color in background
- Version badge (v5.0) in bottom-right corner
- Web Share API + download fallback preserved

### Preserved
- All v4 features intact: harmony suggestions, palette history, CSS/Tailwind export, saved palettes
- PWA, WCAG auto-fix, OKLCH color math unchanged
- All accessibility attributes (aria-*, role, tabindex) maintained

---

## v4.0.0 (2026-03-29) — Feature Pack by Mario

### Color Harmony Suggestions
- Clickable swatch circles below the input bar showing complementary, analogous, and triadic colors
- Current color highlighted with active border; hover reveals harmony label
- Clicking any suggestion swatch regenerates the full palette for that color

### Enhanced CSS Export
- "Copy CSS" button now exports separate light and dark theme variable blocks
- Light theme uses `:root {}`, dark theme uses `[data-theme="dark"], .dark {}`
- Includes primary, secondary, accent, neutral, surface, text, border, and semantic tokens

### Palette History
- Last 10 generated palettes saved to localStorage
- Collapsible "Recent Palettes" section with preview swatches and relative timestamps
- Click to restore any previous palette; clear history button

### Tailwind Config Export
- New "Copy Tailwind" button in header for classic `module.exports` config format
- Exports full color scales (primary, secondary, accent, neutral) + semantic colors
- Ready to paste into `tailwind.config.js`

### Preserved
- All existing JS logic, accessibility, PWA, print styles intact
- v3 glassmorphism, animations, color blindness simulation unchanged

---

## v3.0.0 (2026-03-29) — Visual Overhaul by Sky

### Typography
- Integrated Google Fonts (Inter) with weights 400-900
- Enhanced font-weight hierarchy: 800 headings, 700 section titles, 600/500 body
- Tighter letter-spacing on hero title for modern feel
- Improved line-height (1.55) for better readability

### Gradient Backgrounds
- Multi-layered radial gradient body background (accent, pink, indigo tones)
- Animated gradient on hero accent text (6s infinite shift)
- Gradient section title indicators (accent to pink)
- Deeper base colors (#0f0f1a dark / #f0f0f5 light)

### Glassmorphism
- `backdrop-filter: blur(20px) saturate(180%)` on header, input bar, panels
- Semi-transparent surfaces layered over blurred backgrounds
- Glass effect on toast, collapsible headers, WCAG cards, chips
- New `--glass-saturate` CSS variable for consistent vibrancy

### Micro-Animations & Hover Effects
- Spring easing (`cubic-bezier(0.34,1.56,0.64,1)`) on swatch hover
- Swatches: scale(1.18) + translateY(-4px) + glow on hover
- Semantic cards: scale(1.05) + colored glow on hover
- Hex label slides up on swatch hover
- Shimmer sweep on hero button; logo pulse animation
- All buttons gain lift on hover + press feedback

### Palette Generation Animation
- `swatchAppear` keyframe with staggered delays per swatch (20ms increments)
- Applied to both color swatches and semantic cards

### Shadows
- Dual-layer shadows (ambient + direct light) throughout
- New `--shadow-glow` variable for accent-colored ambient glow
- Swatch hover produces colored glow matching swatch color

### Preserved
- All JavaScript logic untouched
- All id, data-*, aria-* attributes unchanged
- Light/dark theme toggle fully functional
- Print styles, reduced-motion, responsive behavior intact

---

## v1.0 (2026-03-29)
- Initial release
- Ship-ready (9.0/10)
- PWA (service worker + manifest)
- WCAG AA accessible
- Works offline
