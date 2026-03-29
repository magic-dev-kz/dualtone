# DualTone -- Аудит (Нэш)
Дата: 2026-03-29
Оценка: 4/10

---

## Acceptance Criteria

### AC-1: Ввод brand color
**Частично -- ❌**

- HEX ввод работает: поле с маской, валидация regex `^#[0-9a-fA-F]{6}$`.
- Color picker (native `<input type="color">`) -- работает.
- **RGB ввод -- ОТСУТСТВУЕТ.** Спека требует "RGB, HSL или пикер". В коде нет ни RGB-полей, ни HSL-полей. Есть только HEX input + color picker.
- **HSL ввод -- ОТСУТСТВУЕТ.**
- Ошибка при невалидном вводе: нет inline error message. Если ввести "zzz", цвет просто не обновится. Нет визуального feedback (шейк, красная рамка, текст ошибки). Спека говорит "показать ошибку inline".
- Генерация < 100ms: да, всё синхронно в main thread, на десктопе мгновенно. Но спека говорит "< 100ms на среднем телефоне" -- без Web Worker это может быть проблемой при частом input (color picker drag).
- **3-символьный HEX:** поддерживается в `hexToRgb()` (расширяет 3->6), но regex валидации в input handler требует ровно 6 символов. Ввести `#F00` нельзя.

### AC-2: Генерация полной палитры
**Частично -- ✅ с оговорками**

- Primary (11 оттенков) -- да.
- Secondary (11) -- да, из harmony hues.
- Accent (11) -- да.
- Neutral (11) -- да, desaturated (C * 0.08).
- Semantic: 4 цвета -- да, но **НЕ 4 x 11 оттенков**, как требует спека. Генерируется ровно 1 цвет на каждый semantic (success, warning, error, info). Спека требует "4 цвета x 11 оттенков".
- OKLCH используется -- да, полная конвертация HEX -> sRGB -> Linear -> XYZ -> Oklab -> OKLCH.
- Surface и text токены для light и dark -- да, но только в экспорте (CSS/JSON), не как отдельные визуальные элементы.

### AC-3: Автоматический dark mode
**✅ Работает**

- Dark mode генерируется автоматически -- в preview и в экспорте.
- Не простая инверсия -- surfaces берутся из neutral-900/800, текст из neutral-100/300.
- Переключение light/dark в preview -- один клик, мгновенно.
- Но: спека говорит "accent цвета скорректированы по яркости" -- в коде dark mode использует primary-400 вместо primary-500, что корректно.

### AC-4: WCAG accessibility проверка
**Частично -- ❌**

- Contrast ratio рассчитывается корректно (формула WCAG 2.1 relative luminance).
- Отображается числовой ratio -- да.
- Визуальные бейджи pass/fail -- да.
- **Auto-fix ОТСУТСТВУЕТ.** Спека требует кнопку "Auto-fix", которая корректирует проблемные пары с минимальным отклонением. В коде нет ни кнопки, ни алгоритма auto-fix.
- Проверяется только 10 пар. Для полной системы (40+ токенов) этого мало.

### AC-5: Визуализация на реальном UI
**Частично -- ✅ с оговорками**

- Hero section -- да.
- Карточки -- да (2 штуки).
- Кнопки -- да (primary + outline в hero, subscribe в форме).
- Формы -- да (1 input + submit).
- **Таблица -- ОТСУТСТВУЕТ.** Спека требует "таблица".
- **Sidebar -- ОТСУТСТВУЕТ.** Спека требует "sidebar".
- Переключение light/dark -- да.
- **Responsive превью (desktop/mobile вид) -- нет.** Спека требует "показывает desktop и mobile вид".
- **Fullscreen кнопка -- ОТСУТСТВУЕТ.** Дизайн-док требует "Open fullscreen".

### AC-6: Экспорт в код
**Частично -- ✅ с оговорками**

