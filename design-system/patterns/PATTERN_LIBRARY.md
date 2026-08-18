# Pattern Library

## Назначение

Документ фиксирует Interaction Patterns, выявленные при анализе существующей проектной документации Proctor Digital System.

Pattern Library является единым реестром устойчивых способов взаимодействия пользователя с системой. Она служит основой для проектирования Functional Elements, Domain Components и Component Library — без проектирования экранов и без изменения архитектуры.

Источники анализа:

- `architecture/UX_ARCHITECTURE_PRINCIPLES.md.md` (UX Interaction Principles)
- `docs/glossary/GLOSSARY.md`
- `docs/architecture/DOMAIN_ENTITIES.md`
- `docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md`
- Driver / Proctor / AI / Administrator Scenarios
- Functional Zones (`scenarios/administrator/Functional Zones.md`)
- Scenario Specifications и Scenario Maps
- `docs/design principles/Operational State Design.md`
- `docs/rules/INTERFACE_ARCHITECTURE_GUIDELINES.md`

---

## Методология

### Критерии включения

Паттерн включён в библиотеку, если в документации он:

1. встречается минимум в двух независимых контекстах;
2. решает одну и ту же пользовательскую задачу;
3. имеет одинаковую модель поведения;
4. потенциально переиспользуется между ролями или сценариями.

### Критерии исключения

Не считаются паттернами:

- отдельные кнопки, поля, бейджи;
- отдельные UI-компоненты;
- отдельные экраны и сценарии;
- отдельные Domain Entity;
- Functional Zones как таковые (зоны — источник требований, паттерн — модель поведения поверх зон).

### Уровни паттернов

| Уровень | Определение |
|---------|-------------|
| **Macro Pattern** | Каркас рабочего пространства: определяет структуру взаимодействия с Work Unit и набор зон |
| **Interaction Pattern** | Повторяющаяся модель действия пользователя внутри или между рабочими пространствами |
| **Component Pattern** | Атомарная, но переиспользуемая модель отображения состояния или обратной связи |

### Статусы зрелости

| Статус | Значение |
|--------|----------|
| **Ready** | Модель поведения полностью определена в документации |
| **Needs Refinement** | Паттерн используется, но есть расхождения в реализации или терминологии |
| **Candidate** | Встречается один раз, недостаточно стабилен или описан фрагментарно |

### Связь с UX Interaction Principles

Каждый паттерн сопоставлен с принципами из `UX_ARCHITECTURE_PRINCIPLES.md.md`. Паттерны, нарушающие принципы, отмечены в разделе «Рекомендации».

---

## Классификация Pattern

| Группа | Паттерны |
|--------|----------|
| **Navigation** | Master → Detail, Archive Browse, Quick Actions |
| **Workspace** | Work Unit Workspace, Operations Center, Session Workspace Mode Switch, Configuration Workspace |
| **Review** | Attention Queue, Review Required Flow, Video Evidence Review, AI Pipeline Display |
| **Decision** | In-Context Decision, Bulk Action, Confirmation with Impact Analysis |
| **Monitoring** | Live Status Monitor, Resource Workload Monitor, Analytics Dashboard, Real-Time Refresh |
| **Data Management** | Search & Filter, Sort & Group, Catalog Management |
| **Analytics** | Analytics Dashboard, Progress Tracking |
| **Configuration** | Configuration Workspace, Confirmation with Impact Analysis |
| **Feedback** | Status & Lifecycle Display, Notifications, Loading & Processing State |
| **Recovery** | Automated Validation with Recovery, Error Recovery, Graceful Degradation |

---

## Macro Patterns

### MP-01 Work Unit Workspace

| Поле | Значение |
|------|----------|
| **Описание** | Рабочее пространство строится вокруг Work Unit. Контекст, состояние, действия, связанные объекты и история отображаются в единой среде без ухода из задачи |
| **Пользовательская задача** | Выполнить основную задачу, не теряя контекст объекта |
| **Сценарии** | Proctor SC-002, SC-003, SC-005; Driver SC-003–SC-006; Administrator SA-002, SA-003 |
| **Functional Zones** | Review Workspace, Attempt Summary, Environment Check zones, Session Details |
| **Functional Elements** | Work Unit header, Current State, Primary Action, Related Information, History |
| **Domain Entity** | Attempt, Exam Session, Driver, Proctor |
| **Lifecycle State** | Все состояния Attempt; Operational State Exam Session |
| **Роли** | Driver, Proctor, Administrator |
| **Переиспользование** | Высокое — единая модель для всех ролей (принцип 12, 21) |
| **Уровень** | Macro Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 2, 3, 4, 11, 12, 21 |

---

### MP-02 Operations Center

