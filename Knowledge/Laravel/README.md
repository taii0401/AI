# Laravel Knowledge 

## Purpose

此目錄用於整理 Laravel 相關知識、實務經驗、架構思維、效能優化與問題排查紀錄。

本知識庫不綁定任何特定專案。

內容應以：

* 可重複使用
* 可跨專案參考
* 可長期維護

為原則。

---

# Scope

本目錄主要記錄：

* Laravel Framework
* PHP 開發經驗
* 系統架構思維
* 效能優化
* Redis 整合
* Queue 設計
* API 設計
* 第三方服務整合
* 問題排查經驗

---

# Default Development Environment

除非專案有特別需求，Laravel 專案預設採用以下環境：

- PHP 8.3+
- Laravel 最新穩定版
- MySQL
- Redis
- PHPUnit / Pest
- Composer
- React

# Directory Structure

```text
Laravel/
│
├── README.md
│
├── Architecture/
├── API/
├── Database/
├── Redis/
├── Queue/
├── Performance/
├── Security/
├── Integrations/
├── Refactoring/
└── Troubleshooting/
```

---

# Folder Description

## Architecture

記錄架構相關知識。

例如：

* Monolith
* Modular Monolith
* Event Driven
* Domain Design
* Cache Strategy

---

## API

記錄 API 設計相關知識。

例如：

* RESTful API
* Pagination
* Validation
* Error Handling
* API Versioning

---

## Database

預設資料庫：

- MySQL

建立新專案時：

1. `.env` 預設使用 MySQL。
2. 不要改成 SQLite。
3. Migration 必須相容 MySQL。
4. Query Optimization 以 MySQL 為基準。
5. 若需 PostgreSQL 或 SQLite，需經需求確認。

---

## Redis

記錄 Redis 使用經驗。

例如：

* Cache
* Lock
* Rate Limiter
* Debounce
* Distributed Lock

---

## Queue

記錄 Queue 設計經驗。

例如：

* Worker Design
* Retry Strategy
* Delay Queue
* Dead Letter Queue
* Idempotent Design

---

## Performance

記錄效能優化經驗。

例如：

* N+1 Query
* Eager Loading
* Cache Strategy
* API Optimization

---

## Security

記錄安全相關知識。

例如：

* Authentication
* Authorization
* JWT
* API Security
* Webhook Verification

---

## Integrations

記錄第三方服務整合。

例如：

* LINE Bot
* Facebook
* OpenAI
* Payment Gateway
* AWS Services

---

## Refactoring

記錄重構案例。

內容包含：

* 重構原因
* 問題分析
* 解決方案
* 重構成果

---

## Troubleshooting

記錄實際遇到的問題與解法。

建議格式：

```text
YYYY-MM-DD-issue-name.md
```

例如：

```text
2026-06-line-duplicate-reply.md
2026-06-redis-processing-lock.md
2026-07-queue-timeout.md
```

---

# Record Principles

優先記錄：

* 為什麼發生
* 如何排查
* 如何解決
* 如何避免再次發生

避免只保存程式碼片段。

重點應放在：

* 思考過程
* 決策依據
* 架構考量

---

# Knowledge Template

建議每篇文件包含：

```markdown
# 問題

# 現象

# 原因

# 解決方案

# 驗證方式

# Lessons Learned
```

---

# Long-Term Goal

建立個人 Laravel 技術知識庫。

讓以下內容可以快速被查詢與重複利用：

* 架構決策
* 效能優化
* 問題排查
* 第三方整合
* 重構經驗

最終目標：

從「解決問題」進步到「設計可維護系統」。