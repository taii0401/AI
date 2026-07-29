# Laravel Exception Handling

## Purpose

這份文件定義 Laravel 專案的錯誤處理原則。

目標是讓錯誤容易 debug、API 回應一致，並避免吞掉真正的問題。

---

## Core Principles

- 不要用 `return false` 表示 business error
- 不要吞 exception
- business error 使用明確 exception
- validation error 使用 Laravel 預設機制
- log 要有 context，但不能包含敏感資料
- 對外錯誤訊息要穩定，對內 log 要足夠診斷

---

## Exception Types

常見錯誤類型：

- Validation error
- Authorization error
- Model not found
- Business rule violation
- External service failure
- Unexpected system error

不同錯誤類型應該有不同處理方式，不要全部包成 generic exception。

---

## Business Exceptions

Business exception 用來表達明確業務規則失敗。

範例：

```php
class CannotPublishPostException extends RuntimeException
{
}
```

使用：

```php
if (! $post->canBePublished()) {
    throw new CannotPublishPostException('Post is not ready to publish.');
}
```

適合情境：

- 訂單不能取消
- 文章不能發布
- 訂閱已過期
- 庫存不足
- 使用者狀態不允許操作

---

## Controller Handling

Controller 不應塞大量 try-catch。

不建議：

```php
try {
    $service->publish($post);
} catch (CannotPublishPostException $exception) {
    return response()->json(['message' => $exception->getMessage()], 422);
}
```

推薦：

- 在 Service 拋出明確 exception
- 在 exception handler 統一轉換 response
- Controller 保持流程簡潔

---

## Logging

Log 應該提供 debug 所需 context。

推薦：

```php
Log::warning('Payment capture failed', [
    'order_id' => $order->id,
    'payment_id' => $payment->id,
]);
```

避免記錄：

- password
- token
- credit card data
- personal sensitive data
- raw request payload without filtering

---

## External Service Errors

外部服務錯誤要區分：

- timeout
- rate limit
- authentication failed
- invalid response
- service unavailable

建議：

- 設定 timeout
- 可重試錯誤才 retry
- retry 次數有限制
- 失敗要 log context
- 重要流程要有補償或人工處理方式

---

## API Error Response

API 錯誤回應應一致。

範例：

```json
{
  "message": "Post is not ready to publish.",
  "code": "post_not_publishable"
}
```

原則：

- `message` 給使用者或 frontend 理解
- `code` 給程式判斷
- HTTP status code 要合理
- 不回傳 stack trace

---

## Common Anti Patterns

避免：

```php
catch (Exception $exception) {
    return false;
}
```

避免：

```php
catch (Exception $exception) {
    // ignore
}
```

避免：

```php
throw new Exception('Something went wrong');
```

這些寫法會讓問題難以追蹤，也讓呼叫方無法做正確處理。

---

## Checklist

檢查錯誤處理時確認：

- 是否有吞掉 exception？
- 是否使用明確 business exception？
- 是否有足夠 log context？
- 是否避免記錄敏感資料？
- API error format 是否一致？
- HTTP status code 是否合理？
- 外部服務錯誤是否可診斷？
