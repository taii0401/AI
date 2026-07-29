# API Client Standards

## Purpose

本文件定義 React 專案中 API Client 的設計方式、分層架構、錯誤處理、型別設計與最佳實踐。

目的：

- 建立一致的 API 呼叫方式
- UI 與 API 解耦
- 集中管理 HTTP 設定
- 提高可測試性
- 提高可維護性
- 建立 AI Agent 可理解的 API Layer

本文件不綁定任何特定專案。

---

# Goals

API Client 應符合：

- Single Responsibility
- Type Safe
- Centralized
- Testable
- Reusable
- Predictable

---

# Architecture

建議：

```text
Page

↓

Feature Component

↓

Custom Hook

↓

Feature API

↓

Shared API Client

↓

Laravel API
```

API Client：

永遠：

不要：

直接：

Render UI。

---

# Responsibilities

API Client：

負責：

- HTTP Request
- HTTP Response
- Authorization Header
- Refresh Token
- Error Mapping
- Timeout
- Retry（必要時）
- Request Interceptor
- Response Interceptor

API Client：

不負責：

- Business Logic
- Permission
- UI
- Form Validation
- Navigation

---

# Folder Structure

建議：

```text
src/

shared/

    api/

        client.ts

        interceptor.ts

        auth.ts

        error.ts

        index.ts
```

Feature：

自己的：

API：

放：

```text
features/

    member/

        api/

            member.api.ts
```

不要：

全部：

API：

放：

同一個：

資料夾。

---

# API Layers

建議：

```text
Shared Client

↓

Feature API

↓

Hook

↓

Component
```

每一層：

責任：

不同。

---

# Shared Client

例如：

```typescript
client.ts
```

負責：

- Axios Instance
- Base URL
- Timeout
- Interceptor

不要：

放：

Business API。

---

# Feature API

例如：

```text
member.api.ts

appointment.api.ts
```

負責：

Member：

所有：

API。

例如：

```typescript
getMembers()

getMember()

createMember()

updateMember()

deleteMember()
```

---

# Component

不要：

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

Feature API
```

---

# HTTP Client

整個專案：

只有：

一個：

Axios Instance。

例如：

```typescript
client
```

不要：

每個：

Feature：

自己：

建立。

---

# Base URL

使用：

Environment。

例如：

```text
VITE_API_BASE_URL
```

不要：

```text
http://localhost:8080
```

寫死。

---

# Timeout

建議：

統一：

例如：

```text
30 秒
```

避免：

永遠：

等待。

---

# Headers

由：

Client：

統一：

管理。

例如：

```text
Authorization

Accept

Content-Type
```

不要：

每支：

API：

自己：

設定。

---

# Authorization

Token：

由：

Interceptor：

加入。

不要：

```typescript
headers:{

Authorization:...

}
```

每支：

API：

重複：

寫。

---

# Interceptor

Request：

負責：

- Token
- Request ID
- Locale
- Trace ID

Response：

負責：

- Error
- Refresh Token
- Unauthorized
- Logging

---

# API Function

每個：

Function：

只：

代表：

一支：

API。

例如：

```typescript
getMembers()

getMember()

createMember()

updateMember()

deleteMember()
```

不要：

```typescript
saveMember()
```

同時：

Create

Update。

---

# HTTP Method

遵循：

REST。

```text
GET

POST

PUT

PATCH

DELETE
```

不要：

全部：

POST。

---

# Parameters

GET：

使用：

```typescript
params
```

POST：

使用：

```typescript
body
```

不要：

混用。

---

# Return Type

所有：

API：

必須：

定義：

Type。

例如：

```typescript
Promise<ApiResponse<Member>>
```

不要：

```typescript
Promise<any>
```

---

# Generic Response

建議：

```typescript
interface ApiResponse<T>{

    data:T

    message:string

}
```

避免：

每支：

API：

不同。

---

# DTO

API：

Response：

使用：

DTO。

例如：

```typescript
MemberDto
```

不要：

直接：

UI：

使用。

---

# Mapping

若：

DTO：

與：

UI：

不同。

建立：

Mapper。

例如：

```typescript
member.mapper.ts
```

不要：

Component：

自己：

轉換。

---

# Error Handling

API：

只：

Throw Error。

不要：

```typescript
alert()

toast()

modal()
```

UI：

自己：

決定：

如何：

顯示。

---

# Loading

API：

不要：

管理：

Loading。

Hook：

負責。

---

# Retry

只有：

安全：

Request。

例如：

```text
GET
```

可以：

Retry。

不要：

Create：

Delete：

自動：

Retry。

---

# File Upload

建立：

專用：

API。

例如：

```typescript
uploadAvatar()
```

不要：

混：

一般：

API。

---

# Pagination

建立：

共用：

Type。

例如：

```typescript
PaginatedResponse<T>
```

不要：

每支：

API：

自己：

定義。

---

# Search

Search：

參數：

集中：

Object。

例如：

```typescript
getMembers({

keyword,

page,

status

})
```

不要：

大量：

Position Parameter。

---

# API Naming

建議：

```text
get

list

create

update

delete

upload

download
```

不要：

```text
doMember

execute

action
```

---

# Import

Feature：

使用：

```typescript
import {

getMembers

}
```

不要：

直接：

Import：

Axios。

---

# Mock

API：

容易：

Mock。

因為：

全部：

集中。

---

# Testing

API：

可：

獨立：

測試。

Hook：

Mock API。

Component：

Mock Hook。

---

# Common Mistakes

避免：

- Component 呼叫 axios
- Hook 建立 Axios
- 多個 Axios Instance
- API 回傳 any
- API 顯示 UI
- 每支 API 自己加 Token
- Base URL 寫死
- Timeout 不一致
- DTO 與 UI 混用

---

# Best Practices

建議：

1. 全專案一個 Axios Client。
2. Feature 自己管理 API。
3. API 與 UI 分離。
4. Token 使用 Interceptor。
5. API 使用 Generic Response。
6. DTO 與 UI 分離。
7. API 不負責 Loading。
8. API 不負責 Toast。
9. API 回傳 Type。
10. Component 永不直接呼叫 Axios。

---

# Recommended Structure

```text
shared/

    api/

        client.ts

        auth.ts

        interceptor.ts

        error.ts

        types.ts

        index.ts



features/

    member/

        api/

            member.api.ts

            member.mapper.ts

            member.types.ts
```

---

# Checklist

建立新的 API 前：

- [ ] 使用 Shared Client
- [ ] 無 axios.create()
- [ ] Return Type 已定義
- [ ] DTO 已建立
- [ ] Mapper 已確認
- [ ] Error 已處理
- [ ] Token 使用 Interceptor
- [ ] 無 UI Logic
- [ ] 無 any
- [ ] 命名符合 REST

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