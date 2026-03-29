# DualTone -- Design System

**One color in. Full system out.**

**Date:** 2026-03-29
**Author:** Sky (Creative Designer, OpenClaw)
**Status:** Design v1.0

---

## 1. Visual Concept

### Philosophy: "The Toolmaker's Canvas"

DualTone -- инструмент, который генерирует дизайн-системы. Значит, он сам должен быть безупречным примером дизайн-системы. Но при этом -- нейтральным. Его задача -- не красоваться, а подчеркнуть то, что создал пользователь.

### Стиль: Neutral Glassmorphism + Dark-first

- **Dark-first** -- рабочий интерфейс по умолчанию тёмный (как VS Code, Figma, любой dev tool). Тёмный фон позволяет сгенерированным цветам "светиться" и выглядеть точнее. Light mode доступен через toggle.
- **Glassmorphism** -- панели с `backdrop-filter: blur()` и полупрозрачными фонами. Стекло создаёт ощущение слоёв и глубины, но не перетягивает внимание с палитры пользователя.
- **Минимализм** -- никаких градиентов, паттернов, иллюстраций в chrome. Весь "цвет" и "красота" -- в сгенерированной палитре. Сам DualTone -- холст.
- **Soft shadows** -- тени мягкие, размытые, с лёгким цветным оттенком (не чёрные).

### Визуальная метафора

Представь мастерскую ювелира: тёмный бархат стола (фон), стеклянные витрины (панели), и в центре -- камень, который переливается (палитра пользователя). Фокус всегда на камне.

---

## 2. Color Palette (UI самого DualTone)

DualTone использует намеренно нейтральную палитру, чтобы не конфликтовать со сгенерированными цветами пользователя.

### Dark Mode (default)

```css
:root[data-theme="dark"] {
  /* Background layers */
  --dt-bg-base:        oklch(0.15 0.005 270);   /* #1a1a2e -- глубокий тёмно-синий, не чистый чёрный */
  --dt-bg-surface:     oklch(0.19 0.007 270);   /* #22223a -- панели */
  --dt-bg-elevated:    oklch(0.23 0.009 270);   /* #2a2a48 -- карточки, поповеры */
  --dt-bg-glass:       oklch(0.20 0.008 270 / 0.7);  /* стеклянные панели */

  /* Borders */
  --dt-border-subtle:  oklch(0.30 0.010 270 / 0.5);  /* тонкие разделители */
  --dt-border-default: oklch(0.35 0.012 270 / 0.6);  /* обычные бордеры */
  --dt-border-focus:   oklch(0.70 0.15 270);          /* фокус -- единственный яркий accent */

  /* Text */
  --dt-text-primary:   oklch(0.95 0.005 270);   /* почти белый, чуть голубоватый */
  --dt-text-secondary: oklch(0.70 0.010 270);   /* приглушённый */
  --dt-text-muted:     oklch(0.50 0.010 270);   /* labels, hints */

  /* Accent -- один тонкий акцент для интерактивных элементов */
  --dt-accent:         oklch(0.70 0.15 270);    /* мягкий индиго/лавандовый */
  --dt-accent-hover:   oklch(0.75 0.17 270);
  --dt-accent-active:  oklch(0.65 0.13 270);

  /* Semantic (для WCAG индикаторов) */
  --dt-success:        oklch(0.72 0.15 155);    /* мягкий зелёный */
  --dt-warning:        oklch(0.78 0.14 80);     /* тёплый жёлтый */
  --dt-error:          oklch(0.65 0.20 25);     /* приглушённый красный */
  --dt-info:           oklch(0.70 0.12 240);    /* спокойный синий */
}
```

### Light Mode

```css
:root[data-theme="light"] {
  --dt-bg-base:        oklch(0.97 0.003 270);   /* почти белый, тёплый */
  --dt-bg-surface:     oklch(0.99 0.002 270);   /* белые панели */
  --dt-bg-elevated:    oklch(1.00 0.000 0);     /* чисто белые карточки */
  --dt-bg-glass:       oklch(0.98 0.003 270 / 0.8);

  --dt-border-subtle:  oklch(0.88 0.005 270 / 0.5);
  --dt-border-default: oklch(0.82 0.008 270 / 0.6);
  --dt-border-focus:   oklch(0.55 0.18 270);

  --dt-text-primary:   oklch(0.20 0.010 270);
  --dt-text-secondary: oklch(0.40 0.010 270);
  --dt-text-muted:     oklch(0.60 0.008 270);

  --dt-accent:         oklch(0.55 0.18 270);
  --dt-accent-hover:   oklch(0.50 0.20 270);
  --dt-accent-active:  oklch(0.60 0.16 270);

  --dt-success:        oklch(0.55 0.18 155);
  --dt-warning:        oklch(0.65 0.16 80);
  --dt-error:          oklch(0.55 0.22 25);
  --dt-info:           oklch(0.55 0.15 240);
}
```

