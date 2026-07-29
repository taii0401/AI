# React Architecture

## Purpose

本文件定義 React 應用程式的整體架構、設計原則、資料流、模組劃分與最佳實踐。

目的：

- 建立一致的 React 架構
- 提高可維護性
- 提高可擴充性
- 降低耦合
- 提高 Component 可重用性
- 建立 AI Agent 可理解的專案架構

本文件不綁定任何特定專案。

---

# Goals

React Architecture 應符合：

- Single Responsibility
- Separation of Concerns
- Feature-Based
- Reusable
- Type Safe
- Testable
- Scalable

---

# Architecture Overview

建議採用：

```text
Browser

↓

React SPA

↓

Routing

↓

Feature

↓

Component

↓

Hook

↓

API Client

↓

Laravel API

↓

Database
```

React：

只負責：

UI。

Business Logic：

盡量：

不要：

直接：

放：

Component。

---

# SPA Architecture

建議：

```text
Browser

↓

React

↓

React Router

↓

Feature Module

↓

Component

↓

API Client

↓

Laravel API
```

React：

不直接：

操作：

Database。

---

# Layered Architecture

```text
Pages

↓

Features

↓

Components

↓

Hooks

↓

API

↓

Types

↓

Utils
```

每一層：

責任：

不同。

---

# Feature-Based Architecture

建議：

```text
src/

├── app/

├── features/

├── shared/

├── components/

├── hooks/

├── lib/

├── pages/

├── types/

└── utils/
```

Feature：

應：

依：

功能：

拆分。

不要：

依：

檔案類型。

---

# Rendering Flow

```text
User

↓

Page

↓

Feature

↓

Component

↓

Props

↓

Render
```

Component：

不要：

直接：

查詢：

API。

---

# Data Flow

建議：

```text
Page

↓

Hook

↓

API

↓

Laravel API

↓

Response

↓

State

↓

Component
```

避免：

```text
Component

↓

fetch()

↓

Render
```

---

# Component Hierarchy

```text
App

↓

Layout

↓

Page

↓

Feature

↓

Section

↓

Component

↓

Element
```

Component：

保持：

單一責任。

---

# Folder Responsibilities

## app

負責：

- App
- Provider
- Router
- Global Config

---

## features

負責：

Business Feature。

例如：

```text
member

appointment

schedule
```

---

## shared

共用：

Logic。

例如：

```text
Button

Dialog

Table
```

---

## hooks

共用：

Custom Hook。

例如：

```text
useDebounce

usePagination

useModal
```

---

## lib

第三方：

Library。

例如：

```text
axios

dayjs

zod
```

---

## types

共用：

Type。

---

## utils

純函式。

不得：

依賴：

React。

---

# Component Types

React：

建議：

三種：

Component。

---

## Page Component

負責：

- Route
- Layout
- Feature 組合

不要：

大量：

Business Logic。

---

## Feature Component

負責：

一個：

Business Feature。

例如：

```text
MemberTable

AppointmentCalendar
```

---

## Shared Component

可：

跨：

Feature。

例如：

```text
Button

Modal

Input
```

不得：

包含：

Business Logic。

---

# State Architecture

React：

State：

建議：

分三種。

---

## Local State

例如：

```text
useState
```

只：

Component：

自己：

使用。

---

## Feature State

由：

Custom Hook：

管理。

例如：

```text
useMember()

useAppointment()
```

---

## Global State

只有：

真的：

需要：

才：

建立。

例如：

- Theme
- Login User
- Language

不要：

所有：

State：

都：

Global。

---

# API Layer

建議：

```text
Component

↓

Hook

↓

API

↓

Axios

↓

Laravel API
```

不要：

Component：

直接：

```text
axios.get()
```

---

# Business Logic

Business Logic：

應：

放：

Hook。

例如：

```text
useAppointment()
```

不要：

全部：

寫：

JSX。

---

# Routing

Router：

只：

負責：

Navigation。

不要：

Business Logic。

例如：

```text
/login

/member

/member/:id
```

---

# Validation

建議：

React Hook Form

+

Zod

不要：

Component：

自己：

Validate。

---

# Error Handling

建議：

分：

三層。

```text
Validation Error

↓

API Error

↓

Unexpected Error
```

不要：

全部：

alert()

---

# Loading State

每個：

Page：

應：

有：

```text
Loading

Empty

Success

Error
```

不要：

只有：

Success。

---

# Composition

React：

優先：

Composition。

例如：

```tsx
<Card>

    <CardHeader />

    <CardBody />

</Card>
```

不要：

Inheritance。

---

# Dependency Direction

依賴：

方向：

只能：

```text
Page

↓

Feature

↓

Shared
```

Shared：

不能：

依賴：

Feature。

---

# Import Rules

允許：

```text
Feature

↓

Shared
```

禁止：

```text
Shared

↓

Feature
```

避免：

循環依賴。

---

# Type Safety

所有：

Props：

都：

定義：

Type。

不要：

```tsx
props:any
```

---

# Reusability

Component：

可：

重用。

Business Rule：

不可：

重用：

到：

Shared。

---

# Communication

Component：

溝通：

方式：

```text
Props

↓

Callback

↓

Context（必要時）
```

避免：

兄弟：

直接：

存取。

---

# Performance

大型：

Page：

建議：

- Lazy Loading
- Code Splitting
- Memo
- Virtual List

不要：

一開始：

全部：

載入。

---

# Testing

React：

容易：

測試：

因為：

Business Logic：

已：

抽離。

例如：

```text
Hook

API

Utils
```

可：

獨立：

測試。

---

# Common Mistakes

避免：

- Component 呼叫 API
- JSX 太長
- Business Logic 放 JSX
- 所有 State 都 Global
- Props 使用 any
- 共用 Component 包含業務規則
- Shared 依賴 Feature
- Component 超過數百行
- 重複建立相同 Hook

---

# Best Practices

建議：

1. 採用 Feature-Based Architecture。
2. Page 只負責組合 Feature。
3. API 統一由 API Client 管理。
4. Business Logic 放 Hook。
5. Shared Component 保持純 UI。
6. 使用 TypeScript。
7. Component 保持單一責任。
8. State 依照作用範圍選擇 Local、Feature 或 Global。
9. Props 與 API Response 皆定義型別。
10. 保持依賴方向單向流動。

---

# Checklist

建立新的 React 專案前：

- [ ] 採用 Feature-Based Structure
- [ ] API Layer 已規劃
- [ ] Routing 已規劃
- [ ] State Strategy 已確認
- [ ] Component Hierarchy 已確認
- [ ] Shared Components 已規劃
- [ ] TypeScript 已啟用
- [ ] Validation Strategy 已確認
- [ ] Error Handling 已確認
- [ ] Testing Strategy 已確認

---

# Related Documents

建議閱讀順序：

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