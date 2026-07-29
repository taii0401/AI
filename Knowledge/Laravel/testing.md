# Laravel Testing

## Purpose

這份文件定義 Laravel 專案的測試策略。

目標是用合理成本保護核心流程，不追求無意義的覆蓋率數字。

---

## Core Principles

- Feature Test 優先
- 核心 business flow 必測
- Bug fix 要補 regression test
- 不為 private method 寫測試
- 不過度 mock Laravel 與 Eloquent
- 測試要反映使用者行為與系統風險

---

## What To Test First

優先測：

- authentication
- authorization
- validation
- core CRUD flow
- payment / order / subscription
- important API response
- complex query
- background job

MVP 階段至少要覆蓋會造成資料錯誤或安全風險的流程。

---

## Feature Test

Feature Test 適合測 HTTP flow。

檢查：

- status code
- validation error
- authorization
- database state
- response structure

範例：

```php
$this->actingAs($user)
    ->postJson('/api/posts', [
        'title' => 'Hello',
        'content' => 'Content',
    ])
    ->assertCreated()
    ->assertJsonPath('data.title', 'Hello');
```

---

## Unit Test

Unit Test 適合測純邏輯。

適合：

- value object
- calculator
- parser
- policy-like pure rule
- complex Service method

不適合：

- 測 Eloquent 內建行為
- 測 framework 已經保證的功能
- 大量 mock query chain

---

## Database Tests

查詢與資料流程建議使用真實 test database。

原因：

- query correctness 依賴 database behavior
- relationship 與 cast 需要整合測試
- mock Eloquent 容易失真

建議使用：

- factory
- `RefreshDatabase`
- 明確 assertion

---

## Test Data

測試資料要清楚且最小化。

推薦：

```php
$user = User::factory()->create();
$post = Post::factory()->for($user, 'author')->create();
```

避免：

- 測試依賴大量 seeder
- 測試資料與測試目的無關
- magic id

---

## External Services

外部服務使用 fake 或 mock。

常見：

- `Mail::fake()`
- `Notification::fake()`
- `Queue::fake()`
- `Storage::fake()`
- `Http::fake()`

確認副作用：

```php
Mail::assertSent(OrderPaidMail::class);
```

---

## Checklist

檢查測試時確認：

- 是否覆蓋核心流程？
- 是否測 authorization？
- 是否測 validation failure？
- 是否測失敗情境？
- 是否避免過度 mock？
- bug fix 是否有 regression test？
- 測試資料是否清楚？
