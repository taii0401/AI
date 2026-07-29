# React Knowledge

## Purpose

本目錄記錄 React 在軟體開發中的通用知識、架構設計、最佳實踐、開發規範與常見問題。

本知識庫不屬於任何特定專案，可重複使用於不同的 React、TypeScript、Vite 或其他前端專案。

React Template 放置於：

```text
Templates/
```

React Project 放置於：

```text
Projects/
```

---

# Scope

目前主要適用於：

- React
- TypeScript
- Vite
- React Router
- React Hook Form
- Zod
- Axios
- Fetch API
- REST API
- Laravel API
- Feature-Based Architecture

未來可擴充：

- React Query
- TanStack Router
- Zustand
- Redux Toolkit
- Next.js
- React Native

---

# Goals

React Knowledge 的目標：

- 建立一致的 React 架構
- 建立一致的程式碼風格
- 建立一致的 Folder Structure
- 提高 Component 可重用性
- 降低維護成本
- 降低 AI Agent 誤判
- 提高多人協作效率

---

# Knowledge Structure

```text
React/
├── README.md
├── architecture.md
├── api-client.md
├── components.md
├── error-handling.md
├── folder-structure.md
├── forms-validation.md
├── routing.md
├── standards.md
├── state-management.md
├── testing.md
└── typescript.md
```

---

# File Description

## architecture.md

React 整體架構設計。

包含：

- React Architecture
- SPA Architecture
- Feature-Based Design
- Rendering Flow
- Data Flow
- Component Relationship

適用：

- 新專案規劃
- React 架構設計
- 系統重構

---

## folder-structure.md

React 專案資料夾規範。

包含：

- Feature-Based Structure
- Shared Modules
- Components
- Hooks
- Pages
- Types
- Utils

適用：

- 新專案
- 重構專案
- AI 自動建立目錄

---

## standards.md

React Coding Standard。

包含：

- Naming
- Import
- Export
- File Naming
- Component Rules
- Hooks Rules
- Code Style

適用：

- Code Review
- AI Coding
- 團隊協作

---

## components.md

Component 設計原則。

包含：

- Component Responsibility
- Props
- Composition
- Reusability
- Presentational Component
- Container Component

適用：

- UI Design
- Component Library
- Design System

---

## api-client.md

API 呼叫架構。

包含：

- API Client
- Axios
- Fetch
- REST API
- Error Handling
- Token
- Interceptor

適用：

- Laravel API
- Backend API
- 第三方 API

---

## routing.md

React Router 設計。

包含：

- Route Structure
- Nested Route
- Protected Route
- Lazy Route
- Route Naming

適用：

- SPA
- Dashboard
- Admin System

---

## state-management.md

State 管理策略。

包含：

- Local State
- Global State
- Server State
- Context
- Props
- Custom Hook

適用：

- 小型專案
- 中大型專案

---

## forms-validation.md

表單設計。

包含：

- React Hook Form
- Zod
- Validation
- Dynamic Form
- Error Message

適用：

- CRUD
- Login
- Search
- Admin System

---

## error-handling.md

錯誤處理策略。

包含：

- API Error
- Validation Error
- Error Boundary
- Loading
- Empty State
- Retry

適用：

- 全部 React 專案

---

## testing.md

React 測試策略。

包含：

- Unit Test
- Integration Test
- Component Test
- E2E
- Mock API

適用：

- CI
- Code Review

---

## typescript.md

TypeScript 最佳實踐。

包含：

- Interface
- Type Alias
- Generic
- API Type
- Props
- Utility Types

適用：

- 全部 React 專案

---

# Design Principles

React Knowledge 應遵循以下原則。

---

## 1. Project Independent

Knowledge 不應包含任何專案資訊。

例如：

```text
Hopkins Helper

AI Task Manager
```

不應直接出現在 Knowledge。

---

## 2. Framework Focused

React Knowledge：

只描述：

React

TypeScript

Frontend

相關知識。

Laravel：

應放：

Laravel Knowledge。

Docker：

應放：

Docker Knowledge。

---

## 3. Reusable

所有內容：

應：

可重複使用。

例如：

- Component Design
- API Design
- Routing
- Folder Structure
- Error Handling

都是：

通用知識。

---

## 4. Best Practice

Knowledge：

優先紀錄：

- Design Pattern
- Architecture
- Standard
- Checklist
- Best Practice

避免：

記錄：

某一次：

Bug。

Bug：

應整理成：

```text
Problem

↓

Root Cause

↓

Solution

↓

Prevention
```

---

## 5. Feature-Based

React：

建議：

使用：

Feature-Based Architecture。

而不是：

Page-Based。

例如：

```text
features/

shared/

components/
```

---

## 6. Separation of Concerns

React：

應將：

UI

Business Logic

API

State

Validation

Routing

拆開。

不要：

全部：

寫在：

Component。

---

## 7. Composition Over Inheritance

React：

應優先：

Composition。

不要：

建立：

複雜：

Inheritance。

---

## 8. Type Safety

React：

應：

搭配：

TypeScript。

避免：

```text
any
```

---

# Relationship

React Knowledge：

與其它 Knowledge：

關係：

```text
Knowledge

├── Architecture

├── Docker

├── Laravel

├── React

├── Redis

└── Security
```

React：

負責：

Frontend。

Laravel：

負責：

Backend。

Docker：

負責：

Runtime。

---

# Usage

建立：

React：

新專案：

```text
閱讀 README

↓

閱讀 architecture

↓

閱讀 folder-structure

↓

閱讀 standards

↓

建立 Project
```

新增功能：

```text
閱讀 Component Standard

↓

閱讀 API Standard

↓

閱讀 Form Standard

↓

開始開發
```

---

# Maintenance

新增知識：

建議：

一個主題：

一個：

Markdown。

例如：

```text
hooks.md

performance.md

authentication.md
```

避免：

README：

越來越大。

---

# Best Practices

建議：

1. 使用 TypeScript。
2. 使用 Feature-Based Architecture。
3. Component 保持單一責任。
4. API 與 UI 分離。
5. Business Logic 抽離至 Hook 或 Service。
6. Props 型別完整定義。
7. 不使用 any。
8. Component 保持可重用。
9. 保持 Folder Structure 一致。
10. 文件與程式同步更新。

---

# Checklist

建立新的 React 專案前：

- [ ] 已閱讀 README
- [ ] 已確認 Architecture
- [ ] 已確認 Folder Structure
- [ ] 已確認 Coding Standard
- [ ] 已確認 API Strategy
- [ ] 已確認 Routing Strategy
- [ ] 已確認 State Strategy
- [ ] 已確認 Validation Strategy
- [ ] 已確認 Error Handling Strategy
- [ ] 已確認 Testing Strategy

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