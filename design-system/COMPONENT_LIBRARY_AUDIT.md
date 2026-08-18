# Component Library Audit

**Дата аудита:** 13 июля 2026  
**Источник Figma:** `Proctor-Digital-System` (`fileKey: 5nRJojaNYRcXFy6v2Xhp0N`)  
**Страница библиотеки:** `09 Components`  
**Режим:** read-only — изменения в Figma не вносились

---

## Executive Summary

Текущая Figma Component Library представляет собой **раннюю, частично сформированную библиотеку** с сильным фундаментом в Design Tokens и базовых примитивах, но **существенным разрывом** с архитектурой, зафиксированной в `COMPONENT_LIBRARY.md`, `PATTERN_LIBRARY.md` и `VISUAL_LANGUAGE.md`.

### Общая оценка

| Область | Оценка | Комментарий |
|---------|--------|-------------|
| Design Tokens | **Частично готов** | 119 переменных в 6 коллекциях; только Light mode; нет text/effect styles; code syntax не задан |
| Primitives | **В основном готов** | Button, Badge, inputs, controls — есть, но с дублями и неполными variant sets |
| Components | **Частично** | Навигация, feedback, card — базово; отсутствуют Table, Tooltip, Accordion, Dropdown |
| Composite | **Минимально** | Layout shell есть; нет Filter Panel, Bulk Action Bar, Workspace Header |
| Domain | **Кандидаты** | 18 standalone domain-компонентов без properties и без композиции из примитивов |
| Architecture alignment | **Низкая** | ~40% ожидаемых компонентов отсутствуют; naming не совпадает с документацией |
| Visual Language alignment | **Средняя** | Спокойная палитра и типографика; риски: variant explosion, дубли, слабая token binding на layout |
| Platform strategy | **Не дифференцирована** | Нет явного разделения Driver / Proctor / Administrator в библиотеке |

### Ключевые выводы

1. **Библиотека смешивает три слоя:** продуктовые UI-компоненты, layout foundation и артефакты документации (Flow Node, CJM Cell, Screen Card).
2. **Архитектурный gap:** отсутствуют критические P0-компоненты — Queue Item, Timeline Item, Decision Actions, Filter Chips, KPI Card, Video Player.
3. **Технический долг:** дублирование (Search / Search Field, Input Base / TextField, Badge / Chip / StatusIndicator), незавершённые variant sets (Stepper, Tab, Toast, StatusIndicator), Button с 60 вариантами вместо композиции Icon Button.
4. **Token discipline:** примитивы частично привязаны к variables; layout-компоненты (App Shell, Sidebar, Top Bar, Bottom Action Bar) — преимущественно hardcoded fills.
5. **Domain layer:** компоненты существуют как плоские standalone-фреймы без component properties, без nested instances и без связи с Pattern Library.

---

## Inventory

Полный инвентарь получен программным сканированием страницы `09 Components`.

**Итого:** 33 Component Sets + 38 Standalone Components = **71 компонентных узла**

### Foundations (Design Tokens & Styles)

Расположение: страницы `01 Foundation` – `07 Icons`, коллекции Variables.

| Артефакт | Расположение | Назначение | Статус |
|----------|--------------|------------|--------|
| Colors (76 vars) | Collection `Colors`, mode `Light` | Brand, Surface, Text, Border, Semantic, Exam, AI, Monitoring, Charts | Ready (Light only) |
| Spacing (14 vars) | Collection `Spacing` | 2xs → 8xl scale | Ready |
| Radius (7 vars) | Collection `Radius` | None → Pill | Ready |
| Borders (3 vars) | Collection `Borders` | Hairline, Regular, Strong | Ready |
| Typography (16 vars) | Collection `Typography` | Size, Weight, Font family | Ready (no text styles) |
| Motion (3 vars) | Collection `Motion` | Fast, Normal, Slow duration | Candidate |
| Text Styles | — | — | **Missing** |
| Effect Styles (Shadows) | Страница `06 Shadows` | Elevation tokens | **Missing as styles** |
| Icons | Страница `07 Icons` | Icon set | In Progress |
| Grid | Страница `04 Grid & Layout` | Layout grid | Candidate (page empty) |

**Замечания по tokens:**
- `Colors`: многие переменные имеют scope `ALL_FILLS` вместо точечных (`FRAME_FILL`, `TEXT_FILL`) — противоречит `design-system/AGENTS.md`
- `codeSyntax` не задан ни на одной переменной — нет round-trip с Dev Mode
- Dark mode отсутствует (только `Light`)
- Semantic `Error` и `Danger` дублируют смысл

---

### Component Sets — полный реестр

#### Foundation Layout Library (`Foundation Layout Library` frame)