### Design Rationale

- **Hue 270 (violet/indigo)** как undertone нейтралей -- не серый, но и не навязчивый. Создаёт "прохладную" атмосферу dev-инструмента.
- **Мягкий accent** (лавандовый) -- не конкурирует с любым brand color, который введёт пользователь. Не красный, не синий, не зелёный -- самая "тихая" часть спектра.
- **Semantic colors приглушены** -- они нужны только для WCAG-индикаторов, не для привлечения внимания.

---

## 3. Typography

### Font Stack

```css
:root {
  /* UI текст -- гротеск для чистоты */
  --dt-font-sans: "Inter Variable", "Inter", -apple-system, BlinkMacSystemFont,
                  "Segoe UI", system-ui, sans-serif;

  /* Код (экспорт панели, HEX значения) */
  --dt-font-mono: "JetBrains Mono", "Fira Code", "SF Mono", "Cascadia Code",
                  ui-monospace, monospace;
}
```

### Why Inter + JetBrains Mono

- **Inter** -- создан для экранов, отличная читаемость на малых размерах, Variable-версия для оптимального bundle. Табулярные цифры из коробки (`font-variant-numeric: tabular-nums`) -- критично для отображения HEX, RGB, contrast ratio.
- **JetBrains Mono** -- лигатуры для кода, чёткое различение `0/O`, `1/l/I`. Пользователь будет много смотреть на код экспорта.

### Type Scale (Major Third -- 1.250)

```css
:root {
  --dt-text-xs:    0.75rem;    /* 12px -- WCAG badges, tiny labels */
  --dt-text-sm:    0.875rem;   /* 14px -- secondary text, hints */
  --dt-text-base:  1rem;       /* 16px -- body, inputs, buttons */
  --dt-text-md:    1.125rem;   /* 18px -- section subtitles */
  --dt-text-lg:    1.25rem;    /* 20px -- panel headers */
  --dt-text-xl:    1.5rem;     /* 24px -- page sections */
  --dt-text-2xl:   2rem;       /* 32px -- hero headline */
  --dt-text-3xl:   2.5rem;     /* 40px -- landing hero (if needed) */

  /* Line heights */
  --dt-leading-tight:  1.2;    /* headings */
  --dt-leading-normal: 1.5;    /* body text */
  --dt-leading-relaxed: 1.7;   /* long-form (descriptions) */

  /* Font weights */
  --dt-weight-normal:   400;
  --dt-weight-medium:   500;
  --dt-weight-semibold: 600;
  --dt-weight-bold:     700;

  /* Letter spacing */
  --dt-tracking-tight:  -0.02em;  /* headings */
  --dt-tracking-normal:  0;       /* body */
  --dt-tracking-wide:    0.05em;  /* labels, overlines, badges */
}
```

### Usage Rules

| Context | Size | Weight | Font |
|---------|------|--------|------|
| Page title ("DualTone") | 2xl | bold | sans |
| Section headers ("Primary Scale") | lg | semibold | sans |
| Panel labels ("Export", "WCAG") | sm, uppercase | semibold, tracking-wide | sans |
| Body text, descriptions | base | normal | sans |
| HEX values in swatches | sm | medium | mono |
| Code blocks (export) | sm | normal | mono |
| Contrast ratio numbers | base | semibold, tabular-nums | mono |
| Input fields | base | normal | mono (for color values), sans (for labels) |
| Buttons | base | medium | sans |

---

## 4. Layout

### Information Architecture

