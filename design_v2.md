# DualTone v2 -- Design Review & Upgrade Plan (8.0 -> 9.0+)

**Date:** 2026-03-29
**Author:** Sky (Creative Designer, OpenClaw)
**Trigger:** Саня: "Для 9.0 нужна не технология, а история: явная персона, враг, агрессивный hero."

---

## 1. Честный аудит: 6 вопросов

### 1.1 Первое впечатление

**Что видит пользователь:** header "DualTone", sticky input bar с color picker + hex input + harmony select, и сразу палитру. Нет hero, нет заголовка, нет объяснения что это и зачем.

**Проблема:** Продукт открывается как "рабочий стол" без приветствия. Дизайнер поймёт за 3 секунды. Разработчик -- за 10. Маркетолог -- никогда. Нет персоны, нет врага, нет обещания.

**Вердикт:** 5/10. Функционально -- всё на месте. Эмоционально -- холодный инструмент.

### 1.2 Onboarding

**Нет.** Вообще никакого. Ни overlay, ни welcome screen, ни tooltip tour. Пользователь должен сам догадаться что можно:
- менять цвет (очевидно)
- кликать swatch для копирования (не очевидно)
- раскрывать collapsible секции (не очевидно)
- экспортировать код (скрыто)

**Вердикт:** 3/10. Для dev tool простота -- плюс. Но progressive disclosure без disclosure point = скрытый функционал.

### 1.3 Визуальная иерархия

**Главное на экране:** палитра (правильно). Но:
- Header и input bar визуально одного веса -- два одинаковых серых бара сверху
- Section titles ("Color Scales", "Semantic") -- 0.75rem uppercase серый текст, почти невидимый
- Collapsible headers ("UI Preview & Export", "WCAG Contrast Check") выглядят как disabled text, не как интерактивные элементы
- Share и Theme toggle -- одинаковые кнопки, одинаковый вес. Share -- CTA, должна выделяться

**Вердикт:** 6/10. Палитра читается. Всё остальное -- визуальный шум одного уровня.

### 1.4 Progressive disclosure

**Collapsible секции есть** -- это хорошо (UI Preview, WCAG, Saved). По умолчанию закрыты (aria-expanded="false"). Но:
- Chevron как единственный индикатор -- слишком тонкий
- Нет подсказки ЗАЧЕМ раскрывать ("3 contrast issues found", "Preview your palette live")
- Нет badge/counter рядом с заголовком, мотивирующего клик

**Вердикт:** 7/10. Механика правильная, мотивация раскрытия слабая.

### 1.5 Share момент

**Share button есть** -- копирует URL с hash `#c=6366F1&h=complementary`. Но:
- Нет share card / OG image
- URL выглядит как `file:///...index.html#c=6366F1` -- не share-friendly
- Нет "красивой" визуализации палитры для скриншота/соцсетей
- Нет кнопки "Download as PNG" для палитры

**Вердикт:** 4/10. Техническая ссылка есть, share-worthy момент -- нет.

### 1.6 Эмоция

**Текущая эмоция:** "Полезный калькулятор". Работает, всё верно, ноль вау-эффекта.

**Что должно быть:** "Один клик -- и моя палитра готова. Я хочу показать это коллеге." Чувство магии, мгновенного результата, профессионального качества.

**Вердикт:** 5/10. Уважение к пользователю -- да. Желание вернуться -- нет.

---

## 2. Стратегия: от калькулятора к продукту с историей

### Персона

**Мидл-фронтенд разработчик.** Знает CSS, использует Tailwind. Каждый проект начинает с подбора цветов. Тратит 40 минут на цвета вместо того чтобы кодить. Ненавидит когда дизайнер присылает палитру без контраст-чеков.

### Враг

**"Цветовой хаос".** 47 оттенков серого в проекте. Кнопка, которую не видит слабовидящий. Dark mode, где "просто инвертировали". Figma-файл с 200 цветами и ни одним токеном.

