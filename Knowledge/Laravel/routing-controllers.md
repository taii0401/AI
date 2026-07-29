# Laravel Routing And Controllers

## Purpose

這份文件定義 Route、Controller 與 HTTP flow 的基本規範。

目標是讓 request 入口清楚、Controller 薄而可讀，避免 HTTP 層混入過多 business logic。

---

## Route Principles

- Route 命名要清楚
- Route group 使用 middleware 與 prefix 管理
- CRUD 優先使用 resource route
- 複雜動作用具體 route name
- 不在 route closure 寫 business logic

---

## Resource Routes

標準 CRUD 優先使用：

```php
Route::resource('posts', PostController::class);
```

API 可使用：

```php
Route::apiResource('posts', PostController::class);
```

Controller method 使用 Laravel 慣例：

```text
index
store
show
update
destroy
```

---

## Custom Actions

非 CRUD 操作要用清楚動詞。

推薦：

```php
Route::post('/posts/{post}/publish', [PostPublishingController::class, 'store'])
    ->name('posts.publish');
```

或：

```php
Route::post('/posts/{post}/publish', [PublishPostController::class, '__invoke']);
```

避免：

```php
Route::post('/posts/{post}/action', ...);
Route::post('/posts/{post}/do', ...);
```

---

## Controller Responsibility

Controller 負責：

- 接收 request
- 呼叫 Form Request
- 執行 authorization
- 呼叫 Action 或 Service
- 回傳 response

Controller 不負責：

- 複雜 query
- 複雜 business rule
- 外部 API 協調
- 大量資料轉換

---

## Single Action Controllers

單一明確 use case 可使用 single action controller。

適合：

- publish post
- cancel order
- upload avatar
- export report

範例：

```php
class PublishPostController
{
    public function __invoke(Post $post, PublishPostAction $action)
    {
        $this->authorize('publish', $post);

        $action->execute($post);

        return new PostResource($post->refresh());
    }
}
```

---

## Middleware

Middleware 適合處理 HTTP cross-cutting concern。

適合：

- authentication
- rate limit
- locale
- request logging
- tenant identification

不適合：

- 複雜 business rule
- 特定 model 的細部權限
- use case flow

---

## Checklist

檢查 Route 與 Controller 時確認：

- route name 是否清楚？
- 是否有適當 middleware？
- Controller 是否過胖？
- 是否使用 Form Request？
- 是否有 authorization？
- response 是否由 Resource 或 View 處理？
- business logic 是否已移到 Action 或 Service？