| Поле | Значение |
|------|----------|
| **Описание** | Сводный операционный центр отвечает на вопрос «требуется ли вмешательство?» и агрегирует ключевые зоны мониторинга с переходом к деталям |
| **Пользовательская задача** | За несколько секунд оценить состояние системы/рабочего дня и перейти к проблемной области |
| **Сценарии** | Proctor SC-001; Administrator SA-001 |
| **Functional Zones** | AZ-001 Executive Dashboard, AZ-002 Attention Center, Daily Dashboard, Exam Sessions, Attempt Flow, Proctor Workload, System Health, AI Status, Integrations, Quick Actions |
| **Functional Elements** | KPI summary, Critical Events block, widget per zone, quick navigation actions |
| **Domain Entity** | Exam Session, Attempt, Proctor, Provider Session |
| **Lifecycle State** | Review Required, In Progress, AI Processing; Operational State интеграций и AI |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Высокое — единая модель «домашнего экрана» оператора |
| **Уровень** | Macro Pattern |
| **Зрелость** | Needs Refinement — разные названия: Attempt Flow (Proctor SC-001) vs Executive Dashboard (AZ-001) |
| **UX Principles** | 1, 7, 8, 14 |

---

### MP-03 Analytics Dashboard

| Поле | Значение |
|------|----------|
| **Описание** | Аналитическая панель с KPI, фильтрами по периоду, визуализацией и экспортом для оценки эффективности |
| **Пользовательская задача** | Оценить эффективность процессов и подготовить отчётность |
| **Сценарии** | Administrator SA-005; Proctor SC-010 (История собственных проверок проктора, Proctor Scenarios Map) |
| **Functional Zones** | KPI Dashboard, Charts, Reports, Export, Attempt Lifecycle Analytics |
| **Functional Elements** | Period selector, filters, KPI cards, charts, export action |
| **Domain Entity** | Exam Session, Attempt, Proctor, Observation, Decision |
| **Lifecycle State** | Decision Made, Published, Archived |
| **Роли** | Administrator (Ready), Proctor (SC-010 — история собственных проверок проктора) |
| **Переиспользование** | Среднее — общая модель, разный набор KPI по ролям |
| **Уровень** | Macro Pattern |
| **Зрелость** | Needs Refinement — KPI Proctor Daily Dashboard не согласован с SA-005 |
| **UX Principles** | 7, 9, 13 |

---

### MP-04 Configuration Workspace

| Поле | Значение |
|------|----------|
| **Описание** | Рабочее пространство управления бизнес-правилами, интеграциями и параметрами системы с обязательным анализом последствий перед применением |
| **Пользовательская задача** | Безопасно изменить правила и настройки платформы |
| **Сценарии** | Administrator SA-006; SA-004 (параметры Exam); SA-003 (ресурсы) |
| **Functional Zones** | Business Rules, AI Configuration, Integration Management, Notifications, System Parameters, Audit Log, Impact Analysis |
| **Functional Elements** | Section list, parameter editor, impact preview, confirmation, audit entry |
| **Domain Entity** | Configuration (domain area), Exam, Proctor, Provider |
| **Lifecycle State** | — (управление правилами, не Lifecycle Attempt) |
| **Роли** | Administrator |
| **Переиспользование** | Среднее — единая модель для всех разделов Configuration |
| **Уровень** | Macro Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 18, 19, 20 |

---

## Interaction Patterns

### IP-01 Master → Detail

| Поле | Значение |
|------|----------|
| **Описание** | Пользователь выбирает объект из списка/каталога и открывает детальную карточку с полным контекстом и действиями |
| **Пользовательская задача** | Найти объект и работать с ним в детальном контексте |
| **Сценарии** | Proctor SC-001→SC-002, SC-008; Administrator SA-002, SA-003, SA-004; Driver SC-001, SC-003 |
| **Functional Zones** | Session List → Session Card/Details; Resource List → Resource Profile; Exam Catalog → Exam Card; Participant List → Participant Card |
| **Functional Elements** | List with key attributes, card open action, detail panel, back to list |
| **Domain Entity** | Exam Session, Attempt, Driver, Proctor, Exam |
| **Lifecycle State** | Любое состояние объекта в списке |
| **Роли** | Proctor, Administrator, Driver |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 11, 12, 14 |

---

### IP-02 Search & Filter

| Поле | Значение |
|------|----------|
| **Описание** | Пользователь сужает набор объектов через поиск и фильтры; система обновляет список и сообщает об отсутствии результатов |
| **Пользовательская задача** | Быстро найти нужный объект в большом наборе данных |
| **Сценарии** | Proctor SC-008; Administrator SA-002, SA-003, SA-005; SA-002 Session Filters |
| **Functional Zones** | Search, Filters, Session Filters, Analytics Filters, Attempt List (поиск/фильтрация) |
| **Functional Elements** | Global search, field-specific search, period/status/role filters, empty result message |
| **Domain Entity** | Exam Session, Attempt, Driver, Proctor |
| **Lifecycle State** | Archived, Published, Scheduled и др. как критерии фильтра |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 9, 13 |

