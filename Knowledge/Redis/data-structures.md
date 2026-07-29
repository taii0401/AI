# Redis Data Structures

## Purpose

這份文件整理 Redis 常用資料結構與適合情境。

選對資料結構比把所有資料塞成 JSON 更重要。

---

## String

適合：

- cache value
- counter
- token
- feature flag
- serialized JSON

常用操作：

```text
GET
SET
SETEX
INCR
DECR
```

注意：

- JSON blob 適合簡單 cache
- 若常更新部分欄位，改用 hash

---

## Hash

適合：

- entity snapshot
- user profile
- setting map
- 多欄位資料

優點：

- 可更新單一欄位
- 比多個 string key 更集中

注意：

- 不適合無限制增長
- 欄位結構要穩定

---

## Set

適合：

- unique ids
- tag members
- online users
- permission set

常見用途：

```text
SADD
SREM
SISMEMBER
SMEMBERS
```

注意：

- 大 set 要避免一次取出全部資料

---

## Sorted Set

適合：

- leaderboard
- ranking
- delayed job index
- time-based score list

常見用途：

```text
ZADD
ZRANGE
ZREVRANGE
ZRANGEBYSCORE
ZREM
```

注意：

- score 要設計清楚
- 排行榜資料通常需要定期修剪

---

## List

適合：

- 簡單 FIFO queue
- 最近事件列表

注意：

- 若需要可靠消費、ack、consumer group，優先考慮 Redis Streams 或成熟 queue 系統

---

## Stream

適合：

- event stream
- reliable message processing
- consumer group
- audit-like event flow

優點：

- 支援 consumer group
- 支援 pending message
- 比 Pub/Sub 更可靠

注意：

- 要規劃 stream trimming
- consumer failure 要處理 pending entries
