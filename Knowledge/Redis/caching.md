# Redis Caching

## Purpose

這份文件定義 Redis 作為 cache 的使用準則。

目標是提高讀取效能，同時控制資料不一致與 cache stampede 風險。

---

## Cache Strategy

常見策略：

- Cache Aside
- Write Through
- Write Behind
- Refresh Ahead

一般後端與 Laravel 專案預設使用 Cache Aside。

---

## Cache Aside

流程：

1. 先讀 Redis
2. cache miss 時讀 database
3. 將結果寫回 Redis
4. 回傳結果

適合：

- 商品資料
- 設定資料
- 熱門文章
- dashboard summary

---

## TTL

所有 cache key 都應有 TTL。

TTL 建議：

- 高變動資料：30 秒到 5 分鐘
- 中變動資料：5 到 30 分鐘
- 低變動資料：30 分鐘到數小時
- reference data：可更長，但要有 invalidation 策略

---

## Cache Invalidation

常見做法：

- update 後刪除 cache
- update 後重建 cache
- 使用短 TTL 接受短暫不一致
- 使用 versioned key

推薦：

```text
資料正確性重要：更新後刪 cache
效能重要且可接受延遲：短 TTL
複雜聚合資料：排程重建
```

---

## Cache Stampede

Cache stampede 是大量 request 同時 cache miss，導致 database 被打爆。

緩解方式：

- lock
- random TTL jitter
- stale-while-revalidate
- background refresh
- 熱點資料預熱

---

## What Not To Cache

不建議 cache：

- 高度敏感資料
- 權限判斷結果，除非 invalidation 很清楚
- 頻繁變動且正確性要求高的資料
- 無法接受 stale data 的核心交易資料

---

## Checklist

建立 cache 前確認：

- cache key 是否清楚？
- TTL 是否合理？
- 資料是否可以 stale？
- cache miss 成本是否高？
- invalidation 策略是什麼？
- 是否有 stampede 風險？
