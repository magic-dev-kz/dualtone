# DualTone -- Re-audit (Nash)
Date: 2026-03-29
First audit: 4/10
Current score: 7/10

---

## Fixes verification (10 items from fix-log)

### 1. PWA (Service Worker + Manifest inline) -- PARTIAL FIX
**Status: code present, but non-functional in practice**

Service Worker registered via `blob:` URL (line 398-400). Web App Manifest also via `blob:` URL (line 416-420).

**Problem:** browsers require Service Worker scripts to be served from the same origin with a proper URL path. `blob:` URLs are explicitly forbidden for SW registration per the Service Worker spec (step 8.1 of the Register algorithm: "If scriptURL's scheme is not one of 'http' and 'https', reject promise"). Chrome will throw `SecurityError`, Firefox and Safari similarly reject. The SW will silently fail (`.catch(function(){})` swallows the error on line 400).

The manifest via `blob:` also won't enable install-to-homescreen because browsers require manifests at real URLs for PWA installability checks.

**Verdict:** PWA remains non-functional. Need a real `sw.js` file served alongside `index.html` and a real `manifest.json` (or inline `<link rel="manifest">` pointing to a proper path).

### 2. WCAG Auto-fix button -- FIXED
**Status: working correctly**

Algorithm (lines 632-658): binary search on OKLCH lightness with 30 iterations, direction-aware (lightens fg on dark bg, darkens fg on light bg), clamps chroma for gamut safety. Returns `null` if pair already passes 4.5:1.

UI: "Auto-fix All" button (line 347), click handler (lines 1168-1184), shows fix count via toast, renders before/after diff per pair.

Tested logic mentally: binary search converges to within ~1e-9 in 30 iterations over [0,1] range. Direction logic correct. Chroma clamping prevents out-of-gamut results.

**One minor issue:** auto-fix only modifies the WCAG display, not the actual export tokens. If a user clicks "Auto-fix" and then downloads CSS, they get the original (failing) values, not the fixed ones. The `renderExport()` call on line 1181 does not incorporate `state.wcagFixes`. This is a UX inconsistency but not a crash.

### 3. Random color padStart -- FIXED
**Status: correct**

Line 1077: `Math.floor(Math.random()*16777215).toString(16).padStart(6,'0')`. Uniform distribution, always valid 6-digit hex.

### 4. JSON W3C format ($value/$type) -- FIXED
**Status: correct**

Lines 895, 906-911: all token output uses `$value` and `$type`. Verified in `generateJsonExport()`.

### 5. Share button -- FIXED
**Status: working**

Button in header (line 272), copies `window.location.href` to clipboard (lines 1082-1086), shows "Link copied!" toast.

### 6. HEX validation with shake + error text -- FIXED
**Status: working**

CSS: `.hex-error` class with red border + shake animation (lines 80-83). JS: input handler adds class + shows error message when input length >= 2 and doesn't match regex (lines 1049-1063). Clears on valid input or blur (lines 1065-1069). Error message has `aria-live="polite"`.

### 7. Contrast text-tertiary -- PARTIALLY FIXED
**Status: dark theme OK, light theme still fails AA**

- Dark theme: `rgba(255,255,255,0.52)` on `#1a1a2e`. Effective blended color ~rgb(145,145,155). Calculated contrast ratio ~6.3:1. **PASSES AA.**
- Light theme: `rgba(0,0,0,0.50)` on `#f5f5f7`. Effective blended color ~rgb(123,123,124). Calculated contrast ratio ~3.9:1. **FAILS AA** (needs 4.5:1). Passes AA Large only.

Need opacity 0.58+ in light theme to reach 4.5:1.

### 8. rAF debounce on color picker -- FIXED
**Status: correct**

Lines 1039-1047: `requestAnimationFrame` with cancel pattern. Prevents multiple `updateAll()` calls per frame during drag.

### 9. Accessibility (skip-to-content, h1, arrow keys in tabs) -- FIXED
**Status: all three sub-items working**

- Skip-to-content: `<a href="#mainContent" class="skip-to-content">` (line 264), visible on `:focus` (CSS lines 42-46), target `<main id="mainContent">` (line 297).
- `<h1>` on logo: line 269, was `<span>` before. Heading hierarchy now starts from h1.
- Arrow keys in tablists: `setupTablistKeys()` (lines 1094-1111), handles ArrowLeft/Right, wraps around, manages `tabindex` 0/-1. Applied to both preview mode toggle (line 1138) and export tabs (line 1139).
- `role="tabpanel"` added on preview content (line 323) and export code area (line 334).

