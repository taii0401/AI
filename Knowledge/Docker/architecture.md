# Docker Architecture

## Purpose

本文件定義 Docker 開發環境的整體架構、設計原則、Container 責任、服務間關係與資料流。

本文件屬於通用知識，不綁定任何特定專案。

---

# Goals

Docker 開發環境的主要目標：

- 建立一致的開發環境
- 避免不同電腦環境差異
- 降低安裝成本
- 降低版本衝突
- 容易重建環境
- 容易分享給其他開發者
- 可快速建立新專案

---

# Architecture Overview

```text
                        Browser
                    ┌─────────────┐
                    │             │
                    └──────┬──────┘
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
     React SPA                     Laravel API
 (http://localhost:5173)     (http://localhost:8080)
             │                           │
             │ HTTP / JSON               │
             └──────────────┬────────────┘
                            │
                     Docker Network
                            │
    ┌──────────────┬────────┴────────┬──────────────┐
    ▼              ▼                 ▼              ▼
 MySQL          Redis            Mailpit      Queue Worker
    │              │
    └──────────────┘
```

---

# Development Architecture

```text
docker-laravel-react/

├── api/
│
├── web/
│
├── docker/
│
├── compose.yaml
│
└── docs/
```

每個目錄都有明確責任。

---

# Container Responsibility

## API

Container：

```text
api
```

負責：

- PHP Runtime
- Laravel
- Composer
- Artisan
- Business Logic
- API

不負責：

- HTTP Server
- React
- MySQL
- Redis

---

## API Nginx

Container：

```text
api-nginx
```

負責：

- HTTP Request
- Static Files
- Reverse Proxy
- FastCGI

不負責：

- Laravel Logic
- Composer
- Queue

---

## Web

Container：

```text
web
```

負責：

- React
- TypeScript
- npm
- Vite
- Frontend Development

不負責：

- PHP
- MySQL
- Redis

---

## MySQL

Container：

```text
mysql
```

負責：

- Relational Database
- Transaction
- Data Persistence

不負責：

- Cache
- Queue

---

## Redis

Container：

```text
redis
```

負責：

- Cache
- Queue
- Session
- Lock
- Temporary Data

不負責：

- Permanent Business Data

---

## Mailpit

Container：

```text
mailpit
```

負責：

- SMTP Server
- Mail Preview

不負責：

- Real Email Delivery

---

## Queue

Container：

```text
queue
```

負責：

- Queue Worker
- Background Job

不負責：

- HTTP Request

---

## Scheduler

Container：

```text
scheduler
```

負責：

- Laravel Schedule
- Cron Job

不負責：

- Queue Dispatch

---

# Data Flow

## Frontend Request

```text
Browser

↓

React

↓

API Client

↓

Laravel API

↓

Service

↓

Database
```

---

## Queue

```text
Laravel

↓

Dispatch Job

↓

Redis

↓

Queue Worker

↓

Execute Job
```

---

## Mail

```text
Laravel

↓

SMTP

↓

Mailpit

↓

Browser Preview
```

---

# Container Communication

Container 之間透過：

```text
Docker Network
```

例如：

Laravel：

```dotenv
DB_HOST=mysql

REDIS_HOST=redis

MAIL_HOST=mailpit
```

不是：

```dotenv
localhost

127.0.0.1
```

---

# Network

所有 Container：

```text
app_network
```

```text
api

↓

mysql

↓

redis

↓

mailpit

↓

web
```

都在同一個 Network。

因此：

```text
mysql
```

就是：

```text
hostname
```

---

# Volume

## Source Code

使用：

```text
Bind Mount
```

例如：

```yaml
./api:/var/www/html

./web:/app
```

原因：

方便：

- IDE
- Git
- AI
- Debug

---

## Database

使用：

```text
Named Volume
```

例如：

```yaml
mysql_data
```

原因：

避免：

```text
Container 刪除

↓

資料消失
```

---

## Redis

使用：

```yaml
redis_data
```

保存：

Redis Persistence。

---

# Environment

開發環境：

```text
Docker Desktop

↓

WSL2

↓

Ubuntu

↓

Docker Compose

↓

Containers
```

每位開發者：

都應盡量保持一致。

---

# Environment Variables

分三層：

## Root

```text
.env
```

用途：

Docker Compose。

例如：

```text
API_PORT

WEB_PORT

MYSQL_FORWARD_PORT
```

---

## Laravel

```text
api/.env
```

用途：

Laravel。

例如：

```text
APP_KEY

DB_HOST

CACHE_STORE

QUEUE_CONNECTION
```

---

## React

```text
web/.env
```

用途：

Vite。

例如：

```text
VITE_API_BASE_URL
```

---

# Project Independence

Docker Architecture：

不得包含：

```text
Hopkins Helper

AI Task Manager
```

所有：

Project

都共用：

```text
Docker Architecture
```

---

# Design Principles

## One Responsibility

每個 Container：

一個主要責任。

例如：

```text
MySQL

↓

只做 Database
```

不要：

```text
PHP

+

MySQL

+

Redis

同一個 Container
```

---

## Stateless

Application Container：

可隨時：

```text
刪除

↓

重建
```

資料：

不應存在：

```text
Container
```

應：

```text
Volume
```

---

## Replaceable

任何 Container：

都應：

```text
Stop

↓

Remove

↓

Create

↓

Run
```

而：

不用：

重新安裝。

---

## Reproducible

任何電腦：

只要：

```text
Docker Desktop

WSL

Git
```

就應能：

```text
docker compose up
```

建立相同環境。

---

# Development Workflow

建立專案：

```text
Template

↓

Copy

↓

Project

↓

docker compose up

↓

composer install

↓

npm install

↓

Start Development
```

---

# Production Difference

本文件：

主要描述：

```text
Development
```

正式環境：

可能增加：

- Reverse Proxy
- HTTPS
- Load Balancer
- CDN
- Secret Manager
- Monitoring
- Backup
- Auto Scaling

因此：

Development

≠

Production

---

# Checklist

建立 Docker 環境前：

- [ ] Docker Desktop
- [ ] WSL2
- [ ] Ubuntu
- [ ] Git
- [ ] Docker Compose

建立後：

- [ ] MySQL
- [ ] Redis
- [ ] Mailpit
- [ ] API
- [ ] Nginx
- [ ] React

全部正常。

---

# Related Documents

閱讀順序：

```text
README.md

↓

architecture.md

↓

compose-standards.md

↓

dockerfile-standards.md

↓

development-environment.md
```