# TypeScript Standards

## Purpose

本文件定義 React 專案中的 TypeScript 使用規範、型別設計原則與最佳實踐。

目的：

- 建立一致的 TypeScript 風格
- 提高 Type Safety
- 降低 Runtime Error
- 提高 AI Agent 可理解性
- 提高程式可維護性
- 降低 any 的使用

本文件不綁定任何特定專案。

---

# Goals

TypeScript 應符合：

- Type Safe
- Explicit
- Predictable
- Reusable
- Maintainable
- Readable

---

# Design Principles

TypeScript 應遵循：

- Explicit Types
- Strong Typing
- Single Source of Truth
- Don't Repeat Yourself
- Prefer Inference
- Avoid Any

---

# Type Hierarchy

建議：

```text
Primitive Type

↓

Utility Type

↓

Business Type

↓

DTO

↓

View Model

↓

Component Props
```

---

# Prefer Type Inference

若：

TypeScript：

已能推導：

不要：

重複：

宣告。

例如：

```typescript
const count = 0;
```

不要：

```typescript
const count:number=0;
```

---

# Avoid Any

避免：

```typescript
any
```

例如：

```typescript
function save(data:any){}
```

改成：

```typescript
function save(data:Member){}
```

若：

真的：

需要：

```typescript
unknown
```

優先。

---

# Unknown

外部：

資料：

例如：

API：

JSON：

建議：

```typescript
unknown
```

再：

Parse。

不要：

直接：

```typescript
any
```

---

# Never

不會：

回傳：

的：

Function。

例如：

```typescript
function panic():never{}
```

---

# Interface vs Type

建議：

## Interface

描述：

Object。

例如：

```typescript
interface Member{

    id:number

    name:string

}
```

---

## Type

描述：

Union

Function

Utility。

例如：

```typescript
type Status=

"draft"

|

"completed"
```

---

# Naming

Interface：

不要：

```typescript
IMember
```

建議：

```typescript
Member
```

---

Type：

同樣：

```typescript
Member
```

不要：

```typescript
TMember
```

---

Enum

例如：

```typescript
AppointmentStatus
```

不要：

```typescript
STATUS
```

---

# DTO

API：

Response：

建議：

```typescript
MemberDto
```

API：

Request：

```typescript
CreateMemberRequest
```

不要：

混用：

Business Type。

---

# View Model

UI：

可：

建立：

```typescript
MemberView
```

避免：

直接：

使用：

API DTO。

---

# Component Props

每個：

Component：

Props：

都：

定義：

Interface。

例如：

```typescript
interface Props{

    member:Member

    onSave:()=>void

}
```

---

# API Response

建議：

建立：

共用：

Response。

例如：

```typescript
interface ApiResponse<T>{

    data:T

    message:string

}
```

避免：

每支：

API：

自己：

定義。

---

# Pagination

例如：

```typescript
interface Pagination{

    currentPage:number

    perPage:number

    total:number

}
```

---

# Generic

重複：

Type：

使用：

Generic。

例如：

```typescript
ApiResponse<T>
```

不要：

建立：

大量：

重複：

Interface。

---

# Utility Types

建議：

使用：

```typescript
Partial

Required

Readonly

Pick

Omit

Record

ReturnType

Parameters
```

避免：

自行：

重寫。

---

# Nullable

建議：

明確：

表示。

例如：

```typescript
name:string|null
```

不要：

依賴：

any。

---

# Optional

例如：

```typescript
name?:string
```

代表：

可能：

不存在。

不要：

與：

Nullable：

混淆。

---

# Boolean

Boolean：

命名：

建議：

```typescript
isLoading

isOpen

canEdit

hasPermission
```

不要：

```typescript
loading

permission
```

---

# Array

建議：

```typescript
Member[]
```

不要：

```typescript
Array<Member>
```

除非：

Generic：

需要。

---

# Function

Function：

回傳：

建議：

明確。

例如：

```typescript
function loadMember():Promise<Member[]>
```

不要：

依賴：

Implicit。

---

# Async

全部：

使用：

```typescript
Promise<T>
```

例如：

```typescript
Promise<Member>
```

---

# React Hook

Hook：

建議：

明確：

回傳。

例如：

```typescript
interface UseMemberResult{

    members:Member[]

    loading:boolean

}
```

---

# Type Location

共用：

Type：

放：

```text
shared/types
```

Feature：

Type：

放：

```text
features/member/types
```

不要：

全部：

Global。

---

# Constants

固定值：

建議：

Union。

例如：

```typescript
type Role=

"admin"

|

"user"
```

不要：

Magic String。

---

# Enum

只有：

真正：

固定：

常數：

才：

使用。

例如：

```typescript
enum AppointmentStatus
```

避免：

所有：

String：

都：

Enum。

---

# Assertion

避免：

```typescript
as any
```

若：

使用：

必須：

有：

理由。

---

# Non-null Assertion

避免：

```typescript
user!
```

應：

先：

判斷：

是否：

存在。

---

# Type Guard

建議：

建立：

Type Guard。

例如：

```typescript
function isMember(

value:unknown

):value is Member
```

避免：

直接：

Cast。

---

# Casting

避免：

```typescript
as Member
```

若：

外部：

資料：

應：

Validate。

---

# Zod

API：

JSON：

建議：

使用：

```text
Zod
```

先：

Parse。

再：

使用。

---

# Import

Type：

使用：

```typescript
import type
```

例如：

```typescript
import type {

Member

} from "./member";
```

---

# Common Mistakes

避免：

- any
- as any
- user!
- Magic String
- 重複 Interface
- DTO 當 Business Type
- Global Type 過多
- Optional 與 Nullable 混用
- 未定義 API Response
- 未使用 Generic

---

# Best Practices

建議：

1. 不使用 any。
2. Props 全部定義 Type。
3. API Response 使用 Generic。
4. DTO 與 Business Type 分離。
5. View Model 與 DTO 分離。
6. 善用 Utility Types。
7. 使用 Type Guard。
8. 使用 Zod 驗證外部資料。
9. Type 放 Feature。
10. import type。

---

# Checklist

建立新的 Feature 前：

- [ ] 無 any
- [ ] Props 已定義
- [ ] API Response 已定義
- [ ] DTO 已建立
- [ ] View Model 已建立
- [ ] Nullable 已確認
- [ ] Optional 已確認
- [ ] Generic 已評估
- [ ] Utility Types 已評估
- [ ] Type Guard 已評估

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
hooks.md
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