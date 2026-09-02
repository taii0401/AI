# Architecture API Design

## Purpose

這份文件定義通用 API 設計原則。

目標是讓 API 穩定、一致、容易測試，並降低 frontend 與 backend 的耦合。

---

## Core Principles

- resource 命名清楚
- response 格式一致
- error 格式一致
- pagination 明確
- filtering 與 sorting 使用白名單
- breaking change 要版本化
- 不暴露內部資料庫結構

---

## RESTful Default

多數產品 API 預設使用 REST。

常見：

```text
GET /users
GET /users/{id}
POST /users
PATCH /users/{id}
DELETE /users/{id}
```

非 CRUD action 要明確：

```text
POST /orders/{id}/cancel
POST /posts/{id}/publish
```

---

## Response

Response 應穩定。

建議包含：

- data
- meta
- links
- error

不要讓 client 依賴未定義欄位。

---

## Pagination

列表 API 預設 pagination。

選擇：

- offset pagination：一般後台與小型列表
- cursor pagination：大量資料與 infinite scroll

---

## Versioning

公開 API 或 mobile app API 建議版本化。

範例：

```text
/api/orders
```

內部 API 不一定一開始就需要版本化。

---

## Checklist

檢查 API：

- naming 是否一致？
- error format 是否一致？
- 是否有 pagination？
- filter/sort 是否白名單？
- 是否有 authentication？
- 是否有 authorization？
- 是否避免暴露敏感欄位？
