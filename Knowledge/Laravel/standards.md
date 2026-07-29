# Laravel Standards

## Purpose

本文件記錄 Laravel 開發的通用原則與最佳實務。

適用於：

* 新專案
* 舊專案維護
* 重構專案
* Side Project

本文件不包含專案特定規範。

專案相關規範應建立於各專案文件中。

---

# Core Principles

## Maintainability First

優先考慮：

1. 可維護性
2. 可讀性
3. 可測試性
4. 擴充性
5. 效能

避免為了提早優化而增加不必要複雜度。

---

## Keep It Simple

優先選擇：

* 簡單方案
* 容易理解方案
* 團隊容易維護方案

避免過度設計。

---

## Single Responsibility

每個類別應有單一職責。

避免：

* God Controller
* God Service
* God Helper

---

## Explicit Over Implicit

程式碼應容易閱讀。

優先：

* 清楚命名
* 明確流程
* 明確依賴

避免過度隱藏邏輯。

---

# Laravel Best Practices

## Follow Laravel Conventions

優先遵循 Laravel 官方慣例。

除非有充分理由，不要自行發明框架。

---

## Prefer Dependency Injection

優先使用：

* Constructor Injection
* Method Injection

降低耦合度。

---

## Prefer Service Container

善用：

* Service Container
* Interface Binding

提升可測試性。

---

## Configuration Management

避免：

```php
env('APP_KEY')
```

直接出現在業務程式碼中。

優先：

```php
config(...)
```

---

# Database

## Query Efficiency

查詢前思考：

* 是否有 Index
* 是否會產生 N+1 Query
* 是否需要 Cache

---

## Transaction Safety

涉及多筆資料修改時：

優先考慮 Transaction。

---

## Data Consistency

優先確保：

* 資料正確性
* 一致性

再考慮效能優化。

---

# Cache

## Cache Is Optimization

不要依賴 Cache 保持業務正確性。

Cache 的目的是：

* 提升效能
* 降低資料庫負載

---

## Cache Invalidation

設計 Cache 時：

必須同時思考：

* 建立時機
* 更新時機
* 清除時機

---

# Queue

## Async When Appropriate

耗時操作優先考慮：

* Queue
* Background Job

例如：

* Email
* Notification
* AI Request
* Report Generation

---

## Idempotent Design

Queue Job 應具備可重複執行能力。

避免因 Retry 導致資料異常。

---

# API

## Consistency

API 設計應保持一致。

包含：

* 命名
* 狀態碼
* 錯誤處理

---

## Validation First

所有外部輸入皆視為不可信。

應先驗證再處理。

---

# Security

## Never Trust User Input

所有輸入：

* Request
* Header
* Query String
* File Upload

皆須驗證。

---

## Principle of Least Privilege

僅授予必要權限。

避免過大權限設計。

---

# Logging

## Log Important Events

應記錄：

* Error
* Exception
* External API Failure
* Queue Failure

---

## Avoid Sensitive Data

避免記錄：

* Password
* Token
* Secret Key
* Personal Information

---

# Refactoring

## Refactor Incrementally

優先小步重構。

避免一次大規模改寫。

---

## Preserve Existing Behavior

重構目標：

改善程式品質。

不改變既有功能。

---

# Performance

## Measure Before Optimize

優化前先確認瓶頸。

避免憑感覺優化。

---

## Profile First

優先透過：

* Laravel Debugbar
* Telescope
* Explain
* Monitoring

確認問題來源。

---

# Learning Mindset

當遇到問題時：

優先思考：

1. 為什麼發生
2. 如何排查
3. 如何解決
4. 如何避免再次發生

並將經驗記錄至 Knowledge Base。

---

# Long-Term Goal

建立：

* 可維護系統
* 可擴充系統
* 可觀測系統

而不只是完成需求。
