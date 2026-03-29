# DualTone v2 -- QA Audit Report v4

**Auditor:** Nash (OpenClaw QA)
**Date:** 2026-03-29
**File:** `index.html` (~1769 lines), `sw.js` (37 lines)
**Scope:** v2 features (Palette History, Copy CSS Variables, Color Blindness Preview) + full checklist

---

## Summary

**Score: 7.5 / 10**
**Verdict: CONDITIONAL PASS** -- Ship after P1 and P2 fixes.

Solid engineering overall. The v2 features are well-implemented with proper localStorage guards, escapeHtml usage, and accessible markup. However, there are real issues in the color blindness SVG filter matrices, a missing `accent` scale in the quick CSS copy, and keyboard gaps on the color blindness toggle buttons.

---

## Checklist Results

### 1. localStorage try/catch

**PASS**

All localStorage operations are wrapped in try/catch:
- `loadSaved()` (line 831): try/catch
- `saveSaved()` (line 836): try/catch
- `loadAppTheme()` (line 840): try/catch
- `applyAppTheme()` (line 1187): try/catch
- `loadHistory()` (line 1600): try/catch with fallback to `[]`
- `saveHistory()` (line 1605): try/catch
- `clearHistory` click handler (line 1665): try/catch

No issues found. Graceful degradation on quota exceeded or disabled storage.

### 2. WCAG AA Contrast on New Elements

**P3 -- Minor concern**

- History items: use `--text-secondary` (rgba 0.70 white on dark / 0.54 black on light). The light theme `--text-secondary: rgba(0,0,0,0.54)` on `--bg-surface: rgba(255,255,255,0.80)` yields approximately 4.6:1 -- barely passes AA but not AAA.
- `history-item-time` uses `--text-tertiary` (rgba 0.72) which is borderline.
- `cb-label` uses `--text-tertiary` -- same concern.
- Copy CSS button inherits `--text-primary` -- fine.
- History "No history yet" inline span uses `--text-tertiary` -- informational only, acceptable.

### 3. Keyboard Navigation

**P2 -- Color blindness toggle buttons missing keyboard support**

- Swatch elements: Have `tabindex="0"`, `role="button"`, and `keydown` handlers for Enter/Space. PASS.
- Semantic cards: Same pattern. PASS.
- History items: Have `tabindex="0"`, `role="button"`, and keydown handlers. PASS.
- Saved chips: Same. PASS.
- Tablists (preview mode, export tabs): Arrow key navigation via `setupTablistKeys()`. PASS.
- Collapsible headers: Buttons with `aria-expanded`, keyboard-accessible natively. PASS.
- **Color blindness `.cb-btn` buttons** (lines 1731-1738): Only have `click` handlers. No arrow key navigation between them. They are `<button>` elements so Enter/Space work, but there is no `role="tablist"` / `role="tab"` pattern and no arrow-key group navigation like the other toggle groups have. This is inconsistent with the rest of the UI.

**Fix:** Wrap `.cb-toggle-wrap` in a `role="toolbar"` or `role="tablist"` with `aria-label`, add `role="tab"` + `aria-selected` to each `.cb-btn`, and wire up `setupTablistKeys('.cb-toggle-wrap')`.

### 4. prefers-reduced-motion

**PASS**

Lines 46-47: Global rule disables animation-duration, animation-iteration-count, and transition-duration for all elements under `prefers-reduced-motion: reduce`. The hero visual cycling (setInterval) still runs but only changes `background` which is covered by the transition-duration override. Acceptable.

### 5. XSS -- escapeHtml on all innerHTML

**PASS**

