# CONTEXT_BOOK

**Дата снимка:** 14 июля 2026  
**Назначение:** актуальный снимок состояния проекта после синхронизации архитектуры и сценариев по решениям stakeholder meeting.

Это не архитектурный документ и не спецификация. Это точка входа: за несколько минут понять, где находится проект и куда смотреть дальше.

> Примечание: снимок датирован 14.07.2026. После этой даты проектная модель переведена на **Asynchronous Proctoring** (единый пользовательский сценарий Driver без выбора Sync/Async; realtime Proctor workflow и AI-002 Live Monitoring исключены из актуальной модели). Актуальное состояние — в сценариях `scenarios/driver`, `scenarios/proctor`, `scenarios/ai`, `scenarios/administrator` и архитектурных документах.

---

## 1. Project Overview

Цифровая платформа прокторинга для экзаменации водителей (контекст Департамента транспорта / КИС АРТ).

Платформа оркестрирует прокторинг вокруг экзаменации в LMS: расписание, верификация личности, запись и хранение видео, AI-рекомендации, Review, Decision, аналитика и audit.

Модель проверки: **Asynchronous Proctoring**. Driver проходит аттестацию в едином сценарии без выбора Sync/Async; Attempt проходит единый lifecycle; после завершения Attempt выполняется AI Analysis, при необходимости Attempt получает Review Required, Administrator распределяет Review Required Attempt, Proctor выполняет Review и принимает Decision, Administrator может изменить принятое Decision, результат публикуется.

**Зрелость:** discovery / продуктовый прототип UX+документация. Архитектурные границы и сценарии ролей приведены к единому согласованному состоянию после встречи со стейкхолдерами.

**Бизнес-цель:** целостная, инвестиционно пригодная модель продукта и дизайн-документация без размытия ответственности между внешними системами и нашей платформой.

Правила работы с репозиторием: [`AGENTS.md`](AGENTS.md).

---

## 2. Current Architecture

В простых словах система состоит из четырёх внешних контуров и нашей orchestration-платформы.

| Контур | Ответственность |
|--------|-----------------|
| **КИС АРТ** | Identity пользователя, персональные данные водителя, enrollment. Не управляет Exam, прокторингом, AI, Recording, Decision. |
| **SUDIR** | Только Authentication. |
| **LMS (СДО)** | Exam, вопросы, ответы, процесс тестирования, exam score. Исполняет экзаменацию. Наша система не прерывает и не редактирует экзамен. |
| **Наша система** | Roles и Authorization, scheduling, Identity Verification, Recording, AI (Recommendation), Review, Decision, analytics, audit, reports. Orchestration вокруг LMS. |
| **Provider** | Технический канал и источник данных завершённой proctoring-сессии (первичные события, видеоматериалы). Не владеет Recording. |

**AI pipeline:** Observation → Recommendation → Review → Decision → Publish.  
AI рекомендует; Decision принимает Proctor или System (только по явным бизнес-правилам).

Источник истины по архитектуре:

- [`docs/glossary/GLOSSARY.md`](docs/glossary/GLOSSARY.md)
- [`docs/architecture/DOMAIN_ENTITIES.md`](docs/architecture/DOMAIN_ENTITIES.md)
- [`docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md`](docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md)
- [`docs/architecture/PROCTOR_PROVIDER_ADAPTER.md`](docs/architecture/PROCTOR_PROVIDER_ADAPTER.md)

Отчёт о синхронизации архитектуры: [`architecture/Architecture Synchronization Report.md`](architecture/Architecture Synchronization Report.md).

---

## 3. Current Documentation Status

| Область | Документы | Статус |
|---------|-----------|--------|
| **Architecture** | `architecture/Architecture Synchronization Report.md`, `Architecture Update Report.md` (исторический, частично superseded), `UX_ARCHITECTURE_PRINCIPLES.md` | Границы систем синхронизированы после stakeholder meeting |
| **Domain Model** | `docs/architecture/DOMAIN_ENTITIES.md`, `docs/glossary/GLOSSARY.md` | Актуальны; Ownership Matrix согласована |
| **Lifecycle** | `docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md` | Актуальный Attempt lifecycle + Degradation Mode |
| **Integrations** | `docs/architecture/PROCTOR_PROVIDER_ADAPTER.md` | Адаптер Provider; Recording — у нашей платформы |
| **Scenarios** | `scenarios/driver`, `scenarios/proctor`, `scenarios/ai`, `scenarios/administrator` | Синхронизированы с архитектурой (14.07.2026) |
| **Functional Zones** | `scenarios/administrator/Functional Zones.md` и зоны внутри SC/SA | AZ-001…005 описаны; AZ-006…010 — заглушки |
| **Pattern Library** | `design-system/patterns/PATTERN_LIBRARY.md`, `PATTERN_ARCHITECTURE.md`, `Pattern Inventory Report.md` | Существует; требует обновления после scenario sync |
| **Component Library** | `design-system/components/COMPONENT_LIBRARY.md`, `COMPONENT_ARCHITECTURE.md` | Существует; следует после согласования паттернов |
| **Visual / UX** | `design-system/VISUAL_LANGUAGE.md`, `docs/design principles/Operational State Design.md` | База есть; Operational States включают Degradation |
| **Rules** | `docs/rules/*`, `AGENTS.md` | Рабочие правила репозитория |