---

### IP-03 Sort & Group

| Поле | Значение |
|------|----------|
| **Описание** | Система автоматически или по запросу пользователя упорядочивает и группирует объекты по приоритету, статусу или категории |
| **Пользовательская задача** | Работать с упорядоченным набором без ручного перебора |
| **Сценарии** | Proctor SC-001 (группировка потока Attempt), SC-004 (группировка Observation); Administrator SA-002 (группировка сессий); AI-004 (идентификация Attempt) |
| **Functional Zones** | Attempt Flow, Decision Groups, Session List, Attempt List |
| **Functional Elements** | Priority sort, status grouping, collapsible groups, group counters |
| **Domain Entity** | Attempt, Alert, Exam Session |
| **Lifecycle State** | Review Required, Decision Made |
| **Роли** | Proctor, Administrator, AI (системный) |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 8, 9 |

---

### IP-04 Attention Queue

| Поле | Значение |
|------|----------|
| **Описание** | Очередь элементов, требующих внимания пользователя, с автоматической приоритизацией. Пользователь обрабатывает элементы последовательно, не видя полный поток сырых Events |
| **Пользовательская задача** | Сфокусироваться на задачах, требующих человеческого вмешательства |
| **Сценарии** | Proctor SC-001 (приоритизация потока Attempt); Administrator AZ-002 Attention Center |
| **Functional Zones** | Attempt Flow, Attention Center |
| **Functional Elements** | Prioritized list, severity level, recommended action, acknowledge/dismiss, auto-elevation on new Attempt |
| **Domain Entity** | Observation, Alert (UI), Recommendation, Attempt |
| **Lifecycle State** | Review Required |
| **Роли** | Proctor (основной), Administrator (операционные Events) |
| **Переиспользование** | Высокое — единый принцип Attention Management (AI Scenarios Specification) |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — в async-модели применяется к потоку Attempt (не к live-мониторингу) |
| **UX Principles** | 5, 7, 8, 22 |

---

### IP-05 Review Required Flow

| Поле | Значение |
|------|----------|
| **Описание** | Общий поток Attempt со статусом Review Required с прогнозом времени обработки. UI-представление «Требуют проверки» — фильтр потока Attempt, а не отдельная сущность; элементы переходят в проверку после Decision |
| **Пользовательская задача** | Последовательно завершить проверку Attempt после экзамена |
| **Сценарии** | Proctor SC-001; Administrator SA-001, AZ-004 Attempt Flow (Review Required) |
| **Functional Zones** | Attempt Flow, Attempt List, Attempt Summary, Review Progress |
| **Functional Elements** | Counters, wait time forecast, list navigation, auto-advance to next attempt |
| **Domain Entity** | Attempt, Recommendation, Risk Score |
| **Lifecycle State** | Review Required → Decision Made |
| **Роли** | Proctor (обработка), Administrator (мониторинг и распределение) |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — механизм распределения Attempt помечен TODO |
| **UX Principles** | 5, 10, 22 |

---

### IP-06 In-Context Decision

| Поле | Значение |
|------|----------|
| **Описание** | Пользователь принимает Decision непосредственно рядом с объектом: контекст (видео, история, Recommendation) и панель действий в одном рабочем пространстве |
| **Пользовательская задача** | Принять обоснованное Decision без потери контекста |
| **Сценарии** | Proctor SC-002, SC-004, SC-005; Driver SC-006 (просмотр Decision) |
| **Functional Zones** | Action Panel, Decision Panel, Participant Card, Attempt Card |
| **Functional Elements** | Context display, Recommendation block, decision outcomes (Approved/Rejected/Ignore/Warning/Continue observation), comment |
| **Domain Entity** | Attempt, Decision, Recommendation, Observation, Alert |
| **Lifecycle State** | Review Required, Decision Made |
| **Роли** | Proctor (принятие), Driver (просмотр результата) |
| **Переиспользование** | Высокое — единые исходы Decision |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — «подтвердить Observation» vs «принять Decision» (Architecture Synchronization Report §7.4) |
| **UX Principles** | 6, 11, 18, 19 |

---

### IP-07 Bulk Action