```
+---------------------------------------------------------------+
|  HEADER: Logo + Theme toggle + GitHub link                     |
+---------------------------------------------------------------+
|                                                                |
|  +--INPUT SECTION-----------------------------------------+   |
|  |  Color picker  |  HEX  |  RGB  |  HSL  |  Harmony ▼   |   |
|  +--------------------------------------------------------+   |
|                                                                |
|  +--PALETTE GRID------------------------------------------+   |
|  |  Primary (11)  | Secondary (11) | Accent (11)          |   |
|  |  Neutral (11)  | Success | Warning | Error | Info       |   |
|  |  Surface tokens (light) | Surface tokens (dark)         |   |
|  +--------------------------------------------------------+   |
|                                                                |
|  +--TWO-COLUMN SECTION------------------------------------+   |
|  |                          |                              |   |
|  |  UI PREVIEW              |  EXPORT PANEL               |   |
|  |  [Light] [Dark]          |  [CSS] [Tailwind] [JSON]    |   |
|  |                          |                              |   |
|  |  Hero section            |  :root {                    |   |
|  |  Card grid               |    --primary-500: ...;      |   |
|  |  Buttons                 |    --primary-600: ...;      |   |
|  |  Form inputs             |  }                          |   |
|  |  Table                   |                    [Copy]   |   |
|  |  Sidebar nav             |                              |   |
|  |                          +------------------------------+   |
|  |                          |                              |   |
|  |                          |  WCAG CHECKER               |   |
|  |                          |  text-on-bg: 7.2:1 [AAA] ✓  |   |
|  |                          |  muted-on-bg: 4.8:1 [AA] ✓  |   |
|  |                          |  accent-on-bg: 2.1:1 [X] !  |   |
|  |                          |           [Auto-fix All]    |   |
|  |                          |                              |   |
|  +--------------------------+------------------------------+   |
|                                                                |
+---------------------------------------------------------------+
```

### Grid System

```css
/* Mobile-first base layout */
.app-layout {
  display: grid;
  grid-template-rows: auto auto auto 1fr;
  gap: var(--dt-space-6);        /* 24px */
  padding: var(--dt-space-4);    /* 16px */
  min-height: 100dvh;
}

/* Desktop: preview + export side by side */
@media (min-width: 1024px) {
  .main-content {
    display: grid;
    grid-template-columns: 1fr 420px;
    gap: var(--dt-space-6);
  }
}

/* Wide desktop: more room for preview */
@media (min-width: 1440px) {
  .main-content {
    grid-template-columns: 1fr 500px;
  }
  .app-layout {
    max-width: 1400px;
    margin-inline: auto;
  }
}
```

### Spacing System (4px base)

```css
:root {
  --dt-space-1:  0.25rem;   /* 4px */
  --dt-space-2:  0.5rem;    /* 8px */
  --dt-space-3:  0.75rem;   /* 12px */
  --dt-space-4:  1rem;      /* 16px */
  --dt-space-5:  1.25rem;   /* 20px */
  --dt-space-6:  1.5rem;    /* 24px */
  --dt-space-8:  2rem;      /* 32px */
  --dt-space-10: 2.5rem;    /* 40px */
  --dt-space-12: 3rem;      /* 48px */
  --dt-space-16: 4rem;      /* 64px */
}
```

### Panel Design (Glassmorphism)

```css
.panel {
  background: var(--dt-bg-glass);
  backdrop-filter: blur(16px) saturate(1.2);
  -webkit-backdrop-filter: blur(16px) saturate(1.2);
  border: 1px solid var(--dt-border-subtle);
  border-radius: 16px;
  padding: var(--dt-space-6);
  box-shadow:
    0 4px 24px oklch(0 0 0 / 0.1),
    inset 0 1px 0 oklch(1 0 0 / 0.05);
}
```

---

## 5. Components

### 5.1 Color Input Bar

Горизонтальная полоса вверху -- главный input. Всегда видна, sticky на scroll.

```
+--------------------------------------------------------------+
|  [●] #6366F1  |  rgb(99,102,241)  |  hsl(239,84%,67%)  | ▼  |
+--------------------------------------------------------------+
   ^picker         ^HEX input          ^RGB (readonly)     ^harmony dropdown
```

**Поведение:**
- Color picker -- native `<input type="color">`, стилизованный как круг с текущим цветом
- HEX input -- `<input type="text">` с маской, mono font, auto-validates
- RGB и HSL -- readonly, обновляются мгновенно при изменении HEX
- Harmony dropdown -- complementary / analogous / triadic / split-complementary
- Весь бар -- glass panel с blur
- **Sticky** на скролл: `position: sticky; top: 0; z-index: 100;`

**Keyboard:**
- Tab переключает между picker, HEX, harmony
- Enter в HEX input применяет цвет
- Arrow Up/Down в HEX input +/- 1 к текущему компоненту