| Component | ID | Level | Variants | Variant Axes | States | Sizes | Properties | Slots | Auto Layout | Variables | Nested Components | Status |
|-----------|-----|-------|----------|--------------|--------|-------|------------|-------|-------------|-----------|-------------------|--------|
| App Shell | 14:7 | Composite | 2 | Viewport | Mobile, Desktop | — | VARIANT: Viewport | Нет | Да | **Нет** (hardcoded fills) | Нет | Needs Refactoring |
| Page | 14:14 | Composite | 3 | Variant | Default, With Sidebar, Fullscreen | — | VARIANT: Variant | Нет | Да | Нет | Нет | In Progress |
| Content Container | 14:21 | Composite | 3 | Width | Narrow, Default, Wide | — | VARIANT: Width | Нет | Да | Нет | Нет | Ready |
| Section | 14:28 | Composite | 3 | Style | Default, Elevated, Borderless | — | VARIANT: Style | Нет | Да | Нет | Нет | Needs Refactoring — пересечение с Card |
| Top Bar | 14:33 | Composite | 2 | Viewport | Mobile, Desktop | — | VARIANT: Viewport | Нет | Да | Нет | Нет | Needs Refactoring |
| Sidebar | 14:38 | Composite | 2 | State | Expanded, Collapsed | — | VARIANT: State | Нет | Да | Нет | Нет | Needs Refactoring — дубль с Sidebar/Navigation |
| Bottom Action Bar | 14:45 | Composite | 3 | Action | One Action, Two Actions, Sticky | — | VARIANT: Action | Нет | Да | Нет | Нет | In Progress — не совпадает с Action Bar из docs |
| Scroll Area | 14:50 | Composite | 2 | Direction | Vertical, Horizontal | — | VARIANT: Direction | Нет | Да | Нет | Нет | Ready |

#### Foundation UI Library (`Foundation UI Library` frame)

| Component | ID | Level | Variants | Variant Axes | States | Sizes | Properties | Slots | Auto Layout | Variables | Nested Components | Status |
|-----------|-----|-------|----------|--------------|--------|-------|------------|-------|-------------|-----------|-------------------|--------|
| Button | 17:123 | Primitive | 60 | Style, Size, State | Default, Hover, Pressed, Disabled | Small, Medium, Large | VARIANT ×3 | Нет | Да | fills, fontSize, fontWeight | Нет | Needs Refactoring — variant explosion; Icon как Style |
| Card | 17:136 | Component | 4 | Style | Default, Elevated, Bordered, Empty | — | VARIANT: Style | Нет | Да | fills | Нет | Ready |
| Badge | 17:147 | Primitive | 5 | Tone | Neutral, Success, Warning, Danger, Info | — | VARIANT: Tone | Нет | Да | fills | Нет | Ready — не совпадает с Status Badge из docs |
| Input Base | 17:156 | Primitive | 4 | State | Default, Focus, Error, Disabled | — | VARIANT: State | Нет | Да | fills | Нет | Duplicate — дубль TextField |
| Checkbox | 18:11 | Primitive | 3 | State | Unchecked, Checked, Disabled | — | VARIANT: State | Нет | Да | fills, radius, strokes | Нет | Ready |
| Radio | 18:22 | Primitive | 3 | State | Unchecked, Checked, Disabled | — | VARIANT: State | Нет | Да | fills, radius, strokes | Нет | Ready |
| Switch | 18:35 | Primitive | 3 | State | Off, On, Disabled | — | VARIANT: State | Нет | Да | fills, radius, strokes | Нет | Ready |
| Select | 18:42 | Component | 3 | State | Default, Focus, Disabled | — | VARIANT: State | Нет | Да | fills | Нет | In Progress |
| Search Field | 18:49 | Component | 3 | State | Default, Focus, Disabled | — | VARIANT: State | Нет | Да | fills | Нет | Ready |
| Dialog | 19:18 | Component | 4 | Type | Information, Warning, Confirmation, Error | — | VARIANT: Type | Нет | Да | fills, radius | Нет | In Progress — нет action slots |
| Alert | 19:27 | Component | 4 | Type | Info, Success, Warning, Error | — | VARIANT: Type | Нет | Да | fills | Нет | Ready |
| Loader | 19:41 | Primitive | 3 | State | Default, Compact, Large | — | VARIANT: State | Нет | Да | strokes, fills | Нет | Ready |
| Progress | 19:37 | Primitive | 2 | Type | Linear, Circular | — | VARIANT: Type | Нет | Да | fills | Нет | Ready |

#### Navigation & Data (mixed into UI Library)

| Component | ID | Level | Variants | Variant Axes | States | Properties | Auto Layout | Variables | Nested | Status |
|-----------|-----|-------|----------|--------------|--------|------------|-------------|-----------|--------|--------|
| Sidebar/Navigation/Open | 24:182 | Composite | 1 | State=Expanded | Expanded only | VARIANT | Да | fills | Нет | Needs Refactoring — дубль Sidebar |
| Tab | 24:189 | Component | 2 | State | Default, Active | VARIANT | Да | fills | Нет | Needs Refactoring — Disabled вне set |
| Stepper | 24:199 | Component | 1 | State=Upcoming | Upcoming only | VARIANT | Да | fills | Нет | Needs Refactoring — Completed/Current вне set |
| Breadcrumb | 24:208 | Component | 2 | State | Default, Current | VARIANT | Да | fills | Нет | In Progress |
| Pagination | 24:217 | Component | 2 | State | Default, Disabled | VARIANT | Да | **Нет** | Нет | In Progress |
| Toast | 24:230 | Component | 3 | Type | Info, Success, Warning | VARIANT | Да | fills | Нет | Needs Refactoring — Error вне set |
| Chip | 24:239 | Primitive | 3 | Tone | Neutral, Success, Warning | VARIANT | Да | fills | Нет | Duplicate — пересечение с Badge |
| StatusIndicator | 24:252 | Primitive | 1 | Type=Info | Info only | VARIANT | Да | fills | Нет | Needs Refactoring |
| TextField | 24:269 | Primitive | 3 | State | Default, Focus, Error | VARIANT | Да | fills | Нет | Duplicate — дубль Input Base |
| PageHeader | 23:174 | Composite | 2 | Type | Default, WithStatus | VARIANT | Да | partial | Нет | In Progress |