**Не является Source of Truth для текущих границ:** устаревшие утверждения в `Architecture Update Report.md` (есть notice о superseded) и исторический контекст в `docs/rules/BASIC_CONTEXT.md` — сверять с GLOSSARY / DOMAIN_ENTITIES.

---

## 4. Major Decisions Accepted

- **LMS владеет Exam** (содержание, тестирование, exam score).
- **Наша система владеет прокторингом** (scheduling, IV, Recording, AI orchestration, Review, Decision, analytics, audit).
- **КИС АРТ** — источник пользовательских данных и enrollment; не управляет экзаменом и прокторингом.
- **SUDIR = Authentication; Authorization = наша система.**
- **AI даёт только Recommendation;** финальный Decision — Proctor или System (по правилам).
- **Recording принадлежит нашей платформе** (запись, хранение, обработка).
- **Provider** — технический канал и источник данных завершённой сессии; не владелец Recording.
- **Attempt** — lifecycle прокторинга вокруг экзаменации LMS, не сам Exam.
- **Appeal вне MVP;** текущий процесс — Retake only.
- **Администратор может вручную переназначать прокторов** (болезнь, отсутствие, перегрузка) с audit.
- **Degradation Mode:** при недоступности AI продолжаются Recording, экзамен в LMS и ручной Review; admin может снизить ёмкость.

Детали — в архитектурных документах и отчёте синхронизации.

---

## 5. Current Workflow

Рабочий процесс проекта:

```text
Discussion
    ↓
Agreement
    ↓
Architecture Update
    ↓
Architecture Synchronization
    ↓
Scenario Synchronization
    ↓
Git Commit
    ↓
Context Book          ← текущий шаг
    ↓
Knowledge Update
    ↓
Pattern Library
    ↓
Component Library
```

Связанный продуктовый pipeline дизайна (из `AGENTS.md`):

```text
Сценарий → CJM → User Flow → Компоненты → Экран
```

После согласования архитектуры сценарии не проектируются «в сторону» от границ систем. Паттерны и компоненты выводятся из синхронизированных сценариев.

---

## 6. Current Project Status

### Completed

- Архитектурная синхронизация по решениям stakeholder meeting
- Синхронизация сценариев Driver / Proctor / AI / Administrator с архитектурой
- Закрепление Ownership Matrix, AI pipeline, Recording, SUDIR/LMS границ

### Current

- **Context Book** (этот документ) — снимок состояния и точка входа

### Next

1. Knowledge Update (распространение CONTEXT_BOOK / актуальных ссылок в рабочие правила при необходимости)
2. Обновление **Pattern Library** и **Pattern Architecture** под синхронизированные сценарии
3. Обновление **Component Library** и **Component Architecture**
4. Закрытие open topics отдельными итерациями (см. §7)

Архитектурные документы на этом этапе **не менять без нового Agreement**.

---

## 7. Open Architectural Topics

Не блокеры текущего этапа; будущие итерации:

| Тема | Комментарий |
|------|-------------|
| Источник данных AI-анализа завершённой Attempt | Какие материалы/события используются для анализа после Completed |
| Правила System Auto Decision | Какие кейсы явно разрешены бизнесом |
| Граница Provider UI | Provider Session vs наш UI / Recording Viewer |
| Синхронизация каталога Exam из LMS | Канал доставки для SA-004 |
| Exam Schedule / Chat | Не Domain Entity; модель UI |
| AZ-006…010 Administrator | Дозаполнение Functional Zones |
| Naming Normalized Events | Напр. `ViolationConfirmed` vs GLOSSARY |

Список также в [`architecture/Architecture Synchronization Report.md`](architecture/Architecture Synchronization Report.md) §5.

---

## 8. Development Principles

- **Architecture first.** Сначала границы и ownership, потом сценарии.
- **Scenarios follow architecture.** Сценарии не invent’ят новые владельцы и потоки.
- **Patterns follow scenarios.** Паттерны описывают повторяемое поведение из согласованных SC/SA.
- **Components follow patterns.** Компоненты не опережают паттерны.
- **No duplicate ownership.** У каждой ответственности один владелец.
- **One source of truth.** GLOSSARY / DOMAIN_ENTITIES / LIFECYCLE / Adapter — канон; отчёты — история проходов.
- **Decisions before implementation.** Сначала Agreement и фиксация в архитектуре, затем sync и дизайн.
- **Не усложнять.** Любое изменение должно делать проект проще (`AGENTS.md`).
- **Не дублировать.** Расширять существующую библиотеку; не создавать параллельные модели.

---

## Quick Start

| Нужно… | Открыть |
|--------|---------|
| Термины | `docs/glossary/GLOSSARY.md` |
| Владельцы сущностей | `docs/architecture/DOMAIN_ENTITIES.md` |
| Статусы Attempt | `docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md` |
| Provider | `docs/architecture/PROCTOR_PROVIDER_ADAPTER.md` |
| Сценарии ролей | `scenarios/` |
| Дизайн-система | `design-system/` |
| Что изменилось 14.07 | `architecture/Architecture Synchronization Report.md` |

**Следующий осмысленный шаг после Context Book:** Knowledge Update → синхронизация Pattern Library со сценариями.