**Touch:**
- Picker -- 48x48px touch target (больше минимума в 44px)
- HEX input -- full-width на mobile

### 5.2 Palette Grid

Сгенерированная палитра отображается как горизонтальные ряды свотчей.

```
Primary    [50][100][200][300][400][500][600][700][800][900][950]
Secondary  [50][100][200][300][400][500][600][700][800][900][950]
Accent     [50][100][200][300][400][500][600][700][800][900][950]
Neutral    [50][100][200][300][400][500][600][700][800][900][950]
───────────────────────────────────────────────────────────
Success    [50]...[950]   Warning  [50]...[950]
Error      [50]...[950]   Info     [50]...[950]
```

**Каждый swatch:**
- Квадрат/прямоугольник с цветом
- При hover -- увеличение (scale 1.05) + tooltip с HEX/OKLCH
- При click -- copy HEX в clipboard + micro-animation (checkmark)
- Текст внутри swatch: step number (50, 100...) автоматически белый или чёрный в зависимости от luminance
- Locked swatch (input color = primary-500) -- иконка замка

**Responsive:**
- Desktop: все 11 свотчей в ряд
- Tablet: все 11, но меньше размер
- Mobile: горизонтальный скролл в контейнере (snap scrolling) или compact grid 6+5

### 5.3 UI Preview Panel

КРИТИЧЕСКИ ВАЖНЫЙ компонент. Показывает реальный UI, раскрашенный палитрой пользователя.

**Содержит:**
- **Toggle** [Light / Dark] -- переключает превью между режимами
- **Mini hero section** -- заголовок + подзаголовок + CTA кнопка + background
- **Card grid** (2 карточки) -- изображение-заглушка + title + description + badge
- **Button group** -- Primary, Secondary, Outline, Ghost, Destructive
- **Form** -- text input + select + checkbox + submit button
- **Sidebar fragment** -- nav items с иконками + active state
- **Table row** (2-3 строки) -- header + data rows + zebra striping

**Дизайн превью:**
- Масштаб 0.75 от реального размера (чтобы больше помещалось)
- Внутри -- iframe-like контейнер (изолированные стили через CSS scope или Shadow DOM)
- Рамка с radius 12px, тень -- выглядит как "экран устройства"
- Кнопка "Open fullscreen" для просмотра в полный размер

**Маппинг токенов на UI:**

| UI Element | Background | Text | Border | Accent |
|------------|-----------|------|--------|--------|
| Page background | surface-bg | -- | -- | -- |
| Card | surface-card | text-primary | border-subtle | -- |
| Primary button | primary-600 | white | -- | -- |
| Secondary button | surface-elevated | text-primary | border-default | -- |
| Ghost button | transparent | primary-600 | -- | -- |
| Destructive button | error-600 | white | -- | -- |
| Input field | surface-card | text-primary | border-default | primary-500 (focus) |
| Sidebar | surface-bg | text-secondary | -- | primary-500 (active) |
| Table header | surface-elevated | text-primary | border-subtle | -- |
| Table row (even) | surface-bg | text-primary | -- | -- |
| Table row (odd) | surface-card | text-primary | -- | -- |
| Badge | primary-100 | primary-800 | -- | -- |

### 5.4 Export Panel

Табированная панель с готовым кодом.

**Tabs:** `[CSS vars]` `[Tailwind v4]` `[JSON tokens]`

**Каждый таб:**
- Syntax-highlighted code block (тёмный фон даже в light mode)
- Кнопка **[Copy]** -- абсолютно позиционирована в правом верхнем углу блока
- При копировании: кнопка кратковременно становится зелёной + текст "Copied!"
- Код -- полностью валидный, paste-and-go

**CSS формат:**
```css
:root {
  --primary-50: oklch(0.97 0.02 265);   /* #f0f0ff */
  --primary-100: oklch(0.93 0.04 265);  /* #e0e0ff */
  /* ... */
}

@media (prefers-color-scheme: dark) {
  :root {
    --primary-50: oklch(0.20 0.02 265);
    /* ... */
  }
}
```

**Tailwind v4 формат:**
```css
@theme {
  --color-primary-50: oklch(0.97 0.02 265);
  --color-primary-100: oklch(0.93 0.04 265);
  /* ... */
}
```

