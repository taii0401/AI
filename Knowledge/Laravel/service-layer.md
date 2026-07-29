# Laravel Service Layer

## Purpose

本文件記錄 Service Layer 的通用設計原則與使用時機。

Service Layer 的目的不是讓專案變複雜，而是把商業邏輯從 Controller、Model、Repository 中分離出來，讓程式更容易維護、測試與擴充。

本文件不包含特定專案規範。

---

# What Is Service Layer

Service Layer 是負責處理商業流程與應用邏輯的層級。

常見位置：

```text
app/
└── Services/
```

Service 通常負責：

* 商業邏輯
* 流程控制
* 多個 Repository 協作
* 第三方 API 整合
* Transaction 控制
* Queue / Job 派送前的流程整理

---

# When To Use Service

適合使用 Service 的情境：

* Controller 開始變胖
* 一個流程需要操作多個 Model
* 一個功能包含多個步驟
* 需要呼叫第三方 API
* 需要處理 Transaction
* 同一段邏輯被多個地方重複使用
* 功能未來可能擴充

---

# When Not To Use Service

不一定需要 Service 的情境：

* 單純 CRUD
* 只有一行 Query
* 沒有商業邏輯
* 沒有重複使用需求
* 建立 Service 反而讓流程更難懂

原則：

不要為了套架構而建立 Service。

---

# Responsibility

Service 應該負責「做什麼」與「流程怎麼跑」。

例如：

* 建立訂單
* 取消訂單
* 發送通知
* 計算價格
* 建立付款流程
* 整理 AI 回覆流程

Service 不應該負責：

* 接收 HTTP Request
* 回傳 HTTP Response
* 直接處理 View
* 放大量 SQL 細節
* 處理太多不相關功能

---

# Basic Flow

建議流程：

```text
Controller
↓
Service
↓
Repository / Model
↓
Database
```

Controller 負責接收請求。

Service 負責商業流程。

Repository 或 Model 負責資料存取。

---

# Naming

Service 命名應清楚表達用途。

建議：

```text
OrderService
PaymentService
NotificationService
AiTaskService
LineMessageService
```

避免：

```text
CommonService
HelperService
DataService
ProcessService
```

如果 Service 名稱太模糊，通常代表職責不清楚。

---

# Method Naming

Method 命名應描述業務動作。

建議：

```php
createOrder()
cancelOrder()
sendNotification()
calculateTotal()
generateTaskSuggestions()
```

避免：

```php
handle()
process()
run()
doSomething()
```

除非該 Service 本身就是單一用途。

---

# Transaction

涉及多筆資料異動時，Service 是適合控制 Transaction 的地方。

範例：

```php
DB::transaction(function () use ($data) {
    // 建立主資料
    // 建立明細資料
    // 更新狀態
});
```

原則：

* Transaction 範圍要清楚
* 不要在 Transaction 裡執行太慢的外部 API
* 不要在 Transaction 裡放不必要邏輯

---

# External API

第三方 API 整合適合放在 Service 或專門的 Client 類別。

建議：

```text
Services/
├── LineMessageService
├── OpenAiService
└── PaymentService
```

若第三方整合邏輯變複雜，可再拆成：

```text
Clients/
├── LineClient
├── OpenAiClient
└── PaymentClient
```

Service 負責流程。

Client 負責實際呼叫外部 API。

---

# Queue

Service 可以負責判斷是否需要派送 Job。

例如：

```php
SendNotificationJob::dispatch($userId);
```

原則：

* Service 決定業務流程
* Job 處理背景任務
* Job 應盡量保持可重複執行

---

# Avoid God Service

避免一個 Service 處理太多事情。

警訊：

* Service 超過數百行
* Method 太多
* 負責多個不相關模組
* 名稱過於籠統
* 修改任何功能都會動到同一個 Service

解法：

* 依業務流程拆分
* 依功能模組拆分
* 將第三方 API 呼叫拆到 Client
* 將複雜計算拆到專門類別

---

# Service vs Repository

Service 負責商業流程。

Repository 負責資料存取。

簡單區分：

```text
Service：這個功能要怎麼完成
Repository：資料要怎麼查、怎麼存
```

不要把大量 SQL 細節放進 Service。

也不要把商業流程塞進 Repository。

---

# Service vs Helper

Service 有明確業務意義。

Helper 通常是通用工具。

例如：

適合 Service：

```text
OrderService
PaymentService
LineMessageService
```

適合 Helper：

```text
DateHelper
StringHelper
ArrayHelper
```

若 Helper 開始出現業務邏輯，通常應該改成 Service。

---

# Common Mistakes

常見錯誤：

1. 為了架構而過度拆分
2. 把所有邏輯都塞進單一 Service
3. Service 直接處理 Request / Response
4. Service 裡面寫大量複雜 Query
5. Service 名稱太籠統
6. Service 沒有明確職責
7. Transaction 範圍過大
8. 在 Transaction 中呼叫外部 API

---

# Review Checklist

建立或修改 Service 時，請檢查：

* 這個 Service 是否有明確職責？
* Method 名稱是否清楚？
* 是否把 HTTP 邏輯留在 Controller？
* 是否避免大量 SQL 細節？
* 是否需要 Transaction？
* 是否有過度設計？
* 是否容易測試？
* 未來擴充時是否容易修改？

---

# Long-Term Goal

Service Layer 的目標是讓 Laravel 專案更容易維護，而不是讓架構看起來更複雜。

好的 Service 應該讓人一看就知道：

* 這個功能在做什麼
* 流程怎麼跑
* 商業邏輯在哪裡
* 未來要修改哪個地方