| Поле | Значение |
|------|----------|
| **Описание** | Массовое выполнение однотипного действия над группой объектов с подтверждением и откатом до применения |
| **Пользовательская задача** | Не повторять одинаковые действия для каждого элемента |
| **Сценарии** | Proctor SC-005 (массовые операции по Attempt — по бизнес-правилам); Administrator SA-003 (массовое перераспределение) |
| **Functional Zones** | Decision Groups, Attempt Summary, Quick Actions |
| **Functional Elements** | Group select, bulk action, confirmation, cancel before apply |
| **Domain Entity** | Attempt, Decision, Driver |
| **Lifecycle State** | Review Required → Decision Made |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — развести Auto Decision и Bulk Approve (Architecture Synchronization Report §7.4) |
| **UX Principles** | 10, 19, 22 |

---

### IP-08 Video Evidence Review

| Поле | Значение |
|------|----------|
| **Описание** | Просмотр видеодоказательств с приоритетом видео над вторичной информацией; синхронизация с Observation/Alert; автоматическая перемотка к моменту события |
| **Пользовательская задача** | Быстро оценить доказательную базу для Decision |
| **Сценарии** | Proctor SC-003 (Evidence / Recording), SC-008 (повторная проверка); Driver SC-003 (camera preview) |
| **Functional Zones** | Video Review, Recording Viewer, Camera Preview, Active Participant, Driver Details |
| **Functional Elements** | Video stream/playback, timeline markers, auto-seek to Observation (−3–5 sec), speed control, alert navigation |
| **Domain Entity** | Attempt, Observation, Alert, Provider Session, Driver |
| **Lifecycle State** | Completed, Decision Made, Published, Archived |
| **Роли** | Proctor, Driver (preview only) |
| **Переиспользование** | Среднее — recorded-вариант; live-вариант не используется проктором в async-модели |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — Recording не Domain Entity; единые controls не формализованы |
| **UX Principles** | 3, 7, INTERFACE_ARCHITECTURE «Видео» |

---

### IP-09 Timeline & History

| Поле | Значение |
|------|----------|
| **Описание** | Хронологическое отображение Observation, Alert, Recommendation, Decision и действий пользователя/системы |
| **Пользовательская задача** | Восстановить контекст без опоры на память |
| **Сценарии** | Proctor SC-003, SC-004, SC-005, SC-008; Administrator SA-002 Session Timeline, SA-004 history |
| **Functional Zones** | Timeline, Session Timeline, History, Event log |
| **Functional Elements** | Chronological events, filters, links to evidence, actor attribution (AI/Proctor/System) |
| **Domain Entity** | Observation, Alert, Recommendation, Decision, Attempt |
| **Lifecycle State** | Все состояния Attempt |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 20 |

---

### IP-10 Status & Lifecycle Display

| Поле | Значение |
|------|----------|
| **Описание** | Явное отображение Lifecycle State объекта и Operational State сервисов; состояние важнее оформления |
| **Пользовательская задача** | Понять текущий этап процесса и доступные переходы |
| **Сценарии** | Driver SC-003–SC-006; Proctor SC-001, SC-002, SC-009; Administrator SA-002, Functional Zones (все AZ) |
| **Functional Zones** | Session Status, Readiness Status, Lifecycle State Attempt, Session Card states |
| **Functional Elements** | Status badge (color + icon + text), lifecycle progression, blocking rules per state |
| **Domain Entity** | Attempt, Exam Session, Provider, AI Orchestration |
| **Lifecycle State** | Draft → Archived (полный цикл); Operational State: Healthy/Warning/Offline и др. |
| **Роли** | Driver, Proctor, Administrator |
| **Переиспользование** | Высокое — принцип 12: одна Entity — одна модель |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — смешение Lifecycle Attempt и Operational State Exam Session (Architecture Synchronization Report §7.3) |
| **UX Principles** | 3, 15 |

---

### IP-11 Automated Validation with Recovery

| Поле | Значение |
|------|----------|
| **Описание** | Система автоматически выполняет проверки, показывает статус каждой, даёт конкретные рекомендации по исправлению и повторяет только затронутые проверки |
| **Пользовательская задача** | Пройти проверку готовности без экспертной помощи |
| **Сценарии** | Driver SC-003 (Environment Checking); Driver SC-004 (Identity Verification) |
| **Functional Zones** | Environment Check, Identity Verification, Readiness Status, Recovery Guidance, Permission State |
| **Functional Elements** | Parallel checks, per-check status, aggregated readiness, recovery hint, retry single check |
| **Domain Entity** | Attempt, Driver |
| **Lifecycle State** | Environment Checking, Identity Verification, Ready |
| **Роли** | Driver |
| **Переиспользование** | Среднее — потенциал для проверки полноты материалов Attempt (SC-009) по аналогии |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready (Driver); Candidate для других ролей |
| **UX Principles** | 5, 16, 17 |

---

### IP-12 Progress Tracking

