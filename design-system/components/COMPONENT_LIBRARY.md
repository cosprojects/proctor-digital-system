# Component Library

## Назначение

Документ определяет компонентную библиотеку Proctor Digital System.

Компоненты являются минимальными переиспользуемыми элементами пользовательского интерфейса.

Компоненты строятся исключительно на основе Pattern Library.

Документ является единым источником истины для:

- Design System
- Figma Library
- Frontend Development
- UX Patterns
- Screen Templates

---

# Архитектурные принципы

Компоненты не проектируются напрямую из экранов.

Последовательность проектирования:

Business Scenario

↓

Interaction Pattern

↓

Component Pattern

↓

UI Component

↓

Screen

---

# Component Hierarchy

```text
Macro Pattern

↓

Interaction Pattern

↓

Component Pattern

↓

UI Component
```

---

# Pattern → Component Mapping

## Work Unit Workspace

Использует:

- Workspace Header
- Context Card
- Status Badge
- Action Bar
- Primary Workspace
- Side Panel
- Timeline
- Footer

---

## Operations Center

Использует:

- KPI Card
- Widget
- Quick Actions
- Attention Queue
- Status Banner
- Notification Center

---

## Attempt Flow

Использует:

- Attempt List
- Attempt Item
- Filter Bar
- Sort Control
- Bulk Action Bar
- Selection Counter

---

## Decision Panel

Использует:

- Recommendation Card
- Decision Actions
- Comment Field
- Evidence Link
- Decision History

---

## Timeline

Использует:

- Timeline Item
- Timestamp
- Event Badge
- Actor
- Expand Control

---

## Search & Filter

Использует:

- Search Field
- Filter Chips
- Filter Panel
- Sort Dropdown
- Saved Filter
- Results Counter

---

## Video Review

Использует:

- Video Player
- Playback Controls
- Timeline Markers
- Event Marker
- Playback Speed
- Evidence Marker

---

## Analytics Dashboard

Использует:

- KPI Card
- Chart
- Widget
- Filter Bar
- Export Action

---

# Component Categories

## Layout

- Workspace Header
- Side Panel
- Action Bar
- Footer
- Context Panel

---

## Navigation

- Breadcrumbs
- Tabs
- Stepper
- Wizard Navigation

---

## Data Display

- Status Badge
- KPI Card
- Timeline Item
- Queue Item
- Recommendation Card
- Context Card

---

## Actions

- Primary Action
- Secondary Action
- Bulk Action Bar
- Decision Actions
- Quick Actions

---

## Input

- Search Field
- Comment Field
- Filter
- Sort

---

## Feedback

- Alert Banner
- Notification
- Empty State
- Error State
- Loading Indicator

---

## Media

- Video Player
- Evidence Viewer
- Image Preview

---

# Component Specification Template

Каждый компонент далее описывается по единому шаблону.

## Component Name

### Назначение

### Используется в Pattern

### Использует Domain Entity

### Lifecycle State

### Состав

### Состояния

### Варианты

### События

### Ограничения

### Accessibility

### Правила переиспользования

---

# Приоритет проектирования

## P0

Компоненты, без которых невозможна работа системы.

Например:

- Status Badge
- Action Bar
- Queue Item
- Decision Actions
- Timeline Item
- Search Field
- Filter Chips

---

## P1

Компоненты ежедневной работы.

- Video Player
- Recommendation Card
- Context Card
- KPI Card
- Widget

---

## P2

Редко используемые компоненты.

- Export Panel
- Wizard Navigation
- Integration Status
- Maintenance Banner

---

# Правила создания новых компонентов

Новый компонент допускается только если:

- его невозможно собрать из существующих;
- он используется минимум двумя Pattern;
- он соответствует UX Interaction Principles;
- он согласован с Pattern Architecture.

Во всех остальных случаях используется существующий компонент.

# Platform Strategy

Компонентная библиотека поддерживает две платформенные модели.

## Driver Components

Platform Strategy

Mobile First

Все компоненты Driver проектируются сначала для мобильных устройств.

Desktop является адаптацией мобильной версии.

---

## Proctor Components

Platform Strategy

Desktop First

Компоненты проектируются для широких рабочих пространств.

---

## Administrator Components

Platform Strategy

Desktop First

Приоритет отдается аналитическим панелям, большим таблицам и многоколоночным интерфейсам.