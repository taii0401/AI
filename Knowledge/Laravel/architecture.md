# Laravel Architecture

## Purpose

本文件記錄 Laravel 架構設計的通用原則與思考方式。

目標不是追求複雜架構，而是協助 Laravel 專案在不同階段保持：

* 可維護性
* 可擴充性
* 可測試性
* 可讀性
* 可演進性

本文件不包含專案特定規範。

---

# Architecture Philosophy

## Start Simple

Laravel 專案一開始應保持簡單。

優先選擇：

* Laravel 原生慣例
* 清楚的目錄結構
* 容易理解的流程
* 最少必要抽象

避免一開始就導入過多架構模式。

---

## Evolve When Needed

架構應隨需求成長而演進。

常見演進方向：

```text
Simple MVC
↓
Service Layer
↓
Repository / Query Object
↓
Modular Monolith
↓
Event Driven
↓
Microservice
```

不要在需求尚未明確前過度設計。

---

# Common Architecture Styles

## Simple MVC

適合：

* 小型專案
* MVP
* 後台工具
* 單純 CRUD

特點：

* Controller
* Model
* View / API Resource

優點：

* 開發快速
* 簡單直覺
* 符合 Laravel 慣例

缺點：

* Controller 容易變胖
* 商業邏輯容易散落
* 長期維護成本可能上升

---

## Service Layer

適合：

* 商業流程開始變複雜
* Controller 過胖
* 同一段邏輯被重複使用
* 需要整合第三方服務

角色：

```text
Controller
↓
Service
↓
Model / Repository
```

重點：

* Controller 處理 HTTP
* Service 處理商業流程
* Model / Repository 處理資料

---

## Repository Pattern

適合：

* Query 複雜
* 查詢邏輯重複
* 需要封裝資料存取
* Service 不應知道太多 SQL 細節

角色：

```text
Service
↓
Repository
↓
Model / Query Builder
```

注意：

Repository 不是每個專案都必須使用。

---

## Query Object

適合：

* 報表查詢
* 多條件搜尋
* 多表 Join
* 統計查詢
* Repository 開始過胖

範例：

```text
Queries/
├── AppointmentReportQuery
├── PaymentSummaryQuery
└── UserSearchQuery
```

Query Object 可以讓複雜查詢更容易維護。

---

## Modular Monolith

適合：

* 專案開始變大
* 模組邊界逐漸清楚
* 尚未需要 Microservice
* 團隊仍希望保持單一部署

範例：

```text
Modules/
├── User/
├── Task/
├── Payment/
└── Notification/
```

優點：

* 邊界清楚
* 仍維持單一系統
* 部署簡單
* 比 Microservice 成本低

---

## Event Driven Architecture

適合：

* 功能之間需要解耦
* 背景任務多
* 通知、Email、AI 處理、報表產生等非同步流程
* 某些流程不需要即時完成

範例：

```text
OrderCreated
↓
SendNotification
↓
UpdateReport
↓
SyncExternalService
```

注意：

Event Driven 可提升解耦，但也會增加追蹤與除錯成本。

---

## Microservice

適合：

* 系統規模大
* 團隊多人協作
* 模組需要獨立部署
* 不同服務有不同擴充需求
* 已有成熟 DevOps 能力

不適合：

* MVP
* 小型團隊
* 還沒有清楚模組邊界
* 只是為了看起來先進

原則：

Microservice 是組織與系統規模成長後的結果，不是起點。

---

# Layering Principle

常見分層：

```text
HTTP Layer
↓
Application Layer
↓
Domain / Business Logic
↓
Data Access Layer
↓
Infrastructure
```

在 Laravel 中可對應為：

```text
Controller
↓
Service
↓
Domain / Action / Use Case
↓
Repository / Model
↓
Database / External Service
```

分層目的：

* 降低耦合
* 提升可測試性
* 讓責任清楚
* 避免邏輯散落

---

# Boundary Design

架構設計的核心是邊界。

常見邊界：

* Module Boundary
* Service Boundary
* Database Boundary
* External API Boundary
* Queue Boundary

設計邊界時應思考：

* 這段邏輯屬於哪個模組？
* 未來是否可能獨立？
* 是否會被多個地方使用？
* 是否有外部依賴？
* 是否需要非同步處理？

---

