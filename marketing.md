# DualTone — Marketing Plan
**Автор:** Игорь (маркетолог OpenClaw)
**Дата:** 2026-03-29

---

## 1. Название для App Store

**DualTone - Design System**

(24 символа)

---

## 2. Слоган

**Один цвет. Вся система. Light + Dark за секунду.**

Варианты:
- "Забудь Coolors. Тебе нужна система, а не 5 полосок."
- "Brand color in. Design system out."
- "Dark mode больше не проблема."

---

## 3. Описание (первые 3 строки решают всё)

> Введи один цвет -- получи готовую дизайн-систему: 40+ токенов, light + dark mode, WCAG проверен. Скопируй CSS/Tailwind и работай.
>
> Никаких аккаунтов. Никакого интернета. Никаких дизайнеров. Результат за 30 секунд.
>
> Coolors даёт 5 HEX-кодов. DualTone даёт систему, которую можно вставить в код прямо сейчас.

---

## 4. Persona

**Алекс, 29, фронтендер-одиночка из Белграда.**

Делает SaaS для управления подписками. Команда -- он и один бэкендер. Дизайнера нет и бюджета на него тоже. Вчера потратил 3 часа: подобрал palette на Coolors, вручную конвертировал в CSS variables, потом ещё час ковырял dark mode -- вышло как "инвертированный ад". Утром открыл DualTone, вбил #6366F1, скопировал Tailwind v4 конфиг, вставил -- и light, и dark выглядят как из Figma. За 30 секунд сделал то, на что раньше уходил вечер.

---

## 5. Три критических скриншота

**Скриншот 1: "Hero moment" -- ввод цвета и мгновенный результат**
- Слева: инпут с HEX-цветом #6366F1 и color picker
- Справа: полная палитра -- primary, secondary, accent, neutral шкалы (50-950)
- Внизу: переключатель Light/Dark
- Текст на скриншоте: "One color. Full system."

**Скриншот 2: "Реальный UI превью" -- палитра на живом интерфейсе**
- Мокап реального дашборда: sidebar, карточки, кнопки, формы
- Split-view: левая половина light, правая dark
- WCAG бейджи: зелёные чекмарки "AA" рядом с текстовыми парами
- Текст на скриншоте: "See it on real UI. Not color strips."

**Скриншот 3: "Копируй и уходи" -- экспорт в код**
- Три таба: CSS Variables / Tailwind v4 / JSON Tokens
- Код в монотипном шрифте, подсветка синтаксиса
- Кнопка "Copy" с чекмарком "Copied!"
- Текст на скриншоте: "Paste-ready code. Zero config."

---

## 6. ASO ключевые слова

1. design system generator
2. color palette
3. dark mode colors
4. tailwind colors
5. css color variables
6. wcag contrast checker
7. color scheme generator
8. design tokens
9. brand colors
10. accessibility colors
11. oklch palette
12. light dark theme
13. color scale generator
14. ui color tool
15. frontend design tool

---

## 7. Конкуренты и позиционирование

| Конкурент | Что делает | Слабое место | Наш удар |
|-----------|-----------|-------------|----------|
| **Coolors.co** | 5 HEX-полосок, рандомная генерация | Нет dark mode, нет WCAG, нет кода -- просто красивые полоски | "Coolors для вдохновения. DualTone для продакшена." |
| **Realtime Colors** | Палитра + UI preview | Нет шкал (50-950), нет Tailwind export, ограниченный dark mode | "Preview есть, но кода нет. У нас -- и то, и другое." |
| **Tailwind CSS Palette Generator** (javisperez) | Генерирует шкалу одного цвета | Только primary, нет secondary/accent/semantic, нет dark mode | "Один цвет = один масштаб. У нас один цвет = вся система." |
| **Huemint** | AI палитра для бренда | Медленный, требует интернет, не даёт код-ready токены | "Красиво, но не вставишь в проект. У нас -- copy-paste." |
| **Adobe Color** | Колесо + гармонии | Перегруженный UI, нет design tokens, нужен аккаунт Adobe | "Enterprise-монстр для простой задачи. DualTone -- 30 секунд." |

**Позиционирование:** DualTone -- единственный инструмент, который из ОДНОГО цвета генерирует production-ready дизайн-систему с light/dark/WCAG/код-экспортом. Не палитра. Не вдохновение. Система.

---

## 8. Вирусный потенциал

### Share-момент
- **URL-шаринг палитры.** Каждая палитра -- уникальный URL. Разработчик кидает ссылку в чат команды: "вот наши цвета". Ссылка открывается мгновенно, без регистрации.
- **Twitter/X скриншоты.** Split-view light/dark на реальном UI -- это визуально мощный контент. Формат "вбил один цвет, получил это" идеально ложится в тред.

### TikTok / Reels
- **Формат:** 15-секундный ролик. Набирает #6366F1 -> палитра вырастает -> переключает light/dark -> копирует Tailwind -> вставляет в VS Code -> сайт меняет цвета. "30 seconds from zero to design system."
- **Аудитория:** devtok, дизайн-контент, инди-хакеры.

### Product Hunt
- Идеальный PH-продукт: бесплатный, мгновенная ценность, красивый визуал, решает конкретную боль.
- Заголовок: "DualTone -- One color in, full design system out"
- Tagline: "Stop picking colors. Start shipping."

### Reddit / Hacker News
- r/webdev, r/frontend, r/sideproject -- прямая аудитория.
- HN: "Show HN: I built a tool that generates a full design system from one color"

---

## 9. Категория App Store

**Основная:** Developer Tools
**Вторичная:** Design (если доступна)

Для Web/PWA: Product Hunt, Hacker News, Reddit, Twitter/X dev community.
