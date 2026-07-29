# Redis Operations

## Purpose

這份文件定義 Redis production operation 的基本檢查項目。

目標是避免 Redis 成為不可觀測、不可恢復的隱性單點。

---

## Monitoring

必須監控：

- memory usage
- connected clients
- ops per second
- hit rate
- evicted keys
- expired keys
- latency
- slowlog
- replication status
- queue length

---

## Alerts

建議告警：

- memory 接近上限
- evicted keys 異常增加
- latency 升高
- Redis unavailable
- replication lag
- queue backlog
- slowlog 異常

---

## Deployment

Production 建議：

- 使用 managed Redis 或成熟部署方式
- 設定 maxmemory
- 設定 eviction policy
- 規劃 persistence
- 設定 backup
- 設定 access control

---

## High Availability

常見選擇：

- managed Redis HA
- Redis Sentinel
- Redis Cluster

選擇原則：

- 小型產品：managed Redis 優先
- 需要分片與大容量：Redis Cluster
- 自管環境：需要熟悉 failover 與 backup

---

## Incident Handling

Redis 出問題時先確認：

- 是否 memory 滿了？
- 是否有 big key？
- 是否有慢命令？
- 是否網路問題？
- 是否 queue 堆積？
- 是否 persistence rewrite 造成壓力？

---

## Checklist

上线前確認：

- maxmemory 是否設定？
- eviction policy 是否符合用途？
- monitoring 是否完成？
- alert 是否完成？
- backup 是否可 restore？
- Redis 掛掉時 app 是否能降級？
- 是否有 runbook？