#### Documentation / Scenario Artifacts (не продуктовые UI)

| Component | ID | Level | Purpose | Status |
|-----------|-----|-------|---------|--------|
| Flow Node | 6:29 | Meta | User Flow диаграммы | Candidate — вынести из product library |
| CJM Cell | 6:36 | Meta | CJM-артефакты | Candidate — вынести из product library |

---

### Standalone Components — полный реестр

| Component | ID | Level | Purpose | Properties | Auto Layout | Variables | Nested | Status |
|-----------|-----|-------|---------|------------|-------------|-----------|--------|--------|
| Divider | 6:11 | Primitive | Разделитель | — | Нет | Нет | Нет | Ready |
| Note Bullet | 6:12 | Meta | Примечания в спецификациях | TEXT | Да | fills | Нет | Candidate |
| Section Header | 6:2 | Meta | Заголовки секций docs | TEXT ×2 | Да | fills | Нет | Candidate |
| Tag | 6:6 | Primitive | Метка | TEXT | Да | fills | Нет | Duplicate — vs Badge/Chip |
| Field Row | 6:8 | Meta | Строка поля в спецификации | TEXT ×2 | Да | fills | Нет | Candidate |
| Screen Card | 6:15 | Meta | Карточка экрана в scenario map | TEXT ×3 | Да | fills, padding | Нет | Candidate |
| CJM Step Header | 6:37 | Meta | Заголовок шага CJM | TEXT | Да | fills | Нет | Candidate |
| Section Card | 6:39 | Meta | Контейнер секции docs | — | Да | Нет | Нет | Candidate |
| Search | 24:331 | Component | Поиск | — | Да | fills | Нет | **Duplicate** of Search Field |
| Sidebar Navigation/Collapsed | 24:172 | Composite | Свёрнутая навигация | — | Да | fills | Нет | Needs Refactoring |
| Tab/Disabled | 24:187 | Component | Вкладка disabled | — | Да | fills | Нет | Needs Refactoring — вне Tab set |
| Stepper/Completed | 24:196 | Component | Завершённый шаг | — | Да | fills | Нет | Needs Refactoring |
| Stepper/Current | 24:193 | Component | Текущий шаг | — | Да | fills | Нет | Needs Refactoring |
| Toast/Error | 24:227 | Component | Toast ошибки | — | Да | fills | Нет | Needs Refactoring |
| Chip/Danger | — | Primitive | Chip danger tone | — | — | — | — | Needs Refactoring — вне Chip set |
| StatusIndicator/Success | — | Primitive | Индикатор success | — | Да | fills | Нет | Needs Refactoring |
| StatusIndicator/Warning | — | Primitive | Индикатор warning | — | Да | fills | Нет | Needs Refactoring |
| StatusIndicator/Error | — | Primitive | Индикатор error | — | Да | fills | Нет | Needs Refactoring |
| TextField/Disabled | — | Primitive | Disabled input | — | — | — | — | Needs Refactoring |
| ProgressBar | — | Primitive | Линейный прогресс | — | — | — | — | Duplicate of Progress |
| AIAlert | 24:317 | Domain | AI-оповещение | — | Да | fills | **Нет** | Candidate |
| AnswerOption | 24:299 | Domain | Вариант ответа экзамена | — | Да | fills | Нет | Candidate |
| CameraIndicator | 24:325 | Domain | Статус камеры | — | Да | fills | Нет | In Progress |
| CameraPreview | 24:282 | Domain | Превью камеры Driver | — | Да | fills | Нет | In Progress |
| EmptyState | 24:279 | Component | Пустое состояние | — | Да | fills | Нет | In Progress — нет properties |
| InformationCard | 24:270 | Domain | Информационная карточка | — | Да | fills | Нет | Candidate |
| InstructionCard | 24:293 | Domain | Карточка инструкции | — | Да | fills | Нет | Candidate |
| MicrophoneIndicator | 24:328 | Domain | Статус микрофона | — | Да | fills | Нет | In Progress |
| ParticipantCard | 24:312 | Domain | Карточка участника | — | Да | fills | Нет | Candidate |
| PhotoComparison | 24:285 | Domain | Сравнение фото (Identity) | — | Да | fills | Нет | Candidate |
| QuestionCard | 24:296 | Domain | Карточка вопроса | — | Да | fills | Нет | Candidate |
| RecordingIndicator | 24:322 | Domain | Индикатор записи | — | Да | fills | Нет | In Progress |
| ResultCard | 24:276 | Domain | Карточка результата | — | Да | fills | Нет | Candidate |
| StatusCard | 24:273 | Domain | Карточка статуса | — | Да | fills | Нет | Candidate — vs Context Card |
| Timer | 24:306 | Domain | Таймер экзамена | — | Да | fills | Нет | Candidate |
| VerificationStatus | 24:290 | Domain | Статус верификации | — | Да | fills | Нет | Candidate |
| VideoTile | 24:309 | Domain | Плитка видео Proctor | — | Да | fills | Нет | In Progress |
| ViolationBadge | 24:320 | Domain | Бейдж нарушения | — | Да | fills | Нет | Candidate |

