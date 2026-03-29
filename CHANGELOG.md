# DualTone Changelog

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
