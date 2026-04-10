# Figma Guidelines for Plumex Landing

> Рекомендації для дизайнера по організації Figma файлу, щоб AI-інструменти (Claude) могли ефективно конвертувати дизайн у код.

---

## 1. Називання фреймів (КРИТИЧНО)

### Проблема

Більшість фреймів мають автогенеровані назви: `Frame 2121453667`, `Frame 4652`, `Frame 2830`. Claude отримує дерево елементів з назвами — і коли бачить `Frame 2121453667`, не розуміє призначення блоку.

### Що зробити

Кожен фрейм має мати **семантичну назву**, яка описує його роль:

| Зараз (погано) | Має бути (добре) |
|---|---|
| `Frame 2121453574` | `Cash In Out / Section Header` |
| `Frame 2121453667` | `Cash In Out / Cards Grid` |
| `Frame 2121453625` | `Invite Club` |
| `Frame 4652` | `Hero / Content Wrapper` |
| `Frame 2121453678` | `Footer / Links` |
| `Frame 2121453663` | `Footer / Links / Services` |
| `Frame 2830` | `Header / Navigation` |

### Правила називання

- Використовуй `/` як роздільник ієрархії: `Section / Element / Sub-element`
- Назва = роль елементу, не його вміст
- Англійська мова для всіх назв
- Без цифр в кінці (якщо це не варіанти компонента)

---

## 2. Auto Layout — використовувати скрізь

### Проблема

Багато фреймів не мають Auto Layout. Елементи позиціоновані абсолютно (вручну x/y координати).

Приклади без Auto Layout:
- `Picture` (Parallax секція) — 9 дітей без Auto Layout
- `iPad Pro 11" - 4` — абсолютне позиціонування
- `Frame 2121453640` (Security) — 5 елементів без структури
- `Frame 2121453625` (Invite Club) — 4 елемента абсолютно

### Чому це важливо

Claude конвертує Auto Layout напряму:
- `Auto Layout VERTICAL` → `display: flex; flex-direction: column`
- `Auto Layout HORIZONTAL` → `display: flex; flex-direction: row`
- `gap: 16` → `gap: 16px`
- `padding: 24, 40, 24, 40` → `padding: 24px 40px`
- `FILL` → `flex: 1` або `width: 100%`
- `HUG` → `width: fit-content`

Без Auto Layout Claude має вгадувати layout з абсолютних координат — це дає помилки.

### Що зробити

1. **Кожна секція** — вертикальний Auto Layout
2. **Горизонтальні ряди** (карточки, колонки) — горизонтальний Auto Layout
3. **gap** замість ручних відступів між елементами
4. **padding** на фреймі замість порожніх спейсерів
5. **Fill / Hug** замість фіксованих розмірів де можливо

---

## 3. Структура сторінки

### Проблема

Зараз все лежить в `SECTION` "Final", а окремі блоки — фрейми з назвами `iPad Pro`, `3`, `Picture`. Немає чіткого поділу на логічні секції.

### Рекомендована структура

```
Page: Landing final
└── Desktop (1440px) — FRAME, Auto Layout VERTICAL, gap: 0
      ├── Hero                    — FRAME, AL VERTICAL
      ├── Parallax Phone          — FRAME, AL VERTICAL
      ├── Earn Spend CTA          — FRAME, AL VERTICAL
      ├── Travel Cards Carousel   — FRAME, AL HORIZONTAL
      ├── Cash Access             — FRAME, AL VERTICAL
      ├── Cash In Out             — FRAME, AL VERTICAL
      ├── Cryptoheads             — FRAME, AL VERTICAL
      ├── Invite Club             — FRAME, AL VERTICAL
      ├── Security                — FRAME, AL VERTICAL
      ├── FAQ                     — FRAME, AL VERTICAL
      └── Footer                  — FRAME, AL VERTICAL

└── Mobile (375px) — FRAME, Auto Layout VERTICAL, gap: 0
      ├── Hero
      ├── Parallax Phone
      ├── ... (ті самі секції)
      └── Footer
```

### Правила

- **Один кореневий фрейм на breakpoint** (Desktop 1440px, Mobile 375px)
- Кореневий фрейм = Auto Layout VERTICAL, gap: 0
- Кожна секція = окремий фрейм-дитина з full width (`FILL`)
- Секції іменовані однаково в Desktop і Mobile

---

## 4. Компоненти

### Проблема

Є лише кілька компонентів (`Buttons`, `list web` для FAQ). Карточки, заголовки секцій, форми — просто скопійовані фрейми.