---

## Component Classification

### Foundations

- **Colors** — 76 semantic + primitive color variables (Light only)
- **Spacing** — 14-step scale (2xs–8xl)
- **Radius** — 7 values (None–Pill)
- **Borders** — 3 stroke weights
- **Typography** — 16 variables (sizes, weights, families); text styles **не созданы**
- **Motion** — 3 duration tokens; не привязаны к компонентам
- **Icons** — страница существует, интеграция через INSTANCE_SWAP **отсутствует**
- **Grid** — страница пуста
- **Effects/Elevation** — страница Shadows без effect styles

### Primitives

| Есть в Figma | Ожидается docs | Статус |
|--------------|----------------|--------|
| Button | Primary Action, Secondary Action | Ready (нужен refactor) |
| Button Style=Icon | Icon Button | Needs Refactoring |
| Badge | Status Badge | Duplicate naming |
| Checkbox | Checkbox | Ready |
| Radio | Radio | Ready |
| Switch | Switch | Ready |
| Input Base, TextField | Input | Duplicate |
| Divider | Divider | Ready |
| Chip, Tag | Filter Chips | Partial / Duplicate |
| Loader | Loading Indicator | Ready |
| Progress, ProgressBar | Progress | Duplicate |
| StatusIndicator | Status Badge | Incomplete set |

### Components

| Есть | Ожидается | Статус |
|------|-----------|--------|
| Search Field, Search | Search Field | Duplicate |
| Select | Select | In Progress |
| Tab | Tabs | Needs Refactoring |
| Breadcrumb | Breadcrumbs | In Progress |
| Pagination | Pagination | In Progress |
| Alert | Alert Banner | Ready |
| Toast | Notification | Partial |
| Dialog | Confirmation | In Progress |
| EmptyState | Empty State | In Progress |
| Card | Card | Ready |
| — | Accordion | **Missing** |
| — | Tooltip | **Missing** |
| — | Dropdown | **Missing** |
| — | Table | **Missing** |
| — | Timeline | **Missing** |

### Composite Components

| Есть | Ожидается | Статус |
|------|-----------|--------|
| App Shell, Page, Content Container | Workspace structure | In Progress |
| Top Bar | Workspace Header (partial) | Needs Refactoring |
| Sidebar, Sidebar/Navigation | Navigation Sidebar | Duplicate |
| Bottom Action Bar | Action Bar | Partial |
| PageHeader | Workspace Header (partial) | In Progress |
| Section | Context Panel (partial) | Overlap with Card |
| — | Filter Panel | **Missing** |
| — | Bulk Action Bar | **Missing** |
| — | Status Panel | **Missing** |
| — | Timeline Panel | **Missing** |
| — | Alert Feed | **Missing** |
| — | Footer | **Missing** |

### Domain Components

| Есть | Ожидается | Статус |
|------|-----------|--------|
| AIAlert | Alert Card (partial) | Candidate |
| ParticipantCard | Observation Card (partial) | Candidate |
| VideoTile, CameraPreview | Video Player | In Progress |
| PhotoComparison | Identity Verification Block | Candidate |
| ViolationBadge | — | Candidate |
| Timer | — | Candidate |
| QuestionCard, AnswerOption | — | Candidate (Driver exam) |
| InformationCard, InstructionCard, ResultCard, StatusCard | Context Card, Recommendation Card | Candidate — naming mismatch |
| CameraIndicator, MicrophoneIndicator, RecordingIndicator | Monitoring indicators | In Progress |
| VerificationStatus | Identity Verification Block | Candidate |
| — | Decision Panel | **Missing** |
| — | Attention Queue | **Missing** |
| — | Attempt Card | **Missing** |
| — | Review Workspace | **Missing** |
| — | Provider Session Card | **Missing** |
| — | Timeline Event | **Missing** |
| — | Recommendation Card | **Missing** |
| — | Queue Item | **Missing** |
| — | KPI Card | **Missing** |
| — | Widget | **Missing** |

### Meta / Documentation (не должны быть в product library)

Flow Node, CJM Cell, CJM Step Header, Screen Card, Section Card, Section Header, Field Row, Note Bullet

---

## Coverage

### Existing Components

**Layout (8):** App Shell, Page, Content Container, Section, Top Bar, Sidebar, Bottom Action Bar, Scroll Area