- CSS Custom Properties -- да, `:root` для light, `@media (prefers-color-scheme: dark)` для dark.
- Tailwind CSS v4 -- да, `@theme { --color-* }` формат.
- JSON Design Tokens -- да, но **НЕ формат W3C.** W3C Design Tokens требует `$value` и `$type` (с $ prefix). В коде: `value` и `type` без $. Это невалидные W3C tokens.
- Copy одним кликом -- да, Clipboard API с fallback на execCommand.
- **Download .css / .json -- ОТСУТСТВУЕТ.** Дизайн-док требует кнопки скачивания файлов.
- Код валидный, paste-and-go -- CSS и Tailwind да. JSON -- невалидный W3C формат.
- CSS экспорт использует HEX вместо OKLCH с HEX fallback. Спека (раздел 6) требует: `:root { --primary-500: oklch(...); }` с fallback в HEX.

### AC-7: Офлайн работа (PWA)
**❌ Полностью отсутствует**

- **НЕТ Service Worker.** Ни файла sw.js, ни регистрации в index.html.
- **НЕТ Web App Manifest.** Ни файла manifest.json, ни `<link rel="manifest">`.
- localStorage для палитр -- да, работает (с try/catch).
- Без SW и манифеста приложение НЕ работает офлайн и НЕ может быть установлено.

### AC-8: Сохранение и шаринг палитр
**Частично -- ✅ с оговорками**

- URL-based state -- да, `#c=6366F1&h=complementary`.
- **Share кнопка -- ОТСУТСТВУЕТ.** Нет кнопки "Share" которая копирует URL в буфер.
- URL компактный и читаемый -- да.
- Saved palettes -- да, в localStorage.
- Лимит 50 палитр из спеки -- НЕ реализован. Можно сохранять бесконечно, что потенциально забьёт localStorage.

### AC-9: Настройки генерации
**Частично -- ❌**

- Color harmony dropdown -- да (complementary, analogous, triadic, split-complementary).
- **Color temperature нейтральных -- ОТСУТСТВУЕТ.**
- **Lock конкретного цвета -- ОТСУТСТВУЕТ.**
- **Manual override любого цвета -- ОТСУТСТВУЕТ.**

### AC-10: Responsive и доступный UI
**Частично -- ✅ с оговорками**

- Mobile (320px+) -- код есть, breakpoints 480/768/1024. Но на 320px 6-колоночный grid свотчей будет ~50px на swatch что ОК, но **нет горизонтального scroll-snap** как требует дизайн для мобильных свотчей.
- WCAG AA самого DualTone -- ПРОБЛЕМЫ (см. ниже Accessibility).
- Keyboard navigation -- частично (см. ниже).
- prefers-color-scheme -- да, используется для определения начальной темы приложения.

---

## Баги

### Critical (блокеры)

**BUG-C1: Нет PWA (Service Worker + Manifest)**
Спека AC-7 требует полностью офлайн-работающее приложение. Отсутствуют оба ключевых файла. Это блокер для запуска -- пользователь не сможет использовать продукт без интернета.

**BUG-C2: Нет WCAG Auto-fix**
Спека AC-4 явно требует кнопку Auto-fix. Это одна из killer features ("WCAG встроен: каждая пара проверена, плохие автофикшены"). Без неё теряется ключевое конкурентное преимущество.

**BUG-C3: Random color может генерировать невалидный HEX**
Строка 921: `('#'+Math.floor(Math.random()*16777215).toString(16)+'000000').slice(0,7)` -- если `Math.random()` даёт число вроде 255 (= `ff`), hex будет `#ff0000` -- случайно OK. Но если число = 1, hex = `#100000` (неверно, будет `#1000000` с обрезкой до 7 = `#100000`). Хуже: если число = 0, hex = `#000000` -- OK. Но `Math.floor(Math.random()*16777215).toString(16)` для числа 15 даст `f`, и `('#f000000').slice(0,7)` = `#f00000`. Проблема: для чисел < 16 hex будет 1 символ, padding `000000` сработает, но `slice(0,7)` обрежёт. Реально padding неправильный: нужен `.padStart(6,'0')`. Текущий код добавляет `000000` ПОСЛЕ hex и режет до 7 символов. Для hex длиной 1 (`'f'`): `#f000000` -> `#f00000`. Это будет `#f00000` а не случайный цвет -- bias к красным оттенкам для малых чисел. Не crash, но ломает равномерность рандома.

