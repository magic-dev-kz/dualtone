# DualTone Changelog



## v26.0 (2026-03-29) — Deferred Init for Faster First Paint

- **Performance: deferred non-critical init**: History rendering and recent colors wrapped in `requestIdleCallback` (with `setTimeout` fallback) so main UI renders before secondary features load
- SW cache bumped to `dualtone-v26.0`

---
## v25.0 (2026-03-29) — Security Meta Tags

- **Security hardening**: Added `X-Content-Type-Options: nosniff`, `referrer: no-referrer`, and `format-detection: telephone=no` meta tags to `<head>`
- SW cache bumped to `dualtone-v25.0`

---
## v24.0 (2026-03-29) — JSON-LD Structured Data

- **JSON-LD structured data**: Added WebApplication schema markup in `<head>` for improved SEO and rich search results
- SW cache bumped to `dualtone-v24.0`

---
## v23.0 (2026-03-29) — Deferred Font Loading

- **Non-blocking Google Fonts**: Font CSS changed from render-blocking `<link rel="stylesheet">` to `<link rel="preload" ... onload>` with `<noscript>` fallback — improves First Contentful Paint
- SW cache bumped to `dualtone-v23.0`

---
## v22.0 (2026-03-29) — Data Portability

- **Export all palette data as JSON backup**: "Export All Data" button in Saved Palettes section downloads saved palettes, palette history (last 10), recently used colors, and theme preference as a portable JSON file
- SW cache bumped to `dualtone-v22.0`

## v21.0 (2026-03-29) — Palette Lock

- **Palette lock**: Lock icon button on each palette row (Primary, Secondary, Accent, Neutral); locked scales preserve their base color when shuffling/randomizing; unlocked scales regenerate normally; lock state indicated by filled/open lock emoji and opacity; aria-pressed for accessibility
- SW cache bumped to `dualtone-v21.0`

## v20.0 (2026-03-29) — Print Styles Improved

- **Print stylesheet enhanced**: Added swatch color preservation via `print-color-adjust: exact`, page breaks on palette rows, hide PWA banner/footer/onboarding, `@page` margins
- SW cache bumped to `dualtone-v20.0`

## v19.0 (2026-03-29) — Error Handling Hardening

- **Hex input validation**: Strip `#` prefix, reject non-hex characters with clear error message
- SW cache bumped to `dualtone-v19.0`

## v18.0 (2026-03-29) — SEO / Meta Pass

- **description meta**: Added missing `<meta name="description">`
- **theme-color**: Added `<meta name="theme-color" content="#7c3aed">`
- **robots meta**: Added `<meta name="robots" content="index, follow">`
- **apple-touch-icon**: Inline SVG data URL with product emoji (🎨)
- SW cache bumped to `dualtone-v18.0`

## v17.0 (2026-03-29) — Accessibility Pass

Feature update by Mario.

### Skip-to-Content Link
- Already present since earlier versions; verified working for keyboard navigation

### Swatch Aria Labels
- All palette swatches now use `aria-label="Color: #HEX"` for consistent screen reader announcement
- Semantic color cards also updated to `aria-label="Color: #HEX"`

### Collapsible Sections
- All collapsible sections already have `aria-expanded` toggled correctly; verified

### Share Card
- Version badge updated to v17.0

### Service Worker
- Cache version bumped to `dualtone-v17.0`

---

## v16.0 (2026-03-29) — RGB/HSL Color Input

- **Color Input**: Accept rgb() and hsl() formats in addition to hex (e.g., rgb(255,0,128) or hsl(320,100%,50%))
- SW cache bumped to `dualtone-v16.0`
## v15.0.0 (2026-03-29) — Figma Plugin Format

Feature update by Mario.

### Figma Variables Export
- New "Copy Figma" button in header alongside CSS, Tailwind, and Design Tokens exports
- Exports in Figma variables JSON format with `collection: "Colors"` and two modes: Light and Dark
- Includes primary, secondary, accent (with light/dark variants), background, surface, surface-variant, text, text-secondary, and border tokens
- Clipboard copy with toast notification "Copied Figma variables!"
- Keyboard shortcut `F` to copy Figma variables
- Keyboard hint updated to include `F` Figma

### Share Card
- Version badge updated to v15.0

### Service Worker
- Cache version bumped to `dualtone-v15.0`

---

## v14.0.0 (2026-03-29) — Contrast Ratio Display

Feature update by Mario.

### Contrast Ratio on Swatches
- Each palette swatch now shows a WCAG contrast ratio (e.g. "12.5:1") on hover
- Ratio is calculated between the swatch background color and the auto-chosen text color (white or dark)
- Displayed as a small label in the top-right corner of each swatch
- Semantic color cards also show contrast ratio next to the hex code
- Uses the existing `contrastRatio()` function for accurate WCAG calculations

### Share Card
- Version badge updated to v14.0

