# Development Environment

## Purpose

本文件定義 Docker 開發環境的設計原則、建立流程、日常開發方式與最佳實踐。

目的：

- 建立一致的開發環境
- 降低環境差異
- 快速建立新專案
- 降低 Onboarding 成本
- 提高團隊協作效率
- 建立可重現（Reproducible）的開發流程

本文件不綁定任何特定專案。

---

# Goals

理想的開發環境應具備：

- 一致性（Consistency）
- 可重建（Reproducibility）
- 易維護（Maintainability）
- 易分享（Portability）
- 可擴充（Scalability）
- 最小化本機依賴

---

# Environment Overview

建議開發環境：

```text
Developer

↓

Git

↓

Docker Desktop

↓

WSL2（Windows）

↓

Docker Compose

↓

Containers

↓

Laravel
React
MySQL
Redis
Mailpit
```

Application 不直接依賴 Host。

---

# Local Requirements

建議安裝：

- Git
- Docker Desktop
- WSL2（Windows）
- VS Code
- Docker Compose

不建議：

- Host 安裝 PHP
- Host 安裝 Composer
- Host 安裝 MySQL
- Host 安裝 Redis
- Host 安裝 Node.js

Application Runtime 應由 Docker 提供。

---

# Directory Structure

建議：

```text
project/

├── api/
├── web/
├── docker/
├── docs/
├── compose.yaml
├── .env
├── PROJECT.md
├── AGENTS.md
└── README.md
```

---

# Environment Layers

開發環境通常分三層：

```text
Host

↓

Docker

↓

Application
```

各層責任：

## Host

負責：

- Docker Desktop
- Git
- IDE

---

## Docker

負責：

- Runtime
- Database
- Cache
- Queue
- Mail

---

## Application

負責：

- Business Logic
- API
- UI
- Testing

---

# Environment Variables

建議分離：

```text
Root

↓

api

↓

web
```

例如：

```text
.env

api/.env

web/.env
```

Root：

提供 Docker Compose。

Laravel：

提供 Application。

React：

提供 Frontend。

---

# First-Time Setup

標準流程：

```text
Clone Repository

↓

建立 .env

↓

docker compose up

↓

composer install

↓

npm install

↓

artisan key:generate

↓

artisan migrate

↓

開始開發
```

所有開發者：

流程應一致。

---

# Daily Startup

每天開始工作：

```text
Pull Latest Code

↓

docker compose up

↓

確認 Container Healthy

↓

開始開發
```

避免：

每天重新建立 Container。

---

# Daily Shutdown

完成工作：

```bash
docker compose stop
```

通常不需要：

```bash
docker compose down
```

除非：

需要重新建立環境。

---

# Source Code

Source Code：

應：

使用：

Bind Mount。

例如：

```yaml
./api:/var/www/html

./web:/app
```

修改程式：

立即同步：

Container。

---

# Dependency Management

PHP：

使用：

```bash
composer install
```

Node：

使用：

```bash
npm install
```

不要：

直接修改：

```text
vendor

node_modules
```

---

# Database

Database：

建議：

Container。

資料：

使用：

Named Volume。

避免：

每次：

Container 重建：

資料遺失。

---

# Cache

Redis：

建議：

獨立 Container。

用途：

- Cache
- Queue
- Session
- Lock

不要：

使用：

Database：

當 Cache。

---

# Queue

Queue：

建議：

獨立 Worker。

例如：

```text
queue
```

不要：

與：

HTTP：

放一起。

---

# Scheduler

Scheduler：

建議：

獨立 Container。

例如：

```text
scheduler
```

不要：

依賴：

Host Cron。

---

# Mail

開發：

建議：

Mailpit。

不要：

直接：

寄：

正式 Email。

---

# Logs

查看：

```bash
docker compose logs
```

查看：

單一：

```bash
docker compose logs api
```

不要：

進入：

Container：

找 Log。

---

# File Permission

開發：

建議：

Container User：

與：

Host User：

一致。

例如：

```text
UID

GID
```

避免：

root：

建立檔案。

---

# Git

Container：

不得：

管理 Git。

Git：

應：

由：

Host：

執行。

例如：

```bash
git status

git commit

git push
```

---

# IDE

建議：

VS Code。

使用：

Docker：

相關 Extension。

例如：

- Docker
- Dev Containers
- PHP Intelephense
- ESLint
- Prettier

---

# Debug

Debug：

建議：

Container：

內完成。

例如：

```bash
docker compose exec api bash
```

不要：

依賴：

Host。

---

# Testing

Backend：

```bash
php artisan test
```

Frontend：

```bash
npm run test
```

都應：

Container：

內執行。

---

# Updating Dependencies

更新：

Composer：

```bash
composer update
```

應：

經過：

團隊確認。

不要：

日常：

直接：

Update。

Node：

同樣：

避免：

無計畫更新。

---

# Environment Reset

需要重建：

```bash
docker compose down
```

重新：

```bash
docker compose up
```

只有：

需要刪除資料：

才：

```bash
docker compose down -v
```

---

# Backup

重要資料：

例如：

MySQL：

應：

定期：

Backup。

不要：

依賴：

Docker Volume。

---

# Common Mistakes

避免：

- Host 安裝 PHP
- Host 安裝 MySQL
- 修改 vendor
- 修改 node_modules
- 使用 root
- Container 寫入 Host 無權限
- 使用 localhost 連 MySQL
- Commit `.env`
- Commit `vendor`
- Commit `node_modules`

---

# Best Practices

建議：

1. 所有 Runtime 都使用 Docker。
2. Source Code 使用 Bind Mount。
3. Database 使用 Named Volume。
4. Queue、Scheduler 獨立 Container。
5. Host 只保留 Docker、Git 與 IDE。
6. Container 盡量保持 Stateless。
7. 每位開發者使用相同的 Compose 架構。
8. 依賴版本固定，不使用 `latest`。
9. 每次修改環境後同步更新文件。
10. 新成員應能依 README 在短時間內完成環境建置。

---

# Checklist

建立新的開發環境前：

- [ ] Docker Desktop 已安裝
- [ ] WSL2 已啟用（Windows）
- [ ] Git 已安裝
- [ ] Compose 可正常執行
- [ ] Container 可正常啟動
- [ ] Database 可連線
- [ ] Redis 可連線
- [ ] Mailpit 可使用
- [ ] Laravel 正常啟動
- [ ] React 正常啟動
- [ ] Queue 正常運作
- [ ] Scheduler 正常運作
- [ ] Health Check 全部通過

---

# Related Documents

建議閱讀順序：

```text
README.md
    ↓
architecture.md
    ↓
compose-standards.md
    ↓
networking.md
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