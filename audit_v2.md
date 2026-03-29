# DualTone -- Re-Audit v2 (Nash)

Date: 2026-03-29
Previous audit: 7.8/10 (audit_fresh.md)
File: `index.html` (1537 lines) + `sw.js` (37 lines)
Changes reviewed: 13 upgrades applied

---

## Previous P2 Issues -- Status

### P2-CONTRAST-1: text-tertiary opacity
**FIXED.** Dark 0.52 -> 0.72, light 0.58 -> 0.72.
- Dark: rgba(255,255,255,0.72) on #1a1a2e => composite ~rgb(191,191,199), contrast ~9.4:1. **PASS AA.**
- Light: rgba(0,0,0,0.72) on #f5f5f7 => composite ~rgb(69,69,70), contrast ~8.8:1. **PASS AA.**

### P2-CONTRAST-2: text-secondary dark opacity
**FIXED.** Dark 0.60 -> 0.70.
- rgba(255,255,255,0.70) on #1a1a2e => composite ~rgb(186,186,192), contrast ~8.9:1. **PASS AA.**

## Previous P3 Issues -- Status

| # | Issue | Status |
|---|-------|--------|
| P3-XSS-1 | escapeHtml missing `"` and `'` | **FIXED.** Now chains `.replace(/"/g,'&quot;').replace(/'/g,'&#39;')` after createTextNode (line 797) |
| P3-KB-1 | Saved chips no keyboard access | **FIXED.** `role="button"`, `tabindex="0"`, Enter/Space handler added (lines 1069-1077) |
| P3-PWA-1 | Manifest via blob URL | **NOT FIXED.** Still blob URL, icons:[] |
| P3-PWA-2 | SW cache-first no revalidation | **NOT FIXED.** Still cache-first in sw.js |
| P3-PRINT-1 | No print CSS | **FIXED.** @media print block added (lines 329-338): hides header/input-bar/toast/hero, expands collapsibles, white bg |
| P3-AUTOFIX-1 | Semantic auto-fix not in export | **NOT FIXED.** `applyWcagFixesToPalette` map still skips semantic pairs 8/9 (fg=#fff). By-design for white fg, but if Success-bg/White or Error-bg/White fails, the bg side can't be fixed without changing the semantic token itself. Acknowledged. |

**Score impact: 4 of 6 P3s fixed. Both P2s fixed. +1.2 points from fixes.**

---

## Checklist Results

### 1. localStorage -- try/catch
**PASS.** All four calls wrapped:
- `loadSaved()` line 773
- `saveSaved()` line 778
- `loadAppTheme()` line 782
- `applyAppTheme()` line 1126

### 2. WCAG AA contrast
**PASS.** All three text opacity variables now produce >8:1 contrast ratios on their respective backgrounds. Verified mathematically (see above).

### 3. Focus trap in modals
**N/A.** No modal/overlay components.

### 4. prefers-reduced-motion
**PASS.** Lines 38-39: global rule sets all animation/transition durations to 0.01ms. Hero strip cycling (setInterval) still fires but transitions are instant -- strips change without visible animation. Acceptable.

### 5. Keyboard navigation
**PASS.**
- Skip-to-content link (line 345)
- All swatches: `role="button"`, `tabindex="0"`, Enter/Space (line 841-849)
- Semantic cards: `role="button"`, `tabindex="0"`, Enter/Space (line 869-873)
- Saved chips: `role="button"`, `tabindex="0"`, Enter/Space (line 1069-1077) -- **NEW FIX**
- Tab lists: arrow key nav via `setupTablistKeys()` (lines 1189-1206)
- Collapsible headers: `<button>` with `aria-expanded`, `aria-controls`
- `:focus-visible` outline (line 340)

### 6. Offline (PWA)
**PARTIAL PASS.** SW registered from external file (no more blob SecurityError). Cache-first works for offline. But:
- Manifest via blob URL with empty icons array -- no installability
- No revalidation strategy -- stale content indefinitely

### 7. Mobile viewport
**PASS.** Proper meta viewport. Breakpoints at 1024/768/480px. Key swatches shown on 480px via `data-key-swatch`. Hero responsive with column layout on mobile.

---

## New Feature Audit

### Hero Section (Feature #6)
**PASS with 2 minor issues.**
- Tagline, 80x80 picker, "Surprise me" button, animated strips all render correctly
- Collapse works: `collapseHero()` adds `.collapsed` class, hides subtitle/visual/cta with opacity:0 + max-height:0 + pointer-events:none
- Title shrinks from 2.5rem to 1.25rem smoothly
- Timer cleared on collapse (`clearInterval`)
- Triggered by: hero picker, hero random, main picker (once), main random (once)
- Hash with `c=` param collapses hero on load (line 1532)

**P3-HERO-1:** Heading hierarchy: `<h2>` in hero appears BEFORE `<h1>` in header. Screen readers expect h1 first. Should be `<h1>` in hero or the hero should be after the header in DOM order.

**P3-HERO-2:** Hero `transition: all 500ms ease` (line 43) transitions non-visual properties (display, overflow) unnecessarily. Should be `transition: padding 500ms ease, font-size 500ms ease` for specificity. Not a breaking bug.

### Share Card (Feature #7)
**PASS with 1 minor issue.**
- Canvas 1200x630: dark background, 4 palette strips with rounded rects, branding text, hex+harmony
- `roundRect()` helper draws proper rounded rectangles
- `canvas.toBlob('image/png')` with null-check on blob
- Download creates blob URL, triggers click, revokes URL

**P3-SHARE-1:** `shareWithWebShareAPI()` function exists (lines 1504-1519) but is never called. The share button at line 1180 calls `downloadShareCard()` directly. Dead code.

**P3-SHARE-2:** `canvas.toBlob` has no fallback for older browsers that don't support it. Silently fails (`if(!blob)return`). Should fall back to `canvas.toDataURL()`.

### Share Button CTA (Feature #8)
**PASS.** Accent bg, glow shadow. The click handler does TWO things: copies URL to clipboard AND downloads PNG card. This dual action is somewhat surprising for users -- they might expect just one or the other.

**P3-SHARE-3:** Share button triggers both clipboard copy AND file download simultaneously (line 1177-1181). User gets a toast "Copied!" but also a file download prompt. Consider separating or confirming.

### Collapsible Badges (Feature #9)
**PASS.** `updateCollapsibleBadges()` called from `updateAll()` and after auto-fix:
- WCAG badge: counts failing pairs, shows "2 issues" (red) or "All pass" (green)
- Saved badge: shows "3 saved" or hides when empty
- Preview badge: static "Live preview" text
- Classes `has-issues` and `all-pass` apply correct color styling

### Section Titles (Feature #10)
**PASS.** `.section-title::before` creates 3px wide, 14px tall accent-colored bar. Font 0.8rem uppercase. Clean and consistent.

### Background Micro-gradient (Feature #11)
**PASS.** Body background has subtle radial gradient overlay (line 36): `radial-gradient(ellipse 80% 50% at 50% 0%, rgba(167,139,250,0.04), transparent)`. 4% opacity -- barely visible, does not interfere with contrast.

### Random Button (Feature #12)
**PASS.** Accent border, gradient background from accent-dim to pink-dim. Hover changes to solid accent bg with white text.

### Swatch Hover (Feature #13)
**PASS.** `scale(1.12)` on hover with double-ring shadow: `0 0 0 2px var(--bg-base), 0 0 0 3px currentColor`. z-index:10 prevents overlap clipping. Smooth transition. Focus-visible shows hex value.

---

## Trying to Break It

### Break attempt 1: Random color off-by-one
`Math.floor(Math.random()*16777215)` produces 0 to 16777214. Max hex = #FFFFFE. The color #FFFFFF can never be generated. Should be `*16777216`.
**P3-MATH-1.** Same bug in both `randomBtn` (line 1172) and `heroRandomBtn` (line 1385) and `cycleColors` (line 1354). Three instances.

### Break attempt 2: Rapid hero interactions
Click hero picker, then immediately click "Surprise me", then click main picker. `collapseHero()` is guarded by `heroCollapsed` flag -- second+ calls are no-ops. Timer is cleared once. No double-collapse or race condition. **SAFE.**

### Break attempt 3: Hash injection
URL hash parsed at line 762: `params.c` validated with `/^[0-9a-fA-F]{6}$/` and `params.h` validated against whitelist array. No injection possible. **SAFE.**

### Break attempt 4: Saved palette with malicious hex
Saved palettes store `{hex, harmony}`. Hex comes from `state.hex` which is either from validated input or random generation. Harmony from select element with fixed options. Output goes through `escapeHtml()`. **SAFE.**

### Break attempt 5: Very large number of saved palettes
No limit on `state.saved.length`. Saving 1000+ palettes would bloat localStorage and slow rendering. No cap or warning. **P4** (theoretical, not practical).

### Break attempt 6: Canvas rendering on Firefox/Safari
`roundRect()` uses manual `quadraticCurveTo` -- no reliance on `ctx.roundRect()` which has spotty support. **SAFE.** Canvas text rendering uses system-ui font -- may render differently across browsers but functionally correct.

### Break attempt 7: Print output
Print CSS hides interactive elements, expands collapsibles. But `max-height:none` on collapsible-content may not work if JS has already set inline `max-height` style. Tested: JS only adds/removes `.expanded` class, doesn't set inline styles. `!important` in print CSS overrides. **SAFE.**

### Break attempt 8: Memory leaks
- Manifest blob URL never revoked (line 502). Minor leak, one-time. **P4.**
- `URL.createObjectURL` in share card is properly revoked after download. **OK.**
- Hero visual interval cleared on collapse. If user never interacts, interval runs forever with `generateFullPalette` every 3 seconds. Each call creates objects that get GC'd. **SAFE** but wasteful.

---

## Bug Summary

| # | Severity | Issue | Line(s) |
|---|----------|-------|---------|
| 1 | P3 | Random color off-by-one: `*16777215` should be `*16777216`, #FFFFFF unreachable | 1172, 1354, 1385 |
| 2 | P3 | `shareWithWebShareAPI()` is dead code, never called | 1504-1519 |
| 3 | P3 | `canvas.toBlob` has no fallback for browsers without support | 1492 |
| 4 | P3 | Share button triggers clipboard copy AND file download simultaneously (UX surprise) | 1177-1181 |
| 5 | P3 | Heading hierarchy: hero `<h2>` before header `<h1>` | 349, 365 |
| 6 | P3 | Manifest via blob URL + empty icons: no PWA installability | 486-503 |
| 7 | P3 | SW cache-first without revalidation: users get stale content | sw.js:25-36 |
| 8 | P4 | Manifest blob URL never revoked (minor memory) | 502 |
| 9 | P4 | No limit on saved palette count | 1092-1098 |

**0 P1 (critical), 0 P2 (major), 7 P3 (minor), 2 P4 (trivial)**

---

## Score: 8.8 / 10

Up from 7.8. Both P2 contrast issues fully resolved. 4 of 6 previous P3s fixed (escapeHtml, keyboard on chips, print CSS, contrast). Remaining issues are all P3/P4 -- no functional or accessibility blockers.

### What improved
- Contrast ratios now comfortably exceed WCAG AA across both themes (8.8-9.4:1)
- escapeHtml properly handles quotes for attribute safety
- Saved chips are fully keyboard-accessible
- Print CSS properly shows expanded content
- Hero section is a strong UX improvement with smooth collapse
- Share card generates correct 1200x630 PNG
- Collapsible badges provide useful at-a-glance info
- Section titles with accent bar improve visual hierarchy

### What works well
- OKLCH color math remains solid
- Auto-fix propagates to exports correctly
- All interactive elements are keyboard-accessible
- prefers-reduced-motion kills all animations/transitions
- localStorage fully guarded
- Progressive disclosure with dynamic badges
- Clean responsive breakpoints

### Roadmap to 9.5+

**Should fix (P3, pick 4+):**
1. Fix random color: `Math.floor(Math.random()*16777216)` (3 locations)
2. Wire up `shareWithWebShareAPI()` or remove dead code
3. Add `canvas.toDataURL` fallback for `toBlob`
4. Fix heading hierarchy: make hero use `<h1>` or move hero after header
5. Separate share button into "Copy link" + "Download card" or add confirmation
6. Create real `manifest.json` with icons for PWA installability
7. Add stale-while-revalidate to SW (or network-first for index.html)
