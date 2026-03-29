# DualTone -- Fresh Audit (Nash)

Date: 2026-03-29 (night session)
Previous score: 8.0/10 (several iterations ago)
File: `index.html` (1255 lines) + `sw.js` (37 lines)

---

## Checklist Results

### 1. localStorage -- try/catch
**PASS.** All three localStorage calls wrapped in try/catch:
- `loadSaved()` line 697: `try{...}catch(e){}`
- `saveSaved()` line 702: `try{...}catch(e){}`
- `loadAppTheme()` line 706: `try{...}catch(e){}`
- `applyAppTheme()` line 1045: `try{...}catch(e){}`

### 2. WCAG AA contrast 4.5:1
**PARTIAL PASS.** The app's own UI has issues:
- `--text-tertiary: rgba(255,255,255,0.52)` on dark `#1a1a2e` -- computed roughly `#868686` on `#1a1a2e`. Luminance: ~0.086 vs ~0.027. Ratio ~(0.136/0.077) = ~1.77:1. **FAIL AA.** This is used for section titles, collapsible headers, harmony select, random btn.
- `--text-secondary: rgba(255,255,255,0.60)` on dark -- similarly borderline. Computed ~`#999` on `#1a1a2e` = roughly 4.1:1. **Borderline FAIL** for normal text.
- Light theme `--text-tertiary: rgba(0,0,0,0.58)` on `#f5f5f7` -- computed `#6b6b6b` on `#f5f5f7`. Ratio ~3.6:1. **FAIL AA** for normal text.

**P2-CONTRAST-1:** `--text-tertiary` fails WCAG AA on both themes. Used on `.section-title`, `.collapsible-header`, `.harmony-select`, `.random-btn`, `.saved-chip`.

**P2-CONTRAST-2:** `--text-secondary` in dark mode at 0.60 opacity is borderline. Needs bump to 0.65+.

### 3. Focus trap in modals
**N/A.** No modals or overlays in the current implementation. Collapsible sections are inline, not modal. No focus trap needed.

### 4. prefers-reduced-motion
**PASS.** Line 38-39: `@media(prefers-reduced-motion:reduce)` sets all animation/transition durations to 0.01ms.

### 5. Keyboard navigation
**PASS with minor issues.**
- Skip-to-content link present (line 284)
- All swatches have `role="button"`, `tabindex="0"`, Enter/Space handlers
- Semantic cards have `role="button"`, `tabindex="0"`, Enter/Space handlers
- Tab lists (preview mode, export format) have arrow key navigation via `setupTablistKeys()`
- Collapsible headers are `<button>` elements with `aria-expanded` and `aria-controls`
- `:focus-visible` outline style at line 279

**P3-KB-1:** Saved chip delete buttons (`x`) are accessible, but the saved chip `<div>` itself is clickable without `role="button"` or `tabindex`. Not keyboard-accessible for loading a saved palette.

