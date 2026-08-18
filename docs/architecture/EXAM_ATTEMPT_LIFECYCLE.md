# Exam Attempt Lifecycle

## Назначение

Документ определяет единый жизненный цикл Attempt в Proctor Digital System.

Attempt является основной доменной сущностью процесса проведения экзамена.

Все пользовательские сценарии, API, бизнес-логика, интеграции и пользовательские интерфейсы должны использовать только жизненный цикл, определенный в данном документе.

Документ является единым источником истины для:

- Driver Scenarios;
- Proctor Scenarios;
- AI Scenarios;
- Administrator Scenarios;
- Domain Model;
- API;
- Notifications;
- Audit Log.

---

# Архитектурные принципы

## Единый жизненный цикл

Attempt проходит одинаковый жизненный цикл независимо от режима проведения экзамена.

В модели Asynchronous Proctoring переходы между состояниями выполняются без участия проктора.

---

## Единственный владелец состояния

В каждый момент времени Attempt находится только в одном состоянии.

Переход выполняется только после завершения предыдущего состояния.

---

## Разделение состояния и решения

Lifecycle State отвечает на вопрос:

> На каком этапе находится Attempt?

Decision отвечает на вопрос:

> Какое решение принято по Attempt?

Decision не является состоянием жизненного цикла.

---

## Источник истины

LMS (СДО) является владельцем:

- Exam;
- вопросов;
- ответов;
- процесса тестирования;
- exam score.

Наша система управляет жизненным циклом Attempt, Identity Verification, Recording, Review, Decision и orchestration вокруг LMS.

Наша система записывает, хранит и обрабатывает видео.

Наша система не может изменять или прерывать процесс экзаменации в LMS.

---

## AI Pipeline

```text
Observation
    ↓
Recommendation
    ↓
Review
    ↓
Decision
    ↓
Publish
```

AI предоставляет только Recommendation.

Окончательное Decision принимает Proctor или System (только если явно разрешено бизнес-правилами).

---

## Degradation Mode

Если AI недоступен:

- Recording продолжается;
- экзамены в LMS продолжаются;
- ручной Review продолжается;
- Administrator может снизить ёмкость сессий.

Переходы жизненного цикла Attempt не блокируются недоступностью AI; этап AI Processing при Degradation Mode пропускается или откладывается в пользу ручного Review.

---

## Appeal

Appeal вне MVP.

После отрицательного Decision доступен только Retake.

---

# Жизненный цикл

```text
Draft
    ↓
Scheduled
    ↓
Environment Checking
    ↓
Identity Verification
    ↓
Ready
    ↓
In Progress
    ↓
Completed
    ↓
AI Processing
    ↓
Review Required
    ↓
Decision Made
    ↓
Published
    ↓
Archived
```

---

# Состояния

## Draft

### Назначение

Попытка создана, но запись еще не подтверждена.

### Вход

Создание новой Attempt.

### Выход

- Scheduled
- Cancelled

### Ответственный

System

### Используется

Driver

Administrator

---

## Scheduled

### Назначение

Попытка успешно зарегистрирована.

Ожидает времени проведения экзамена.

### Вход

Из Draft.

### Выход

- Environment Checking
- Cancelled
- Expired

### Ответственный

Driver

### Используется

Driver

Administrator

---

## Environment Checking

### Назначение

Проверка оборудования и условий прохождения экзамена.

### Вход

Из Scheduled.

### Выход

- Identity Verification
- Cancelled

### Ответственный

Driver

### Используется

Driver

---

## Identity Verification

### Назначение

Подтверждение личности участника.

### Особенности

Identity Verification выполняет наша система.

Данные личности поступают из КИС АРТ.

Технический канал видео может проходить через Provider; ответственность за Identity Verification остаётся у нашей системы.

### Выход

- Ready
- Cancelled

### Ответственный

System

---

## Ready

### Назначение

Попытка полностью подготовлена.

Экзамен может быть начат в LMS.

### Выход

- In Progress
- Cancelled

### Ответственный

System

---

## In Progress

### Назначение

Экзамен находится в процессе прохождения.

Процесс тестирования исполняется LMS.

Наша система обеспечивает прокторинг, Recording и orchestration и не прерывает экзаменацию в LMS.

### Особенности

Экзамен проходит без участия проктора (Asynchronous Proctoring).

### Выход

- Completed
- Cancelled

### Ответственный

Driver / LMS

---

## Completed

### Назначение

Экзамен завершен.

Ответы больше не принимаются.

Exam score формируется LMS.

### Выход