### Обещание

**"One color in. Full system out. Accessible by default."** -- уже есть в title, но пользователь этого НЕ ВИДИТ при открытии.

---

## 3. Конкретные изменения для 9.0+

### 3.1 Hero / Onboarding (NEW)

Не modal overlay. Встроенный первый экран, который виден ДО палитры.

**Структура:**

```html
<section class="hero" id="heroSection">
  <div class="hero-content">
    <h2 class="hero-title">One color in.<br><span class="hero-accent">Full system out.</span></h2>
    <p class="hero-subtitle">Pick any color. Get a complete design system with OKLCH scales, semantic tokens, dark mode, and WCAG-checked contrast. Ready for CSS, Tailwind, or JSON.</p>
    <div class="hero-cta">
      <div class="hero-picker-wrap">
        <!-- Крупный color picker, 80x80 -->
      </div>
      <span class="hero-or">or</span>
      <button class="hero-random-btn">Surprise me</button>
    </div>
  </div>
  <div class="hero-visual">
    <!-- Анимированный мини-превью: 4 полоски цветов, плавно меняющихся -->
  </div>
</section>
```

**CSS:**

```css
.hero {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 48px 24px 40px;
  max-width: 1400px;
  margin: 0 auto;
  gap: 48px;
}

.hero-title {
  font-size: 2.5rem;
  font-weight: 700;
  letter-spacing: -0.03em;
  line-height: 1.15;
  color: var(--text-primary);
}

.hero-accent {
  background: linear-gradient(135deg, var(--accent), #ec4899);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero-subtitle {
  font-size: 1.05rem;
  color: var(--text-secondary);
  line-height: 1.6;
  max-width: 480px;
  margin-top: 12px;
}

.hero-cta {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-top: 28px;
}

.hero-random-btn {
  padding: 14px 28px;
  border-radius: 12px;
  border: none;
  background: var(--accent);
  color: #fff;
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  transition: all 200ms ease;
  box-shadow: 0 0 20px rgba(167, 139, 250, 0.3);  /* button glow */
}

.hero-random-btn:hover {
  box-shadow: 0 0 32px rgba(167, 139, 250, 0.5);
  transform: translateY(-1px);
}

/* Hero visual: анимированные полоски палитры */
.hero-visual {
  display: flex;
  flex-direction: column;
  gap: 8px;
  width: 320px;
  flex-shrink: 0;
}

.hero-strip {
  height: 32px;
  border-radius: 8px;
  display: flex;
  overflow: hidden;
  animation: hero-shimmer 8s ease-in-out infinite alternate;
}

.hero-strip-cell {
  flex: 1;
  transition: background 600ms ease;
}

/* После первого взаимодействия hero схлопывается */
.hero.collapsed {
  padding: 16px 24px;
}

.hero.collapsed .hero-subtitle,
.hero.collapsed .hero-visual {
  display: none;
}

.hero.collapsed .hero-title {
  font-size: 1.25rem;
}

@media (max-width: 768px) {
  .hero { flex-direction: column; text-align: center; padding: 32px 16px; }
  .hero-title { font-size: 1.8rem; }
  .hero-visual { width: 100%; }
  .hero-subtitle { max-width: 100%; }
  .hero-cta { justify-content: center; }
}
```

**Поведение:**
- Hero виден при первом открытии
- После выбора цвета (picker или "Surprise me") -- hero плавно схлопывается в компактный вариант (только заголовок)
- Hero visual -- 4 полоски по 11 ячеек, медленно перебирающие случайные палитры (loop анимация)
- При схлопывании фокус переходит на палитру

### 3.2 Collapsible -- мотивация раскрытия

Добавить badge-counter к каждому collapsible header:

```css
.collapsible-hint {
  font-size: 0.7rem;
  font-weight: 500;
  color: var(--accent);
  padding: 2px 8px;
  border-radius: 10px;
  background: var(--accent-dim);
  margin-left: 8px;
}

/* Для WCAG секции -- красный badge если есть failures */
.collapsible-hint.has-issues {
  background: rgba(248, 113, 113, 0.15);
  color: var(--error);
}
```

**Тексты badge:**
- UI Preview & Export: "Live preview"
- WCAG Contrast: "2 issues" (красный) или "All pass" (зеленый)
- Saved Palettes: "3 saved"

### 3.3 Share button -- CTA styling

Текущая share button визуально идентична theme toggle. Share -- это CTA.

```css
.share-btn {
  background: var(--accent);
  color: #fff;
  border: none;
  border-radius: var(--radius-sm);
  padding: 8px 16px;
  font-weight: 600;
  font-size: 0.8rem;
  cursor: pointer;
  transition: all 200ms ease;
  box-shadow: 0 0 16px rgba(167, 139, 250, 0.25);
}

.share-btn:hover {
  box-shadow: 0 0 24px rgba(167, 139, 250, 0.4);
  transform: translateY(-1px);
}
```

### 3.4 Share Card (NEW)

Генерируемая Canvas-картинка 1200x630 (OG-формат) для шеринга палитры.

**Дизайн share card:**

```
+----------------------------------------------------------+
|                                                          |
|  [Primary strip: 11 swatches горизонтально]              |
|  [Secondary strip]                                       |
|  [Accent strip]                                          |
|  [Neutral strip]                                         |
|                                                          |
|  DualTone                     #6366F1 Complementary      |
|  One color in. Full system out.                          |
|                                                          |
+----------------------------------------------------------+
```

**Реализация:**

```javascript
function generateShareCard() {
  const canvas = document.createElement('canvas');
  canvas.width = 1200;
  canvas.height = 630;
  const ctx = canvas.getContext('2d');

  // Background
  ctx.fillStyle = '#1a1a2e';
  ctx.fillRect(0, 0, 1200, 630);

  // 4 palette strips
  const scales = [palette.primary, palette.secondary, palette.accent, palette.neutral];
  const stripHeight = 80;
  const startY = 60;
  const stripGap = 12;

  scales.forEach((scale, i) => {
    const y = startY + i * (stripHeight + stripGap);
    scale.colors.forEach((c, j) => {
      const x = 40 + j * (1120 / 11);
      const w = 1120 / 11 - 4;
      ctx.fillStyle = c.hex;
      roundRect(ctx, x, y, w, stripHeight, 8);
    });
  });

  // Branding
  ctx.fillStyle = 'rgba(255,255,255,0.92)';
  ctx.font = '700 32px system-ui';
  ctx.fillText('DualTone', 40, 500);

  ctx.fillStyle = 'rgba(255,255,255,0.5)';
  ctx.font = '400 18px system-ui';
  ctx.fillText('One color in. Full system out.', 40, 535);

  // HEX + harmony
  ctx.fillStyle = 'rgba(255,255,255,0.7)';
  ctx.font = '500 20px monospace';
  ctx.textAlign = 'right';
  ctx.fillText(state.hex + '  ' + state.harmony, 1160, 505);

  return canvas.toDataURL('image/png');
}
```

**Кнопки в share flow:**
1. "Copy Link" -- URL с hash (уже есть)
2. "Download Card" -- PNG 1200x630
3. "Copy Card" -- canvas to clipboard (navigator.clipboard.write с blob)

### 3.5 Визуальная иерархия -- фиксы

**Section titles:**
```css
.section-title {
  font-size: 0.8rem;   /* было 0.75 */
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--text-secondary);  /* было tertiary -- слишком бледно */
  margin-bottom: 16px;           /* было 12 */
  display: flex;
  align-items: center;
  gap: 8px;
}

/* Цветная полоска слева для типизации секций */
.section-title::before {
  content: '';
  width: 3px;
  height: 14px;
  border-radius: 2px;
  background: var(--accent);
}
```

