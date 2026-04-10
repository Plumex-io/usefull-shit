# Figma Guidelines for Plumex Landing

> Рекомендації для дизайнера по організації Figma файлу, щоб AI-інструменти (Claude) могли ефективно конвертувати дизайн у код.

---

## 1. Називання фреймів (КРИТИЧНО)

### Проблема

Більшість фреймів мають автогенеровані назви: `Frame 2121453667`, `Frame 4652`, `Frame 2830`. Claude отримує дерево елементів з назвами — і коли бачить `Frame 2121453667`, не розуміє призначення блоку.

Ще гірше — деякі секції названі просто цифрами (`1`, `3`, `6`) або іменами пристроїв (`iPad Pro 11" - 4`), а деякі мають дублікати імен (два різних фрейми `Frame 2121453670`).

### Що зробити

Кожен фрейм має мати **семантичну назву**, яка описує його роль:

**Desktop (666:5211) — повний маппінг:**

| Зараз (погано) | Має бути (добре) | Секція |
|---|---|---|
| `Desktop - Concept` | `Desktop` | Корінь |
| `Picture` | `Page Content` (або видалити цей рівень) | Обгортка |
| `1` | `Hero` | Block 1 |
| `4352390` | `Earn Spend CTA` | Block 3 |
| `Frame 2121453641` | `Parallax Phone` | Block 2 |
| `3` | `Cash Access` | Block 5 |
| `Frame 2121453574` | `Cash In Out / Header` | Block 6 |
| `Frame 2121453670` (5327) | `Cash In Out / Cards` | Block 6 |
| `6` | `Cryptoheads` | Block 7 |
| `Frame 2121453647` | `Invite Club` | Block 8 |
| `Frame 2121453670` (5342) | `Invite Club / Cards` | Block 8 |
| `Frame 2121453640` | `Security` | Block 9 |
| `Frame 2121453676` | `FAQ / Accordion` | FAQ |
| `Frame 2121453678` | `Footer / Links` | Footer |
| `Frame 2830` | `Header / Navigation` | Header |

**Зображення та сміття:**

| Зараз | Має бути |
|---|---|
| `1 111111113` | `Hero / Video Placeholder` |
| `Rectangle 1810` | Видалити або `Hero / Background Shape` |
| `Знімок екрана 2026-03-27 о 10.48.41 2` | Видалити (це скріншот, не елемент дизайну) |
| `Isometric iPhone mockup 1` | `Parallax / Phone Mockup` |
| `ChatGPT Image Mar 29, 2026...` | `Cash Access / Globe Image` |
| `photo_5379795092475942447_y 1` | `Security / Lock Photo` |
| `Ellipse 1736` | `Security / Glow Effect` |
| `Rectangle 24` | `Footer / Divider` |
| `Vector 211` | `Invite Club / Arrow Decoration` |

### Правила називання

- Використовуй `/` як роздільник ієрархії: `Section / Element / Sub-element`
- Назва = роль елементу, не його вміст
- Англійська мова для всіх назв
- Без цифр в кінці (якщо це не варіанти компонента)
- **Не використовувати назви пристроїв** (`iPad Pro 11"`) — називати за контентом
- **Не дублювати назви** між різними фреймами

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

Section "Final" містить **24 елементи на одному рівні** без ієрархії:
- 3 основних breakpoints (Desktop, iPad, iPhone)
- 5 станів форми email (focus, typing, error, success — для desktop та mobile)
- 5 окремих Card фреймів
- 3 фрейми Maintenance
- Popup, loose rectangles, screenshots

Все це пласким списком. Крім того, Desktop фрейм (`Desktop - Concept`) має всередині зайвий рівень `Picture`, який обгортає весь контент сторінки без жодної причини.

Footer у Desktop — розсипаний: links, divider, legal text, copyright, великий логотип — все окремі діти `Picture` без обгортки.

### Рекомендована структура

```
Page: Landing final
├── Desktop (1440px) — FRAME, Auto Layout VERTICAL, gap: 0
│     ├── Hero                    — FRAME, AL VERTICAL
│     ├── Parallax Phone          — FRAME, AL VERTICAL
│     ├── Earn Spend CTA          — FRAME, AL VERTICAL
│     ├── Travel Cards Carousel   — FRAME, AL HORIZONTAL
│     ├── Cash Access             — FRAME, AL VERTICAL
│     ├── Cash In Out             — FRAME, AL VERTICAL
│     ├── Cryptoheads             — FRAME, AL VERTICAL
│     ├── Invite Club             — FRAME, AL VERTICAL
│     ├── Security                — FRAME, AL VERTICAL
│     ├── FAQ                     — FRAME, AL VERTICAL
│     └── Footer                  — FRAME, AL VERTICAL
│           ├── Footer / Links
│           ├── Footer / Divider
│           ├── Footer / Legal Text
│           ├── Footer / Copyright
│           └── Footer / Logo
│
├── Mobile (375px) — FRAME, Auto Layout VERTICAL, gap: 0
│     ├── Hero
│     ├── ... (ті самі назви секцій)
│     └── Footer
│
├── States / Email Form           — окрема група для станів форми
│     ├── Desktop / Focus
│     ├── Desktop / Typing
│     ├── Desktop / Error
│     ├── Desktop / Success
│     ├── Mobile / Focus
│     ├── Mobile / Typing
│     ├── Mobile / Error
│     └── Mobile / Success
│
└── Maintenance                   — окрема група
      ├── Desktop
      ├── Tablet
      └── Mobile
```