### Major (важные)

**BUG-M1: JSON export не соответствует W3C Design Tokens формату**
Используется `value`/`type` вместо `$value`/`$type`. Разработчик, вставляющий этот JSON в свою систему токенов, получит невалидный формат.

**BUG-M2: Semantic colors -- только 1 цвет вместо 11-ступенчатой шкалы**
Спека AC-2 требует "4 цвета x 11 оттенков". Реализован 1 цвет на semantic category. Это серьёзное ограничение для реальной дизайн-системы.

**BUG-M3: Нет Share кнопки**
URL-based state работает, но нет UX-элемента для шаринга. Пользователь должен вручную копировать URL из адресной строки.

**BUG-M4: CSS экспорт использует HEX вместо OKLCH**
Спека раздел 6 и дизайн-док секция 5.4 требуют OKLCH значения с HEX fallback. Реализован только HEX. Теряется точность для широкогамутных дисплеев.

**BUG-M5: Нет inline error для невалидного HEX**
При вводе невалидного значения цвет просто не обновляется. Нет визуального feedback: ни shake-анимации (описана в дизайн-доке), ни красной рамки, ни текста ошибки.

**BUG-M6: Нет RGB/HSL полей ввода**
Спека AC-1 требует "HEX, RGB, HSL или пикер". Реализован только HEX + пикер. Дизайн-док показывает RGB и HSL как readonly поля, обновляющиеся при изменении HEX -- даже это не реализовано.

**BUG-M7: Нет таблицы и sidebar в preview**
Спека AC-5 и дизайн-док секция 5.3 требуют "table row" и "sidebar fragment". Оба отсутствуют.

**BUG-M8: Нет кнопок Download (.css / .json)**
Дизайн-док секция 5.4 требует кнопки скачивания файлов.

### Minor (мелочи)

**BUG-m1: Saved palettes без лимита**
Спека AC-7 требует "до 50 палитр". Лимит не реализован. При 500+ сохранениях localStorage может быть переполнен.

**BUG-m2: Saved palette не хранит timestamp**
Дизайн-док секция 5.7 требует "цветной кружок + HEX + timestamp". Timestamp не сохраняется.

**BUG-m3: Шрифты не соответствуют дизайн-доку**
Дизайн требует Inter Variable + JetBrains Mono. Код использует system-ui стек для UI и SF Mono/Consolas для моно. Шрифты не подключены (нет @font-face, нет Google Fonts link). Это осознанный trade-off (bundle size), но расхождение с дизайном.

**BUG-m4: Нет skip-to-content link**
Дизайн-док accessibility checklist требует "Skip-to-content link for keyboard users".

**BUG-m5: Logo-icon не имеет semantic purpose**
`aria-hidden="true"` корректен, но логотип "DualTone" -- это просто `<span>` внутри `<div>`. Нет `<a href="/">` для навигации домой, как ожидается.

**BUG-m6: Нет GitHub link в header**
Дизайн-док секция 5.6 требует ссылку на GitHub. В header только theme toggle.

**BUG-m7: Preview mode toggle не управляется стрелками**
`role="tablist"` + `role="tab"` семантика есть, но нет обработчика Arrow Left/Right для переключения между табами. ARIA tablist pattern требует arrow key navigation.

**BUG-m8: Export tabs аналогично не управляются стрелками**
То же самое: `role="tablist"` без arrow key support.

**BUG-m9: Нет `aria-controls` и `id` для tabpanel на export/preview tabs**
Tab ARIA pattern требует `aria-controls` на tab и `id` на tabpanel. Отсутствует.

