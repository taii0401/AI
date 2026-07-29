# React Coding Standards

## Purpose

本文件定義 React 專案的 Coding Standards、命名規範、程式設計原則與最佳實踐。

目的：

- 建立一致的程式碼風格
- 提高可讀性
- 提高可維護性
- 降低 AI Agent 產生不一致程式碼
- 提高多人協作效率

本文件不綁定任何特定專案。

---

# Goals

React Coding Standards 應符合：

- Readable
- Predictable
- Reusable
- Testable
- Type Safe
- Maintainable

---

# General Principles

React 專案應遵循：

- Single Responsibility Principle
- Separation of Concerns
- Composition Over Inheritance
- Keep It Simple
- Don't Repeat Yourself
- Explicit is Better than Implicit

---

# File Naming

## Components

使用：

```text
PascalCase
```

例如：

```text
MemberTable.tsx

LoginForm.tsx

AppointmentCalendar.tsx
```

不要：

```text
memberTable.tsx

member_table.tsx

member-table.tsx
```

---

## Hooks

全部：

```text
useXXXX.ts
```

例如：

```text
useMember.ts

usePagination.ts

useDebounce.ts
```

---

## Types

全部：

```text
camelCase.ts
```

例如：

```text
member.ts

appointment.ts
```

---

## API

全部：

```text
feature.api.ts
```

例如：

```text
member.api.ts

appointment.api.ts
```

---

## Schema

全部：

```text
feature.schema.ts
```

例如：

```text
member.schema.ts
```

---

## Utils

全部：

```text
camelCase.ts
```

例如：

```text
formatDate.ts

formatMoney.ts
```

---

# Folder Naming

資料夾：

全部：

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

member_management
```

---

# Component Design

一個 Component：

只負責：

一件事情。

例如：

```text
MemberTable
```

不要：

同時：

- 查 API
- Validate
- Save
- Render
- Modal

全部：

一起。

---

# Component Size

建議：

Component：

控制：

```text
300 行
```

以內。

若：

超過：

建議：

拆分。

---

# JSX

避免：

大量：

巢狀。

例如：

```tsx
<Component>

    <A>

        <B>

            <C>

                ...

```

建議：

拆：

Component。

---

# Props

所有：

Props：

必須：

定義：

Type。

例如：

```typescript
interface Props {

    memberId:number

}
```

不要：

```typescript
props:any
```

---

# Default Props

優先：

使用：

預設值。

例如：

```typescript
function Button({

    disabled=false

}:Props)
```

避免：

DefaultProps。

---

# State

Local：

使用：

```typescript
useState()
```

不要：

所有：

State：

都：

Global。

---

# Derived State

避免：

重複：

State。

例如：

不要：

```typescript
const [fullName,setFullName]
```

若：

可以：

由：

```typescript
firstName

lastName
```

組合。

---

# Business Logic

不要：

寫：

JSX。

例如：

不要：

```tsx
return (

    users

        .filter(...)

        .map(...)

)
```

建議：

抽：

Hook。

---

# API

Component：

不得：

直接：

```typescript
axios.get()
```

應：

```text
Component

↓

Hook

↓

API

↓

Axios
```

---

# Async

使用：

```typescript
async/await
```

避免：

大量：

```typescript
.then()
```

---

# Error Handling

所有：

API：

都：

應：

處理：

Error。

不要：

忽略：

Promise。

---

# Optional Chaining

建議：

```typescript
user?.name
```

不要：

```typescript
user && user.name
```

---

# Null Handling

所有：

Nullable：

必須：

明確：

處理。

不要：

假設：

一定：

存在。

---

# Magic Number

避免：

```typescript
if(status===3)
```

建議：

```typescript
AppointmentStatus.Completed
```

---

# Constants

固定值：

放：

```text
constants/
```

不要：

散落：

程式。

---

# Functions

Function：

名稱：

應：

代表：

動作。

例如：

```text
loadMember

saveAppointment

deleteItem
```

不要：

```text
doData

test

aaa
```

---

# Boolean Naming

Boolean：

建議：

```text
isLoading

hasPermission

canEdit

shouldReload
```

不要：

```text
loading

permission

reload
```

---

# Event Handler

全部：

```text
handleXXXX
```

例如：

```text
handleSubmit

handleDelete

handleSave
```

---

# Custom Hook

Hook：

全部：

```text
useXXXX
```

例如：

```text
useMember

useAuth

usePagination
```

---

# Import Order

建議：

```text
React

↓

Third-party

↓

Shared

↓

Feature

↓

Relative
```

例如：

```typescript
import { useState } from "react";

import axios from "axios";

import { Button } from "@/shared";

import { useMember } from "../hooks";
```

---

# Export

建議：

Named Export。

例如：

```typescript
export function MemberTable()
```

避免：

大量：

Default Export。

---

# Comments

優先：

寫：

Why。

不要：

寫：

What。

例如：

```typescript
// 必須保留排序，避免與後端預約規則不一致
```

不要：

```typescript
// 建立 Button
```

---

# TypeScript

不要：

```typescript
any
```

若：

真的：

需要：

必須：

說明：

原因。

---

# CSS

避免：

Inline Style。

建議：

CSS Module

Tailwind

或：

統一：

UI Framework。

---

# Accessibility

Button：

必須：

使用：

```html
<button>
```

不要：

```html
<div onClick>
```

---

# Performance

避免：

每次：

Render：

建立：

新：

Function。

必要時：

使用：

```typescript
useCallback
```

不要：

過度：

Memo。

---

# Reusability

Shared Component：

不得：

包含：

Business Rule。

例如：

Button：

不能：

知道：

Member。

---

# Common Mistakes

避免：

- any
- 巨型 Component
- JSX 太長
- Component 呼叫 API
- 重複 State
- Inline Function 過多
- Magic Number
- Default Export 過多
- 共用 Component 包含業務規則
- Shared 依賴 Feature

---

# Best Practices

建議：

1. Component 保持單一責任。
2. Business Logic 放 Hook。
3. API 放 API Layer。
4. Props 定義完整 Type。
5. 使用 Named Export。
6. 避免 any。
7. 保持 Import 順序一致。
8. Function 命名清楚。
9. Component 保持精簡。
10. 保持一致的程式風格。

---

# Checklist

提交程式前：

- [ ] Component 單一責任
- [ ] 無 any
- [ ] Props 已定義 Type
- [ ] API 已抽離
- [ ] Hook 命名正確
- [ ] Function 命名清楚
- [ ] Import 順序正確
- [ ] 無 Magic Number
- [ ] Error Handling 完成
- [ ] TypeScript 無錯誤

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