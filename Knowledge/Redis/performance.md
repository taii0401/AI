# Redis Performance

## Purpose

這份文件整理 Redis 效能設計與常見瓶頸。

Redis 很快，但錯誤的 key 設計、巨大資料結構與阻塞命令仍會造成 production 問題。

---

## Key Principles

- 避免大 key
- 避免阻塞命令
- 使用 pipeline 降低 round trip
- 設定合理 TTL
- 監控 memory 與 latency
- 不要在 production 使用 `KEYS`

---

## Avoid Big Keys

Big key 風險：

- 操作慢
- 刪除慢
- replication 壓力
- memory 使用不均

常見 big key：

- 超大 hash
- 超大 list
- 超大 set
- 單一巨大 JSON string

建議：

- 拆分 key
- 分頁讀取
- 設定上限
- 定期修剪

---

## Dangerous Commands

Production 避免：

```text
KEYS
FLUSHALL
FLUSHDB
SMEMBERS on huge set
LRANGE huge range
HGETALL huge hash
```

替代：

- 使用 `SCAN`
- 使用 pagination
- 限制 collection size

---

## Pipeline

多個獨立 Redis command 可用 pipeline 降低 network round trip。

適合：

- 批量讀取
- 批量寫入
- counter update

注意：

- pipeline 不是 transaction
- 批量大小要控制

---

## Memory Policy

Production 必須設定 maxmemory 與 eviction policy。

常見 policy：

- `allkeys-lru`
- `volatile-lru`
- `allkeys-lfu`
- `noeviction`

選擇原則：

- 純 cache：可用 LRU / LFU
- queue 或重要資料：避免被 eviction，或拆 Redis instance

---

## Checklist

效能檢查：

- 是否有 big key？
- 是否用了 `KEYS`？
- 是否有無 TTL cache？
- memory policy 是否明確？
- latency 是否監控？
- slowlog 是否檢查？
- 是否需要 pipeline？