**BUG-m10: Toast "Copied!" не доступен для screen reader при fallback copy**
При использовании `execCommand('copy')` fallback, `showToast()` вызывается, но `aria-live="polite"` не гарантирует мгновенное объявление. Clipboard API path вызывает `showToast` в `.then()`, что может быть потеряно при быстрых повторных кликах.

---

## Accessibility

### WCAG AA контраст (самого DualTone)

**Проблема: `--text-tertiary` в dark mode**
`rgba(255,255,255,0.38)` на `#1a1a2e` -- это примерно 3.2:1. Не проходит WCAG AA (4.5:1) для обычного текста. `--text-tertiary` используется для section titles (`.section-title`), hints. Это **FAIL AA для нормального текста**. Для крупного текста (font-size 0.75rem = 12px) -- не квалифицируется как "large text" (>= 18px regular или 14px bold), так что требуется 4.5:1.

**Проблема: `--text-secondary` в dark mode**
`rgba(255,255,255,0.60)` на `#1a1a2e`. Приблизительно 5.7:1 -- проходит AA, не проходит AAA.

**Проблема: `--text-tertiary` в light mode**
`rgba(0,0,0,0.32)` на `#f5f5f7`. Примерно 3.0:1 -- **FAIL AA**.

### Focus management

- `focus-visible` outline на всех элементах -- да, 2px solid accent. Хорошо.
- **Нет focus trap.** Нет модалок/overlay в текущей реализации, так что это не критично сейчас. Но дизайн-док упоминает fullscreen preview -- если будет реализован, нужен focus trap.

### prefers-reduced-motion

- Реализован глобально (строка 38-39): `animation-duration: 0.01ms !important; transition-duration: 0.01ms !important`. Соответствует дизайн-доку. **OK.**

### Keyboard navigation

- Tab между picker, hex input, harmony select, shuffle -- работает (стандартный tab order).
- Enter/Space на swatches -- работает (строка 637).
- Enter/Space на semantic cards -- работает.
- **Arrow keys в hex input для +/- значения -- НЕ реализовано** (дизайн-док секция 5.1 требует).
- **Arrow keys в tablists -- НЕ реализовано** (ARIA pattern).
- Escape для закрытия поповеров -- нет поповеров, не применимо.

### Screen reader

- `aria-label` на основных секциях и контролах -- хорошо.
- `role="button"` + `tabindex="0"` на swatches -- хорошо.
- `aria-live="polite"` на toast -- хорошо.
- `lang="en"` на `<html>` -- хорошо.
- Нет `<h1>`. Первый heading -- `<h2>` в preview. Нарушение heading hierarchy.
- `<main>` landmark -- есть. `<header>` -- есть. `<nav>` -- нет (можно добавить для saved palettes или harmony).
- `.section-title` -- это `<div>`, а не `<h2>`/`<h3>`. Screen reader не распознает как heading.

---

## Performance

### Bundle size
- Одиночный HTML файл ~40KB (unminified). Gzipped будет ~8-10KB. Отлично -- далеко ниже лимита 100KB.
- Нет внешних зависимостей (библиотека culori НЕ используется, OKLCH math ручной). Это и плюс (bundle), и минус (потенциальные баги в цветовой математике).

### Rendering
- `innerHTML` для всех render-функций. При каждом изменении цвета (color picker drag) полностью перестраивается DOM: paletteGrid, semanticRow, preview, export, WCAG, saved. Это потенциально ~200+ DOM-элементов, пересоздаваемых на каждый input event.
- **Нет debounce/throttle** на color picker `input` event. При drag пикера это будет вызывать `updateAll()` десятки раз в секунду. На слабом устройстве -- заметные лаги.
- Нет `requestAnimationFrame` для батчинга.

### Web Worker
- Спека (раздел 6) упоминает: "All OKLCH math runs in a Web Worker". **Не реализовано** -- всё в main thread.

