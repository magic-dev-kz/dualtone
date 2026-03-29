# DualTone -- Design System Generator

**One color in. Full system out. Light + Dark + Accessible + Code-ready.**

**Date:** 2026-03-29
**Author:** Sanya (Product Manager, OpenClaw)
**Status:** Spec v1.0

---

## 1. Название: DualTone

Почему "DualTone":
- **Dual** = light mode + dark mode (главная фишка)
- **Tone** = цвет, оттенок, тональность
- Короткое, запоминающееся, доменопригодное
- Звучит как инструмент, а не как ещё один "Color Something"

Альтернативы (backup): ShadeShift, Lumino, PaletteDrop

---

## 2. Persona

### Alex, 29 лет -- инди-хакер / фронтенд-разработчик

**Контекст:** Делает SaaS-проект в одиночку или в маленькой команде (2-3 человека). Дизайнера нет. Нужно быстро собрать приличный UI. Знает CSS/Tailwind, но теорию цвета знает поверхностно.

**Боли:**
- Тратит 2-3 часа на подбор цветов, потом ещё столько же на dark mode
- Гуглит "WCAG contrast checker" отдельно, фиксит цвета вручную
- Копирует HEX из Coolors, вручную переводит в CSS variables
- Dark mode делает "на глаз" -- результат выглядит дёшево
- Каждый раз, когда меняет brand color, переделывает ВСЁ заново

**Цель:** "Дай мне один цвет -- а дальше сделай за меня всю систему. Чтобы я скопировал код и поехал дальше."

**Вторичные персоны:**
- Фаундер стартапа, который делает лендинг сам
- Junior-дизайнер, которому нужна быстрая отправная точка
- Фрилансер, который делает 3-5 проектов в месяц и не хочет тратить время на рутину

---

## 3. Главная функция

### One-Click Design System

Пользователь вводит ОДИН HEX-цвет (brand color). DualTone генерирует:

```
Input:  #6366F1 (indigo)
Output:
  - Primary scale:    50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950
  - Secondary:        auto-generated complementary scale
  - Accent:           auto-generated triadic/split-complementary
  - Neutral/Gray:     desaturated version of primary (не чистый серый, а "тёплый" или "холодный" в тон бренду)
  - Semantic:         success (green), warning (amber), error (red), info (blue) -- гармонизированы с brand
  - Surface colors:   background, card, popover, muted -- для ОБОИХ режимов
  - Text colors:      foreground, muted-foreground -- для ОБОИХ режимов
```

Каждая пара text/background проверена на WCAG AA (4.5:1) и AAA (7:1).
Если пара не проходит -- автоматически корректируется с минимальным отклонением от исходного цвета.

---

## 4. Изюм -- что отличает от Coolors

| Coolors (и аналоги) | DualTone |
|----------------------|----------|
| Генерирует 5 HEX-кодов | Генерирует полную дизайн-систему (40+ токенов) |
| Dark mode? Сам разбирайся | Dark mode автоматически из того же brand color |
| WCAG? Иди на WebAIM | WCAG встроен: каждая пара проверена, плохие автофикшены |
| Экспорт: PNG, PDF, HEX | Экспорт: CSS vars, Tailwind v4, JSON tokens, Figma tokens |
| Нужен интернет | Работает полностью офлайн (PWA) |
| Визуализация: полоски цветов | Визуализация: реальный UI (кнопки, карточки, формы, дашборд) |

**Killer feature:** "Brand Color DNA" -- из одного цвета алгоритм строит всю систему с учётом перцептуального цветового пространства (OKLCH/CIELAB), а не простой HSL-интерполяции. Это значит:
- Шкала выглядит равномерно для человеческого глаза (а не математически равномерно)
- Dark mode сохраняет "ощущение бренда", а не просто инвертирует lightness
- Нейтральные серые имеют оттенок бренда (warm gray для тёплых, cool gray для холодных)

---

## 5. Acceptance Criteria

