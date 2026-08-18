# Pattern Architecture

## Назначение

Документ определяет архитектуру Interaction Pattern Library, связи между паттернами, правила их композиции и принципы построения пользовательских интерфейсов.

Документ является мостом между:

- UX Interaction Principles;
- Pattern Library;
- Component Library;
- Screen Architecture.

Pattern Architecture описывает не сами паттерны, а то, как они образуют единую систему.

---

# Архитектурные уровни

Пользовательский интерфейс строится по пяти уровням.

```text
Business Scenario

↓

Work Unit

↓

Macro Pattern

↓

Interaction Pattern

↓

Component Pattern

↓

UI Components
```

Каждый уровень зависит только от уровня выше.

Компоненты не определяют паттерны.

Паттерны не определяют сценарии.

Направление проектирования всегда сверху вниз.

---

# Work Unit First

Основой любого интерфейса является Work Unit.

Work Unit определяет:

- контекст;
- жизненный цикл;
- доступные действия;
- права пользователя;
- историю;
- связанные сущности.

Все остальные паттерны существуют только вокруг Work Unit.

---

# Macro Pattern

Macro Pattern определяет общую структуру рабочего пространства.

Он отвечает на вопрос:

> Как организована работа пользователя?

Macro Pattern не содержит компонентов.

Он определяет структуру взаимодействия.

Например:

- Operations Center
- Work Unit Workspace
- Review Workspace
- Wizard Flow

Macro Pattern может включать несколько Interaction Pattern.

---

# Interaction Pattern

Interaction Pattern описывает устойчивый способ решения пользовательской задачи.

Он независим от роли пользователя.

Interaction Pattern может использоваться внутри нескольких Macro Pattern.

Например:

- Master → Detail
- Search + Filter
- Timeline
- Attempt Flow
- Decision Panel
- Bulk Actions
- Status Display

Interaction Pattern состоит из Component Pattern.

---

# Component Pattern

Component Pattern описывает повторяющуюся композицию компонентов.

Например:

Decision Panel

↓

Status Badge

Recommendation

Primary Actions

Comment

History

Component Pattern не содержит бизнес-логики.

---

# UI Components

Компоненты являются минимальными строительными блоками интерфейса.

Компоненты:

- не знают о сценариях;
- не знают о ролях;
- работают только с Domain Entity;
- переиспользуются между всеми паттернами.

---

# Иерархия Pattern

```text
Macro Pattern

└── Interaction Pattern

      └── Component Pattern

            └── UI Component
```

Паттерн не может ссылаться на уровень выше.

Допускаются только зависимости вниз.

---

# Композиция Pattern

Каждый Pattern строится одинаково.

```text
Pattern

↓

Purpose

↓

Composition

↓

Dependencies

↓

Interactions

↓

Constraints
```

---

# Composition

Composition определяет,

из каких Pattern состоит текущий Pattern.

Например

```text
Review Workspace

├── Attention Queue

├── Participant Workspace

├── Decision Panel

├── Timeline

└── Video Review
```

Composition всегда направлена сверху вниз.

---

# Dependencies

Dependencies определяют,

без каких Pattern невозможна работа текущего Pattern.

Например

Decision Panel

зависит от

- Status Display
- Recommendation
- Action Group

Timeline

не имеет зависимостей.

---

# Anti Dependencies

Pattern не должен включать несвязанные способы взаимодействия.

Например

Decision Panel

не содержит

- Search
- Analytics
- Navigation

Workspace

не должен открываться как Modal Window.

---

# Reuse First

При проектировании новой функциональности необходимо:

1.

Найти существующий Macro Pattern.

2.

Найти существующий Interaction Pattern.

3.

Найти существующий Component Pattern.

4.

Использовать существующие UI Components.

Создание нового Pattern допускается только после невозможности использования существующего.

---

# Pattern Evolution

Развитие Pattern происходит последовательно.

```text
Candidate

↓

Validated

↓

Core

↓

Deprecated
```

Candidate

Используется экспериментально.

Validated

Используется минимум двумя сценариями.

Core

Используется системой повсеместно.

Deprecated

Не используется в новых сценариях.

---

# Pattern Priority

Все Pattern делятся по степени важности.

Core

Без него невозможна работа системы.

Common

Используется регулярно.

Specialized

Используется отдельными ролями.

Rare

Используется редко.

---

# Pattern Relationships

Каждый Pattern обязан определить:

Parent Pattern

Child Pattern

Composition

Dependencies

Used Components

Used Domain Entities

Used Lifecycle States

Supported Roles

---

# Архитектурные ограничения

Запрещается:

- создавать новый Pattern без анализа существующих;
- создавать Component Pattern без Interaction Pattern;
- создавать Component, не принадлежащий Pattern;
- использовать разные модели взаимодействия для одной Domain Entity.

---

# Переход к Component Library

Component Library строится исключительно на основе Pattern Library.

Ни один компонент не проектируется напрямую из пользовательского сценария.

Последовательность проектирования:

Business Scenario

↓

Pattern

↓

Component Pattern

↓

Component

↓

Screen

↓

Figma

## Pattern Decision Tree

Нужно спроектировать новую функциональность

↓

Есть Work Unit?

↓

Да

↓

Есть подходящий Macro Pattern?

↓

Да

↓

Есть подходящий Interaction Pattern?

↓

Да

↓

Есть Component Pattern?

↓

Да

↓

Используем существующие компоненты.

↓

Нет

↓

Создаем новый Component Pattern.