- AI Processing
- Review Required (Degradation Mode: при недоступности AI)

### Ответственный

System

---

## AI Processing

### Назначение

AI анализирует Attempt.

Формирует:

- Observations;
- Risk Score;
- Recommendations.

AI не принимает Decision.

### Выход

- Review Required
- Decision Made (только если System Auto Decision явно разрешён бизнес-правилами)

### Ответственный

AI

---

## Review Required

### Назначение

Attempt ожидает проверки проктором.

### Выход

- Decision Made

### Ответственный

Proctor

---

## Decision Made

### Назначение

По Attempt принято окончательное решение.

Decision содержит одно из значений:

- Approved;
- Rejected;
- Cancelled;
- Expired.

### Выход

- Published

### Ответственный

Proctor

или

System (только если явно разрешено бизнес-правилами).

AI не является ответственным за Decision.

---

## Published

### Назначение

Результат опубликован.

Decision и связанные данные orchestration переданы потребителям; exam score остаётся во владении LMS.

### Выход

- Archived

### Ответственный

Integration Layer

---

## Archived

### Назначение

Попытка завершена.

Доступна только для просмотра.

### Ответственный

System

---

# Матрица ответственности

| State | Driver | Proctor | AI | Administrator |
|--------|--------|----------|----|---------------|
| Draft | — | — | — | Monitor |
| Scheduled | View | — | — | Monitor |
| Environment Checking | Active | — | Observe | Monitor |
| Identity Verification | Active | — | Observe | Monitor |
| Ready | Wait | — | — | Monitor |
| In Progress | Active | — | Observe | Monitor |
| Completed | Wait | — | Process | Monitor |
| AI Processing | Wait | — | Process | Monitor |
| Review Required | Wait | Review | Recommend | Monitor |
| Decision Made | View | Decide | — | Monitor |
| Published | View | View | — | Analytics |
| Archived | View | View | — | Analytics |

---

# Допустимые переходы

| Из | В |
|----|---|
| Draft | Scheduled |
| Draft | Cancelled |
| Scheduled | Environment Checking |
| Scheduled | Cancelled |
| Scheduled | Expired |
| Environment Checking | Identity Verification |
| Environment Checking | Cancelled |
| Identity Verification | Ready |
| Identity Verification | Cancelled |
| Ready | In Progress |
| Ready | Cancelled |
| In Progress | Completed |
| In Progress | Cancelled |
| Completed | AI Processing |
| Completed | Review Required |
| AI Processing | Review Required |
| AI Processing | Decision Made |
| Review Required | Decision Made |
| Decision Made | Published |
| Published | Archived |

---

# Связь со сценариями

Колонка Proctor отражает asynchronous-модель работы проктора (см. [Proctor Scenarios Map](../../scenarios/proctor/Proctor%20Scenarios%20Map.md)): проктор не участвует в состояниях до Review Required.

Проекция Proctor-процесса на жизненный цикл Attempt:

```text
Completed Attempt
    ↓
AI Processing
    ↓
Review Required
    ↓
распределение Review Required Attempt — административная функция (SA-003); механизм не определён
    ↓
Proctor Review — SC-001…SC-005
    ↓
Decision / Conclusion
    ↓
Decision Made
```

<!-- TODO: BUSINESS RULE REQUIRED — критерии назначения Review (условия распределения Review Required Attempt и перераспределения) не определены. -->

| Lifecycle | Driver | Proctor | AI | Administrator |
|------------|--------|----------|----|---------------|
| Draft | SC-001 | — | — | — |
| Scheduled | — | — | — | SA-002 |
| Environment Checking | SC-003 | — | — | — |
| Identity Verification | SC-004 | — | — | SA-002 |
| Ready | SC-004 | — | — | SA-002 |
| In Progress | SC-005 | — | — | SA-002 |
| Completed | SC-006 | — | AI-001 | SA-002 |
| AI Processing | — | — | AI-001 / AI-003 | SA-001 |
| Review Required | — | SC-001 | AI-004 | SA-001 |
| Decision Made | SC-006 | SC-005 | — | SA-005 |
| Published | SC-006 | SC-008 | — | SA-005 |
| Archived | SC-006 | SC-008 | — | SA-005 |

# Что не описывает данный документ

Данный документ НЕ определяет:

- пользовательские сценарии;
- интерфейсы;
- последовательность экранов;
- доменную модель Review;
- внутренние алгоритмы AI;
- бизнес-правила проведения экзамена.

Документ описывает исключительно жизненный цикл Attempt и допустимые переходы между состояниями.