**Primitives (15 sets + 4 standalone):** Button, Badge, Checkbox, Radio, Switch, Input Base, Loader, Progress, Chip, StatusIndicator, TextField, Divider, Tag, ProgressBar

**Components (14 sets + 6 standalone):** Card, Select, Search Field, Dialog, Alert, Tab, Stepper, Breadcrumb, Pagination, Toast, EmptyState, Search

**Composite (3):** PageHeader, Sidebar/Navigation/Open, Sidebar Navigation/Collapsed

**Domain (18 standalone):** AIAlert, AnswerOption, CameraIndicator, CameraPreview, EmptyState, InformationCard, InstructionCard, MicrophoneIndicator, ParticipantCard, PhotoComparison, QuestionCard, RecordingIndicator, ResultCard, StatusCard, Timer, VerificationStatus, VideoTile, ViolationBadge

**Meta (8):** Flow Node, CJM Cell, CJM Step Header, Screen Card, Section Card, Section Header, Field Row, Note Bullet

### Missing Components

По `design-system/components/COMPONENT_LIBRARY.md` (P0–P2):

**P0 — критические:**
- Status Badge (именование; есть Badge/StatusIndicator, но не унифицировано)
- Action Bar (именование; есть Bottom Action Bar)
- Queue Item
- Decision Actions
- Timeline Item
- Filter Chips (именование; есть Chip)
- Comment Field

**P1 — ежедневная работа:**
- Video Player (есть VideoTile/CameraPreview — неполно)
- Recommendation Card
- Context Card (есть StatusCard/InformationCard — naming gap)
- KPI Card
- Widget

**P2:**
- Export Panel
- Wizard Navigation
- Integration Status
- Maintenance Banner

**Дополнительно по Pattern Library:**
- Attention Queue
- Decision Panel
- Bulk Action Bar
- Quick Actions
- Filter Panel
- Sort Control / Sort Dropdown
- Saved Filter
- Results Counter
- Evidence Link / Evidence Viewer
- Decision History
- Playback Controls, Timeline Markers, Event Marker
- Chart
- Notification Center
- Side Panel, Footer, Context Panel (именование)

### Duplicate Components

| Проблема | Экземпляры | Рекомендация (audit only) |
|----------|------------|---------------------------|
| Search input | `Search Field` (set), `Search` (standalone), `Input Base` | Объединить в один Input family |
| Text input | `Input Base`, `TextField`, `TextField/Disabled` | Один component set с полным State axis |
| Status display | `Badge`, `Chip`, `Tag`, `StatusIndicator` + standalones | Единый Status Badge по docs |
| Progress | `Progress` (set), `ProgressBar` (standalone) | Один компонент |
| Sidebar | `Sidebar` (set), `Sidebar/Navigation/Open`, `Sidebar Navigation/Collapsed` | Один Navigation Sidebar |
| Card containers | `Card`, `Section`, domain *Card components | Card как primitive; domain — композиция |
| Layout header | `Top Bar`, `PageHeader` | Workspace Header по архитектуре |
| Toast types | `Toast` (set), `Toast/Error` (standalone) | Включить Error в variant set |
| Stepper states | `Stepper`, `Stepper/Current`, `Stepper/Completed` | Единый variant set |
| Tab states | `Tab`, `Tab/Disabled` | Единый variant set |
| Icon button | `Button Style=Icon` (внутри Button) | Отдельный Icon Button primitive |

### Candidate Components

Компоненты, существующие в Figma, но не формализованные в `COMPONENT_LIBRARY.md`:

- AIAlert, ViolationBadge — потенциально для IP-04 Attention Queue
- PhotoComparison, VerificationStatus — IP-11 Identity Verification
- QuestionCard, AnswerOption, Timer — Driver exam flow
- CameraIndicator, MicrophoneIndicator, RecordingIndicator — monitoring primitives
- Meta-компоненты (Flow Node, CJM Cell) — полезны для workflow, но не для product UI library

### Architectural Conflicts

| Конфликт | Документация | Figma | Severity |
|----------|--------------|-------|----------|
| Naming: Status Badge | `Status Badge` в COMPONENT_LIBRARY | `Badge`, `StatusIndicator`, `ViolationBadge` | High |
| Naming: Action Bar | `Action Bar` | `Bottom Action Bar` | Medium |
| Naming: Workspace Header | `Workspace Header` | `Top Bar`, `PageHeader` | Medium |
| Component explosion | Button + Icon Button отдельно | 60 variants в одном Button | High |
| Incomplete variant sets | Полные state machines | Stepper, Tab, Toast, StatusIndicator разбиты | High |
| No composition | Domain components из primitives | Все domain — flat frames, nestedInstances = 0 | High |
| Mixed library layers | Product components only | Meta + Product на одной странице | Medium |
| No TEXT properties | Интерактивные labels | Button, Badge, Alert без TEXT props | High |
| No INSTANCE_SWAP | Icons через swap | Ни один компонент не использует icon swap | Medium |
| Token binding | Все свойства → Variables (AGENTS.md) | Layout: 0 bound fills; Pagination: 0 bindings | High |
| Dark mode | Potential multi-mode | Colors: Light only | Medium |
| AI visual separation | Observation ≠ Recommendation ≠ Decision (UX Principle 6) | AIAlert не отделён от Alert | High |
| Filter vs Chip | `Filter Chips` в Search & Filter pattern | `Chip` без filter-specific behavior | Medium |