### Що створити як компоненти

| Компонент | Варіанти | Де використовується |
|---|---|---|
| `Button` | Primary / Secondary, Size: SM / MD / LG | Скрізь |
| `Card` | Light / Dark / Image, Size: SM / LG | Travel Cards, Cash In Out, Invite Club |
| `Section Header` | With Badge / Without Badge | Cash Access, Cash In Out, Security |
| `FAQ Item` | Collapsed / Expanded | FAQ (вже є `list web`) |
| `Footer Link Column` | — | Footer |
| `Email Form` | Default / Focus / Error / Success | Invite Club |
| `Badge` | — | Cash Access, карточки |

### Чому це важливо

- Claude розпізнає `INSTANCE` як використання компонента → генерує переиспользований React компонент
- Без компонентів — Claude створює дублікати коду для кожної карточки

---

## 5. Стилі та змінні

### Що вже добре

**Paint Styles (12 штук):**
`Main/purple`, `Main/black`, `Main/white-100`, `Main/white-60%`, `Main/white-10%`, `Main/black-90%`, `Main/grey`, `Main/accent`, `Main/black-42%`, `Error`, `error bg`, `Main/grey-1`

**Text Styles (23 штуки):**
`Title/100 bold`, `Title/94 bold`, `Title/80 semb`, `Title/68 semb`, `Title/58 bold`, `Text/28 reg`, `Text/24 reg`, `Text/20 med`, `Text/18 reg`, `Text/16 reg`, `Text/14 reg`, `Bttn`, `Label`, тощо.

### Що виправити

1. **Typo:** `Title/20 bols` → `Title/20 bold`
2. **Inconsistent naming:** `Main/black-90%` vs `Main/white-60%` vs `Main/black-42%` — обрати один формат: `Main/black-90` або `Main/black/90%`
3. **Variable Collection** порожня (1 змінна, `Mode 1`) — або заповнити кольорами/spacing, або видалити
4. **Застосувати стилі до всіх елементів** — кожен текст має мати Text Style, кожен фон — Paint Style

### Рекомендація по Variables

Створити Variable Collection для:
- **Colors:** `color/purple`, `color/lime`, `color/black`, `color/white` тощо
- **Spacing:** `spacing/4`, `spacing/8`, `spacing/16`, `spacing/24`, `spacing/32`, `spacing/48`
- **Border Radius:** `radius/sm (8)`, `radius/md (16)`, `radius/lg (24)`, `radius/pill (999)`

---

## 6. Назви зображень

### Проблема

Зображення мають незрозумілі назви:

| Зараз | Має бути |
|---|---|
| `1 111111113` | `Hero / Video Placeholder` |
| `Isometric iPhone mockup 1` | `Parallax / Phone Mockup` |
| `ChatGPT Image Mar 22, 2026, 10_11_13 PM 1` | `Security / Lock Illustration` |
| `Image__` | `Security / Lock Image` |
| `Plumex 5` | `Parallax / Background Gradient` |
| `Ellipse 1736` | `Security / Glow Effect` |
| `Rectangle 24` | `Footer / Divider` |
| `Vector 211` | `Invite Club / Arrow Decoration` |

---

## 7. Уникати GROUP

### Проблема

Є GROUP вузли (наприклад `Group 1597881458`). Groups не підтримують Auto Layout, padding, constraints.

### Що зробити

Замінити всі GROUP на FRAME з Auto Layout. GROUP корисні тільки для тимчасового групування при переміщенні — не для фінальної структури.

---

## 8. Breakpoints

### Поточний стан

- `iPad Pro` (834px) — є
- `Iphone 11 Pro` (375px) — є
- Desktop (1440px) — **НЕМАЄ**

### Рекомендація

- **Desktop (1440px)** — додати, це основний breakpoint для web
- **Mobile (375px)** — вже є
- **Tablet (834px)** — опціонально, може бути зроблений через CSS

---

## Чеклист

- [ ] Перейменувати ВСІ фрейми семантично
- [ ] Увімкнути Auto Layout на всіх секціях
- [ ] Створити один кореневий фрейм на breakpoint
- [ ] Створити компоненти для Card, Section Header, Email Form
- [ ] Замінити GROUP на FRAME
- [ ] Назвати зображення зрозуміло
- [ ] Виправити typo `bols` → `bold`
- [ ] Додати Desktop (1440px) фрейм
- [ ] Прив'язати всі тексти/кольори до стилів
- [ ] Заповнити або видалити порожню Variable Collection
