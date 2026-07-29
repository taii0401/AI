# Docker Health Check

## Purpose

本文件定義 Docker Health Check 的設計原則、最佳實踐與使用方式。

Health Check 的目的不是確認 Container 是否正在執行，而是確認 **Service 是否已經可以正常提供服務**。

本文件適用於：

- Docker Engine
- Docker Compose
- Laravel
- PHP
- Nginx
- MySQL
- Redis
- Mailpit
- Queue Worker
- Scheduler

---

# Why Health Check

Docker 的狀態：

```text
Created

↓

Running
```

並不代表：

```text
Ready
```

例如：

```text
MySQL

Container Running

↓

Database 還在初始化

↓

Laravel 已開始連線

↓

SQLSTATE[HY000] Connection refused
```

因此：

Running

≠

Healthy

---

# Health Lifecycle

```text
Container Start

↓

Starting

↓

Healthy

↓

Unhealthy
```

只有：

```text
Healthy
```

才能代表：

Service Ready。

---

# Design Principles

Health Check 應符合：

- 快速
- 穩定
- 可重複
- 無副作用
- 可自動恢復
- 容易診斷

Health Check 不應：

- 修改資料
- 建立資料
- 刪除資料
- 呼叫外部 API
- 執行耗時工作

---

# Running vs Healthy

Container：

```text
Running
```

表示：

Docker Process 正在執行。

例如：

```text
mysqld
```

仍可能：

```text
尚未接受連線
```

Health：

```text
Healthy
```

表示：

```text
已可提供服務
```

---

# Compose Integration

Compose 建議：

```yaml
depends_on:

  mysql:

    condition: service_healthy

  redis:

    condition: service_healthy
```

不要：

```yaml
depends_on:

- mysql
```

因為：

只保證：

Container 啟動。

不保證：

Service Ready。

---

# Health Check Frequency

Health Check：

不需要：

每秒執行。

建議：

```yaml
interval: 10s
```

或：

```yaml
30s
```

依服務特性調整。

---

# Retry

避免：

一次失敗：

就判定：

Unhealthy。

例如：

```yaml
retries: 5
```

代表：

允許：

五次失敗。

---

# Timeout

Health Check：

應設定：

Timeout。

例如：

```yaml
timeout: 5s
```

避免：

永遠等待。

---

# Start Period

有些 Service：

初始化較慢。

例如：

- MySQL
- PostgreSQL
- Elasticsearch

可設定：

```yaml
start_period: 30s
```

避免：

剛啟動：

立即：

判定失敗。

---

# MySQL

建議：

```yaml
healthcheck:

  test:

    - CMD

    - mysqladmin

    - ping

    - -h

    - localhost

  interval: 10s

  timeout: 5s

  retries: 5
```

目的：

確認：

MySQL：

可以接受連線。

不要：

使用：

```text
Container Running
```

當成：

Database Ready。

---

# Redis

建議：

```yaml
healthcheck:

  test:

    - CMD

    - redis-cli

    - ping
```

預期：

```text
PONG
```

代表：

Redis Ready。

---

# Nginx

可使用：

```text
HTTP 200
```

例如：

```text
/

↓

200 OK
```

確認：

Web Server：

正常。

---

# Laravel API

Laravel：

Health Check：

建議：

建立：

```text
/health
```

例如：

```http
GET /health
```

回傳：

```json
{
    "status":"ok"
}
```

而不是：

首頁。

原因：

首頁：

可能：

需要：

登入。

---

# Queue Worker

Queue：

Health Check：

不應：

只確認：

Process。

應確認：

Worker：

仍正常運作。

例如：

- Queue 未停止
- Redis 可連線
- Worker 未 Crash

---

# Scheduler

Scheduler：

通常：

只確認：

Process。

例如：

```text
php artisan schedule:work
```

仍在執行。

---

# Mailpit

確認：

HTTP：

可連線。

例如：

```text
http://mailpit:8025
```

回傳：

```text
200 OK
```

即可。

---

# Custom Health Endpoint

大型專案：

建議：

建立：

```text
/health
```

以及：

```text
/ready
```

例如：

```text
GET /health
```

確認：

Application：

仍活著。

---

```text
GET /ready
```

確認：

Application：

真的：

Ready。

例如：

需要：

- Database
- Redis
- Queue

全部正常。

---

# Liveness vs Readiness

## Liveness

代表：

Application：

是否：

仍活著。

例如：

```text
GET /health
```

---

## Readiness

代表：

Application：

是否：

可以：

接受請求。

例如：

```text
GET /ready
```

可能：

檢查：

- Database
- Redis
- Queue

---

# Common Mistakes

避免：

- 沒有 Health Check
- interval 太短
- timeout 太長
- retry 太少
- Health Check 修改資料
- Health Check 呼叫第三方 API
- Health Check 執行 SQL INSERT
- Health Check 建立 Cache

---

# Debug

查看：

Container：

Health：

```bash
docker ps
```

查看：

詳細資訊：

```bash
docker inspect container_name
```

查看：

Health Log：

```bash
docker inspect container_name
```

搜尋：

```text
Health
```

即可看到：

- Exit Code
- Output
- Time
- Retry

---

# Checklist

建立新的 Service 前：

- [ ] 是否需要 Health Check
- [ ] 是否可快速完成
- [ ] 是否沒有副作用
- [ ] 是否設定 interval
- [ ] 是否設定 timeout
- [ ] 是否設定 retries
- [ ] 是否需要 start_period
- [ ] 是否搭配 depends_on
- [ ] 是否容易除錯

---

# Related Documents

建議閱讀：

```text
README.md
    ↓
architecture.md
    ↓
compose-standards.md
    ↓
healthcheck.md
    ↓
dockerfile-standards.md
    ↓
development-environment.md
    ↓
networking.md
    ↓
permissions.md
    ↓
volumes.md
    ↓
image-versioning.md
    ↓
troubleshooting.md
```