### Service Worker
- Cache version bumped to `dualtone-v14.0`

---

## v13.0.0 (2026-03-29) — Design Tokens Export

### Design Tokens Export
- New "Copy Design Tokens" button in header alongside CSS and Tailwind export buttons
- Exports in W3C Design Tokens Community Group format (JSON)
- Includes both light and dark theme variants with primary, secondary, accent, background, surface, and text tokens
- Each token uses `$value` and `$type` fields per the DTCG spec
- Clipboard copy with toast notification
- Keyboard shortcut `D` to copy Design Tokens

### Share Card
- Version badge updated to v13.0

### Service Worker
- Cache version bumped to `dualtone-v13.0`

---

## v12.0.0 (2026-03-29) — Live UI Preview

### Mini UI Preview
- New "Live Preview" section between Semantic Colors and the collapsible panels
- 200x300px mock UI card showing: header bar with traffic light dots, paragraph text, secondary text, input field, and two buttons (Primary + Accent outline)
- All elements styled with CSS variables from the current palette in real time
- Light/Dark toggle to switch preview between themes
- Updates instantly on every color change, harmony switch, or random shuffle

### Font Pairing Suggestion
- Below the mini preview card, shows a recommended font pairing based on palette characteristics
- Warm palettes (hue 0-70, 310-360) suggest Georgia + Inter
- Cool palettes (hue 150-280) suggest SF Pro + Fira Code
- Neutral palettes suggest Inter + JetBrains Mono
- Vibrant palettes (high chroma > 0.15) suggest Poppins + Space Grotesk
- Each suggestion displays heading font, body font, and a descriptive vibe label

### Palette Naming
- Automatic palette name generated from hue, saturation, and lightness
- Displayed as a badge next to "DualTone" in the header
- Hardcoded hue ranges mapped to names (e.g., "Ember Sunset", "Breeze Ocean", "Dream Lavender")
- Low-chroma colors get neutral names (Silver Mist, Ash Cloud, Slate Shadow, Obsidian Night)
- Updates live with every color change

### Share Card
- Version badge updated to v12.0

### Service Worker
- Cache version bumped to `dualtone-v12.0`

---

## v11.0.0 (2026-03-29) — First Impression

### Onboarding Overlay
- Full-screen glassmorphism overlay on first visit (replaces v9 tooltip + v10 toast)
- Title with emoji, tagline "One color in. Full system out."
- Three feature bullets: OKLCH + WCAG, image extraction, multi-format export
- CTA button "Pick Your First Color" dismisses overlay
- Dismiss via CTA click, backdrop click, or Escape key
- Purple accent glow matching app theme
- Spring animation on card entrance

### Service Worker
- Cache version bumped to `dualtone-v11.0`

---

## v10.0.0 (2026-03-29) — Micro-polish

### First-Visit Welcome Toast
- On first visit (no localStorage), shows "Pick a color or drop an image to start" toast

### Gradient Preview Strip
- Hero palette strips height increased from 32px to 64px for better visual presence

### Share Card Button Glow
- Share button now pulses with an accent glow animation (`shareGlow`) for more prominence
- Hover glow intensified

### Service Worker
- Cache version bumped to `dualtone-v10.0`

---

## v9.0.0 (2026-03-29) — Share, Hints & Drop Zone Polish

### Share on Twitter/X
- New Twitter/X button in header opens tweet with palette hex color in URL hash

### First-Visit Keyboard Shortcut Tooltip
- On first visit, a floating tooltip appears: "Press Space for random color"
- Auto-dismisses after 5.5 seconds, stored in localStorage so it only shows once

### Image Drop Zone Visual Polish
- Animated pulsing border on drag-over (alternates between accent and pink)
- Enhanced glow effect during drag

### Service Worker
- Cache version bumped to `dualtone-v9.0`

---

## v8.0.0 (2026-03-29) — Image Color Extraction by Mario

### Image Color Extraction
- "Extract from Image" button added to hero section (alongside picker and random)
- "Extract from Image" button also in input toolbar
- Hidden file input accepts JPG, PNG, WebP (max 5 MB)
- Drag-and-drop support: drag any image onto the page to extract colors
- Full-screen drop zone overlay with visual feedback on dragover
- Image resized to 200px max dimension via Canvas for performance
- K-means clustering (k=6, 10 iterations, 1000 pixel sample) extracts dominant colors
- Extracted colors shown in a modal with image preview and 6 clickable color circles
- Click any extracted color to set it as base color and generate full palette
- Staggered pop-in animation on extracted color circles
- Hover reveals hex code below each circle
- Escape key closes extraction modal and drop zone
- Keyboard accessible (Enter/Space to select colors)

### Share Card
- Version badge updated to v8.0

### Service Worker
- Cache version bumped to `dualtone-v8.0`

### Print
- Extract button, drop zone, and extraction modal hidden in print mode

---

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
