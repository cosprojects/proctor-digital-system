# Layer Naming Convention

Все создаваемые объекты Figma должны иметь осмысленные названия.

Запрещается оставлять автоматически созданные имена.

Не использовать:

Frame 1

Group 25

Rectangle 18

Vector 14

Copy

Copy 2

Instance

Component 1

---

# Язык

Все технические названия компонентов и слоев оформляются на английском языке.

Причины:

- совместимость с Figma;
- единый стиль библиотеки;
- удобство разработки;
- отсутствие проблем при экспорте.

Пользовательский текст интерфейсов остается на русском языке.

---

# Общий формат

Использовать PascalCase.

Примеры

Button

TopBar

CameraPreview

ParticipantCard

QuestionCard

IdentityStatus

VerificationStep

---

# Component Sets

Использовать существительное.

Примеры

Button

Input

Badge

Card

Alert

Dialog

VideoTile

QuestionCard

---

# Variants

Использовать Properties Figma.

Не включать состояние в название компонента.

Правильно

Button

Variant

Type=Primary

Size=Medium

State=Default

Неправильно

Button Primary

Button Large

Button Hover

---

# Frames

Название должно отражать назначение.

Правильно

Driver Exam

Driver Verification

Driver Result

Proctor Dashboard

Proctor Monitoring

AI Analysis

Неправильно

Frame 1

Desktop

Screen

New Frame

---

# Layout

Использовать следующие названия.

AppShell

TopBar

Sidebar

Page

Section

Content

Footer

BottomActionBar

ScrollArea

---

# Containers

Использовать существительные.

Card

Panel

Block

Container

Stack

Row

Column

Grid

---

# Domain Components

Использовать предметные названия.

CameraPreview

PhotoComparison

EnvironmentCheck

VerificationStatus

ExamWorkspace

AnswerOption

VideoTile

ParticipantCard

ViolationBadge

AIAlert

RecordingIndicator

---

# Layers внутри компонентов

Использовать короткие названия.

Container

Content

Header

Body

Footer

Icon

Label

Title

Subtitle

Value

Description

Image

Avatar

Actions

Divider

Background

Overlay

---

# Auto Layout

Главный Auto Layout всегда называется

Container

Вложенные:

Header

Content

Footer

Actions

---

# Icons

Использовать

Icon

или

Icon / Camera

Icon / Warning

Icon / Success

---

# Images

Image

Avatar

Photo

Preview

Document

---

# Text

Title

Subtitle

Label

Caption

Body

Value

Hint

Description

---

# States

Не включать состояние в название слоя.

Использовать Component Properties.

Правильно

Button

State=Hover

Неправильно

Button Hover

---

# Запрещается

Frame 1

Group

Rectangle

Vector

Ellipse

Copy

New Component

Instance

---

# Итоговый принцип

Название должно отвечать на вопрос:

"Что это?"

а не

"Как оно выглядит?"

Правильно

VerificationStatus

Неправильно

Green Box

Big Card

Left Rectangle

Blue Button