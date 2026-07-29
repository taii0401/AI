# React State Management

## Purpose

本文件定義 React 專案中的 State Management 架構、State 分類、生命週期與最佳實踐。

目的：

- 建立一致的 State 管理方式
- 降低不必要的 Global State
- 提高可維護性
- 提高可測試性
- 提高 AI Agent 可理解性
- 建立可擴充的 State Architecture

本文件不綁定任何特定專案。

---

# Goals

State Management 應符合：

- Single Source of Truth
- Predictable
- Minimal
- Type Safe
- Testable
- Maintainable

---

# Design Principles

React State 應遵循：

- State 越小越好
- State 越接近使用者越好
- 不重複保存資料
- 避免 Global State
- 避免 Derived State
- UI State 與 Business State 分離

---

# State Architecture

建議：

```text
UI State

↓

Feature State

↓

Application State

↓

Server State
```

不是：

全部：

Global。

---

# State Decision Tree

建立 State 前：

請先判斷：

```text
只有一個 Component 使用？

↓

Yes

↓

useState
```

---

```text
只有 Feature 使用？

↓

Yes

↓

Custom Hook
```

---

```text
多個 Feature 共用？

↓

Yes

↓

Context

或

Global State
```

---

```text
來自 Backend？

↓

Yes

↓

Server State

(API / React Query)
```

---

# Local State

適用：

```text
Modal

Dialog

Tab

Collapse

Selected Row

Current Page

Current Input
```

使用：

```typescript
useState()
```

不要：

放：

Global。

---

# Feature State

Feature：

自己：

管理。

例如：

```text
Member

Appointment

Schedule
```

建議：

使用：

```typescript
useMember()

useAppointment()
```

不要：

Global。

---

# Application State

只有：

整個 App：

需要。

例如：

```text
Current User

Theme

Locale

Sidebar

Notification
```

可：

Context。

---

# Server State

資料：

來自：

Backend。

例如：

```text
Members

Appointments

Doctors
```

不要：

使用：

Context。

未來：

建議：

React Query。

---

# UI State

例如：

```text
Loading

Open

Collapse

Current Tab

Current Step
```

全部：

Local。

---

# Business State

例如：

```text
Current Member

Selected Doctor

Appointment Filter
```

通常：

Feature。

---

# Derived State

避免：

```typescript
const [fullName]

const [age]
```

若：

可以：

由：

其它：

State：

算出。

例如：

```typescript
const fullName=

firstName+

lastName
```

不要：

再：

存。

---

# Single Source of Truth

每一份：

資料：

只有：

一個：

來源。

例如：

```text
Member

↓

Server
```

不要：

三個：

State：

保存：

同一：

Member。

---

# useState

適合：

- Input
- Toggle
- Dialog
- Selected

不要：

管理：

整個：

Application。

---

# useReducer

適合：

複雜：

UI。

例如：

```text
Wizard

Form

State Machine
```

不要：

簡單：

Boolean。

---

# Context

Context：

只：

放：

Global。

例如：

```text
Theme

User

Language
```

不要：

放：

```text
Members

Orders

Products
```

---

# Custom Hook

Feature：

State：

建議：

Hook。

例如：

```typescript
useMember()

useAppointment()

useSchedule()
```

Hook：

管理：

Business Logic。

---

# API State

不要：

Component：

保存：

API。

建議：

```text
Hook

↓

API

↓

State
```

---

# Global State

Global：

只：

放：

真正：

Global。

例如：

```text
Auth

Theme

Permission

Language
```

不要：

全部：

Global。

---

# Loading State

每個：

Feature：

自己：

管理。

例如：

```typescript
loading

saving

deleting
```

不要：

Global：

Loading。

---

# Error State

Feature：

自己：

管理。

例如：

```typescript
error
```

不要：

全部：

共用：

一個：

Error。

---

# Pagination

Pagination：

Feature：

自己：

保存。

不要：

Global。

---

# Search Filter

Filter：

Feature：

自己：

保存。

例如：

```text
keyword

status

page
```

---

# Selected Item

例如：

```text
Selected Member
```

只有：

Member：

需要。

不要：

Global。

---

# Form State

使用：

React Hook Form。

不要：

大量：

useState。

---

# Cache

不要：

自己：

實作：

Cache。

未來：

React Query。

---

# Refresh

重新：

讀取：

Server。

不要：

複製：

State。

---

# Immutable

更新：

State：

建立：

新物件。

不要：

直接：

修改。

例如：

不要：

```typescript
member.name="A"
```

---

# Naming

Boolean：

```text
isLoading

isSaving

isOpen
```

Collection：

```text
members
```

Current：

```text
currentMember
```

Selected：

```text
selectedMember
```

---

# State Location

Local：

放：

Component。

Feature：

放：

Hook。

Global：

放：

Provider。

Server：

放：

API。

---

# Future

大型：

專案：

建議：

Server State：

使用：

```text
React Query
```

不是：

Context。

---

# Common Mistakes

避免：

- 所有 State 放 Context
- Redux 管理全部
- 重複保存資料
- Derived State
- API 放 Context
- UI State Global
- 修改原物件
- Loading Global
- Error Global

---

# Best Practices

建議：

1. Local 優先。
2. Feature 使用 Hook。
3. Global 越少越好。
4. Server State 不放 Context。
5. Form 使用 React Hook Form。
6. API 不保存於 Component。
7. State 不重複。
8. Immutable Update。
9. TypeScript 定義完整。
10. 每個 State 都有唯一來源。

---

# State Decision Table

| State | 建議位置 |
|--------|----------|
| Modal | useState |
| Input | useState |
| Current Tab | useState |
| Login User | Context |
| Theme | Context |
| Language | Context |
| Member List | Server State |
| Appointment List | Server State |
| Member Filter | Feature Hook |
| Pagination | Feature Hook |
| Wizard Step | useReducer |
| Permission | Context |

---

# Checklist

建立新的 State 前：

- [ ] 是否真的需要 State
- [ ] 是否可由其他資料推導
- [ ] 是否只有一個 Component 使用
- [ ] 是否屬於 Feature
- [ ] 是否屬於 Global
- [ ] 是否來自 Backend
- [ ] 是否可放 Hook
- [ ] 是否保持 Immutable
- [ ] 是否有完整 Type
- [ ] 是否只有一個來源

---

# Related Documents

建議閱讀：

```text
README.md
    ↓
architecture.md
    ↓
folder-structure.md
    ↓
standards.md
    ↓
components.md
    ↓
hooks.md
    ↓
typescript.md
    ↓
api-client.md
    ↓
routing.md
    ↓
state-management.md
    ↓
forms-validation.md
    ↓
error-handling.md
    ↓
testing.md
```