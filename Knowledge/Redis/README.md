# Redis Knowledge

## Purpose

這個資料夾用來整理 Redis 的通用實務知識。

目標不是取代官方文件，而是建立一套能用於後端系統、Side Project、Laravel 專案與中小型產品的 Redis 決策準則。

---

## Reading Order

建議閱讀順序：

1. `standards.md`
2. `data-structures.md`
3. `caching.md`
4. `queue.md`
5. `pubsub-streams.md`
6. `patterns.md`
7. `performance.md`
8. `persistence.md`
9. `security.md`
10. `operations.md`

---

## Core Principles

- Redis 優先用來解決速度、暫存、併發與即時性問題
- 不要把 Redis 當主要資料庫，除非架構已明確設計
- Key 命名要一致
- TTL 要有策略
- Cache miss、cache stampede、資料不一致要事先考慮
- Production 一定要規劃 monitoring、backup、memory policy

---

## Common Use Cases

適合使用 Redis：

- Cache
- Session storage
- Rate limiting
- Queue backend
- Distributed lock
- Leaderboard
- Real-time counter
- Pub/Sub
- Stream processing
- Temporary token or OTP

不適合預設使用 Redis：

- 需要複雜查詢的主資料
- 需要長期保存且不能遺失的唯一資料
- 需要強一致交易的核心資料

---

## Decision Rule

使用 Redis 前先問：

- 這份資料是否可以重建？
- 資料過期策略是什麼？
- Redis 掛掉時系統能否降級？
- 資料一致性要求有多高？
- 是否需要持久化？
- 是否有 memory 上限與淘汰策略？

如果答案不清楚，先不要把 Redis 放進核心流程。
