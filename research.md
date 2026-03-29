# Color Palette Tool -- Competitive Research

**Date:** 2026-03-29
**Author:** Sanya (Product Manager, OpenClaw)

---

## 1. Top-5 конкурентов

### 1.1 Coolors (coolors.co)
- **Цена:** Free (с рекламой) / Pro $3-5/мес ($36/год)
- **Рейтинг:** ~4.7 на G2, 4.8 App Store
- **Сильные стороны:** Скорость (spacebar = новая палитра), экспорт в PDF, мобильное приложение, плагины для Adobe/Figma
- **Слабые стороны:**
  - Без интернета бесполезен -- нельзя даже просмотреть сохранённые палитры
  - Биллинг-проблемы: пользователи жалуются на списания после отмены подписки (до 2 лет!)
  - Cookie-попапы, туториалы и монетизация мешают UX
  - Ограничение на кол-во цветов (min/max)
  - Не генерирует dark mode автоматически
  - Нет экспорта в CSS variables / Tailwind "из коробки" (только HEX/RGB)

### 1.2 Adobe Color (color.adobe.com)
- **Цена:** Бесплатно (часть Creative Cloud)
- **Рейтинг:** ~4.5 на G2
- **Сильные стороны:** Гармонии по теории цвета, интеграция с CC Libraries, тренды с Behance, извлечение палитры из фото
- **Слабые стороны:**
  - Привязка к экосистеме Adobe -- бесполезно без CC подписки для полного workflow
  - Нет экспорта в CSS/Tailwind/design tokens
  - Нет автоматической генерации dark mode
  - Перегруженный интерфейс для простых задач
  - Нет проверки WCAG контраста прямо в генераторе палитры

### 1.3 Khroma (khroma.co)
- **Цена:** Free trial / Pro $20/мес
- **Рейтинг:** ~4.2
- **Сильные стороны:** AI учится на твоих предпочтениях (выбираешь 50 цветов), бесконечные персонализированные комбинации
- **Слабые стороны:**
  - Дорого ($20/мес за цветовой инструмент)
  - Онбординг 50 цветов -- долгий и утомительный
  - Генерирует пары цветов, а не полные дизайн-системы
  - Нет экспорта в код
  - Нет dark mode generation

### 1.4 Colormind (colormind.io)
- **Цена:** Free / API Pro от $19/мес
- **Рейтинг:** ~4.0
- **Сильные стороны:** Deep learning, палитры из фото, понимает UI-паттерны
- **Слабые стороны:**
  - Устаревший UI (выглядит как 2018)
  - Нет WCAG проверки
  - Нет dark mode
  - API дорогой для инди-разработчиков
  - Нет экспорта в современные форматы (CSS vars, Tailwind)

### 1.5 Realtime Colors (realtimecolors.com) + UIColors.app
- **Цена:** Бесплатно
- **Рейтинг:** ~4.6 (Product Hunt)
- **Сильные стороны:** Визуализация палитры на реальном UI в реальном времени, экспорт в Tailwind
- **Слабые стороны:**
  - UIColors: только генерация шкалы одного цвета (50-950), не полная система
  - Realtime Colors: нет автоматической WCAG проверки всех пар
  - Нет автоматического dark mode (ручной переключатель, но палитру надо подбирать самому)
  - Нет офлайн-режима

---

## 2. Боли пользователей (из отзывов 1-2 звезды)

### Категория A: "Мне нужен КОД, а не картинка"
> Разработчики и инди-хакеры жалуются, что палитру нужно вручную переносить в CSS/Tailwind. Coolors даёт HEX, но не CSS variables. Adobe Color сохраняет в CC Libraries, но не в код.

### Категория B: "Dark mode -- это отдельный ад"
> Создать палитру для light mode -- легко. Но сделать корректный dark mode, где контраст сохраняется, цвета не "кислотные", а бренд узнаваем -- это 2-4 часа ручной работы. Ни один топ-инструмент не делает это автоматически и хорошо.

### Категория C: "Accessibility -- отдельный инструмент"
> Пользователи генерируют палитру в одном месте, проверяют контраст в другом (WebAIM), фиксят цвета, возвращаются. Цикл из 3-4 инструментов.

### Категория D: "Офлайн не работает"
> Coolors без интернета бесполезен. Для разработчиков, работающих в самолёте или в кафе с плохим Wi-Fi -- это проблема.

### Категория E: "Слишком много выбора, нет guidance"
> Не-дизайнеры (фаундеры, инди-хакеры) теряются. Им нужен не генератор 5 HEX-кодов, а готовая дизайн-система: "вот твой primary, вот secondary, вот так выглядит на кнопках, вот CSS -- копируй".

---

## 3. Анализ ниш

| Ниша | Конкуренция | Потенциал | Вердикт |
|------|-------------|-----------|---------|
| Генератор палитр (generic) | Coolors, Color Hunt, 50+ | Низкий | SKIP |
| Палитра по настроению/AI | MoodPalette, ColrLab, 5+ | Средний | SKIP |
| Палитра для бренда | Zoviz, HubSpot, 10+ | Средний | SKIP |
| Accessibility checker | WebAIM, AccessiblePalette, 8+ | Средний | SKIP |
| Dark mode generator | Colorffy (basic), ToolsJam (basic) | Высокий | INTERESTING |
| **Design System из 1 цвета (light+dark+a11y+code)** | **Частичные решения, нет "всё в одном"** | **Очень высокий** | **WINNER** |

---

## 4. Выбранная ниша

**"One Color In -- Full Design System Out"**

Пользователь вводит ОДИН brand color. Система генерирует:
- Полную палитру (primary, secondary, accent, neutral, semantic)
- Light mode + Dark mode (автоматически)
- WCAG AA/AAA проверку каждой пары text/background
- Готовый код: CSS variables, Tailwind config, design tokens JSON
- Визуальный превью на реальном UI

**Почему эта ниша свободна:**
1. Coolors/Adobe Color -- генерируют палитру, но не design system
2. UIColors/Tints.dev -- генерируют шкалу 1 цвета, но не полную систему
3. Realtime Colors -- показывает UI, но dark mode ручной
4. AccessiblePalette -- проверяет контраст, но не генерирует dark mode
5. Colorffy dark theme -- генерирует dark, но без a11y проверки

Никто не делает ВСЁ ВМЕСТЕ в одном инструменте, офлайн, бесплатно.

---

## Sources

- [Coolors](https://coolors.co/)
- [Coolors Pricing](https://coolors.co/pricing)
- [Coolors Reviews on G2](https://www.g2.com/products/coolors/reviews)
- [Coolors Reviews on Trustpilot](https://www.trustpilot.com/review/coolors.co)
- [Adobe Color](https://color.adobe.com/)
- [Khroma](https://khroma.co/)
- [Colormind](http://colormind.io/)
- [UIColors.app](https://uicolors.app/generate)
- [Realtime Colors](https://realtimecolors.com/)
- [Accessible Palette](https://accessiblepalette.com/)
- [InclusiveColors](https://www.inclusivecolors.com/)
- [Colorffy Dark Theme Generator](https://colorffy.com/dark-theme-generator)
- [AI Color Palette Generator (CSS/Tailwind)](https://www.colorpalettegenerator.ai/)
- [Tints.dev](https://www.tints.dev/)
- [Muzli Blog -- Best Color Tools 2026](https://muz.li/blog/best-color-palette-tools-and-generators-for-designers-2026/)
- [Venngage -- 15 Best Color Palette Generators](https://venngage.com/blog/color-palette-generators/)
- [Dark Mode Color Palettes Guide](https://mypalettetool.com/blog/dark-mode-color-palettes)
