# Domain Entities

## Назначение

Документ определяет основные доменные сущности Proctor Digital System, их ответственность, владельцев, источники данных и взаимосвязи.

Документ является единым источником истины для:

- Domain Model;
- API;
- Database;
- Integration Layer;
- Scenario Specifications;
- UX Pattern Inventory;
- Component Library.

---

# Архитектурные принципы

## Единый владелец

Каждая сущность имеет одного владельца.

Владелец отвечает за жизненный цикл сущности.

---

## Единый источник истины

Каждая сущность имеет один источник данных.

Дублирование данных не допускается.

---

## Явные связи

Все отношения между сущностями должны быть явно определены.

Не допускаются скрытые зависимости.

---

## Разделение ответственности

Каждая сущность отвечает только за одну бизнес-задачу.

---

## Границы внешних систем

| Система | Ответственность |
|---------|-----------------|
| КИС АРТ | Identity пользователя, персональные данные водителя, enrollment |
| SUDIR | Authentication |
| LMS (СДО) | Exam, вопросы, ответы, процесс тестирования, exam score |
| Provider | Технический канал видеоконференции и первичные AI-события (не владеет Recording) |
| Наша система | Roles, scheduling, Identity Verification, Recording, AI processing, Review, analytics, audit, Decision |

Authorization, роли и права — только наша система.

Наша система — orchestration-платформа вокруг LMS и не может изменять или прерывать процесс экзаменации.

---

# Доменные области

| Domain | Назначение |
|---------|------------|
| Identity | Пользователи и идентификация |
| Session Management | Управление экзаменационными сессиями |
| Attempt Management | Управление попытками прохождения экзамена |
| Review | Проверка результатов |
| AI Orchestration | Анализ событий и рекомендации |
| Resource Management | Управление прокторами |
| Analytics | Аналитика и отчеты |
| Configuration | Конфигурация системы |
| Integration | Внешние сервисы |

---

# Сущности

## Driver

### Назначение

Участник экзамена.

### Владелец

КИС АРТ

### Источник данных

КИС АРТ

### Создается

Во внешней системе.

### Изменяется

Никогда.

Только синхронизируется.

### Используется

- Exam Session
- Attempt
- Identity Verification

---

## Proctor

### Назначение

Пользователь, принимающий решения по результатам экзаменов.

### Владелец

Наша система

### Создается

Administrator

### Изменяется

Administrator

Administrator может вручную переназначить Proctor на Exam Session (болезнь, перегрузка, отсутствие).

### Используется

- Exam Session
- Decision

---

## Administrator

### Назначение

Управляет системой, ресурсами и интеграциями.

### Владелец

Наша система

### Используется

- Configuration
- Resources
- Analytics
- ручное переназначение Proctor

---

## Exam

### Назначение

Экзаменационный материал.

### Владелец

LMS (СДО)

### Источник данных

LMS (СДО)

### Создается

Во внешней системе (LMS).

### Изменяется

Во внешней системе (LMS).

Наша система не изменяет и не прерывает процесс экзаменации.

### Используется

- Exam Session

---

## Exam Session

### Назначение

Экземпляр проведения экзамена.

Объединяет участников, режим проведения и расписание.

### Владелец

Наша система

### Создается

Планировщиком системы.

### Изменяется

Administrator может вручную переназначить Proctor.

### Используется

- Driver
- Attempt
- Proctor

---

## Attempt

### Назначение

Попытка прохождения экзамена.

Центральная сущность системы.

### Владелец

Наша система

### Lifecycle

EXAM_ATTEMPT_LIFECYCLE.md

### Используется

- Observation
- Recommendation
- Decision
- Recording

---

## Observation

### Назначение

Нормализованное наблюдение.

Не является нарушением.

Не является решением.

### Владелец

AI Orchestration

### Создается

- Adapter Layer
- Internal AI

### Используется

Recommendation

---

## Recommendation

### Назначение

Рекомендация AI.

