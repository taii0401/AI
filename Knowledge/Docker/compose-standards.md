# Docker Compose Standards

## Purpose

本文件定義 Docker Compose 的通用設計規範。

目的：

- 建立一致的 Docker Compose 架構
- 降低不同專案間的差異
- 提高可維護性
- 提高可讀性
- 降低 AI Agent 誤判
- 降低多人協作成本

本文件不綁定任何特定專案。

---

# Compose File

建議使用：

```text
compose.yaml
```

若需要不同環境：

```text
compose.yaml
compose.override.yaml
compose.production.yaml
```

不建議：

```text
docker-compose.yml
```

除非維護舊專案。

---

# Project Name

每個專案都應設定：

```yaml
name: project-name
```

例如：

```yaml
name: hopkins-helper
```

避免：

不同專案：

產生相同：

- Container
- Network
- Volume

名稱。

---

# Directory Structure

Compose 應放於：

```text
project/

├── api/
├── web/
├── docker/
├── compose.yaml
└── .env
```

不建議：

```text
docker/

└── compose.yaml
```

因為：

相對路徑會變得複雜。

---

# Service Naming

Service 名稱應直接代表責任。

建議：

```yaml
services:

  api:

  api-nginx:

  web:

  mysql:

  redis:

  mailpit:

  queue:

  scheduler:
```

避免：

```text
server

backend

backend2

php

php8

mysql-test

container1
```

---

# Service Responsibility

一個 Service：

一個主要責任。

例如：

## api

只負責：

- PHP
- Laravel

不要：

- Nginx
- MySQL

---

## web

只負責：

- React
- Node
- npm
- Vite

---

## mysql

只負責：

Database。

---

## redis

只負責：

Redis。

---

## queue

只負責：

Laravel Queue Worker。

---

## scheduler

只負責：

Laravel Scheduler。

---

# Image

Image 必須固定版本。

例如：

```yaml
image: mysql:8.4

image: redis:7-alpine

image: nginx:alpine

image: node:22-alpine
```

避免：

```yaml
image: latest
```

因為：

版本不可預測。

---

# Build

若專案需要自建 Image：

使用：

```yaml
build:
  context: .
  dockerfile: docker/php/Dockerfile
```

不要：

```yaml
build:
  context: docker/php
```

因為：

會讓：

COPY

路徑容易錯誤。

---

# Environment

Compose：

所有可修改參數：

都應：

使用：

```yaml
${VARIABLE}
```

例如：

```yaml
ports:

- "${API_PORT:-8080}:80"
```

不要：

```yaml
ports:

- "8080:80"
```

直接寫死。

---

# Root .env

Compose：

只讀：

根目錄：

```text
.env
```

用途：

例如：

```dotenv
API_PORT=8080

WEB_PORT=5173

DB_DATABASE=app

DB_USERNAME=app

DB_PASSWORD=password
```

Laravel：

不要：

直接讀：

Compose 的：

```text
.env
```

Laravel：

有自己的：

```text
api/.env
```

React：

有自己的：

```text
web/.env
```

---

# Ports

Compose：

Port：

統一：

```text
Host

↓

Container
```

例如：

```yaml
3307:3306
```

代表：

Host：

3307

Container：

3306

Container：

互相連線：

仍然：

使用：

```text
3306
```

不要：

使用：

```text
3307
```

---

# Network

所有 Service：

加入：

```yaml
networks:

- app_network
```

Network：

建議：

```yaml
networks:

  app_network:

    driver: bridge
```

Container：

彼此：

透過：

Service Name：

連線。

例如：

```dotenv
DB_HOST=mysql

REDIS_HOST=redis

MAIL_HOST=mailpit
```

不要：

使用：

```text
localhost

127.0.0.1
```

---

# Volumes

## Source Code

使用：

Bind Mount。

例如：

```yaml
volumes:

- ./api:/var/www/html

- ./web:/app
```

---

## Database

使用：

Named Volume。

例如：

```yaml
mysql_data:

redis_data:
```

---

## 不建議

將：

MySQL：

直接：

Bind Mount：

到：

Host。

除非：

有特殊需求。

---

# Depends On

有啟動順序需求：

使用：

```yaml
depends_on:

  mysql:

    condition: service_healthy

  redis:

    condition: service_healthy
```

不要：

只寫：

```yaml
depends_on:

- mysql
```

因為：

Container：

可能：

尚未 Ready。

---

# Healthcheck

MySQL：

Redis：

應設定：

Healthcheck。

例如：

```yaml
healthcheck:

  test:

  retries:

  interval:

  timeout:
```

避免：

Laravel：

啟動：

早於：

Database。

---

# Restart

一般：

開發環境：

建議：

```yaml
restart: unless-stopped
```

不要：

```yaml
restart: always
```

除非：

Production。

---

# Profiles

非必要：

Service：

使用：

```yaml
profiles:

- application
```

例如：

```text
api

api-nginx

web

queue

scheduler
```

Infrastructure：

例如：

```text
mysql

redis

mailpit
```

可：

直接：

啟動。

---

# Command

建議：

使用：

Array。

例如：

```yaml
command:

- php

- artisan

- queue:work

- redis
```

不要：

```yaml
command: php artisan queue:work redis
```

原因：

Array：

避免：

Shell：

跳脫。

---

# User

若需要：

Host：

共同開發：

建議：

```yaml
user:

"${APP_UID:-1000}:${APP_GID:-1000}"
```

避免：

Container：

建立：

root：

檔案。

---

# Logging

一般：

使用：

Docker：

預設：

Log。

查看：

```bash
docker compose logs api

docker compose logs web
```

不要：

自行：

Redirect：

Log。

---

# Container Name

不建議：

設定：

```yaml
container_name:
```

讓：

Compose：

自動：

管理。

原因：

避免：

不同專案：

名稱：

衝突。

---

# Environment Separation

Compose：

負責：

Infrastructure。

Laravel：

負責：

Application。

React：

負責：

Frontend。

不要：

將：

Laravel：

設定：

全部：

搬到：

Compose。

---

# Security

不得：

在：

compose.yaml：

寫：

```text
Password

API Key

Token

Secret
```

應：

使用：

```text
.env

Secret Manager
```

---

# Common Commands

查看：

```bash
docker compose config
```

查看：

Services：

```bash
docker compose --profile application config --services
```

啟動：

```bash
docker compose up -d
```

停止：

```bash
docker compose stop
```

刪除：

Container：

```bash
docker compose down
```

不要：

```bash
docker compose down -v
```

除非：

確定：

可以：

刪除：

Volume。

---

# Checklist

建立新的 compose 前：

- [ ] Project Name
- [ ] Service Naming
- [ ] Environment Variables
- [ ] Network
- [ ] Volume
- [ ] Healthcheck
- [ ] Depends On
- [ ] Restart
- [ ] Profiles
- [ ] Security
- [ ] Documentation

全部：

確認。

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

dockerfile-standards.md

↓

development-environment.md

↓

permissions.md

↓

volumes.md

↓

troubleshooting.md
```