---

## Visual Language Assessment

Оценка по `design-system/VISUAL_LANGUAGE.md`.

### Product Character

**Соответствует (на уровне tokens и базовых примитивов):**
- **Calm** — нейтральная палитра Neutral/0–1000, сдержанные semantic colors
- **Competent** — структурированная layout library (App Shell, Page, Section)
- **Precise** — чёткая типографическая шкала (Display XL → Caption)
- **Modern** — flat surfaces, минимальные effects на примитивах
- **Predictable** — консистентные variant axes (State, Type, Tone)

**Риски отклонения:**
- **Decorative** — множество domain Card без content properties выглядят как декоративные заглушки
- **Noisy** — 60 Button variants создают визуальную и когнитивную перегрузку в библиотеке
- **Bootstrap-like** — Alert/Toast/Dialog с классическими semantic types (Info/Success/Warning/Error)
- **Material-like** — Chip, Bottom Action Bar, FAB-подобный Icon в Button

### Information First

| Проверка | Результат |
|----------|-----------|
| Декорация конкурирует с информацией? | Domain cards — placeholder text без data properties; информация не приоритетна |
| Можно убрать элементы без потери usability? | Meta-компоненты (Screen Card, CJM) — да, из product library |
| Помогает понять / решить / действовать? | Button, Alert, Search Field — да; domain layer — пока нет (нет properties) |

### Visual Hierarchy

**Правильно используется:**
- Layout и spacing в App Shell, Page, Section
- Typography variables для размеров текста в Button, Card
- Contrast через semantic color tokens

**Неправильно / риск:**
- Card Style=Elevated без effect styles — elevation не формализована
- Множественные accent paths (Badge, Chip, StatusIndicator, ViolationBadge) — риск multiple accent colors
- Domain cards одинаковой визуальной массы — нет иерархии Observation vs Decision

### Consistency Locks

| Lock | Compliance | Issues |
|------|------------|--------|
| Typography Lock | Partial | Variables есть; text styles не созданы; компоненты не ссылаются на styles |
| Color Lock | Partial | Semantic colors определены; Exam/AI/Monitoring — хорошо; `ALL_SCOPES` на многих vars |
| Radius Lock | Good | 7-step scale; Checkbox/Radio привязаны |
| Spacing Lock | Poor | Variables есть; layout components — hardcoded gap/padding |
| Motion Lock | None | Motion tokens не используются в компонентах |

### Anti-Patterns Detected

| Anti-Pattern | Evidence |
|--------------|----------|
| Card inside Card | Section Style=Elevated + Card Style=Elevated — потенциал при композиции |
| Excessive borders | Card Style=Bordered + Section Style=Default |
| Multiple accent colors | Badge + Chip + StatusIndicator + ViolationBadge + Exam colors |
| Duplicate implementations | Search, inputs, status, progress, sidebar |
| Artificial complexity | Button 60 variants |
| No slots | Dialog, Card, Alert — нет content slots |
| Dashboard decoration | Meta Screen Card, CJM components в product library |

---

## Platform Strategy Validation

### Driver Components (Mobile First, Touch First)

| Requirement | Status | Notes |
|-------------|--------|-------|
| Mobile First | Partial | App Shell Viewport=Mobile; Bottom Action Bar |
| Touch targets | Unknown | Button Large exists; min 44px не верифицирован |
| Portrait First | Partial | Mobile viewport в App Shell |
| Large interactive areas | Partial | Bottom Action Bar Sticky |
| One-handed usage | Candidate | Bottom Action Bar — правильное направление |
| High contrast | Good | Semantic colors defined |
| Reduced cognitive load | At risk | Множество card types без differentiation |

**Driver-specific в Figma:** CameraPreview, VerificationStatus, PhotoComparison, QuestionCard, AnswerOption, Timer — есть как candidates.

### Proctor Components (Desktop First, Keyboard, Density)

| Requirement | Status | Notes |
|-------------|--------|-------|
| Desktop First | Partial | App Shell Viewport=Desktop, Sidebar |
| Keyboard efficiency | Missing | Нет focus ring system; focus только в input states |
| High information density | Missing | Нет Table, Queue Item, Timeline |
| Fast scanning | Missing | Нет Attention Queue, KPI Card |
| Multi-panel workflows | Partial | Page Variant=With Sidebar; нет Side Panel component |

**Proctor-specific в Figma:** VideoTile, ParticipantCard, AIAlert — candidates без полной спецификации.

### Administrator Components (Analytical, Configuration)

| Requirement | Status | Notes |
|-------------|--------|-------|
| Information density | Missing | Нет Table, Chart, KPI Card |
| Analytical workflows | Missing | Нет Analytics Dashboard components |
| Configuration workflows | Missing | Нет Wizard Navigation, Configuration forms |
| Long-session usability | Unknown | Нет данных о focus/contrast для extended use |