| Поле | Значение |
|------|----------|
| **Описание** | Отображение прогресса выполнения задачи: обработано/осталось, прогноз времени завершения, среднее время операции |
| **Пользовательская задача** | Оценить объём оставшейся работы |
| **Сценарии** | Proctor SC-001 Review Progress; Driver SC-003 Readiness; Driver SC-005 Exam progress; SA-005 report generation |
| **Functional Zones** | Review Progress, Exam Progress, Readiness Status, Processing State |
| **Functional Elements** | Processed count, remaining count, ETA, average decision time |
| **Domain Entity** | Attempt, Exam |
| **Lifecycle State** | In Progress, Review Required, AI Processing |
| **Роли** | Driver, Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 3, 13 |

---

### IP-13 Confirmation with Impact Analysis

| Поле | Значение |
|------|----------|
| **Описание** | Перед критическим изменением система рассчитывает и показывает последствия; изменение не применяется без подтверждения |
| **Пользовательская задача** | Безопасно применить изменение, понимая затронутые объекты |
| **Сценарии** | Administrator SA-003, SA-004, SA-006; Admin Scenarios Specification (Impact Analysis) |
| **Functional Zones** | Impact Analysis, Available Actions (SA-002), Audit Log |
| **Functional Elements** | Affected sessions count, affected participants, SLA impact, risk level, extra confirmation, audit record |
| **Domain Entity** | Proctor, Exam Session, Exam, Configuration |
| **Lifecycle State** | Scheduled, Active Exam Session |
| **Роли** | Administrator |
| **Переиспользование** | Высокое в Administrator; Candidate для Proctor critical decisions |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 18, 19, 20 |

---

### IP-14 Error Recovery

| Поле | Значение |
|------|----------|
| **Описание** | При ошибке система объясняет что/почему/что делать; предлагает конкретное восстановление; не оставляет пользователя в тупике |
| **Пользовательская задача** | Продолжить работу после сбоя |
| **Сценарии** | Driver SC-003, SC-004, SC-005, SC-006; Proctor SC-009; Administrator все SA (edge cases); Functional Zones «Восстановление» |
| **Functional Zones** | Ошибки и восстановление, Recovery (все AZ) |
| **Functional Elements** | Error reason, affected check, recommended action, retry, support channel |
| **Domain Entity** | Attempt, Provider Session, Exam Session |
| **Lifecycle State** | Любое; Processing State: Failed |
| **Роли** | Driver, Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 16, 17 |

---

### IP-15 Workspace Mode Switch

| Поле | Значение |
|------|----------|
| **Описание** | Рабочее пространство перестраивается под новую фазу Work Unit без навигации на другой экран |
| **Пользовательская задача** | Перейти к следующей фазе работы с тем же объектом |
| **Сценарии** | Proctor SC-003→SC-005 (Evidence → Decision внутри Attempt); Proctor SC-002→SC-003 flow |
| **Functional Zones** | Attempt Workspace (Evidence → Decision) |
| **Functional Elements** | Mode indicator, zone reconfiguration, preserved attempt context |
| **Domain Entity** | Exam Session, Attempt |
| **Lifecycle State** | Review Required → Decision Made |
| **Роли** | Proctor |
| **Переиспользование** | Среднее — задокументировано для Proctor; аналог не описан для Administrator |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — порядок SC-002→SC-005 в рамках Attempt требует продуктового решения |
| **UX Principles** | 4, 11, 14 |

---

### IP-16 Live Status Monitor

| Поле | Значение |
|------|----------|
| **Описание** | Непрерывный мониторинг состояния группы объектов в реальном времени с автоматическим обновлением и визуальными индикаторами отклонений |
| **Пользовательская задача** | Контролировать процесс без ручного обновления |
| **Сценарии** | Administrator SA-002, AZ-003 Live Session Monitor |
| **Functional Zones** | Live Participant List, Attempt Monitor, Live Sessions, Session Status |
| **Functional Elements** | Real-time status cards, online count, problem indicators, auto-refresh |
| **Domain Entity** | Exam Session, Attempt, Driver, Provider Session |
| **Lifecycle State** | In Progress, Identity Verification, Ready |
| **Роли** | Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 7, 8, 15 |

---

### IP-17 Archive Browse & Analysis

| Поле | Значение |
|------|----------|
| **Описание** | Поиск и анализ завершённых объектов в режиме только чтения с доступом к истории и материалам |
| **Пользовательская задача** | Разобраться в результатах прошлых процессов |
| **Сценарии** | Proctor SC-008; Administrator SA-002 (история), SA-005 (аналитика Archived) |
| **Functional Zones** | Session List (completed), Session Summary, Recording Viewer, Participant List (read-only) |
| **Functional Elements** | Search/filter archived, session summary stats, read-only participant card, recording access |
| **Domain Entity** | Exam Session, Attempt, Decision, Observation |
| **Lifecycle State** | Published, Archived |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 9, 13, 20 |

