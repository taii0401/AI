# Redis Pub/Sub And Streams

## Purpose

這份文件整理 Redis Pub/Sub 與 Streams 的使用差異。

結論：需要可靠消費時，不要只用 Pub/Sub，應考慮 Streams。

---

## Pub/Sub

Pub/Sub 適合即時通知。

適合：

- websocket broadcast
- 即時 UI 更新
- 非關鍵事件通知
- ephemeral message

限制：

- 訂閱者離線就收不到
- 沒有 message persistence
- 沒有 ack
- 不適合關鍵業務流程

---

## Streams

Streams 適合可靠事件處理。

適合：

- event processing
- consumer group
- audit-like event flow
- webhook pipeline
- background processing

優點：

- message 有 id
- 支援 consumer group
- 支援 pending entries
- 可追蹤未確認訊息

---

## Pub/Sub Vs Streams

選擇原則：

```text
訊息可以遺失：Pub/Sub
訊息不能遺失：Streams
需要 consumer group：Streams
只做即時廣播：Pub/Sub
需要重試與追蹤：Streams
```

---

## Stream Maintenance

Streams 要規劃：

- trimming policy
- consumer group naming
- pending message handling
- failed consumer recovery
- monitoring

避免 stream 無限制成長。

---

## Checklist

使用前確認：

- message 是否可以遺失？
- consumer 離線時是否要補收？
- 是否需要 ack？
- 是否需要多 consumer group？
- stream 是否有 trimming？
- consumer failure 如何處理？
