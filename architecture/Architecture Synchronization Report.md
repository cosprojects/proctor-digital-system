# Architecture Synchronization Report

**Дата:** 14 июля 2026  
**Тип:** синхронизация архитектурных документов по решениям stakeholder meeting.

**Источники истины (обновлены в этом проходе):**

- `docs/glossary/GLOSSARY.md`
- `docs/architecture/DOMAIN_ENTITIES.md`
- `docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md`
- `docs/architecture/PROCTOR_PROVIDER_ADAPTER.md`

**Не изменялись:** сценарии в `scenarios/` (отдельный проход).

---

## 1. Изменённые документы

| Документ | Статус |
|----------|--------|
| `docs/glossary/GLOSSARY.md` | Изменён |
| `docs/architecture/DOMAIN_ENTITIES.md` | Изменён |
| `docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md` | Изменён |
| `docs/architecture/PROCTOR_PROVIDER_ADAPTER.md` | Изменён |
| `docs/design principles/Operational State Design.md` | Изменён (Degradation Mode) |
| `architecture/Architecture Synchronization Report.md` | Обновлён (этот отчёт) |
| `architecture/Architecture Update Report.md` | Дополнен notice о superseded boundaries |

**Итого изменено:** 7 файлов архитектурной/интеграционной документации.

---

## 2. Внесённые изменения

### 2.1 Внешние системы

| Система | Закреплённая ответственность |
|---------|------------------------------|
| **КИС АРТ** | Identity пользователя, персональные данные водителя, enrollment. Не управляет Exam, прокторингом, AI, видео, Decision. |
| **SUDIR** | Только Authentication. |
| **LMS (СДО)** | Exam, вопросы, ответы, процесс тестирования, exam score. Исполняет экзаменацию. |
| **Provider** | Технический канал видеоконференции и первичные AI-события. Не владеет Recording. |
| **Наша система** | Roles, scheduling, Identity Verification, Recording, AI processing, Review, analytics, audit, Decision. Orchestration вокруг LMS. |

Authorization и роли — только наша система.

Наша система не изменяет и не прерывает процесс экзаменации в LMS.

### 2.2 Exam ownership

- Было: владелец Exam — Департамент транспорта.
- Стало: владелец Exam — **LMS (СДО)**.
- Департамент транспорта сохранён как заказчик/регуляторный контекст, не как Source of Truth для Exam / Recording / Decision.

### 2.3 Video / Recording

- Было: видеозаписи у Департамента / предоставляются Provider.
- Стало: наша платформа **записывает, хранит и обрабатывает** видео.
- Добавлена сущность **Recording** (владелец — наша система).
- Provider Session остаётся техническим каналом Sync и не владеет Recording.

### 2.4 Authentication / Authorization

- Authentication: **SUDIR**.
- Authorization: **наша система**.
- Роли и права управляются только внутри нашей системы.

### 2.5 AI Pipeline

Закреплён конвейер:

```text
Observation → Recommendation → Review → Decision → Publish
```

- AI предоставляет только Recommendation.
- Decision: Proctor или System (только где явно разрешено бизнес-правилами).
- AI не принимает окончательное решение.

### 2.6 Identity Verification

- Ownership и исполнение: **наша система**.
- Данные личности: КИС АРТ.
- Sync: Provider может быть техническим каналом видео; ответственность за Identity Verification у нашей системы.

### 2.7 Appeal

- Appeal **вне MVP**.
- Текущий процесс: **Retake only**.

### 2.8 Manual Proctor Assignment

- Administrator может вручную переназначать Proctor на Exam Session.
- Причины: болезнь, перегрузка, отсутствие.

### 2.9 Degradation Mode

Если AI недоступен:

- Recording продолжается;
- экзамены в LMS продолжаются;
- ручной Review продолжается;
- Administrator может снизить ёмкость сессий.

В Lifecycle добавлен переход `Completed → Review Required` для Degradation Mode.

---

## 3. Устранённые противоречия

| # | Было | Стало |
|---|------|-------|
| 1 | Exam / video у Департамента транспорта | Exam → LMS; Recording → наша система |
| 2 | Provider как владелец/источник записи | Provider не владеет Recording |
| 3 | Sync Identity Verification = Provider | Identity Verification = наша система |
| 4 | Decision может приписываться AI (Auto Approve) | Decision = Proctor / System |
| 5 | Неявные границы auth | SUDIR = Authentication; наша система = Authorization |
| 6 | Не описан degradation при недоступности AI | Degradation Mode зафиксирован |
| 7 | Appeal не зафиксирован в архитектуре | Outside MVP; Retake only |

---

## 4. Сознательно НЕ изменялось

- Структура и имена файлов.
- Сценарии в `scenarios/` (отдельный проход после согласования несоответствий).
- UX / CJM / user-flows / design-system components.
- Переименование Normalized Events в Adapter (`ViolationConfirmed` и др.) — технический контракт адаптера; терминологическая чистка — отдельный TODO.

---

## 5. Оставшиеся TODO

| TODO | Комментарий |
|------|-------------|
| Синхронизация сценариев с новой Ownership Matrix | `scenarios/` — LMS, Recording, SUDIR, Degradation, Retake |
| Async-контур без Provider AI | Источник Events/Observations для Async |
| Exam Schedule как модель | Не Domain Entity; сценарии Proctor |
| Chat / Session Report | Не Domain Entity |
| Source Exam Catalog (SA-004) | Канал доставки каталога Exam из LMS |
| Provider UI граница | Provider Session vs наш UI |
| Normalized Events naming | `ViolationConfirmed` vs канон GLOSSARY |
| Auto Decision правила | Какие бизнес-правила разрешают System Decision |
| Appeal / Retake UX | Вне MVP; при появлении — отдельный сценарий |

---

## 6. Итог

Архитектурные документы приведены к единым границам систем после stakeholder meeting.

**Ключевой результат:** единые владельцы Exam (LMS), Recording (наша система), Authentication (SUDIR), Authorization (наша система); AI = Recommendation only; Degradation Mode и Retake зафиксированы.

**Следующий шаг:** синхронизация `scenarios/` с обновлёнными архитектурными документами.