### 6. Offline (PWA)
**PASS.** External `sw.js` file exists and is registered properly (`navigator.serviceWorker.register('./sw.js')`). Cache-first strategy with runtime caching. Manifest via blob URL (won't enable installability, but offline caching works).

**P3-PWA-1:** Manifest via blob URL means the app won't pass PWA installability checks (no "Add to Home Screen"). Need a real `manifest.json` file.

**P3-PWA-2:** SW is cache-first without revalidation (stale-while-revalidate). Users won't get updates unless they manually clear cache or the CACHE_NAME version is bumped. Known issue from Roast My Listing audit.

### 7. Mobile viewport
**PASS.** `<meta name="viewport" content="width=device-width,initial-scale=1">` present. Responsive breakpoints at 1024px, 768px, 480px. On 480px, non-key swatches are hidden via `display:none` with `data-key-swatch` filter.

### 8. escapeHtml (including " and ')
**FAIL.** Line 718-721:
```js
function escapeHtml(str){
  var div=document.createElement('div');
  div.appendChild(document.createTextNode(str));
  return div.innerHTML;
}
```
This escapes `<`, `>`, `&` but NOT `"` or `'`. The function is used in contexts where output goes into HTML attributes (e.g., line 973: `style="background:'+escapeHtml(pair.bg)+'"` and `color:'+escapeHtml(displayFg)+'"`).

**P3-XSS-1:** Self-XSS only (user would have to inject via their own hex input which is validated to `[0-9a-fA-F]{6}`), so not exploitable in practice. But the pattern is wrong. `escapeHtml` should also replace `"` with `&quot;` and `'` with `&#39;` for attribute safety.

### 9. Progressive disclosure (collapsible sections)
**PASS.** Three collapsible sections implemented:
- "UI Preview & Export" (`previewExportToggle`)
- "WCAG Contrast Check" (`wcagToggle`)
- "Saved Palettes" (`savedToggle`)

All start collapsed (`aria-expanded="false"`). Chevron rotates on expand. `max-height` transition for animation.

### 10. OKLCH auto-fix -- correctness
**PASS.** Binary search with 30 iterations on OKLCH lightness (line 651). Direction-aware: lightens fg on dark bg, darkens on light bg. Chroma clamped per iteration. Converges to ~1e-9 precision.

**IMPORTANT FIX SINCE LAST AUDIT:** `applyWcagFixesToPalette()` (line 1182-1195) now propagates fixes into `state.palette`, so `renderExport()` called on line 1211 reflects fixed values. The previous audit noted this was broken -- it is now fixed.

**P3-AUTOFIX-1:** The fix mapping (line 1189) only covers pairs 0,1,3,4,5,6,7. Pairs 2,8,9 have fg=#ffffff which can't be meaningfully fixed by darkening white -- this is correct behavior. However, the mapping skips semantic colors entirely. If Success/White or Error/White fail, the auto-fix calculates a new white-ish color but doesn't apply it to the semantic tokens in the export.

---

## DualTone-Specific Checks

### Palette generation -- diversity
**PASS.** Harmony modes produce genuinely different hues:
- Complementary: +180 degrees
- Analogous: +30, +330
- Triadic: +120, +240
- Split-complementary: +150, +210

Neutral scale uses `C*0.08` (8% of base chroma) -- sufficiently desaturated. OKLCH lightness map covers full range (0.97 to 0.15) across 11 steps. Chroma scaling reduces at lightness extremes via power curve.

### Export CSS/Tailwind/JSON
**PASS.** All three formats generate correctly:
- CSS: `:root { --color-primary-50: ... }` with OKLCH comments, dark mode media query
- Tailwind v4: `@theme { --color-primary-50: ... }` format
- JSON: W3C Design Tokens format with `$value`/`$type`

### WCAG contrast checker
**PASS.** 10 meaningful pairs tested. `contrastRatio()` uses correct W3C relative luminance formula (line 617-629). `wcagLevel()` thresholds correct: 7 for AAA, 4.5 for AA, 3 for AA Large.

### Color blind simulation
**NOT IMPLEMENTED.** No color blindness simulation feature exists. Not mentioned in design.md as a requirement, but would be a strong enhancement for a color palette tool.

### Copy color -- clipboard
**PASS.** `copyText()` function (line 729-737) uses `navigator.clipboard.writeText()` with fallback to `document.execCommand('copy')`. Toast confirmation on success.

### Print CSS
**NOT IMPLEMENTED.** No `@media print` styles. Collapsible sections start collapsed, so print would show empty sections. No print-specific layout.

**P3-PRINT-1:** No print stylesheet. Users who want to print their palette get a broken layout.

---

## Bug Summary

| # | Severity | Issue | Line(s) |
|---|----------|-------|---------|
| 1 | P2 | `--text-tertiary` opacity 0.52/0.58 fails WCAG AA (~1.8:1 dark, ~3.6:1 light) | 19, 29 |
| 2 | P2 | `--text-secondary` dark mode opacity 0.60 is borderline (~4.1:1) | 18 |
| 3 | P3 | `escapeHtml()` does not escape `"` and `'` for attribute contexts | 718-721 |
| 4 | P3 | Saved chips not keyboard-accessible (no role/tabindex on chip div) | 989-1008 |
| 5 | P3 | Manifest via blob URL prevents PWA installability | 410-427 |
| 6 | P3 | SW cache-first without revalidation -- users won't get updates | sw.js:25-36 |
| 7 | P3 | No print CSS | -- |
| 8 | P3 | WCAG auto-fix doesn't propagate semantic color fixes to export | 1182-1195 |

**0 P1 (critical), 2 P2 (major), 6 P3 (minor)**

---

## Score: 7.8 / 10

Downgraded from previous 8.0 because fresh analysis reveals the text-tertiary contrast issue is more severe than previously assessed (used on many interactive elements), and the escapeHtml pattern remains unfixed despite being flagged across multiple products.

### What works well
- OKLCH color math is solid and correct
- Auto-fix algorithm is genuinely useful and now propagates to exports
- Progressive disclosure is well-implemented
- Keyboard navigation covers all major interactive elements
- localStorage is properly guarded
- prefers-reduced-motion respected
- Export formats are all correct and useful

### Roadmap to 9.0+

**Must fix (P2):**
1. Bump `--text-tertiary` to opacity 0.72+ (dark) and 0.72+ (light) to clear 4.5:1
2. Bump `--text-secondary` dark mode to opacity 0.70+ for comfortable AA compliance

**Should fix (P3, pick 3+):**
3. Fix `escapeHtml()` to also replace `"` -> `&quot;` and `'` -> `&#39;`
4. Add `role="button"` and `tabindex="0"` + keyboard handler to `.saved-chip`
5. Create real `manifest.json` file for PWA installability
6. Add basic `@media print` (expand all sections, remove sticky bar, ensure visibility)
7. Propagate semantic auto-fix values to export tokens
8. Add stale-while-revalidate pattern to SW (or at minimum, network-first for index.html)