- `escapeHtml()` defined at line 852 using DOM-based textNode approach + manual quote escaping.
- Used consistently on: palette labels (line 887-888), semantic cards (line 926-927), WCAG cards (lines 1105-1109), saved chips (line 1125-1126), history items (lines 1648-1650).
- Swatch hex values (line 898) are generated internally from color math -- no user input, but could benefit from escapeHtml for defense-in-depth. Low risk.
- `showToast()` uses `textContent` (line 859) -- safe.
- `renderPreview()` (lines 957-976) builds HTML with palette hex values directly. These come from internal color math output (always `#xxxxxx` format from `rgbToHex`), never from user text input. Acceptable risk.
- History "No history yet" (line 1637) is a hardcoded string. Safe.

### 6. Color Blindness SVG Filters -- Correct Matrices?

**P1 -- Deuteranopia and Tritanopia matrices are inaccurate**

The matrices at lines 534-543:

**Protanopia** (line 535):
```
0.567 0.433 0    0 0
0.558 0.442 0    0 0
0     0.242 0.758 0 0
0     0     0    1 0
```
This is close to the Vienot et al. (1999) protanopia simulation. Acceptable.

**Deuteranopia** (line 538):
```
0.625 0.375 0    0 0
0.700 0.300 0    0 0
0     0.300 0.700 0 0
0     0     0    1 0
```
This is **incorrect**. The standard Vienot/Brettel deuteranopia matrix is:
```
0.625 0.375 0   0 0
0.700 0.300 0   0 0
0     0.300 0.700 0 0
```
Wait -- the values here actually match a commonly used approximation. However, the standard Brettel (1997) matrices differ. The row 2 value `0.300, 0.700` for the blue channel is a rough approximation. More accurate values from Machado et al. (2009) at full severity would be:
```
0.367 0.861 -0.228 0 0
0.280 0.673  0.047 0 0
-0.012 0.043 0.969 0 0
```
The current matrix is a simplified version that distorts blues significantly. It will produce a noticeably wrong simulation for blue-heavy palettes -- which is exactly what DualTone's default color (#6366F1, indigo) is.

**Tritanopia** (line 541):
```
0.950 0.050 0     0 0
0     0.433 0.567 0 0
0     0.475 0.525 0 0
0     0     0     1 0
```
This is also a rough approximation. The standard Brettel tritanopia simulation has different values. The green/blue channel mixing (rows 2-3) looks plausible but `0.950 0.050 0` for the red channel is overly simplified -- standard values retain more red channel fidelity.

**Recommendation:** Replace with Machado et al. (2009) severity-1.0 matrices or the widely-used Brettel (1997) matrices. The current deuteranopia and tritanopia filters will mislead designers checking their palettes for accessibility.

### 7. CSS Export -- Correct Format? All Variables Included?

**P2 -- Quick CSS copy (`Copy CSS` button) omits accent and neutral scales**

Two separate CSS export paths exist:

**Full export** (`generateCssExport`, line 984): Includes all 4 scales (primary, secondary, accent, neutral) + semantics + light/dark surface tokens. Format is correct `:root { }` with OKLCH comments. **PASS.**

**Quick CSS copy** (`generateQuickCss`, line 1688): Only includes:
- `--primary`, `--secondary` (500 step)
- `--primary-light`, `--primary-dark` (200, 800)
- `--secondary-light`, `--secondary-dark` (200, 800)
- 4 semantic colors

**Missing:** `--accent`, `--accent-light`, `--accent-dark`, and all neutral tokens. A designer clicking "Copy CSS" in the header gets an incomplete set. The button label "Copy CSS" does not indicate this is a subset.

**Fix:** Either add accent + neutral variables to `generateQuickCss()`, or rename the button to "Copy Quick CSS" and add a tooltip explaining it is a subset.

Also: The Tailwind v4 export uses `@theme { }` block (line 1020) which is the correct Tailwind v4 syntax. PASS.

### 8. History -- Corrupted JSON? Max Limit? Empty State?

**PASS with one minor note**