**Missing:** `aria-controls` on tabs and `id` on tabpanels. ARIA tabs pattern technically requires these attributes for full compliance, but screen readers generally work without them. Minor gap.

### 10. Download buttons + OKLCH in CSS export -- PARTIALLY FIXED
**Status: Download works, OKLCH only as comments**

Download: lines 1151-1165. Uses `Blob` + `createObjectURL` + temporary `<a>` element. Correct MIME types. Filenames: `dualtone-tokens.css` / `dualtone-tokens.json`. `URL.revokeObjectURL()` called after click to prevent memory leak. **Working.**

OKLCH in CSS: line 840 pattern shows `--color-primary-50: #hex; /* oklch(...) */`. Spec (section 6) requires `--primary-500: oklch(...);` as the actual value with HEX as fallback. Current implementation has HEX as the actual value and OKLCH only as a comment. This does NOT satisfy the spec requirement. Wide-gamut displays won't benefit because the comment is not parsed by browsers.

---

## AC Verification (all 10)

### AC-1: Brand color input -- PARTIAL
- HEX input: works, with validation (shake + error).
- Color picker: works.
- RGB/HSL input: still ABSENT. Spec says "HEX, RGB, HSL or picker".
- 3-char HEX (#F00): still not accepted by validation regex (requires exactly 6 digits after #).
- Generation < 100ms: yes, with rAF debounce now.

### AC-2: Full palette generation -- PARTIAL (unchanged)
- Primary/Secondary/Accent/Neutral 11 shades each: yes.
- Semantic: still only 1 color per category, not 11 shades. Spec requires "4 colors x 11 shades".
- OKLCH used: yes.
- Surface/text tokens: yes.

### AC-3: Auto dark mode -- PASS
- Auto-generated, not simple inversion, one-click toggle. No changes from first audit -- was already passing.

### AC-4: WCAG accessibility check -- PASS (new)
- Contrast ratio calculated: yes.
- Visual badges pass/fail: yes.
- Auto-fix button: now present, algorithm correct.
- Numeric ratio: yes.
- **Note:** auto-fix results not propagated to export. Visual-only fix.

### AC-5: UI preview -- PARTIAL (unchanged)
- Hero, cards, buttons, form, badges: yes.
- Table: absent. Sidebar: absent. Spec requires both.
- Responsive desktop/mobile toggle: absent.

### AC-6: Code export -- PARTIAL (improved)
- CSS Custom Properties: yes, but HEX values instead of OKLCH with fallback.
- Tailwind v4: yes.
- JSON W3C tokens: now correct ($value/$type).
- Copy: yes.
- Download: now works.
- OKLCH in CSS values: only as comments, not as actual property values.

### AC-7: Offline work (PWA) -- FAIL
- Service Worker: code present but will not register (blob: URL forbidden).
- Manifest: code present but non-functional (blob: URL).
- localStorage: works with try/catch.
- 50 palette limit: still not implemented.

### AC-8: Save and share -- PASS (improved)
- URL-based state: yes.
- Share button: now present, copies URL.
- Compact URL: yes.

### AC-9: Generation settings -- PARTIAL (unchanged)
- Color harmony dropdown: yes.
- Color temperature: absent.
- Lock color: absent.
- Manual override: absent.

### AC-10: Responsive and accessible UI -- PARTIAL (improved)
- Mobile 320px+: yes.
- WCAG AA for DualTone itself: dark theme mostly passes, light theme text-tertiary still fails.
- Keyboard navigation: improved with arrow keys in tabs, skip-to-content.
- prefers-color-scheme: yes.
- prefers-reduced-motion: yes.

---

## Remaining Bugs

### Critical

**BUG-C1: PWA non-functional (blob: URL for Service Worker)**
Service Worker registration via `blob:` URL will throw SecurityError in all major browsers. The `.catch(function(){})` silently swallows the error. Application does NOT work offline and is NOT installable. Need separate `sw.js` file or `<script>` inline approach won't work for SW.

### Major

**BUG-M1: CSS export uses HEX, not OKLCH values**
OKLCH appears only in comments (`/* oklch(...) */`), not as actual CSS property values. Spec section 6 explicitly requires `--primary-500: oklch(...);` with HEX fallback. Wide-gamut displays get no benefit from comments.

**BUG-M2: Light theme text-tertiary contrast ~3.9:1 (FAIL AA)**
`rgba(0,0,0,0.50)` on `#f5f5f7` gives approximately 3.9:1. Needs 4.5:1 for AA normal text. Increase opacity to at least 0.58.

**BUG-M3: Semantic colors -- still 1 color instead of 11-shade scale**
Spec AC-2 requires "4 colors x 11 shades". Only 1 color per semantic category (success, warning, error, info). This limits real-world usage in design systems.

**BUG-M4: Auto-fix results not reflected in export**
When user clicks "Auto-fix All", the WCAG panel shows corrected colors, but `generateCssExport()`, `generateTailwindExport()`, and `generateJsonExport()` still output original (potentially failing) token values. The `renderExport()` call on line 1181 does not read `state.wcagFixes`. User who exports after auto-fix gets unfixed tokens.

**BUG-M5: No RGB/HSL input fields**
Spec AC-1 requires "HEX, RGB, HSL or picker". Only HEX + picker implemented.

**BUG-M6: No table and sidebar in preview**
Spec AC-5 requires "table" and "sidebar" in UI preview. Both absent.

### Minor

**BUG-m1: Saved palettes no 50-item limit**
Spec AC-7 requires "up to 50 palettes". No limit enforced. Potential localStorage overflow.

**BUG-m2: No `aria-controls`/`id` for tab-tabpanel linkage**
ARIA tabs pattern requires `aria-controls` on `[role="tab"]` pointing to tabpanel `id`. Both tablists (preview and export) miss this. Screen readers still generally work, but it's not spec-compliant.

**BUG-m3: 3-character HEX not accepted**
`hexToRgb()` supports 3-char expansion, but input validation regex requires exactly 6 hex digits. User can't type `#F00`.

**BUG-m4: No timestamp in saved palettes**
Design doc requires "color circle + HEX + timestamp". Timestamp not stored.

**BUG-m5: `textColorForBg()` uses 0.4 luminance threshold**
Standard WCAG switching threshold is ~0.179. Threshold of 0.4 means some medium-brightness backgrounds get white text when black would provide better contrast.

**BUG-m6: CSS export dark mode uses `@media (prefers-color-scheme: dark)` instead of `[data-theme="dark"]`**
Spec AC-6 says "`:root` for light, `[data-theme='dark']` **or** `@media`". Current implementation uses `@media`. Acceptable per spec, but less flexible for manual toggle. Minor preference issue.

---

## Scoring Rationale

| AC | First Audit | Re-audit | Notes |
|----|-------------|----------|-------|
| AC-1 | Partial | Partial | HEX validation improved, still no RGB/HSL |
| AC-2 | Partial | Partial | Unchanged (semantic 1 vs 11) |
| AC-3 | Pass | Pass | Unchanged |
| AC-4 | Fail | Pass | Auto-fix implemented and correct |
| AC-5 | Partial | Partial | Unchanged (no table/sidebar) |
| AC-6 | Partial | Improved | JSON W3C fixed, download added, OKLCH still comments-only |
| AC-7 | Fail | Fail | blob: SW = non-functional |
| AC-8 | Partial | Pass | Share button added |
| AC-9 | Partial | Partial | Unchanged |
| AC-10 | Partial | Improved | a11y fixes done, light contrast still fails |

**Scoring: 7/10**

Improvement from 4/10: +3 points.
- +1 for AC-4 (auto-fix now works)
- +1 for AC-8 (share button)
- +1 for combined improvements across AC-6, AC-10 (download, JSON W3C, keyboard a11y)

Not 8/10 because: PWA still broken (AC-7 fail), light theme contrast still fails AA (AC-10 partial), auto-fix not connected to export (AC-4 functional gap), CSS export still HEX-only (AC-6 gap).

---

## Verdict

Mario addressed 8 out of 10 listed fixes with working implementations. Two fixes are incomplete:

1. **PWA** -- code exists but blob: URL approach is architecturally broken. Need real files or a different embedding strategy.
2. **Light theme text-tertiary** -- opacity 0.50 insufficient, needs ~0.58 for AA compliance.

Additionally, the auto-fix feature has a functional gap: fixed colors are visual-only and don't propagate to export tokens.

**What's needed for 8+/10:**
- Fix PWA: create a real `sw.js` file (even a 20-line one) + real `manifest.json`
- Raise light theme text-tertiary opacity to 0.58
- Connect auto-fix results to export output
- Add OKLCH as actual CSS values (not just comments)

**What's needed for 9+/10 (additionally):**
- Semantic 11-shade scales
- Table + sidebar in preview
- RGB/HSL fields (at least readonly)

The trajectory is good. The core product works well. Most of Mario's fixes demonstrate solid engineering (auto-fix algorithm, rAF debounce, ARIA tabs, download with cleanup). The PWA approach was the wrong architectural choice, not a code quality issue -- it needs a different strategy.

---

## Round 3 Re-audit (после фиксов раунд 2)
Дата: 2026-03-29
Оценка: 8/10

### Фиксы раунд 2
1. PWA sw.js: ✅ — Регистрация теперь через `'./sw.js'` (строка 371), файл `sw.js` существует рядом с `index.html`. Корректный cache-first SW с install/activate/fetch handlers, `skipWaiting()` + `clients.claim()`. Blob-код удалён. SW будет работать на HTTPS. Manifest по-прежнему через blob: URL — это не блокер для офлайн-работы, но install-to-homescreen не будет работать в Chrome/Safari.
2. Light contrast: ✅ — `--text-tertiary` в light theme теперь `rgba(0,0,0,0.58)` (строка 29). Расчёт: blended на `#f5f5f7` даёт ~`rgb(103,103,104)`, контраст ~5.26:1. Проходит AA (>=4.5:1).
3. Auto-fix→export: ✅ — `applyWcagFixesToPalette()` (строки 1139-1152) мутирует `state.palette` in-place перед вызовом `renderExport()` (строка 1168). Все три экспорта (CSS/Tailwind/JSON) читают из `state.palette` — фиксы отражаются в экспорте.

### Оставшиеся проблемы

**Major (не новые, перенесены из раунда 2):**
- BUG-M1: CSS export — OKLCH только в комментариях, не как значения свойств. Spec требует `oklch(...)` как value.
- BUG-M3: Semantic — 1 цвет вместо 11-shade scale (AC-2).
- BUG-M5: Нет полей RGB/HSL (AC-1 — только HEX + picker).
- BUG-M6: Нет table и sidebar в preview (AC-5).

**Minor (не новые):**
- Manifest через blob: URL — офлайн работает через SW, но PWA install prompt не сработает.
- `applyWcagFixesToPalette`: pairs 1 и 3 оба маппят на `neu[9]`, pairs 5 и 7 на `neu[1]` — если auto-fix даёт разные значения для одного элемента в разных контекстах, последний перезаписывает первый. Edge case, но может привести к непрохождению контраста в одном из контекстов.
- Нет лимита 50 палитр в localStorage (AC-7).
- 3-char HEX не принимается (`#F00`).
- Нет `aria-controls`/`id` для tab-tabpanel связки.

**Регрессий от новых фиксов не обнаружено.**

### Скоринг

| AC | Раунд 2 | Раунд 3 | Изменение |
|----|---------|---------|-----------|
| AC-1 | Partial | Partial | без изменений |
| AC-2 | Partial | Partial | без изменений |
| AC-3 | Pass | Pass | без изменений |
| AC-4 | Pass | Pass | auto-fix теперь пробрасывается в экспорт |
| AC-5 | Partial | Partial | без изменений |
| AC-6 | Improved | Improved | без изменений (OKLCH всё ещё comments-only) |
| AC-7 | Fail | Partial | SW работает, офлайн возможен, install prompt — нет |
| AC-8 | Pass | Pass | без изменений |
| AC-9 | Partial | Partial | без изменений |
| AC-10 | Improved | Pass | light theme contrast теперь проходит AA |

**7 → 8:** +1 за три целевых фикса (PWA SW работает, контраст OK, auto-fix→export OK). AC-7 с Fail на Partial, AC-10 полностью Pass.

### Вердикт

Все три бага из раунда 2 исправлены корректно. Регрессий нет. Качество фиксов хорошее — Марио выбрал правильные архитектурные решения (внешний SW файл, мутация state.palette in-place).

Для 9/10 нужно: OKLCH как CSS-значения (не комментарии), semantic 11-shade scales, table+sidebar в preview. Для 10/10 добавить: RGB/HSL поля, real manifest.json, 50-palette limit, 3-char HEX support.
