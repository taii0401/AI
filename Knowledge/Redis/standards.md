# Redis Standards

## Purpose

這份文件定義 Redis 的通用使用規範。

目標是讓 Redis key、TTL、資料結構與錯誤處理保持一致，避免專案成長後難以維護。

---

## Key Naming

Key 命名要可讀、可搜尋、可分類。

推薦格式：

```text
{domain}:{entity}:{id}:{attribute}
```

範例：

```text
user:123:profile
post:456:view_count
rate_limit:login:user:123
cache:product:popular
lock:order:789:checkout
```

原則：

- 使用小寫
- 使用 `:` 分隔
- 避免空白
- 避免過長 key
- key 中不要放敏感資料

---

## TTL Rules

暫存資料預設要有 TTL。

適合設定 TTL：

- cache
- OTP
- token
- rate limit counter
- lock
- temporary workflow state

不建議無 TTL：

- cache data
- user-specific temporary data
- lock key

---

## Data Format

資料格式要根據用途選擇。

推薦：

- string：簡單值、JSON blob、counter
- hash：同一 entity 多欄位資料
- set：唯一集合
- sorted set：排行榜、權重排序
- list：簡單 queue，但 production queue 優先用成熟工具
- stream：事件流、可靠消費

---

## Error Handling

Redis 失敗時要依用途決定處理方式。

Cache 失敗：

- 可以 fallback 到 database
- log warning
- 不應讓核心流程直接失敗

Lock 失敗：

- 必須明確處理
- 不可默默忽略

Queue 失敗：

- 視為系統錯誤
- 必須告警或重試

---

## Checklist

使用 Redis 前確認：

- key 命名是否一致？
- 是否需要 TTL？
- 資料是否可重建？
- Redis 掛掉時是否有 fallback？
- 是否有 memory policy？
- 是否需要 persistence？
- 是否有 monitoring？