**JSON формат (W3C Design Tokens):**
```json
{
  "primary": {
    "50": { "$value": "#f0f0ff", "$type": "color" },
    "100": { "$value": "#e0e0ff", "$type": "color" }
  }
}
```

**Дополнительные кнопки:**
- **[Download .css]** -- скачивает файл
- **[Download .json]** -- скачивает файл
- **[Share URL]** -- копирует URL с параметрами палитры

### 5.5 WCAG Checker Panel

Таблица пар foreground/background с результатами проверки.

```
┌──────────────────────────────────────────────────┐
│  WCAG Accessibility                    [Auto-fix] │
├──────────────────────────────────────────────────┤
│  text-primary on bg-base     12.8:1  AAA  ✓      │
│  text-secondary on bg-base    7.2:1  AAA  ✓      │
│  text-muted on bg-base        4.8:1  AA   ✓      │
│  primary-600 on white        5.1:1  AA   ✓      │
│  white on primary-600         5.1:1  AA   ✓      │
│  accent on bg-surface         2.1:1  FAIL !      │
├──────────────────────────────────────────────────┤
│  Passed: 18/20 pairs          Failed: 2          │
└──────────────────────────────────────────────────┘
```

**Визуальное кодирование:**
- AAA (7:1+) -- зелёный badge + checkmark
- AA (4.5:1+) -- зелёный badge + checkmark
- AA Large (3:1+) -- жёлтый badge + warning
- Fail (<3:1) -- красный badge + крестик

**Auto-fix button:**
- Один клик -- корректирует все проблемные пары
- Алгоритм: минимально сдвигает lightness в OKLCH до достижения AA
- Показывает diff: "accent changed from oklch(...) to oklch(...)"

### 5.6 Header

```
┌──────────────────────────────────────────────────┐
│  ◆ DualTone          [☀/🌙] [♿ Info] [⟨⟩ GitHub] │
└──────────────────────────────────────────────────┘
```

- **Logo** -- "DualTone" в font-weight: 700, с diamond icon (CSS-only)
- **Theme toggle** -- switch для dark/light mode самого DualTone (не превью!)
- **Accessibility info** -- tooltip с описанием a11y features
- **GitHub link** -- иконка, ведёт на repo

Минимальная высота: 56px. Не sticky (input bar sticky вместо header).

### 5.7 Share / Save

- **URL-based state** -- brand color + harmony type кодируются в URL hash: `#c=6366F1&h=complementary`
- **Share button** рядом с input bar -- копирует URL в clipboard
- **Saved palettes** -- dropdown с последними 10, хранятся в localStorage
- Каждая сохранённая палитра: цветной кружок + HEX + timestamp

---

## 6. Animations

### Philosophy: "Purposeful Motion"

Каждая анимация должна нести смысл -- показывать связь, подтверждать действие, направлять внимание. Никакой декоративной анимации.

### Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Все анимации ниже полностью отключаются при `prefers-reduced-motion: reduce`.

### Animation Catalog

| Trigger | Animation | Duration | Easing | Purpose |
|---------|-----------|----------|--------|---------|
| Color input changes | Palette swatches crossfade to new colors | 200ms | ease-out | Показать мгновенную связь input -> output |
| Swatch hover | Scale up 1.05 + soft shadow grow | 150ms | ease-out | Invite interaction |
| Swatch click (copy) | Checkmark icon fade-in + brief green flash | 300ms | ease-in-out | Confirm action |
| Light/Dark toggle | UI Preview crossfade between modes | 250ms | ease-in-out | Smooth transition, no jarring flash |
| Export tab switch | Content slide left/right + fade | 200ms | ease-out | Spatial relationship between tabs |
| Copy button click | Button text "Copy" -> "Copied!" + green bg | 300ms + 1500ms hold | ease | Confirm + auto-revert |
| WCAG auto-fix | Affected swatches pulse once + contrast numbers animate | 400ms | ease-out | Draw attention to changes |
| Panel scroll into view | Subtle fade-up (8px translate + opacity) | 300ms | ease-out | Progressive disclosure |
| Page load | Staggered panel entrance from bottom | 100ms stagger, 400ms each | cubic-bezier(0.16, 1, 0.3, 1) | Polished first impression |
| Error state (invalid HEX) | Input border flash red + shake (2px, 2 cycles) | 300ms | ease | Alert without being aggressive |

### CSS Implementation Pattern

