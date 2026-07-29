# Forms and Validation Standards

## Purpose

本文件定義 React 專案中 Form、Validation、Schema、Submit Flow 與最佳實踐。

目的：

- 建立一致的 Form 架構
- UI 與 Validation 分離
- UI 與 Business Logic 分離
- 建立可重複使用的 Form
- 提高 Type Safety
- 建立 AI Agent 可理解的 Form Flow

本文件不綁定任何特定專案。

---

# Goals

Form 應符合：

- Reusable
- Predictable
- Type Safe
- Testable
- Maintainable

---

# Design Principles

Form 應遵循：

- React Hook Form
- Zod Validation
- Controlled Form
- Schema First
- Validation 與 UI 分離
- API Error 與 Validation Error 分離

---

# Form Architecture

建議：

```text
Page

↓

Feature

↓

Form Component

↓

React Hook Form

↓

Zod Schema

↓

Feature Hook

↓

API
```

---

# Responsibilities

Page：

負責：

- Layout
- Route
- Feature

---

Form Component：

負責：

- Render
- Input
- Error UI

不要：

Business Logic。

---

Hook：

負責：

- Submit
- Loading
- API
- Business Rule

---

Schema：

負責：

Validation。

不要：

寫：

Component。

---

# Folder Structure

建議：

```text
features/

    member/

        components/

            MemberForm.tsx

        hooks/

            useMemberForm.ts

        schemas/

            member.schema.ts

        api/

        types/
```

---

# React Hook Form

建議：

所有：

Form：

都：

使用：

React Hook Form。

不要：

大量：

```tsx
useState()
```

管理：

每個：

Input。

---

# Validation

建議：

全部：

使用：

Zod。

例如：

```text
member.schema.ts
```

不要：

Component：

自己：

Validate。

---

# Schema

每個：

Form：

一個：

Schema。

例如：

```text
login.schema.ts

member.schema.ts

appointment.schema.ts
```

---

# Submit Flow

建議：

```text
User

↓

Input

↓

React Hook Form

↓

Zod

↓

Hook

↓

API

↓

Success
```

---

# Validation Layers

建議：

三層：

```text
Client Validation

↓

Server Validation

↓

Business Validation
```

不要：

全部：

放：

Frontend。

---

# Client Validation

例如：

- Required
- Email
- Max Length
- Min Length
- Regex

---

# Server Validation

例如：

Laravel：

422。

例如：

```text
Email 已存在
```

Frontend：

負責：

顯示。

不要：

重新：

判斷。

---

# Business Validation

例如：

```text
醫師今日請假

不可預約
```

Backend：

判斷。

Frontend：

顯示。

---

# Input Components

建議：

使用：

共用：

Input。

例如：

```text
TextField

NumberField

DatePicker

Select

Checkbox

Radio
```

不要：

每個：

Form：

重新：

建立。

---

# Default Values

全部：

集中：

```typescript
defaultValues
```

不要：

Input：

自己：

設定。

---

# Controlled Components

建議：

全部：

Controlled。

不要：

混：

Uncontrolled。

---

# Form State

React Hook Form：

管理：

```text
Values

Dirty

Touched

Errors
```

不要：

自己：

建立：

State。

---

# Error Messages

Validation：

由：

Schema。

UI：

只：

Render。

不要：

Input：

自己：

判斷。

---

# API Error

不要：

直接：

Alert。

建議：

```text
Toast

Inline Error

Dialog
```

依：

需求。

---

# Submit Button

Submit：

期間：

Disable。

例如：

```text
Saving...
```

避免：

重複：

Submit。

---

# Loading

Form：

自己：

管理：

Saving。

不要：

Global。

---

# Reset

成功：

可：

Reset。

例如：

Create。

Edit：

通常：

不：

Reset。

---

# Dirty Check

離開：

頁面：

可：

提醒：

```text
資料尚未儲存
```

---

# Dynamic Form

例如：

療程：

多筆。

建議：

使用：

Field Array。

不要：

自行：

管理：

Index。

---

# File Upload

Upload：

獨立：

Component。

不要：

一般：

Input。

---

# Date

日期：

建議：

DatePicker。

不要：

自由：

Input。

---

# Number

使用：

Number Field。

不要：

Text。

---

# Select

Option：

集中：

管理。

不要：

Magic String。

---

# Enum

固定：

選項：

使用：

Enum。

例如：

```text
Gender

Status

Role
```

---

# Form Types

建議：

建立：

```typescript
MemberFormValues
```

不要：

直接：

使用：

DTO。

---

# Mapping

Form

↓

DTO

↓

API

建立：

Mapper。

不要：

Component：

自己：

轉。

---

# Common Mistakes

避免：

- 每個 Input useState
- Component Validation
- API 呼叫 Component
- DTO 當 Form
- Form 呼叫 axios
- Magic String
- 重複 Validation
- Alert Error
- Submit 多次

---

# Best Practices

建議：

1. React Hook Form。
2. Zod。
3. Schema 與 UI 分離。
4. Hook 管理 Submit。
5. API 不在 Form。
6. Form Values 獨立 Type。
7. Mapping Form → DTO。
8. Submit Disable。
9. API Error 與 Validation Error 分離。
10. Component 保持純 UI。

---

# Checklist

建立新的 Form 前：

- [ ] 使用 React Hook Form
- [ ] 建立 Zod Schema
- [ ] 建立 Form Values Type
- [ ] 建立 Default Values
- [ ] 建立 Hook
- [ ] API 已抽離
- [ ] Submit Loading
- [ ] Validation 完成
- [ ] API Error 完成
- [ ] Dirty Check 已評估

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