**Administrator pages (`12 Proctor`, `13 Administrator`)** — пусты; компоненты не вынесены на role-specific pages.

---

## Architecture Compliance

### Interaction Principles (`architecture/UX_ARCHITECTURE_PRINCIPLES.md`)

| Principle | Compliance | Gap |
|-----------|------------|-----|
| 6. AI рекомендует, человек утверждает | **Violated** | AIAlert не отделён от Alert; нет Recommendation Card / Decision Actions |
| 7. Управление вниманием | **Partial** | Нет Attention Queue; StatusIndicator неполный |
| 10. Массовые действия | **Missing** | Нет Bulk Action Bar |
| 11. Решение в контексте | **Missing** | Нет Decision Panel |
| 12. Одна Entity — одна модель | **Violated** | 5+ типов Card для status/context |
| 13. Постепенное раскрытие | **Partial** | Accordion отсутствует; Expand Control отсутствует |

### Pattern Architecture

- **Top-down design violated:** domain cards созданы без Pattern → Component mapping
- **Component Pattern layer missing:** нет композиций Decision Panel, Attempt Flow, Search & Filter
- **Work Unit components missing:** Queue Item, Timeline Item, Context Card

### Pattern Library Mapping

| Pattern | Required Components | Figma Coverage |
|---------|--------------------|--------------------|
| IP-02 Search & Filter | Search Field, Filter Chips, Filter Panel, Sort | 25% (Search Field only) |
| IP-04 Attention Queue | Prioritized queue, severity, actions | 0% |
| IP-05 Review Required Flow | Attempt List, counters, navigation | 0% |
| IP-06 In-Context Decision | Recommendation Card, Decision Actions, Comment | 0% |
| IP-08 Video Evidence Review | Video Player, Playback Controls, Markers | 15% (VideoTile only) |
| IP-09 Timeline | Timeline Item, Timestamp, Event Badge, Actor | 0% |
| MP-02 Operations Center | KPI Card, Widget, Attention Queue | 0% |

### Design Tokens

| Rule (AGENTS.md) | Status |
|------------------|--------|
| Все свойства → Variables | **Violated** на layout layer |
| Не использовать локальные значения | Partial — primitives OK, layout NO |
| codeSyntax | **Not set** |
| Explicit scopes | **Violated** — ALL_FILLS на Brand/Semantic |

---

## Architectural Violations

### Critical

1. **Отсутствие P0-компонентов** — система не может быть собрана из библиотеки для Attempt Flow, Decision Panel, Timeline.
2. **Нарушение Principle 6** — нет визуального разделения Observation / Recommendation / Decision.
3. **Нет композиции** — domain components не собираются из primitives (0 nested instances).
4. **Variant sets не завершены** — Stepper, Tab, Toast, StatusIndicator, Chip (нет Danger в set).

### Major

5. **Дублирование** — 6+ пар дублирующих компонентов.
6. **Button variant explosion** — 60 variants вместо архитектурного разделения.
7. **Layout без token binding** — App Shell, Sidebar, Top Bar, Bottom Action Bar.
8. **Meta + Product mix** — 8 documentation components на странице Components.

### Minor

9. **Naming mismatch** с COMPONENT_LIBRARY.md (15+ расхождений).
10. **Нет text/effect styles** при наличии typography variables.
11. **Нет Dark mode** в color collection.
12. **Role pages пусты** — нет platform-specific organization.

---

## Components Requiring Refactoring

### Priority 0 — Critical

| Component | Issue | Action (future) |
|-----------|-------|-----------------|
| Button | 60 variants; Icon as Style | Split Button + Icon Button; reduce State to interactive props |
| StatusIndicator + Badge + Chip | 4 status implementations | Unify to Status Badge per docs |
| Stepper, Tab, Toast | Incomplete variant sets | Merge standalone into sets |
| Input Base + TextField + Search | 3 input implementations | Single Input family |
| Sidebar + Sidebar/Navigation | Duplicate navigation | Single Navigation Sidebar |
| Layout components | No variable bindings | Bind fills, spacing, radius to tokens |
| Domain *Card components | Flat, no properties, no composition | Rebuild from Card + Badge + primitives |

### Priority 1 — Important

| Component | Issue |
|-----------|-------|
| PageHeader + Top Bar | Naming / responsibility overlap |
| Card + Section | Functional overlap |
| Dialog | No action slots, no TEXT properties |
| EmptyState | No illustration/icon swap, no TEXT props |
| Pagination | No variable bindings |
| Alert / AIAlert | Need semantic separation for AI vs system |

### Priority 2 — Enhancement

| Component | Issue |
|-----------|-------|
| Chip | Missing Danger in set; overlap with Filter Chips |
| Breadcrumb | Minimal — no truncation/overflow |
| Progress | Missing indeterminate state |
| Loader | No integration with Progress pattern |
| Meta components | Should move to separate Documentation page |

---

## Quick Wins

Малые изменения с высоким архитектурным impact (для следующей итерации):

