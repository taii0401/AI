# React Folder Structure

## Purpose

本文件定義 React 專案的資料夾架構、模組拆分方式與命名規範。

目的：

- 建立一致的資料夾結構
- 降低模組耦合
- 提高可維護性
- 提高可擴充性
- 提高 AI Agent 可理解性
- 提高多人協作效率

本文件不綁定任何特定專案。

---

# Design Principles

React Folder Structure 應符合：

- Feature-Based
- Single Responsibility
- Low Coupling
- High Cohesion
- Reusable
- Scalable
- Predictable

---

# Why Feature-Based

不要：

```text
components/

pages/

hooks/

api/

types/
```

全部平放。

因為：

功能越多：

會變成：

```text
components/

├── Button.tsx
├── UserTable.tsx
├── LoginForm.tsx
├── ProductCard.tsx
├── ...
```

很快：

數百個檔案。

---

Feature-Based：

改成：

```text
features/

    member/

    appointment/

    schedule/

    dashboard/
```

每個功能：

獨立維護。

---

# Recommended Structure

```text
src/

├── app/
│
├── assets/
│
├── features/
│
├── shared/
│
├── hooks/
│
├── lib/
│
├── providers/
│
├── routes/
│
├── styles/
│
├── types/
│
├── utils/
│
└── main.tsx
```

---

# Folder Responsibilities

## app

負責：

Application Root。

包含：

- App.tsx
- Provider
- Router
- Theme
- Global Config

---

## assets

放置：

- images
- icons
- fonts
- svg

避免：

Business Logic。

---

## features

每個：

Business Feature。

例如：

```text
features/

    member/

    appointment/

    schedule/
```

React：

主要程式：

都在：

這裡。

---

## shared

所有：

Feature：

都可以：

共用。

例如：

```text
Button

Modal

Table

Dialog

Input
```

不得：

包含：

Business Rule。

---

## hooks

跨 Feature：

共用：

Custom Hook。

例如：

```text
useDebounce

usePagination

useModal

useLocalStorage
```

不要：

放：

Feature：

專屬：

Hook。

---

## lib

第三方：

Library。

例如：

```text
axios

dayjs

zod

queryClient
```

避免：

Business Logic。

---

## providers

放：

React Provider。

例如：

```text
ThemeProvider

AuthProvider

QueryProvider
```

---

## routes

Route：

設定。

例如：

```text
router.tsx

protected.tsx
```

不要：

Business Logic。

---

## styles

全域：

CSS。

例如：

```text
globals.css

variables.css
```

---

## types

共用：

Type。

例如：

```text
api.ts

common.ts
```

Feature：

自己的：

Type：

不要：

放：

這裡。

---

## utils

純函式。

例如：

```text
formatDate()

formatMoney()

sleep()

debounce()
```

不得：

依賴：

React。

---

# Feature Structure

每個：

Feature：

建議：

```text
features/

└── member/

    ├── api/

    ├── components/

    ├── hooks/

    ├── pages/

    ├── schemas/

    ├── services/

    ├── types/

    ├── utils/

    └── index.ts
```

Feature：

自己：

管理：

自己的：

程式。

---

# api

例如：

```text
member.api.ts

member.mapper.ts
```

負責：

API。

不要：

UI。

---

# components

例如：

```text
MemberTable.tsx

MemberCard.tsx

MemberForm.tsx
```

只：

UI。

不要：

Business Logic。

---

# hooks

例如：

```text
useMember.ts

useMemberForm.ts
```

負責：

Business Logic。

---

# pages

例如：

```text
MemberListPage.tsx

MemberDetailPage.tsx
```

負責：

Route。

不要：

API。

---

# schemas

例如：

```text
member.schema.ts
```

放：

Zod。

Validation。

---

# services

例如：

```text
member.service.ts
```

若：

Feature：

需要：

Business Service。

可：

放：

這裡。

不是：

所有：

Feature：

都需要。

---

# types

Feature：

自己的：

Type。

例如：

```text
Member.ts

MemberDto.ts
```

不要：

全部：

丟：

Global。

---

# utils

Feature：

自己的：

Helper。

例如：

```text
memberFormatter.ts
```

不要：

放：

共用：

utils。

---

# index.ts

每個：

Feature：

建議：

提供：

統一：

Export。

例如：

```typescript
export * from "./pages";
export * from "./components";
```

避免：

深層：

Import。

---

# Shared Structure

```text
shared/

├── components/

├── hooks/

├── constants/

├── icons/

├── layouts/

├── types/

└── utils/
```

Shared：

不得：

依賴：

Feature。

---

# Naming

資料夾：

全部：

使用：

```text
kebab-case
```

例如：

```text
member-management

medical-record
```

不要：

```text
MemberManagement

Member_Management
```

---

檔名：

Component：

```text
PascalCase
```

例如：

```text
MemberTable.tsx
```

---

Hook：

```text
useMember.ts
```

---

Type：

```text
member.ts
```

---

Schema：

```text
member.schema.ts
```

---

API：

```text
member.api.ts
```

---

# Import Direction

允許：

```text
Page

↓

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

# Shared Rules

只有：

符合：

以下：

才：

放：

Shared。

至少：

兩個：

Feature：

需要。

例如：

```text
Button

Modal

Dialog

Input
```

不要：

太早：

抽：

Shared。

---

# Asset Rules

圖片：

放：

```text
assets/images
```

SVG：

放：

```text
assets/icons
```

不要：

Feature：

各自：

存：

Logo。

---

# Barrel Export

建議：

每個：

資料夾：

都有：

```text
index.ts
```

例如：

```typescript
export * from "./MemberTable";
```

方便：

Import。

---

# Common Mistakes

避免：

- 所有 Component 放一起
- 所有 Hook 放一起
- 所有 API 放一起
- Feature 共用同一 Type
- Shared 依賴 Feature
- utils 放 Business Logic
- pages 呼叫 API
- JSX 超過數百行

---

# Best Practices

建議：

1. 採用 Feature-Based。
2. Feature 自己管理程式。
3. Shared 保持純 UI。
4. Hook 放 Business Logic。
5. API 放 api。
6. Validation 放 schemas。
7. Type 放 Feature。
8. 共用才放 Shared。
9. Barrel Export。
10. 保持資料夾一致。

---

# Checklist

建立新的 Feature 前：

- [ ] 建立 feature 資料夾
- [ ] 建立 api
- [ ] 建立 components
- [ ] 建立 hooks
- [ ] 建立 pages
- [ ] 建立 schemas
- [ ] 建立 types
- [ ] 建立 index.ts
- [ ] Import Direction 正確
- [ ] Shared 無依賴 Feature

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