# Dependency Direction

依賴方向應盡量單向。

建議：

```text
Controller → Service → Repository → Model
```

避免：

```text
Model → Controller
Repository → Controller
Service A ↔ Service B
```

循環依賴會讓系統難以維護。

---

# Business Logic Placement

商業邏輯不應散落在：

* Controller
* Blade
* Middleware
* Helper
* Migration
* Seeder

應根據情境放在：

* Service
* Action
* Domain Class
* Job
* Event Listener

---

# Action / Use Case

當某個業務動作很明確時，可以考慮使用 Action 或 Use Case。

例如：

```text
CreateOrderAction
CancelOrderAction
GenerateTaskSuggestionAction
SendLineMessageAction
```

適合：

* 單一明確行為
* 邏輯不適合塞進大型 Service
* 想降低 Service 肥大問題

---

# Job / Queue

非同步任務適合放入 Job。

例如：

* 發送 Email
* 發送 LINE 訊息
* 呼叫 AI API
* 匯出報表
* 同步外部系統

Job 設計重點：

* 可重試
* 可追蹤
* 可觀測
* 最好具備冪等性

---

# Event / Listener

Event 適合用來表達「某件事已經發生」。

例如：

```text
UserRegistered
OrderPaid
TaskCompleted
MessageReceived
```

Listener 負責後續反應。

例如：

```text
SendWelcomeEmail
UpdateStatistics
PushNotification
```

使用 Event 的原則：

* Event 不應知道 Listener
* Listener 不應改變 Event 事實
* 避免事件流程過度隱性

---

# Cache Strategy

Cache 是效能優化，不是業務正確性的來源。

設計 Cache 時應考慮：

* Cache Key
* TTL
* 失效時機
* 資料一致性
* 是否需要 Redis

避免：

* 不清楚何時清除 Cache
* 將重要狀態只存在 Cache
* Cache 與 DB 資料長期不一致

---

# External Integration

第三方服務整合應隔離。

例如：

```text
OpenAI
LINE
Facebook
Payment Gateway
AWS
```

建議：

```text
Service
↓
Client / Adapter
↓
External API
```

目的：

* 降低耦合
* 容易替換服務
* 容易 Mock 測試
* 避免外部 API 細節污染業務邏輯

---

# Architecture Smells

常見架構異味：

* Controller 過胖
* Service 變成 God Service
* Helper 放大量業務邏輯
* Repository 放商業流程
* Model 塞太多查詢
* Queue Job 無法重試
* Event 流程難以追蹤
* Cache 失效規則不清楚
* 第三方 API 呼叫散落各處
* 報表查詢直接卡住主流程

---

# Refactoring Direction

重構時優先處理：

1. 重複邏輯
2. 過胖 Controller
3. 複雜 Query
4. 不清楚的流程
5. 高風險外部整合
6. 無法測試的商業邏輯

重構原則：

* 小步修改
* 保留原有行為
* 先補 Log 或測試
* 不要一次改太多層

---

# Architecture Decision

做架構決策時，請比較：

* 開發成本
* 維護成本
* 測試成本
* 部署成本
* 未來擴充性
* 團隊理解成本

不要只因為技術看起來先進就導入。

---

# Common Mistakes

常見錯誤：

1. MVP 一開始就做 Microservice
2. 小功能過度拆分
3. 沒有模組邊界
4. 所有東西都塞進 Service
5. Event 太多導致流程不可追蹤
6. Queue 沒有 Retry 與冪等設計
7. Cache 沒有失效策略
8. Repository 只包一行 Model Query
9. 外部 API 呼叫散落各處
10. 架構文件與實際程式不一致

---

# Review Checklist

設計或重構架構時，請檢查：

* 是否符合目前系統規模？
* 是否解決真實問題？
* 是否過度設計？
* 是否容易理解？
* 是否容易測試？
* 是否容易維護？
* 是否有清楚邊界？
* 是否降低耦合？
* 是否增加不必要複雜度？
* 是否適合目前團隊能力？

---

# Long-Term Goal

Laravel 架構設計的目標不是追求模式，而是建立可長期維護的系統。

好的架構應該讓系統：

* 容易理解
* 容易修改
* 容易測試
* 容易擴充
* 容易除錯

最終目標：

從「功能能動」進步到「系統能長期演進」。