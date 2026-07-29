# Docker Troubleshooting

## Purpose

本文件提供 Docker 開發環境常見問題的診斷流程（Troubleshooting Runbook）。

目的：

- 快速定位問題
- 建立一致的排查流程
- 降低重複除錯時間
- 提供 Root Cause Analysis（RCA）
- 建立可重複使用的解決方案

本文件不綁定任何特定專案。

---

# Troubleshooting Principles

遇到問題時：

不要直接修改設定。

應依照：

```text
現象

↓

定位

↓

Root Cause

↓

修正

↓

驗證

↓

Prevent Future
```

不要：

```text
一直 docker compose down

一直重建

一直 chmod 777
```

---

# Diagnostic Layers

Docker 問題通常可分成：

```text
Host

↓

Docker Engine

↓

Compose

↓

Image

↓

Container

↓

Network

↓

Volume

↓

Application
```

建議依序檢查。

---

# Standard Troubleshooting Workflow

```text
問題發生

↓

Container 是否存在

↓

Container 是否 Running

↓

Container 是否 Healthy

↓

Logs

↓

Network

↓

Volume

↓

Environment

↓

Application
```

不要跳步驟。

---

# 1. Docker Desktop

## Symptoms

- Docker 無法啟動
- 無法 Build
- 無法 Pull

## Check

```bash
docker version
docker info
```

確認：

- Docker Engine
- Client

正常。

---

# 2. Compose

查看：

```bash
docker compose ps
```

若：

Container：

不存在：

查看：

```bash
docker compose config
```

確認：

Compose：

是否合法。

---

# 3. Image

查看：

```bash
docker images
```

若：

Image：

不存在：

重新：

```bash
docker compose build
```

---

# 4. Container

查看：

```bash
docker ps -a
```

若：

Exited：

查看：

```bash
docker logs container_name
```

不要：

直接：

重建。

先看：

Log。

---

# 5. Health Check

查看：

```bash
docker ps
```

確認：

```text
healthy
```

若：

```text
unhealthy
```

查看：

```bash
docker inspect container_name
```

搜尋：

```text
Health
```

---

# 6. Logs

查看：

```bash
docker compose logs
```

單一：

```bash
docker compose logs api
```

最近：

```bash
docker compose logs --tail=100 api
```

持續：

```bash
docker compose logs -f api
```

---

# 7. Environment Variables

查看：

```bash
docker compose exec api env
```

Laravel：

查看：

```bash
cat .env
```

確認：

- DB_HOST
- REDIS_HOST
- APP_KEY

---

# 8. Network

查看：

```bash
docker network ls
```

查看：

```bash
docker network inspect network_name
```

測試：

DNS：

```bash
docker compose exec api ping mysql
```

測試：

Port：

```bash
docker compose exec api nc -z mysql 3306
```

---

# 9. Volume

查看：

```bash
docker volume ls
```

查看：

```bash
docker volume inspect volume_name
```

確認：

是否：

Mount：

正確。

---

# 10. Permission

查看：

```bash
id
```

查看：

```bash
ls -ln
```

確認：

UID/GID。

---

# Common Problems

---

## Container 無法啟動

### Symptoms

```text
Exited
Restarting
```

### Check

```bash
docker logs container_name
```

### Common Causes

- Command 錯誤
- Environment 缺少
- Permission
- Port 衝突

---

## Port Already In Use

### Symptoms

```text
Bind for 0.0.0.0 failed
```

### Check

Linux：

```bash
ss -lntp
```

Windows：

```text
netstat -ano
```

### Solution

修改：

Host Port。

---

## Database Connection Refused

### Symptoms

```text
SQLSTATE

Connection Refused
```

### Check

```dotenv
DB_HOST=mysql
```

不是：

```text
localhost
```

確認：

```bash
docker compose exec api ping mysql
```

---

## Redis Connection Failed

### Check

```bash
docker compose exec api ping redis
```

Laravel：

```dotenv
REDIS_HOST=redis
```

---

## Permission Denied

### Symptoms

```text
Permission denied

Operation not permitted
```

### Check

```bash
ls -ln
```

確認：

Owner。

查看：

```bash
id
```

---

## vendor Permission

重新：

```bash
chown -R 1000:1000 vendor
```

不要：

777。

---

## node_modules Permission

重新：

```bash
chown -R 1000:1000 node_modules
```

---

## storage Permission

Laravel：

```bash
chmod -R ug+rwX storage
```

---

## bootstrap/cache

Laravel：

```bash
chmod -R ug+rwX bootstrap/cache
```

---

## APP_KEY Missing

Symptoms：

```text
No application encryption key
```

Solution：

```bash
php artisan key:generate
```

---

## Migration Failed

查看：

```bash
php artisan migrate:status
```

確認：

Database。

---

## Composer Install Failed

查看：

```bash
composer diagnose
```

確認：

- Network
- Permission
- PHP Version

---

## npm Install Failed

查看：

```bash
npm config list
```

確認：

- Registry
- Network
- Permission

---

## Vite 504

Symptoms：

```text
504 Outdated Optimize Dep
```

Solution：

刪除：

```text
node_modules/.vite
```

重新：

```bash
npm install
```

重新：

```bash
npm run dev
```

---

## Docker DNS

Container：

不要：

使用：

```text
localhost
```

使用：

```text
mysql

redis

mailpit
```

---

## Image Build Failed

查看：

```bash
docker build .
```

確認：

Dockerfile。

---

## Build Cache

重新：

```bash
docker compose build --no-cache
```

只有：

必要：

才：

使用。

---

## Container Name Conflict

查看：

```bash
docker ps -a
```

刪除：

不用：

Container。

---

## Volume Corruption

只有：

確定：

可以：

刪除：

資料：

才：

```bash
docker compose down -v
```

---

## Network Conflict

重新：

建立：

Network：

```bash
docker network prune
```

注意：

會：

刪除：

未使用：

Network。

---

# Root Cause Analysis

每次問題：

建議：

紀錄：

```text
Problem

↓

Symptoms

↓

Root Cause

↓

Solution

↓

Verification

↓

Prevention
```

不要：

只：

記：

解法。

---

# Diagnostic Commands

Docker：

```bash
docker ps -a
docker images
docker volume ls
docker network ls
docker info
```

Compose：

```bash
docker compose ps
docker compose config
docker compose logs
```

Container：

```bash
docker exec

docker inspect

docker logs
```

Laravel：

```bash
php artisan about

php artisan optimize:clear

php artisan migrate:status
```

Network：

```bash
ping

nc

curl
```

---

# Best Practices

建議：

1. 先看 Logs。
2. 不要第一時間重建 Container。
3. 不要第一時間刪 Volume。
4. 不要直接 chmod 777。
5. 先確認 Environment。
6. 再確認 Network。
7. 最後才重建。
8. 每個 Bug 都記錄 RCA。

---

# Checklist

排查前：

- [ ] Logs
- [ ] Container
- [ ] Health
- [ ] Network
- [ ] Volume
- [ ] Environment
- [ ] Permission
- [ ] Application
- [ ] Root Cause
- [ ] Prevention

---

# Related Documents

```text
README.md
    ↓
architecture.md
    ↓
compose-standards.md
    ↓
networking.md
    ↓
security.md
    ↓
healthcheck.md
    ↓
dockerfile-standards.md
    ↓
development-environment.md
    ↓
permissions.md
    ↓
volumes.md
    ↓
image-versioning.md
    ↓
troubleshooting.md
```