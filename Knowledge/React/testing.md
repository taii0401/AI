# React Testing Standards

## Purpose

本文件定義 React 專案中的測試策略、測試層級、測試範圍與最佳實踐。

目的：

- 建立一致的測試方式
- 提高程式可靠性
- 降低 Regression Risk
- 提高 AI Agent 可理解性
- 建立可維護的測試架構

本文件不綁定任何特定專案。

---

# Goals

Testing 應符合：

- Predictable
- Repeatable
- Independent
- Maintainable
- Fast
- Reliable

---

# Design Principles

React Testing 應遵循：

- 測試行為，不測實作
- 測試使用者操作
- Component 與 Hook 分開測
- Mock 外部依賴
- 每個測試彼此獨立

---

# Testing Pyramid

建議：

```text
        E2E

   Integration

Unit Test
```

比例：

```text
大量 Unit

↓

少量 Integration

↓

少量 E2E
```

---

# Test Categories

建議：

```text
Unit Test

↓

Component Test

↓

Hook Test

↓

Integration Test

↓

E2E
```

---

# Unit Test

測試：

純函式。

例如：

```text
formatDate()

calculateTotal()

mapper()

validator()
```

不需要：

Render React。

---

# Component Test

測試：

Component。

例如：

```text
Button

Dialog

MemberTable

MemberForm
```

確認：

- Render
- Click
- Props
- Loading
- Error

---

# Hook Test

測試：

```text
useMember()

usePagination()

useDebounce()
```

確認：

Business Logic。

不要：

測：

UI。

---

# Integration Test

測試：

```text
Form

↓

Hook

↓

API Mock
```

確認：

流程。

---

# E2E Test

測試：

完整：

使用者流程。

例如：

```text
登入

↓

建立會員

↓

搜尋

↓

修改

↓

刪除
```

---

# Recommended Tools

建議：

```text
Vitest

React Testing Library

Mock Service Worker

Playwright
```

---

# Folder Structure

建議：

```text
features/

    member/

        __tests__/

            MemberTable.test.tsx

            useMember.test.ts

            member.api.test.ts
```

不要：

全部：

放：

tests/

---

# Naming

建議：

```text
Component.test.tsx

Hook.test.ts

Utils.test.ts
```

---

# What To Test

應測：

- Business Logic
- User Behavior
- Error Handling
- Validation
- Permission
- Loading
- Empty State

---

# What Not To Test

不要：

測：

- React 自己
- 第三方 Library
- CSS
- HTML 結構細節
- useState 本身

---

# User Perspective

測試：

例如：

```text
使用者是否能送出表單
```

不要：

```text
是否呼叫 setState()
```

---

# Component Test

確認：

- Render
- Props
- Click
- Disabled
- Loading
- Error
- Empty

---

# Hook Test

確認：

- State
- Business Logic
- Loading
- Error
- Retry

---

# API

API：

使用：

Mock。

不要：

真的：

呼叫：

Backend。

---

# Mock

建議：

MSW。

不要：

每個：

Test：

自己：

Mock axios。

---

# Snapshot

不要：

大量：

Snapshot。

只有：

穩定：

UI。

---

# Accessibility

可：

測：

```text
Button

Label

Role

Aria
```

---

# Error Test

確認：

```text
500

404

422

Network Error
```

都：

正常。

---

# Loading Test

確認：

Loading：

會：

出現。

結束：

會：

消失。

---

# Empty State

確認：

沒有：

資料。

畫面：

正常。

---

# Permission

確認：

沒有：

Permission。

畫面：

正常。

---

# Form Test

確認：

- Required
- Validation
- Submit
- Reset
- API Error

---

# Routing

確認：

Route：

正確。

例如：

```text
404

Protected Route
```

---

# Fake Data

建立：

Factory。

例如：

```text
MemberFactory

AppointmentFactory
```

不要：

每個：

Test：

自己：

建立。

---

# Test Data

避免：

Magic Number。

例如：

```typescript
const member=

MemberFactory.create()
```

---

# Independent

每個：

Test：

獨立。

不要：

依賴：

上一個。

---

# Cleanup

每次：

Test：

Reset。

不要：

共享：

State。

---

# Coverage

重點：

Coverage：

不是：

100%。

而是：

重要：

Business。

---

# AI Generated Tests

AI：

產生：

Test：

應：

確認：

- Business Rule
- Error
- Loading
- Empty
- Success

不要：

只有：

Happy Path。

---

# Common Mistakes

避免：

- 測 React
- 測 CSS
- Snapshot 過多
- 真正呼叫 API
- 共用 State
- Test 順序依賴
- Magic Data
- Happy Path Only
- Mock 過多

---

# Best Practices

建議：

1. 測行為。
2. Hook 與 Component 分開。
3. API 使用 Mock。
4. 使用 Factory。
5. Loading 必測。
6. Error 必測。
7. Empty 必測。
8. Validation 必測。
9. Permission 必測。
10. E2E 測核心流程。

---

# Testing Decision Table

| 功能 | 建議測試 |
|-------|-----------|
| formatDate | Unit |
| MemberTable | Component |
| useMember | Hook |
| Login Form | Integration |
| 建立會員流程 | E2E |
| API Mapper | Unit |
| Route Guard | Integration |
| Button | Component |
| Modal | Component |
| Pagination | Hook |

---

# Checklist

建立新的 Feature 前：

- [ ] Unit Test
- [ ] Component Test
- [ ] Hook Test
- [ ] Validation Test
- [ ] Error Test
- [ ] Loading Test
- [ ] Empty Test
- [ ] Permission Test
- [ ] API Mock
- [ ] Factory 已建立

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