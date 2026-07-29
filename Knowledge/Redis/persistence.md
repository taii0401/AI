# Redis Persistence

## Purpose

這份文件整理 Redis persistence 的基本概念與使用取捨。

Redis 可以持久化，但不代表所有資料都適合只存在 Redis。

---

## RDB

RDB 是快照式持久化。

優點：

- 檔案小
- 重啟載入快
- 適合備份

缺點：

- 可能遺失最後一次快照後的資料

適合：

- cache 可以重建
- session 可接受少量遺失
- 開發與一般用途

---

## AOF

AOF 會記錄寫入操作。

優點：

- 資料遺失較少
- 可讀性較高

缺點：

- 檔案較大
- rewrite 需要管理
- 效能成本較高

適合：

- queue
- session
- 較重視資料保留的 Redis 使用情境

---

## No Persistence

純 cache 可考慮不開 persistence。

前提：

- 所有資料都能從 database 重建
- Redis restart 可接受
- 有 cache warmup 或 graceful fallback

---

## Backup

需要備份時確認：

- 備份頻率
- restore 流程
- restore 測試
- 備份檔保護
- production 與 staging 分離

---

## Decision Rule

選擇方式：

```text
純 cache：可不開或只用 RDB
session：RDB 或 AOF，視可接受遺失程度
queue：偏向 AOF，且要有 failed job 機制
核心資料：不要只放 Redis
```

---

## Checklist

確認：

- Redis 資料是否可重建？
- 可接受遺失多少資料？
- 是否需要 backup？
- 是否測過 restore？
- AOF rewrite 是否監控？
- persistence 是否符合 queue/session 需求？