### AC-1: Ввод brand color
- Пользователь может ввести HEX (#RRGGBB), RGB, HSL или выбрать пикером
- При вводе невалидного значения -- показать ошибку inline, без перезагрузки
- Палитра генерируется мгновенно (< 100ms) при каждом изменении цвета

### AC-2: Генерация полной палитры
- Из одного цвета генерируется: primary (11 оттенков), secondary (11), accent (11), neutral (11), semantic (4 цвета x 11 оттенков)
- Алгоритм использует OKLCH цветовое пространство для перцептуальной равномерности
- Палитра включает surface и text токены для light и dark режимов

### AC-3: Автоматический dark mode
- При генерации палитры автоматически создаётся dark mode версия
- Dark mode НЕ является простой инверсией -- surfaces тёмные (не чёрные), accent цвета скорректированы по яркости
- Переключение light/dark в UI -- один клик, мгновенно

### AC-4: WCAG accessibility проверка
- Каждая пара foreground/background проверяется на WCAG 2.1 AA (4.5:1 для текста, 3:1 для крупного текста)
- Пары, не прошедшие проверку, отмечены визуально (красный индикатор)
- Кнопка "Auto-fix" корректирует проблемные пары с минимальным отклонением от оригинала
- Показан числовой contrast ratio для каждой пары

### AC-5: Визуализация на реальном UI
- Палитра применяется к превью реального UI: hero section, карточки, кнопки, формы, таблица, sidebar
- Превью переключается между light и dark режимами
- Превью responsive: показывает desktop и mobile вид

### AC-6: Экспорт в код
- CSS Custom Properties (`:root` для light, `[data-theme="dark"]` или `@media (prefers-color-scheme: dark)` для dark)
- Tailwind CSS v4 (CSS-based config с `@theme`)
- JSON Design Tokens (формат W3C Design Tokens)
- Копирование одним кликом (кнопка "Copy" у каждого формата)
- Скопированный код -- валидный, без ошибок, paste-and-go

### AC-7: Офлайн работа (PWA)
- Приложение работает полностью офлайн после первого посещения
- Service Worker кеширует все ресурсы
- Палитры сохраняются в localStorage (до 50 палитр)
- При повторном онлайне -- нет потери данных

### AC-8: Сохранение и шаринг палитр
- Палитра сохраняется в URL (query params или hash) -- можно поделиться ссылкой
- Кнопка "Share" копирует URL в буфер обмена
- URL содержит только brand color + настройки -- компактный и читаемый

### AC-9: Настройки генерации
- Можно выбрать color harmony: complementary, analogous, triadic, split-complementary
- Можно задать "color temperature" нейтральных серых (от холодных до тёплых)
- Можно заблокировать конкретный цвет (lock) и перегенерировать остальные
- Можно вручную подправить любой цвет -- система пересчитывает зависимые токены

### AC-10: Responsive и доступный UI
- Работает на mobile (320px+), tablet (768px+), desktop (1024px+)
- Сам интерфейс DualTone проходит WCAG AA
- Keyboard navigation: Tab, Enter, Escape работают на всех элементах
- Поддержка prefers-color-scheme для самого приложения

---

## 6. Технические требования

### Stack
- **HTML5 + CSS3 + Vanilla JS** (или Svelte для компонентов, если нужен state management)
- Без тяжёлых фреймворков (React/Vue) -- минимальный bundle size
- Вся генерация на клиенте, zero backend
- Цветовые вычисления: библиотека `culori` (OKLCH, CIELAB, gamut mapping)

### PWA
- Service Worker с cache-first стратегией
- Web App Manifest для установки на home screen
- Offline-first: все функции работают без интернета

### Responsive
- Mobile-first CSS
- CSS Grid / Flexbox layout
- Touch-friendly: min 44px touch targets
- No horizontal scroll на любом устройстве

### Performance
- First Contentful Paint < 1s
- Time to Interactive < 2s
- Total bundle < 100KB gzipped (без шрифтов)
- Генерация палитры < 100ms на среднем телефоне

### Accessibility
- Semantic HTML (headings, landmarks, labels)
- ARIA labels для интерактивных элементов
- Focus management при модальных окнах
- High contrast mode support
- Screen reader тестирование (VoiceOver, NVDA)

### Экспорт форматов
- CSS: `:root { --primary-500: oklch(...); }` с fallback в HEX
- Tailwind v4: `@theme { --color-primary-500: oklch(...); }`
- JSON: W3C Design Tokens Community Group формат
- Clipboard API для копирования

### Хранение
- localStorage для сохранённых палитр (JSON)
- URL-based state (brand color + harmony + settings в query string)
- Нет аккаунтов, нет логина, нет серверной части

---

## 7. Метрики успеха (v1)

| Метрика | Target |
|---------|--------|
| Время от входа до "скопировал код" | < 30 секунд |
| Кол-во экспортов (copy events) | > 40% сессий |
| Возврат пользователей (7d retention) | > 15% |
| Lighthouse Performance Score | > 95 |
| Lighthouse Accessibility Score | 100 |
| Product Hunt upvotes (launch day) | > 200 |

---

## 8. Roadmap (phases)

### Phase 1 -- MVP (2 недели)
- Ввод 1 цвета -> полная палитра (light + dark)
- WCAG проверка всех пар
- Экспорт: CSS vars + Tailwind v4
- UI превью (hero + cards + buttons)
- PWA + офлайн
- Responsive

### Phase 2 -- Polish (1 неделя)
- JSON Design Tokens экспорт
- Настройки гармонии (complementary/analogous/triadic)
- Manual override любого цвета
- Share via URL

### Phase 3 -- Growth (ongoing)
- Figma plugin
- VS Code extension
- Gallery общедоступных палитр
- AI: "Describe your brand" -> brand color suggestion

---

## 9. Не делаем (anti-scope)

- Аккаунты и авторизацию
- Backend и базу данных
- Градиенты и паттерны
- Палитры из фотографий (есть у Coolors/Adobe, не наша ниша)
- AI генерацию "по настроению" (есть у MoodPalette, не наша ниша)
- Платные функции (v1 полностью бесплатный)
