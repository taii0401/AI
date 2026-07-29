# Laravel API Design

## Purpose

這份文件定義 Laravel API 的通用設計規範。

目標是讓 API 穩定、可測、容易維護，並避免 Controller 直接輸出雜亂資料結構。

---

## Core Principles

- API response 使用一致格式
- 輸入使用 Form Request 驗證
- 輸出使用 API Resource
- 列表資料預設 pagination
- 錯誤回應要可理解
- 不直接回傳完整 Eloquent Model
- 不讓 frontend 依賴 database 欄位細節

---

## Response Format

單筆資料推薦：

```php
return new PostResource($post);
```

列表資料推薦：

```php
return PostResource::collection($posts);
```

需要額外 metadata 時：

```php
return PostResource::collection($posts)->additional([
    'meta' => [
        'filters' => $filters,
    ],
]);
```

---

## API Resources

Resource 負責對外資料格式。

適合放在 Resource：

- 欄位命名轉換
- 日期格式
- relationship 輸出
- computed field
- conditionally loaded data

不適合放在 Resource：

- query
- authorization
- business logic
- 修改資料狀態

---

## Pagination

列表 API 預設使用 pagination。

推薦：

```php
Post::query()
    ->with('author')
    ->latest()
    ->paginate($request->integer('per_page', 20));
```

規範：

- 預設 `per_page` 為 20
- 設定最大 `per_page`，避免一次拉太多資料
- 大量資料或 infinite scroll 可考慮 cursor pagination

---

## Filtering And Sorting

Filter 與 sort 要明確白名單。

避免直接信任 request 欄位：

```php
$query->orderBy($request->sort);
```

推薦：

```php
$sort = $request->validated('sort', 'latest');

match ($sort) {
    'oldest' => $query->oldest(),
    'title' => $query->orderBy('title'),
    default => $query->latest(),
};
```

---

## Versioning

MVP 階段不一定需要 API versioning。

需要公開給第三方或 mobile app 使用時，建議加入版本：

```text
/api/v1/posts
```

版本化原則：

- breaking change 才升 major version
- 非 breaking change 不新增版本
- 不為內部 API 過早版本化

---

## Authentication

API authentication 優先使用 Laravel 官方方案。

常見選擇：

- SPA：Laravel Sanctum
- Mobile app：Sanctum token
- 第三方 OAuth：Laravel Passport 或外部 Identity Provider

不要自行手刻 token authentication，除非有非常明確需求。

---

## Error Response

Validation error 使用 Laravel 預設格式即可。

Business error 使用明確 exception，並在 exception handler 統一轉成 API response。

錯誤回應應包含：

- 清楚 message
- 合理 HTTP status code
- 必要時提供 error code

避免：

- 永遠回 200
- 把 exception stack trace 回傳給 client
- 回傳不穩定的錯誤格式

---

## Checklist

設計 API 時檢查：

- 是否使用 Form Request？
- 是否使用 Resource？
- 是否有 authorization？
- list endpoint 是否 pagination？
- filter / sort 是否白名單？
- 是否避免 N+1 query？
- 是否避免暴露敏感欄位？
- error response 是否一致？