---

### IP-18 AI Pipeline Display

| Поле | Значение |
|------|----------|
| **Описание** | Визуальное и логическое разделение Observation, Recommendation и Decision; AI рекомендует — человек утверждает |
| **Пользовательская задача** | Понять рекомендацию AI и принять собственное Decision |
| **Сценарии** | Proctor SC-004, SC-005; Driver SC-005, SC-006; AI-001, AI-003; все роли при работе с AI output |
| **Functional Zones** | Alert Summary, Recommendations block, Decision Panel, AI Orchestration Summary |
| **Functional Elements** | Observation facts, Recommendation type (Ignore/Review/Warning/Reject), explanation, Decision outcome |
| **Domain Entity** | Observation, Recommendation, Decision, Risk Score |
| **Lifecycle State** | AI Processing, Review Required |
| **Роли** | Proctor, Driver (Alert only), Administrator (мониторинг AI) |
| **Переиспользование** | Высокое — сквозной pipeline |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 6, 22 |

---

### IP-19 Notifications

| Поле | Значение |
|------|----------|
| **Описание** | Трёхуровневые уведомления (информационное / предупреждение / критическое) с объяснением и действием |
| **Пользовательская задача** | Получить своевременную информацию без перегрузки |
| **Сценарии** | Driver SC-005 (Alert); Proctor SC-001 (уведомления о новых Attempt и сроках); Administrator Critical Events; INTERFACE_ARCHITECTURE |
| **Functional Zones** | Critical Alerts, Notifications, Informational notifications (Driver SC-006) |
| **Functional Elements** | Severity level, message, consequence, recommended action, dismiss/acknowledge |
| **Domain Entity** | Alert, Event, Attempt |
| **Lifecycle State** | Любое активное |
| **Роли** | Driver, Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — Alert vs Notification vs Event не унифицированы в UI-модели |
| **UX Principles** | 7, 8 |

---

### IP-20 Resource Workload Monitor

| Поле | Значение |
|------|----------|
| **Описание** | Мониторинг загрузки ресурсов (прокторов) с состоянием доступности и прогнозом; действия по перераспределению |
| **Пользовательская задача** | Обеспечить достаточность ресурсов для проведения экзаменов |
| **Сценарии** | Administrator SA-001, SA-003, AZ-005 Proctor Workload |
| **Functional Zones** | Proctor Workload, Resource List, Resource Profile |
| **Functional Elements** | Availability status, current load, next assignment, redistribute action |
| **Domain Entity** | Proctor, Exam Session, Attempt |
| **Lifecycle State** | — ; Operational State: Available/Busy/Offline/Overloaded |
| **Роли** | Administrator |
| **Переиспользование** | Среднее — только Administrator, но модель применима к любым ресурсам |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — две модели статуса (Available/Busy vs Online/Vacation) |
| **UX Principles** | 9, 17 |

---

### IP-21 Catalog Management

| Поле | Значение |
|------|----------|
| **Описание** | Управление справочным каталогом внешних объектов: просмотр, синхронизация, активация, версионирование |
| **Пользовательская задача** | Поддерживать актуальный каталог используемых объектов |
| **Сценарии** | Administrator SA-004 |
| **Functional Zones** | Exam Catalog, Exam Card, Sync Status, Version History |
| **Functional Elements** | Catalog list, sync action, version indicator, activate/deactivate, category assignment |
| **Domain Entity** | Exam, Provider |
| **Lifecycle State** | Lifecycle: Draft/Active/Archived (Exam version) |
| **Роли** | Administrator |
| **Переиспользование** | Candidate — один детализированный сценарий; аналогия с Provider catalog |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Candidate |
| **UX Principles** | 12, 19 |

---

### IP-22 Real-Time Refresh

| Поле | Значение |
|------|----------|
| **Описание** | Данные обновляются автоматически; при задержке или потере связи система сигнализирует об актуальности |
| **Пользовательская задача** | Работать с актуальными данными без ручного обновления |
| **Сценарии** | Administrator SA-001, SA-002; Proctor SC-001 (обновление потока Attempt); Functional Zones (автообновление состояния) |
| **Functional Elements** | Auto-refresh, stale data indicator, manual refresh action |
| **Domain Entity** | Exam Session, Attempt, Event |
| **Lifecycle State** | Активные состояния |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Высокое |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 15 |

---

### IP-23 Graceful Degradation Display

