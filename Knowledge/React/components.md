# React Components

## Purpose

本文件定義 React Component 的設計原則、責任劃分、拆分策略與最佳實踐。

目的：

- 建立一致的 Component 設計方式
- 提高 Component 可重用性
- 降低 Component 耦合
- 提高可測試性
- 提高 AI Agent 可理解性
- 建立長期可維護的 UI 架構

本文件不綁定任何特定專案。

---

# Goals

React Component 應符合：

- Single Responsibility
- Reusable
- Predictable
- Testable
- Composable
- Maintainable

---

# Design Principles

React Component 應遵循：

- Single Responsibility Principle
- Composition Over Inheritance
- UI 與 Business Logic 分離
- Props 明確定義
- Component 保持純粹（Pure）
- 避免副作用

---

# Component Hierarchy

建議：

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

應只負責：

UI。

---

# Component Categories

建議分成：

```text
Page Component

↓

Feature Component

↓

Shared Component

↓

Primitive Component
```

---

# Page Component

負責：

- Route
- Layout
- Feature 組合

例如：

```text
MemberListPage

AppointmentPage

DashboardPage
```

Page：

不要：

- 呼叫 API
- 撰寫大量 Business Logic
- 建立複雜 Validation

---

# Feature Component

負責：

單一功能。

例如：

```text
MemberTable

MemberForm

AppointmentCalendar
```

可以：

使用：

Feature Hook。

不要：

直接：

使用：

Global State。

---

# Shared Component

例如：

```text
Button

Input

Modal

Dialog

Card

Table
```

Shared：

不得：

知道：

Member

Appointment

Order

等：

Business Rule。

---

# Primitive Component

最小：

UI。

例如：

```text
Text

Icon

Spinner

Badge

Avatar
```

高度：

Reusable。

---

# Component Responsibility

一個 Component：

只負責：

一件事情。

例如：

```text
MemberTable
```

不要：

同時：

- 查詢 API
- 儲存資料
- Validation
- Modal
- Permission

全部：

放一起。

---

# Component Size

建議：

```text
300 行
```

以內。

若：

超過：

建議：

拆分。

---

# Component Composition

React：

優先：

Composition。

例如：

```tsx
<Card>

    <CardHeader />

    <CardBody />

    <CardFooter />

</Card>
```

不要：

建立：

複雜：

BaseComponent。

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

    member:Member

    onEdit:(id:number)=>void

}
```

不要：

```typescript
props:any
```

---

# Props Design

Props：

應：

簡潔。

避免：

傳：

大量：

Boolean。

例如：

不要：

```tsx
<Button

success

danger

large

rounded

primary

/>
```

建議：

```tsx
<Button

variant="primary"

size="large"

/>
```

---

# Children

若：

Component：

需要：

插槽。

使用：

```tsx
children
```

例如：

```tsx
<Card>

    {children}

</Card>
```

不要：

固定：

內容。

---

# Callback

事件：

全部：

由：

Parent：

傳入。

例如：

```tsx
onSave

onDelete

onSubmit

onClose
```

不要：

Component：

自己：

決定：

Business Flow。

---

# Controlled Component

表單：

建議：

Controlled。

例如：

```tsx
value

onChange
```

不要：

混用：

Controlled

與：

Uncontrolled。

---

# Business Logic

Business Logic：

不要：

放：

Component。

例如：

不要：

```tsx
const filtered = users.filter(...)

const result = calculate(...)

const canDelete = ...
```

建議：

放：

```text
Hook
```

例如：

```tsx
const {

    members,

    loading

}=useMember()
```

---

# API Calls

Component：

不得：

直接：

```typescript
axios.get()
```

流程：

```text
Component

↓

Hook

↓

API

↓

Laravel
```

---

# Local State

只有：

UI：

使用：

```tsx
useState()
```

例如：

```text
Modal

Tab

Accordion

Collapse
```

---

# Global State

不要：

放：

Component。

例如：

```text
Login User

Theme

Language
```

應：

Provider

或：

State Manager。

---

# Side Effects

所有：

Side Effect：

集中：

```tsx
useEffect()
```

不要：

Render：

時：

執行。

---

# Conditional Rendering

建議：

```tsx
if(isLoading){

    return <Loading/>

}
```

不要：

大量：

```tsx
&&

?:
```

混在：

JSX。

---

# Loading State

每個：

Component：

建議：

都有：

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

# Error State

不要：

直接：

```tsx
alert()
```

建議：

Error Component。

---

# Memo

只有：

必要：

使用：

```tsx
React.memo
```

不要：

全部：

Memo。

---

# Keys

List：

一定：

使用：

穩定：

Key。

不要：

```tsx
index
```

除非：

真的：

固定。

---

# CSS

Component：

不要：

大量：

Inline Style。

建議：

CSS Module

Tailwind

CSS-in-JS（依專案規範）。

---

# Accessibility

Button：

使用：

```html
<button>
```

Input：

使用：

```html
<label>
```

不要：

```html
<div onClick>
```

---

# Testing

Component：

容易：

測試。

因為：

Business Logic：

已：

抽離。

---

# Component Structure

建議：

```text
MemberTable/

├── MemberTable.tsx
├── MemberTableRow.tsx
├── MemberTableToolbar.tsx
├── MemberTableSkeleton.tsx
├── MemberTableEmpty.tsx
├── MemberTable.types.ts
└── index.ts
```

大型：

Component：

再：

拆。

---

# Import Direction

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

---

# Naming

Component：

全部：

```text
PascalCase
```

例如：

```text
MemberTable

AppointmentCard

LoginForm
```

---

# Common Mistakes

避免：

- JSX 超過數百行
- Component 呼叫 API
- Component 做 Validation
- Component 做 Business Logic
- Props 使用 any
- Shared Component 包含業務規則
- Inline Function 過多
- 使用 index 當 key
- 過度使用 React.memo

---

# Best Practices

建議：

1. Component 保持單一責任。
2. Business Logic 放 Hook。
3. API 放 API Layer。
4. Shared 保持純 UI。
5. Props 定義完整型別。
6. Composition 優先。
7. Component 保持精簡。
8. 拆分大型 JSX。
9. Loading/Error/Empty 狀態完整。
10. Component 易於測試。

---

# Checklist

建立新的 Component 前：

- [ ] 單一責任
- [ ] Props 已定義
- [ ] 無 any
- [ ] 無 API 呼叫
- [ ] 無 Business Logic
- [ ] Loading State
- [ ] Error State
- [ ] Empty State
- [ ] Accessibility 已確認
- [ ] Component 可重用

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