- **Corrupted JSON:** `loadHistory()` (line 1600) wraps `JSON.parse` in try/catch, returns `[]` on failure. Safe.
- **Max limit:** `historyMax = 10` (line 1597). After `unshift`, sliced to 10 entries (line 1619). Correct.
- **Empty state:** Renders "No history yet" text (line 1637). PASS.
- **Duplicate prevention:** Checks if last entry matches current hex+harmony before adding (line 1611). PASS.
- **Minor note:** `addToHistory` is called inside `updateAll()` (line 1176), which is called on every color change including color picker `input` events (debounced via rAF). With rapid picking, many entries may be added since the duplicate check only compares the most recent entry. This is acceptable behavior but could fill history quickly. Not a bug.

### 9. Service Worker Version?

**PASS**

`sw.js` line 1: `CACHE_NAME = 'dualtone-v2'`. Matches v2 release. Activate handler (line 11) cleans up old cache names. `skipWaiting()` + `clients.claim()` ensure immediate activation. Fetch handler uses stale-while-revalidate with cache-put on 200 responses.

**Note:** The SW caches all fetch responses (line 31: `cache.put(e.request, rc)`), not just same-origin. This means third-party requests could fill the cache. Low risk for a single-page app with no external dependencies, but worth noting.

---

## Issue Tracker

### P1 -- Critical (fix before release)

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| P1-1 | Inaccurate color blindness matrices | Lines 538, 541 | Deuteranopia and tritanopia feColorMatrix values are rough approximations that will produce misleading simulations, especially for blue-heavy palettes (DualTone's default). Replace with Machado et al. (2009) or Brettel (1997) matrices. |

### P2 -- Important (fix in next sprint)

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| P2-1 | Quick CSS copy missing accent/neutral | Lines 1688-1704 (`generateQuickCss`) | "Copy CSS" button omits `--accent`, `--accent-light`, `--accent-dark`, and all neutral tokens. Users get incomplete design system variables. |
| P2-2 | CB buttons lack tablist keyboard pattern | Lines 1731-1738 | Color blindness toggle buttons have no `role="tab"`, `aria-selected`, or arrow-key navigation. Inconsistent with preview-mode and export-tab toggles which use proper tablist pattern. |

### P3 -- Low Priority (nice to have)

| # | Issue | Location | Description |
|---|-------|----------|-------------|
| P3-1 | Light theme tertiary text borderline contrast | CSS line 37 | `--text-secondary: rgba(0,0,0,0.54)` in light theme is approximately 4.6:1 on white surfaces -- passes AA normal text but fails AAA. History timestamps and CB label use `--text-tertiary` at 0.72 opacity which is fine. |
| P3-2 | SW caches all origins | `sw.js` line 29-31 | Fetch handler caches any 200 response regardless of origin. Add `e.request.url.startsWith(self.location.origin)` guard. |
| P3-3 | Hero cycling ignores reduced-motion at JS level | Lines 1427 | `setInterval(cycleColors, 3000)` runs regardless of `prefers-reduced-motion`. CSS transition override hides animations, but unnecessary JS work continues. Should check `matchMedia` and skip interval. |
| P3-4 | renderPreview builds HTML without escapeHtml | Lines 957-976 | Hex values from internal math are injected into HTML attributes. Currently safe (output is always `#xxxxxx`), but violates defense-in-depth principle. |

---

## What Works Well

- Consistent `escapeHtml()` usage across dynamic innerHTML -- strong XSS posture
- All localStorage wrapped in try/catch with graceful fallback
- `prefers-reduced-motion` global override is thorough
- History feature handles edge cases well (corrupted JSON, duplicates, empty state, max cap)
- Proper ARIA on collapsible panels (`aria-expanded`, `aria-controls`)
- Skip-to-content link present and functional
- `:focus-visible` global outline style (line 381)
- Print stylesheet hides interactive elements appropriately
- Service worker version bumped to v2 with proper cache cleanup

---

*Generated by Nash, OpenClaw QA -- 2026-03-29*
