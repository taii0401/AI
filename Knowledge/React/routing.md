# React Routing Standards

## Purpose

本文件定義 React SPA 的 Routing 架構、Route 設計、Layout 分層、權限控制與最佳實踐。

目的：

- 建立一致的 Route 架構
- 提高可維護性
- 提高可擴充性
- 降低 Routing 耦合
- 建立 AI Agent 可理解的 Routing Strategy

本文件不綁定任何特定專案。

---

# Goals

Routing 應符合：

- Predictable
- Scalable
- Secure
- Lazy Load
- Nested
- Type Safe

---

# Design Principles

React Routing 應遵循：

- Route 只負責 Navigation
- Business Logic 不放 Router
- Permission 與 Route 分離
- Layout 與 Page 分離
- 採用 Nested Route
- 採用 Feature-Based Routing

---

# Routing Architecture

建議：

```text
Browser

↓

React Router

↓

Layout

↓

Page

↓

Feature

↓

Component
```

Router：

只決定：

去哪裡。

不要：

處理：

Business Logic。

---

# Recommended Structure

```text
src/

routes/

    index.tsx

    public.tsx

    protected.tsx

    admin.tsx

    error.tsx

features/

shared/

app/
```

---

# Route Responsibilities

Router：

負責：

- URL
- Layout
- Lazy Loading
- Nested Route
- Route Guard

Router：

不負責：

- API
- Permission Logic
- Business Rule
- Validation

---

# Route Categories

建議：

```text
Public Route

↓

Authenticated Route

↓

Admin Route

↓

Error Route
```

---

# Public Route

例如：

```text
/

/login

/register

/forgot-password
```

不需要：

登入。

---

# Protected Route

例如：

```text
/appointments

/members

/profile
```

需要：

Authentication。

---

# Admin Route

例如：

```text
/admin

/admin/users

/admin/settings
```

需要：

Admin Permission。

---

# Error Route

例如：

```text
/403

/404

/500
```

---

# Route Naming

全部：

使用：

```text
kebab-case
```

例如：

```text
/member-list

/medical-record

/new-customer
```

不要：

```text
/MemberList

/member_list
```

---

# REST Style URL

Detail：

使用：

```text
/member/:id
```

不要：

```text
/member?id=10
```

若：

資源：

唯一。

---

# CRUD Routes

建議：

```text
/members

/members/create

/members/:id

/members/:id/edit
```

不要：

```text
/member-edit

/member-update
```

---

# Nested Route

例如：

```text
/settings

    /profile

    /security

    /notification
```

Router：

應：

支援：

巢狀。

---

# Layout

建議：

```text
Root Layout

↓

Dashboard Layout

↓

Page
```

不要：

每個：

Page：

自己：

建立：

Sidebar。

---

# Layout Structure

例如：

```text
layouts/

    RootLayout

    DashboardLayout

    AuthLayout

    EmptyLayout
```

---

# Lazy Loading

大型：

Page：

建議：

```text
React.lazy()
```

搭配：

```text
Suspense
```

不要：

首頁：

全部：

Import。

---

# Route Guard

Route Guard：

只：

判斷：

是否：

允許：

進入。

例如：

```text
Authenticated

↓

Allowed

↓

Render
```

不要：

放：

Business Rule。

---

# Authentication

登入：

失效：

導向：

```text
/login
```

不要：

每個：

Component：

自己：

判斷。

---

# Authorization

Role：

Permission：

建議：

Route Meta。

例如：

```text
Admin

Manager

Doctor
```

不要：

散落：

Component。

---

# Breadcrumb

Breadcrumb：

由：

Route：

產生。

不要：

每個：

Page：

自己：

建立。

---

# Navigation

Menu：

建議：

依：

Route。

不要：

重複：

維護。

---

# Scroll Restoration

換頁：

建議：

回到：

Top。

特殊：

頁面：

可：

保留：

Scroll。

---

# Dynamic Route

例如：

```text
/member/:memberId

/order/:orderId
```

不要：

Magic String。

---

# Query Parameters

搜尋：

Filter：

使用：

```text
?keyword=

?page=

?status=
```

不要：

放：

Path。

---

# Route Constants

建立：

```text
routes.ts
```

例如：

```typescript
export const Routes={

member:"/members"

}
```

不要：

大量：

Hard Code。

---

# Route Metadata

每個：

Route：

建議：

包含：

```text
Title

Permission

Breadcrumb

Layout
```

方便：

管理。

---

# Route File

建議：

一個：

Router：

一個：

檔案。

例如：

```text
public.tsx

protected.tsx

admin.tsx
```

不要：

全部：

500 行。

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

導：

首頁。

---

# Loading Route

Lazy：

Route：

建議：

Loading。

不要：

白畫面。

---

# SEO

SPA：

若：

需要：

SEO。

建議：

設定：

Title。

例如：

```text
document.title
```

或：

Helmet。

---

# Common Mistakes

避免：

- Router 呼叫 API
- Router 判斷 Business Rule
- URL 使用 PascalCase
- Route Hard Code
- Page 自己建立 Layout
- Component 自己 Redirect
- 無 404
- 無 Lazy Loading
- Route 超過數百行

---

# Best Practices

建議：

1. 採用 Nested Route。
2. 採用 Layout。
3. Route Guard 集中管理。
4. Route Constants。
5. Feature-Based Routing。
6. Lazy Loading。
7. Breadcrumb 由 Route 產生。
8. Menu 與 Route 同步。
9. Error Page 完整。
10. Route 保持單一責任。

---

# Checklist

建立新的 Route 前：

- [ ] URL 命名正確
- [ ] Layout 已確認
- [ ] Permission 已確認
- [ ] Lazy Loading 已評估
- [ ] Breadcrumb 已設定
- [ ] Route Constants 已更新
- [ ] 404 已確認
- [ ] Loading 已確認
- [ ] Navigation 已更新
- [ ] 無 Business Logic

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