| Поле | Значение |
|------|----------|
| **Описание** | При отказе компонента система показывает деградированный режим, недоступные возможности и продолжает работу |
| **Пользовательская задача** | Продолжить работу при частичном отказе системы |
| **Сценарии** | Administrator SA-001, SA-006; Proctor SC-009; Admin Scenarios Specification |
| **Functional Elements** | Degraded mode banner, disabled features list, fallback mode indicator |
| **Domain Entity** | AI Orchestration, Provider, Integration |
| **Lifecycle State** | Operational State: Degraded/Offline |
| **Роли** | Proctor, Administrator |
| **Переиспользование** | Среднее |
| **Уровень** | Interaction Pattern |
| **Зрелость** | Needs Refinement — режимы деградации описаны на уровне принципов, не формализованы в сценариях |
| **UX Principles** | 17 |

---

## Component Patterns

### CP-01 Loading & Processing State

| Поле | Значение |
|------|----------|
| **Описание** | Кратковременное состояние выполнения операции: Loading, Synchronizing, Completed, Failed |
| **Пользовательская задача** | Понять, что система обрабатывает запрос |
| **Сценарии** | SA-005 (KPI Dashboard Processing State); Driver SC-006 (AI Processing); GLOSSARY Processing State |
| **Functional Elements** | Loading indicator, progress for long operations, completion transition |
| **Domain Entity** | — (UI state) |
| **Lifecycle State** | AI Processing; Processing State |
| **Роли** | Все |
| **Переиспользование** | Высокое |
| **Уровень** | Component Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 3, 15 |

---

### CP-02 Empty State

| Поле | Значение |
|------|----------|
| **Описание** | Сообщение при отсутствии данных с объяснением причины и следующим действием |
| **Пользовательская задача** | Понять, почему данных нет, и что делать |
| **Сценарии** | SA-005 (нет данных); Proctor SC-001 (пустая очередь); SC-008 (нет результатов поиска) |
| **Functional Elements** | Empty message, reason, suggested action |
| **Domain Entity** | — |
| **Lifecycle State** | — |
| **Роли** | Все |
| **Переиспользование** | Высокое |
| **Уровень** | Component Pattern |
| **Зрелость** | Candidate — описан в edge cases, не как единый паттерн |
| **UX Principles** | 16 |

---

### CP-03 Status Badge

| Поле | Значение |
|------|----------|
| **Описание** | Единая структура статуса: цвет + иконка + короткий текст |
| **Пользовательская задача** | Мгновенно считать состояние объекта |
| **Сценарии** | INTERFACE_ARCHITECTURE; все сценарии со статусами; Operational State Design |
| **Functional Elements** | Color, icon, label; consistent across Lifecycle and Operational states |
| **Domain Entity** | Attempt, Exam Session, Proctor, Integration |
| **Lifecycle State** | Все |
| **Роли** | Все |
| **Переиспользование** | Высокое |
| **Уровень** | Component Pattern |
| **Зрелость** | Ready |
| **UX Principles** | 15 |

---

## Candidate Patterns

Паттерны, не прошедшие порог стабильности (один контекст или фрагментарное описание).

| ID | Название | Причина Candidate | Где встречается |
|----|----------|-------------------|-----------------|
| CN-01 | Sequential Driver Flow | Линейный маршрут SC-001→SC-006 описан как сценарии, не как переиспользуемый паттерн | Driver Scenarios Specification |
| CN-02 | Chat Communication | Для Driver только Sync-взаимодействие без UI-модели; для Proctor не актуален в async-модели | Driver Scenarios |
| CN-03 | Mass Notification | Удалён: async-модель не предусматривает массовые уведомления участникам | Proctor Scenarios Map |
| CN-04 | Quick Actions Bar | Используется в SC-001, SA-001 без единой спецификации | Proctor SC-001, SA-001 |
| CN-05 | Integration Status Panel | AZ-006, AZ-007 не детализированы («...») | Functional Zones |
| CN-06 | Exam Schedule Calendar | UI-представление, не Domain Entity; один контекст (Proctor) | Proctor Scenarios Specification |
| CN-07 | Session Report View | Conclusion / отчёт по Attempt — нет единой модели просмотра | Proctor SC-005, SC-008 |
| CN-08 | Sidebar Navigation | Описана в INTERFACE_ARCHITECTURE для ролей, не в сценариях как паттерн | INTERFACE_ARCHITECTURE |

---

## Дубли

### Дублирующиеся паттерны (одна модель — разные названия)

| Паттерн A | Паттерн B | Рекомендация |
|-----------|-----------|--------------|
| Attempt Flow (Proctor SC-001) | Executive Dashboard (AZ-001) | Объединить под **Attempt Flow / Operations Center** |
| Review Workspace (Proctor Spec) | Attempt Flow (SC-001) | Объединить: Review Workspace = поток Attempt и рабочее место проверки |
| Alert Queue (Proctor) | Attention Center (Administrator) | Обобщить как **Attention Queue (IP-04)** с контекстными вариантами |
| Recording Viewer (SC-008) | Video Review (SC-003) | Объединить под **Video Evidence Review (IP-08)** |
| History (SC-002) | Timeline (SC-003) | Объединить под **Timeline & History (IP-09)** |
| Participant Identity (SC-002) | Driver Details (Proctor Spec) | Единое название: **Participant Detail Panel** |
| KPI Dashboard (SA-005) | История собственных проверок проктора (Proctor SC-010) | Общий **Analytics Dashboard (MP-03)** с role-specific KPI |