1. **Объединить Toast/Error в Toast set** — добавить `Type=Error` variant; удалить standalone.
2. **Объединить Stepper/Current, Stepper/Completed в Stepper set** — полная State axis: Upcoming, Current, Completed.
3. **Объединить Tab/Disabled в Tab set** — State: Default, Active, Disabled.
4. **Объединить StatusIndicator/* в StatusIndicator set** — Type: Info, Success, Warning, Error.
5. **Удалить Search standalone** — использовать Search Field.
6. **Удалить ProgressBar** — использовать Progress set.
7. **Добавить TEXT property на Button** — label как component property.
8. **Задать codeSyntax на color/spacing variables** — включить Dev Mode round-trip.
9. **Сузить scopes** — заменить ALL_FILLS на FRAME_FILL / TEXT_FILL / SHAPE_FILL.
10. **Вынести meta-компоненты** на отдельную страницу `Utilities` / `Documentation`.

---

## Recommended Refactoring Roadmap

### P0 — Critical Fixes

1. Зафиксировать **canonical naming map** Figma ↔ COMPONENT_LIBRARY.md.
2. Завершить **все неполные variant sets** (Stepper, Tab, Toast, StatusIndicator, Chip).
3. **Разделить Button** на Button + Icon Button; сократить matrix до ~12–16 variants + interactive states.
4. **Унифицировать Input family** — один component set (Default, Focus, Error, Disabled) + Search/Select как специализации.
5. **Создать P0 missing components:** Queue Item, Timeline Item, Decision Actions, Filter Chips (или переименовать Chip), Status Badge (unified).
6. **Привязать layout components к variables** — App Shell, Sidebar, Top Bar, Bottom Action Bar.
7. **Разделить AIAlert и Alert** — визуально и семантически (UX Principle 6).

### P1 — Important Improvements

1. Создать **composite patterns:** Decision Panel, Filter Panel, Bulk Action Bar, Attention Queue.
2. Создать **P1 components:** Video Player, Recommendation Card, Context Card, KPI Card, Widget.
3. Добавить **TEXT и BOOLEAN properties** на все interactive components.
4. Создать **text styles** из Typography variables.
5. Создать **effect styles** для elevation (Card Elevated, Section Elevated).
6. Добавить **INSTANCE_SWAP** для icons в Button, Alert, Toast.
7. Реорганизовать страницу Components: **Primitives | Components | Composites | Domain | Utilities**.

### P2 — Long-term Enhancements

1. Добавить **Dark mode** в Colors collection.
2. Создать **role-specific pages** (Driver, Proctor, Administrator) с composed examples.
3. Добавить Table, Chart, Accordion, Tooltip, Dropdown.
4. Создать **Wizard Navigation**, Export Panel, Integration Status.
5. **Code Connect mappings** для всех P0/P1 components.
6. Accessibility audit: touch targets (Driver), focus visibility (Proctor), contrast ratios.
7. Удалить или изолировать зависимость от community kits (Material 3, SDS) — available but not added.

---

## Appendix

### Figma File Structure

```
00 Cover
01 Foundation
02 Color Tokens
03 Typography
04 Grid & Layout
05 Radius
06 Shadows
07 Icons
08 Design Principles
09 Components          ← Component Library (71 nodes)
10 Scenario and Patterns
11 Driver              (empty)
12 Proctor             (empty)
13 Administrator       (empty)
14 AI                  (empty)
15 Assets
16 Interaction Map
Context                (documentation frames, 0 components)
```

### Token Summary

| Collection | Variables | Modes |
|------------|-----------|-------|
| Colors | 76 | Light |
| Spacing | 14 | Default |
| Radius | 7 | Default |
| Borders | 3 | Default |
| Typography | 16 | Default |
| Motion | 3 | Default |
| **Total** | **119** | **1 color mode** |

### Audit Methodology

- Программный инвентарь через Figma Plugin API (`use_figma`, read-only)
- Сопоставление с `VISUAL_LANGUAGE.md`, `COMPONENT_LIBRARY.md`, `PATTERN_ARCHITECTURE.md`, `PATTERN_LIBRARY.md`, `UX_ARCHITECTURE_PRINCIPLES.md`
- Token binding audit на выборке из 15 component sets
- Визуальная верификация Button component set (screenshot)
- Изменения в Figma **не производились**

### Document References

| Document | Path | Role in audit |
|----------|------|---------------|
| Visual Language | `design-system/VISUAL_LANGUAGE.md` | Visual assessment |
| Interaction Principles | `architecture/UX_ARCHITECTURE_PRINCIPLES.md` | UX compliance |
| Pattern Architecture | `design-system/patterns/PATTERN_ARCHITECTURE.md` | Level hierarchy |
| Pattern Library | `design-system/patterns/PATTERN_LIBRARY.md` | Coverage mapping |
| Component Library | `design-system/components/COMPONENT_LIBRARY.md` | Expected inventory |
| Design System Rules | `design-system/AGENTS.md` | Token discipline |

---

*Этот документ фиксирует текущее состояние библиотеки. Реализация и рефакторинг — в отдельных итерациях после review.*