### Font loading
- Кастомные шрифты не используются. System fonts. Нет проблем с FOIT/FOUT.

---

## Код

### Качество
- Чистый vanilla JS, IIFE, strict mode -- хорошо.
- Функциональная структура, понятное разделение: color math, palette generation, rendering, events.
- Все localStorage операции обёрнуты в try/catch -- **хорошо, чеклист пройден**.

### Проблемы

1. **Вся цветовая математика ручная.** Спека требует библиотеку `culori`. Ручная реализация XYZ/Oklab -- потенциальные ошибки в матричных коэффициентах. Проверил коэффициенты для sRGB <-> XYZ (строки 358-370) -- используются стандартные D65 матрицы. Oklab коэффициенты (строки 373-395) совпадают с Bjorn Ottosson reference. **Математика корректна.**

2. **Gamut mapping примитивный.** Binary search по chroma (строки 434-442) -- работает, но может давать неоптимальные результаты. Более продвинутые алгоритмы (CSS Color Level 4 gamut mapping) учитывают адаптацию lightness. Для MVP приемлемо.

3. **Нет gamut warning.** Если пользователь вводит цвет, который при генерации шкалы выходит за sRGB gamut -- он тихо clamp'ится. Нет визуального индикатора.

4. **`textColorForBg()` использует порог 0.4 luminance.** Стандартный порог WCAG -- около 0.179 для переключения чёрный/белый. Порог 0.4 означает, что средне-яркие цвета получат белый текст, когда чёрный был бы контрастнее. Это может приводить к плохому контрасту на некоторых свотчах.

5. **XSS через URL hash.** Не проблема -- hash парсится с regex whitelist (`/^[0-9a-fA-F]{6}$/`), harmony через indexOf whitelist. Безопасно.

6. **Нет error boundaries.** Если `hexToOklch()` получит невалидный input (например, NaN), вся генерация сломается без graceful degradation. Маловероятно при текущей валидации, но defensive coding отсутствует.

---

## Рекомендации

### P0 (до запуска)

1. **Реализовать PWA**: Service Worker (cache-first) + Web App Manifest. Без этого AC-7 = FAIL. Это ~50 строк кода для basic SW.
2. **Реализовать WCAG Auto-fix**: Алгоритм простой -- binary search по OKLCH lightness до достижения ratio >= 4.5. Кнопка "Auto-fix All" + визуальный diff.
3. **Исправить JSON export на W3C формат**: `$value`, `$type` вместо `value`, `type`.
4. **Добавить debounce** на color picker input (16-33ms, rAF).
5. **Исправить контраст `--text-tertiary`**: увеличить opacity до 0.50+ в dark, 0.45+ в light.

### P1 (неделя после запуска)

6. **Добавить RGB/HSL readonly поля** в input bar.
7. **Реализовать semantic scales** (11 оттенков для каждого semantic цвета).
8. **Добавить таблицу и sidebar** в preview.
9. **Добавить Share кнопку** (копирует URL).
10. **Добавить Download .css / .json кнопки**.
11. **CSS export в OKLCH** с HEX fallback.
12. **Arrow key navigation** в tablists.

### P2 (polish)

13. Inline error для невалидного HEX (shake + red border).
14. Skip-to-content link.
15. Heading hierarchy (`<h1>` для DualTone, `<h2>` для секций).
16. Лимит 50 saved palettes.
17. Timestamp в saved palettes.
18. `padStart(6,'0')` для random color generation.
19. GitHub link в header.

---

## Итог

Продукт на стадии "рабочий прототип". Основная механика (один цвет -> палитра + preview + export) работает корректно. Цветовая математика реализована правильно. Но 3 из 10 AC полностью провалены (PWA, Auto-fix, настройки генерации), ещё 5 реализованы частично. Для запуска на Product Hunt в текущем виде -- не готов. Нужно минимум 2-3 дня на P0 исправления.
