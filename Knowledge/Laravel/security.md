# Laravel Security

## Purpose

這份文件定義 Laravel 專案的基本安全規範。

目標是避免常見安全漏洞，特別是 authorization、input validation、敏感資料與 mass assignment。

---

## Core Principles

- 不信任任何 user input
- 所有 mutation endpoint 都要 authorization
- 後端決定權限與價格等重要資料
- validated data 才能寫入資料庫
- 敏感資料不進 log
- 不自行實作密碼與 token 機制

---

## Authentication

優先使用 Laravel 官方方案。

常見選擇：

- Web app：Laravel Breeze 或 Jetstream
- SPA / API：Laravel Sanctum
- OAuth server：Laravel Passport

不要自行手刻 authentication，除非有明確安全審查能力。

---

## Authorization

授權優先使用 Policy。

推薦：

```php
$this->authorize('update', $post);
```

不可以只靠 frontend 隱藏按鈕。

檢查：

- create
- update
- delete
- view sensitive data
- export
- admin operation

---

## Validation

所有 user input 都要 validation。

推薦：

- 使用 Form Request
- string 欄位加 max
- enum-like 欄位白名單
- file upload 限制 type 與 size
- id 欄位使用 exists 或 Policy 驗證擁有權

---

## Mass Assignment

避免：

```php
User::create($request->all());
```

推薦：

```php
User::create($request->validated());
```

重要欄位不可由 frontend 決定：

- `role`
- `is_admin`
- `user_id`
- `price`
- `status`
- `permission`

---

## Sensitive Data

不可記錄：

- password
- token
- API key
- credit card data
- personal sensitive data

`.env` 不可 commit。

production 要確認：

```text
APP_DEBUG=false
APP_ENV=production
```

---

## File Upload

檔案上傳要限制：

- mime type
- file size
- storage disk
- public/private access
- 檔名處理

不要信任原始檔名或 extension。

---

## Rate Limiting

需要 rate limit 的位置：

- login
- register
- password reset
- OTP
- public API
- expensive search endpoint

Laravel middleware 可先滿足多數需求。

---

## Checklist

安全檢查：

- mutation endpoint 是否有 authorization？
- 是否使用 Form Request？
- 是否避免 `$request->all()`？
- 是否避免 frontend 控制重要欄位？
- log 是否避開敏感資料？
- production 是否關閉 debug？
- file upload 是否限制 type 與 size？
- public API 是否有 rate limit？