```css
.swatch {
  transition: transform 150ms ease-out, box-shadow 150ms ease-out;
}
.swatch:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 12px oklch(0 0 0 / 0.2);
}

.palette-update .swatch {
  animation: color-shift 200ms ease-out;
}
@keyframes color-shift {
  from { opacity: 0.7; }
  to   { opacity: 1; }
}

.copy-confirm {
  animation: confirm-pop 300ms ease-in-out;
}
@keyframes confirm-pop {
  0%   { transform: scale(0.8); opacity: 0; }
  50%  { transform: scale(1.1); }
  100% { transform: scale(1); opacity: 1; }
}
```

---

## 7. Responsive Design

### Breakpoints

```css
/* Mobile-first: base styles = mobile (320px+) */

/* Tablet */
@media (min-width: 768px)  { /* ... */ }

/* Desktop */
@media (min-width: 1024px) { /* ... */ }

/* Wide */
@media (min-width: 1440px) { /* ... */ }
```

### Layout Changes Per Breakpoint

#### Mobile (320px -- 767px)

```
[Header]
[Color Input -- full width, stacked]
[Palette -- horizontal scroll per row]
[UI Preview -- full width]
[Export Panel -- full width, tabs]
[WCAG Checker -- full width]
```

- Color input: picker + HEX на одной строке, RGB/HSL скрыты в expandable
- Palette swatches: 32x32px, horizontal scroll с snap
- UI Preview: scale(0.65), можно открыть fullscreen
- Export: full width, code block с горизонтальным скроллом
- Copy button: 48x48px, fixed-position внутри code block
- Все панели стопкой вертикально

#### Tablet (768px -- 1023px)

```
[Header]
[Color Input -- full width, inline]
[Palette -- fits without scroll, smaller swatches]
[UI Preview -- full width]
[Export + WCAG -- side by side or tabbed]
```

- Color input: все поля в одну строку
- Palette swatches: 36x36px, все помещаются
- UI Preview: scale(0.7)
- Export и WCAG могут быть в двух колонках или tabbed

#### Desktop (1024px+)

```
[Header]
[Color Input -- full width, inline with harmony]
[Palette Grid -- full width, spacious]
[UI Preview (left, ~60%)  |  Export + WCAG (right, ~40%)]
```

- Palette swatches: 44x44px
- UI Preview и Export/WCAG бок о бок
- Export panel: достаточно ширины для кода без горизонтального скролла
- WCAG checker под Export panel в правой колонке

#### Wide Desktop (1440px+)

- Max-width контейнер: 1400px, centered
- Больше пространства для UI Preview
- Export panel шире -- видно больше строк кода

### Touch Targets

Все интерактивные элементы -- минимум 44x44px touch area:

```css
.interactive {
  min-height: 44px;
  min-width: 44px;
  /* Если визуально элемент меньше, расширяем touch area через padding или ::after */
}
```

---

## 8. Dark Mode (самого DualTone)

### Implementation

```css
/* System preference (default) */
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    /* dark variables */
  }
}

@media (prefers-color-scheme: light) {
  :root:not([data-theme="dark"]) {
    /* light variables */
  }
}

/* Manual override */
:root[data-theme="dark"]  { /* dark variables */ }
:root[data-theme="light"] { /* light variables */ }
```

### Toggle Logic

1. По умолчанию -- следует `prefers-color-scheme`
2. Пользователь может переключить вручную
3. Выбор сохраняется в `localStorage`
4. При следующем визите: если есть сохранённый выбор -- использовать его, иначе system preference
5. Кнопка имеет 3 состояния визуально: auto (system icon) / light (sun) / dark (moon)

### Что меняется между dark и light