### Що виправити конкретно

1. **Видалити рівень `Picture`** — перемістити всі секції напряму в Desktop фрейм
2. **Згрупувати Footer** — links + divider + legal + copyright + logo → один фрейм `Footer`
3. **Винести стани форми** в окрему групу або на іншу сторінку
4. **Винести Maintenance** в окрему групу
5. **Видалити сміття** — `Rectangle 1810`, `Знімок екрана 2026-03-27...`, loose `Isometric iPhone mockup 1`
6. **Винести окремі Card фрейми** (717:12455-12481) на сторінку Components або видалити

### Правила

- **Один кореневий фрейм на breakpoint** (Desktop 1440px, Mobile 375px)
- Кореневий фрейм = Auto Layout VERTICAL, gap: 0
- Кожна секція = окремий фрейм-дитина з full width (`FILL`)
- Секції іменовані однаково в Desktop і Mobile
- **Стани UI** (форми, modals) — окремо від основного потоку
- **Без зайвих рівнів вкладеності** (як `Picture`)

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

- `Desktop - Concept` (1440px) — є, але має проблеми (див. нижче)
- `iPad Pro` (834px) — є
- `Iphone 11 Pro` (375px) — є

### Проблеми з поточними breakpoints

1. **Назви не консистентні:** `Desktop - Concept` vs `iPad Pro` vs `Iphone 11 Pro` — мікс пристроїв і абстрактних назв
2. **Desktop без Auto Layout:** кореневий фрейм і всі секції всередині — абсолютне позиціонування
3. **Зайвий рівень `Picture`** в Desktop — обгортає весь контент без причини
4. **Слово "Concept"** — натякає що це чернетка, а не фінальна версія. Claude може проігнорувати

### Рекомендація

Перейменувати:
- `Desktop - Concept` → `Desktop` (1440px)
- `iPad Pro` → `Tablet` (834px)
- `Iphone 11 Pro` → `Mobile` (375px)

---

## 9. Сміття та зайві елементи

### Що видалити/прибрати

| Елемент | ID | Причина |
|---|---|---|
| `Знімок екрана 2026-03-27 о 10.48.41 2` | `666:5367` | Скріншот, не елемент дизайну |
| `Rectangle 1810` (658x654) | `666:5366` | Невідомий прямокутник без видимої ролі |
| Loose `Isometric iPhone mockup 1` | `729:8214` | Дублікат, лежить окремо від секції |
| 5x `Card` фреймів (717:12455-12481) | — | Окремі карточки поза потоком, мабуть заготовки |

### Дублікати імен (Claude не розрізнить)

- `Frame 2121453670` використовується для **двох різних** секцій: Cash In Out Cards (666:5327) та Invite Club Cards (666:5342)
- `Text` — використовується як ім'я для 8+ різних фреймів в Security та Cards секціях

---

## Чеклист

### Критично (без цього Claude не може нормально працювати)
- [ ] Перейменувати ВСІ фрейми семантично (особливо `1`, `3`, `6`, `Picture`, `4352390`)
- [ ] Увімкнути Auto Layout на Desktop фреймі та всіх секціях
- [ ] Виправити дублікати назв (`Frame 2121453670`, `Text`)

### Високий пріоритет
- [ ] Видалити зайвий рівень `Picture` в Desktop
- [ ] Загорнути Footer в один фрейм
- [ ] Створити компоненти для Card, Section Header, Email Form
- [ ] Замінити GROUP на FRAME
- [ ] Винести стани форми (email focus/typing/error/success) в окрему групу

### Середній пріоритет
- [ ] Перейменувати breakpoints: `Desktop`, `Tablet`, `Mobile`
- [ ] Назвати зображення зрозуміло
- [ ] Виправити typo `bols` → `bold`
- [ ] Прив'язати всі тексти/кольори до стилів
- [ ] Видалити сміття (скріншоти, зайві прямокутники)
- [ ] Заповнити або видалити порожню Variable Collection
