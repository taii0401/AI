# Error Handling Standards

## Purpose

本文件定義 React 專案中的 Error Handling 架構、錯誤分類、處理流程與最佳實踐。

目的：

- 建立一致的 Error Flow
- UI 與 Error 分離
- API Error 集中管理
- 降低重複錯誤處理
- 提高可維護性
- 提高 AI Agent 可理解性

本文件不綁定任何特定專案。

---

# Goals

Error Handling 應符合：

- Predictable
- Recoverable
- User Friendly
- Traceable
- Testable
- Maintainable

---

# Design Principles

React Error Handling 應遵循：

- Error 分層
- UI 與 Error 分離
- API 不顯示 UI
- Hook 不顯示 UI
- Component 負責呈現
- Error 可追蹤

---

# Error Architecture

建議：

```text
User

↓

Component

↓

Hook

↓

API

↓

Laravel API

↓

Error

↓

Hook

↓

Page

↓

Error UI
```

Error：

不要：

直接：

由：

API：

顯示。

---

# Error Categories

建議：

分成：

```text
Validation Error

↓

Authentication Error

↓

Authorization Error

↓

Business Error

↓

Network Error

↓

Unexpected Error
```

不要：

全部：

當：

Exception。

---

# Validation Error

例如：

HTTP：

```text
422
```

例如：

```text
Email 已存在

姓名必填
```

由：

Form：

顯示。

不要：

Toast。

---

# Authentication Error

例如：

```text
401
```

流程：

```text
Token 過期

↓

重新登入

↓

導向 Login
```

不要：

每個：

Page：

自己：

判斷。

---

# Authorization Error

例如：

```text
403
```

顯示：

```text
無權限
```

不要：

重新登入。

---

# Not Found

例如：

```text
404
```

建議：

導向：

404 Page。

---

# Conflict

例如：

```text
409
```

例如：

```text
資料已更新

版本衝突
```

顯示：

Business Error。

---

# Server Error

例如：

```text
500
```

顯示：

```text
系統發生錯誤
```

不要：

直接：

顯示：

Stack Trace。

---

# Network Error

例如：

- Timeout
- Offline
- DNS
- Connection Refused

建議：

Toast：

```text
網路連線異常
```

---

# Unexpected Error

例如：

JavaScript：

Exception。

應：

Error Boundary。

不要：

整個：

React：

Crash。

---

# Error Flow

建議：

```text
API

↓

Throw Error

↓

Hook

↓

Page

↓

UI
```

不要：

API：

直接：

Toast。

---

# API Responsibility

API：

負責：

- Throw Error
- Mapping
- HTTP Status

不要：

UI。

---

# Hook Responsibility

Hook：

負責：

- Retry
- Loading
- State
- Error

不要：

Alert。

---

# Component Responsibility

Component：

只：

Render。

例如：

```text
Error Message

Toast

Dialog
```

---

# Error Boundary

App：

應：

建立：

```text
ErrorBoundary
```

避免：

React：

全部：

Crash。

---

# Error Page

至少：

提供：

```text
403

404

500
```

不要：

全部：

回：

首頁。

---

# Loading

Loading：

與：

Error：

分開。

例如：

```text
Loading

↓

Error

↓

Success
```

不要：

混：

一起。

---

# Empty State

沒有：

資料：

不是：

Error。

例如：

```text
查無資料
```

不要：

Toast。

---

# Retry

Retry：

只：

適合：

安全：

Request。

例如：

```text
GET
```

不要：

Create：

Delete：

自動：

Retry。

---

# Toast

Toast：

適合：

```text
Save Success

Delete Success

Network Error
```

不要：

Validation。

---

# Inline Error

Validation：

使用：

Inline。

例如：

```text
Email：

已存在
```

---

# Dialog

重大：

Error。

例如：

```text
刪除失敗

資料衝突
```

可：

Dialog。

---

# Console

Production：

不要：

大量：

console.log()。

應：

集中：

Logger。

---

# Logging

Error：

建議：

送：

Monitoring。

例如：

```text
Sentry

Datadog

LogRocket
```

---

# Error Codes

Frontend：

不要：

判斷：

Message。

建議：

Backend：

回：

```text
Code
```

例如：

```text
MEMBER_EXISTS

PERMISSION_DENIED
```

不要：

```text
會員已存在
```

---

# Error Mapping

建立：

```text
error.mapper.ts
```

負責：

Code

↓

UI。

不要：

每個：

Component：

自己：

Switch。

---

# User Messages

User：

看到：

```text
儲存失敗
```

不要：

```text
SQLSTATE 23000
```

---

# Developer Messages

Log：

保留：

完整：

Error。

User：

不要：

看到。

---

# API Response

建議：

```json
{
    "code":"VALIDATION_ERROR",
    "message":"Validation failed",
    "errors":{

    }
}
```

Frontend：

依：

Code：

處理。

---

# Common Mistakes

避免：

- alert(error)
- console.log(error)
- API 顯示 Toast
- Component try/catch
- 判斷 Message
- Validation 使用 Toast
- 所有 Error 都 Dialog
- 500 顯示 Stack Trace
- 無 Error Boundary

---

# Best Practices

建議：

1. Error 分層。
2. API 只 Throw。
3. Hook 管理 Error。
4. Component Render Error。
5. Validation 使用 Inline。
6. API Error 使用 Toast。
7. Unexpected Error 使用 Error Boundary。
8. Backend 回傳 Error Code。
9. 建立 Error Mapper。
10. Error 可追蹤。

---

# Error Decision Table

| Error | UI |
|---------|----------------|
| Validation | Inline |
| 401 | Redirect Login |
| 403 | Forbidden Page |
| 404 | Not Found Page |
| 409 | Dialog / Inline |
| 500 | Error Page |
| Network | Toast |
| JS Exception | Error Boundary |

---

# Checklist

建立新的 Feature 前：

- [ ] Validation Error
- [ ] API Error
- [ ] Loading
- [ ] Empty State
- [ ] Retry
- [ ] Error Boundary
- [ ] Error Mapping
- [ ] Error Code
- [ ] Monitoring
- [ ] User Message

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