Используется для приоритизации проверки.

Никогда не является окончательным Decision.

### Владелец

AI Orchestration

### Используется

Decision

---

## Decision

### Назначение

Финальное решение по Attempt.

### Владелец

Наша система

### Создается

- Proctor
- System (только если явно разрешено бизнес-правилами)

AI не создаёт Decision.

### Используется

Publish Result

---

## Review Item (deprecated)

Ранее — единица проверки, содержащая одну Attempt.

В актуальной модели не используется как самостоятельная рабочая единица: единица Review — Attempt со статусом Review Required.

---

## Recording

### Назначение

Видеозапись Attempt.

### Владелец

Наша система

### Источник данных

Наша система

Наша платформа записывает, хранит и обрабатывает видео.

Provider не является владельцем Recording.

### Используется

- Attempt
- Review
- AI Processing

---

## Provider Session

### Назначение

Представление провайдерской сессии на уровне Adapter Layer (технический канал во внешнем Provider).

Интеграционная сущность: в asynchronous-модели отражает данные завершённой proctoring-сессии, связанной с Attempt, и служит источником материалов для последующего Review. Владельцем жизненного цикла сущности является Provider; наша система не управляет ею как собственной доменной сущностью.

### Владелец

Provider

### Используется

- Attempt (данные завершённой proctoring-сессии);
- Review (материалы для проверки).

Не владеет Recording.

---

# Связи

```text
Driver
    │
    ├──────────────┐
    │              │
    ▼              ▼
Exam Session ───► Attempt
                     │
                     ├────────► Observation
                     │              │
                     │              ▼
                     │       Recommendation
                     │              │
                     ├────────► Recording
                     │
                     ▼
               Attempt (Review Required) ───► Decision
                     │
                     ▼
                  Proctor
```

---

# Ownership Matrix

| Entity | Owner | Source of Truth |
|---------|-------|-----------------|
| Driver | КИС АРТ | КИС АРТ |
| Proctor | Наша система | Наша система |
| Administrator | Наша система | Наша система |
| Exam | LMS (СДО) | LMS (СДО) |
| Exam Session | Наша система | Наша система |
| Attempt | Наша система | Наша система |
| Observation | AI Orchestration | Adapter Layer |
| Recommendation | AI Orchestration | AI |
| Decision | Наша система | Proctor / System |
| Recording | Наша система | Наша система |
| Provider Session | Provider | Provider |

---

# CRUD Matrix

| Entity | Create | Read | Update | Delete |
|---------|--------|------|--------|--------|
| Driver | КИС АРТ | Все роли | КИС АРТ | — |
| Proctor | Administrator | Administrator | Administrator | Administrator |
| Exam | LMS (СДО) | Все роли | LMS (СДО) | LMS (СДО) |
| Exam Session | System | Все роли | System / Administrator* | System |
| Attempt | System | Driver / Proctor / AI | System | — |
| Observation | AI | AI / Proctor | AI | — |
| Recommendation | AI | AI / Proctor | AI | — |
| Decision | Proctor / System** | Все роли | Proctor (до публикации) / Administrator | — |
| Recording | System | Proctor / AI / Administrator | System | — |

\* В том числе ручное переназначение Proctor.  
\*\* System — только если явно разрешено бизнес-правилами.  
\*\*\* Proctor — только до публикации результата; Administrator может изменять принятое Decision (изменённое Decision — актуальное итоговое решение).

---

# Не являются самостоятельными Entity

Следующие объекты являются частью других сущностей и не моделируются как отдельные доменные Entity:

- Alert
- Risk Score
- Notification
- Timeline Event
- Audit Record
- Status
- Badge
- Widget
- KPI
- Chart
- Filter
- Search Query

---

# Связанные документы

- GLOSSARY.md
- EXAM_ATTEMPT_LIFECYCLE.md
- Driver Scenarios
- Proctor Scenarios
- AI Scenarios
- Administrator Scenarios
