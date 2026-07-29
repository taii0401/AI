# Redis Queue

## Purpose

這份文件定義 Redis 作為 queue backend 的使用準則。

Redis 常被用於背景工作，但可靠性、重試與監控必須明確設計。

---

## Common Use Cases

適合放入 queue：

- email
- notification
- image processing
- report generation
- third-party API sync
- webhook delivery
- long-running task

不適合 queue：

- 必須同步完成的核心交易判斷
- 無法重試且不能失敗的唯一流程
- 沒有 idempotency 的外部副作用

---

## Job Design

Job 設計原則：

- job payload 不要過大
- 儲存 id，不儲存完整大物件
- job 要可重試
- job 要盡量 idempotent
- 設定 timeout 與 retry 次數

---

## Idempotency

Queue job 可能被重試，因此要避免重複副作用。

需要保護：

- 重複寄信
- 重複扣款
- 重複建立訂單
- 重複呼叫 webhook

常見方式：

- unique key
- processed flag
- idempotency key
- database constraint

---

## Failure Handling

必須處理：

- retry
- failed job
- timeout
- dead letter queue
- alerting

不要只依賴 log 才知道 queue 壞了。

---

## Redis As Queue Backend

Redis queue 優點：

- 快
- 設定簡單
- Laravel 支援成熟

風險：

- persistence 設定影響可靠性
- memory pressure 會影響 queue
- worker 掛掉需要監控

---

## Checklist

使用 Redis Queue 前確認：

- job 是否可重試？
- 是否有 idempotency？
- retry 次數是否合理？
- timeout 是否設定？
- failed job 是否有人處理？
- worker 是否有 supervisor？
- queue 長度是否有監控？