**Input bar -- визуальное разделение от header:**
```css
.input-bar {
  padding: 16px 24px;     /* было 12px -- тесно */
  gap: 16px;               /* было 12px */
  background: var(--bg-elevated);  /* отличается от header */
  border-bottom: none;     /* убираем двойной border */
  box-shadow: var(--shadow-sm);    /* тонкая тень вниз */
}
```

**Collapsible headers -- сделать интерактивными:**
```css
.collapsible-header {
  padding: 12px 16px;
  border-radius: var(--radius-sm);
  background: var(--bg-surface);
  border: 1px solid var(--border-color);
  transition: all 200ms ease;
  margin-bottom: 12px;
}

.collapsible-header:hover {
  background: var(--bg-surface-hover);
  border-color: var(--accent);
}

.collapsible-header[aria-expanded="true"] {
  border-color: var(--accent);
  background: var(--accent-dim);
}
```

### 3.6 Swatch -- усиление hover

```css
.swatch:hover {
  transform: scale(1.12);     /* было 1.08 */
  box-shadow:
    var(--shadow-md),
    0 0 0 2px var(--bg-base),
    0 0 0 3px currentColor;   /* outline кольцо цвета свотча */
  z-index: 10;
}

/* Показывать hex всегда на фокусе (a11y) */
.swatch:focus-visible .swatch-hex {
  opacity: 0.9;
}
```

### 3.7 Background -- теплее

Саня прав, чистый `#1a1a2e` -- холодный. Добавить микро-градиент:

```css
body {
  background:
    radial-gradient(ellipse 80% 50% at 50% 0%, rgba(167, 139, 250, 0.04), transparent),
    var(--bg-base);
}
```

Это создаёт едва заметное свечение от accent вверху страницы -- ощущение "тепла" без потери нейтральности.

### 3.8 Random button -- более заметный

```css
.random-btn {
  background: linear-gradient(135deg, var(--accent-dim), rgba(236, 72, 153, 0.1));
  border-color: var(--accent);
  color: var(--accent);
  font-weight: 600;
}

.random-btn:hover {
  background: var(--accent);
  color: #fff;
}
```

---

## 4. Приоритеты реализации

### Must-have (8.0 -> 8.5)
1. Hero section с tagline и крупным picker
2. Section title с цветной полоской
3. Share button как CTA с glow
4. Collapsible headers с hover state и badge-hints
5. Background micro-gradient
6. Input bar визуальное отделение от header

### Should-have (8.5 -> 9.0)
7. Share card (Canvas PNG 1200x630)
8. WCAG badge count в collapsible header
9. Hero collapse при первом взаимодействии
10. Random button с accent styling
11. Swatch hover усиление (ring)

### Nice-to-have (9.0 -> 9.5)
12. Hero visual -- анимированные палитры
13. "Download Card" + "Copy Card" в share flow
14. Onboarding tooltips на первые 3 действия (первый запуск)
15. Palette comparison mode (до/после autofix)

---

## 5. Метрики успеха

| Метрика | 8.0 (сейчас) | 9.0 (цель) |
|---------|-------------|-------------|
| Понятно что это за 3 сек | Нет | Да (hero) |
| Мотивация раскрыть collapsible | Нет | Да (badges) |
| Share-worthy момент | URL only | Card + Link |
| CTA выделяется | Нет | Да (glow) |
| Эмоция при открытии | Нейтральная | "О, красиво" |
| Визуальная иерархия | Плоская | 3 уровня |

---

## 6. Что НЕ менять

- OKLCH math -- работает идеально
- Collapsible mechanism -- правильный паттерн, нужна только мотивация
- Dark-first approach -- верно для dev tool
- Export panel tabs -- хорошая реализация
- A11y foundations (skip-to-content, aria, focus styles) -- сильная база
- Glassmorphism panels -- работает, не трогать