### Одинаковые паттерны с разной реализацией

| Паттерн | Расхождение | Источник |
|---------|-------------|----------|
| In-Context Decision (IP-06) | «Подтвердить Observation» vs «Принять Decision» — разные формулировки одного действия | Architecture Synchronization Report §7.4 |
| Bulk Action (IP-07) | Auto Decision vs массовое утверждение vs Recommendation Ignore — не разведены в UI | Architecture Synchronization Report §7.4 |
| Status Display (IP-10) | Lifecycle Attempt vs Operational State Exam Session в одной карточке | Architecture Synchronization Report §7.3 |
| Resource Status (IP-20) | Available/Busy/Offline vs Online/Vacation — две модели | Architecture Synchronization Report §7.3; AZ-005 vs SA-003 |
| Video Evidence (IP-08) | Recorded playback в async-модели; live stream проктором не используется | Proctor SC-003, SC-008 |

### Паттерны, нарушающие UX Interaction Principles

| Паттерн / зона | Принцип | Нарушение |
|----------------|---------|-----------|
| Attempt Summary (Proctor SC-002) | 12 (одна Entity — одна модель) | Смешивает Attempt и агрегат потока |
| Participant Card | 6 (AI рекомендует, человек утверждает) | Риск смешения Observation/Recommendation/Decision в одном блоке |
| Executive Dashboard widgets | 7 (управление вниманием) | Риск перегрузки без приоритизации между зонами |
| Recommendation Card | 6 | Risk Score не Entity — неясно как отображать без смешения с Recommendation |

### Паттерны для объединения

1. **Operations Center** ← Session Dashboard, Daily Dashboard, Executive Dashboard
2. **Attention Queue** ← Alert Queue, Attention Center, Critical Alerts block
3. **Video Evidence Review** ← Recording Viewer, Video Review, live video in Participant Card
4. **Timeline & History** ← Timeline, History, Event log, Session Timeline
5. **Analytics Dashboard** ← KPI Dashboard, Daily Dashboard, Charts+Export

### Паттерны для исключения из Pattern Library

| Объект | Причина |
|--------|---------|
| Отдельные Functional Zones (AZ-001…010) | Зоны — не паттерны; источник требований для паттернов |
| Отдельные сценарии (SC-*, SA-*, AI-*) | Сценарии используют паттерны, но сами не являются паттернами |
| Domain Entity (Attempt, Decision и др.) | Entity — данные; паттерн — способ взаимодействия с Entity |
| Required Components из сценариев | Компоненты — следствие паттернов, не паттерны |
| Exam Schedule | UI-артефакт без Domain Model — Candidate, не Pattern |

---

## Рекомендации

### Для уточнения перед Component Library

1. **Зафиксировать единую терминологию** Operations Center / Dashboard / Workspace в глоссарии.
2. **Развести Lifecycle State и Operational State** в Status Display (IP-10) — отдельные визуальные каналы.
3. **Формализовать Decision Panel** — единый паттерн для SC-005 (Decision) и SC-004 (решения по Observation).
4. **Разделить Bulk Approve и Auto Decision** в документации и UI-модели.
5. **Определить механизм распределения Attempt** (TODO) для IP-05.
6. **Унифицировать Video Controls** для live и recorded в IP-08.
7. **Детализировать AZ-006…010** или исключить из ссылок до готовности.

### Приоритет проектирования Component Library

| Приоритет | Паттерны | Обоснование |
|-----------|----------|-------------|
| P0 | MP-01, MP-02, IP-04, IP-06, IP-10, IP-18, CP-03 | Ядро Proctor live + AI pipeline |
| P1 | IP-05, IP-07, IP-08, IP-09, IP-15, MP-04, IP-13 | Review + Administrator operations |
| P2 | IP-01, IP-02, IP-03, IP-11, IP-12, IP-14, CP-01 | Driver flow + общие data patterns |
| P3 | MP-03, IP-17, IP-20, IP-21, CN-* | Analytics, archive, candidates |

### Связанные документы

- `architecture/UX_ARCHITECTURE_PRINCIPLES.md.md`
- `docs/architecture/DOMAIN_ENTITIES.md`
- `docs/architecture/EXAM_ATTEMPT_LIFECYCLE.md`
- `architecture/Architecture Synchronization Report.md`
- `Pattern Inventory Report.md`