| Element | Dark | Light |
|---------|------|-------|
| Background | Deep navy (#1a1a2e) | Off-white (#f8f8fc) |
| Panels | Semi-transparent dark glass | Semi-transparent white glass |
| Borders | Light lines on dark (white @ 10%) | Dark lines on light (black @ 10%) |
| Text | Light on dark | Dark on light |
| Code blocks | Dark bg (always) | Dark bg (always -- consistent) |
| Shadows | Darker, more diffused | Lighter, sharper |
| Accent | Brighter lavender | Deeper lavender |

### Transition

```css
:root {
  transition: background-color 250ms ease-in-out,
              color 250ms ease-in-out;
}
/* Panels transition too */
.panel {
  transition: background-color 250ms ease-in-out,
              border-color 250ms ease-in-out,
              box-shadow 250ms ease-in-out;
}
```

---

## 9. References

### 9.1 Realtime Colors (realtimecolors.com)

**What to take:** Live UI preview concept -- seeing colors applied to real components in real time. The core idea that a palette should be visualized on actual UI, not just swatches. Their simplicity of input.

**What to improve:** They don't auto-generate dark mode (manual), no WCAG checking, no code export beyond basic. DualTone does all of this automatically.

### 9.2 Linear (linear.app)

**What to take:** The gold standard of dark-first developer tool UI. Clean, fast, minimal chrome. Glassmorphism elements, soft gradients on interactive elements. The way they use a single accent color (violet) against a near-black background without it feeling monotonous.

**What to improve:** Linear is an issue tracker, not a color tool. But the vibe -- "professional tool that respects your time" -- is exactly what DualTone should feel like.

### 9.3 Shadcn/ui Themes (ui.shadcn.com/themes)

**What to take:** The concept of theming with CSS variables. Their token naming convention (--background, --foreground, --card, --primary, etc.) is becoming a standard. The "Copy code" experience is excellent -- one click, clean output. Their UI preview components are a direct inspiration for DualTone's preview panel.

**What to improve:** Shadcn themes require manual selection from pre-made themes. DualTone generates from any color. Shadcn doesn't show WCAG compliance.

### 9.4 Raycast (raycast.com)

**What to take:** The polish of a developer tool that feels like a design product. Subtle blur effects, smooth animations, keyboard-first UX. The way Raycast makes complex functionality feel simple and fast.

**What to improve:** Raycast is a launcher, not a color tool. But the design language -- dark, clean, fast, no clutter -- is the target for DualTone.

### 9.5 Palettte (palettte.app)

**What to take:** The concept of editing a color scale as a curve, understanding lightness/chroma distribution across steps. Their visualization of how colors relate to each other in perceptual space.

**What to improve:** Palettte is for manual scale editing. DualTone auto-generates with one click. But if we add manual override in Phase 2, the curve editing UX from Palettte is an excellent reference.

---

## 10. Implementation Notes

### CSS Custom Properties Strategy

DualTone uses two layers of custom properties:

1. **`--dt-*`** -- DualTone's own UI tokens (the app chrome)
2. **`--gen-*`** (or no prefix) -- Generated palette tokens (shown in preview and export)

This separation ensures the app's own UI never conflicts with user-generated colors.

### Performance Considerations

- **Font loading:** Inter Variable + JetBrains Mono loaded async with `font-display: swap` and preload hints. Total: ~100KB for both variable fonts.
- **Backdrop-filter:** Has GPU cost. Use `will-change: backdrop-filter` on panels, limit blur to 16px. On low-end devices, fallback to solid semi-transparent bg.
- **Color calculations:** All OKLCH math runs in a Web Worker to avoid blocking the main thread during rapid input changes.
- **Swatch rendering:** CSS Grid, no canvas. Each swatch is a `<button>` (semantic, keyboard-accessible).

### Accessibility Checklist (DualTone itself)

- [ ] All interactive elements have visible focus rings (2px solid, --dt-border-focus)
- [ ] Color is never the only way to convey information (badges have text + icon)
- [ ] All images/icons have alt text or aria-label
- [ ] Modals trap focus
- [ ] Escape closes any popover/dropdown
- [ ] Skip-to-content link for keyboard users
- [ ] WCAG AA contrast for all DualTone's own text/background pairs
- [ ] Touch targets >= 44px
- [ ] No auto-playing animations
- [ ] `prefers-reduced-motion` respected everywhere
- [ ] Screen reader announcements for copy actions (`aria-live="polite"`)
- [ ] Language attribute: `<html lang="en">`

---

## 11. Visual Summary

### The Feel

> Dark velvet workspace. Glass panels float above. One input, one color -- and the entire UI blooms with a harmonious system. Every swatch clickable, every code block copyable, every contrast checked. The tool gets out of your way and lets the colors speak.

### Key Design Principles

1. **Neutral chrome, vivid content** -- DualTone itself is muted; the user's palette is the star
2. **One click, one result** -- no wizard, no steps, no tutorial. Input -> output -> copy -> done
3. **Developer-first aesthetics** -- dark mode, monospace code, keyboard shortcuts, fast
4. **Accessibility by example** -- DualTone passes its own WCAG checks, proving the tool's philosophy
5. **Offline-ready** -- feels like a native app, works without internet
