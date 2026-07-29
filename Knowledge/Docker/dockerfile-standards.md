# Dockerfile Standards

## Purpose

本文件定義 Dockerfile 的通用設計規範。

目的：

- 建立一致的 Docker Image
- 提高 Build 效率
- 提高可維護性
- 降低 Image 大小
- 提高 Build Cache 命中率
- 降低安全風險
- 建立可重複建置（Reproducible Build）的 Docker Image

本文件不綁定任何特定語言或專案。

---

# Dockerfile Responsibilities

Dockerfile 的責任：

- 建立 Runtime Environment
- 安裝必要套件
- 建立執行環境
- 建立預設 User
- 建立 Entrypoint
- 建立可重複建置的 Image

Dockerfile 不應負責：

- 安裝專案程式碼
- 執行 Migration
- 初始化資料庫
- 建立測試資料
- 啟動多個服務

---

# One Image, One Responsibility

每個 Image 應只有一個主要責任。

例如：

```text
PHP Runtime
```

或：

```text
Node Runtime
```

避免：

```text
PHP

+

MySQL

+

Redis

+

Nginx
```

放在同一個 Image。

---

# Base Image

應選擇：

- 官方 Image
- 維護中的 Image
- 長期支援版本（LTS）
- 安全更新頻繁

例如：

```dockerfile
FROM php:8.4-fpm
```

```dockerfile
FROM node:22-alpine
```

```dockerfile
FROM nginx:alpine
```

避免：

```dockerfile
FROM ubuntu
```

再自行安裝所有套件。

---

# Pin Version

Image 必須固定版本。

建議：

```dockerfile
FROM php:8.4-fpm
```

不要：

```dockerfile
FROM php:latest
```

原因：

- Build 不可重現
- 新版可能造成相容性問題
- 難以追蹤問題來源

---

# Small Image Principle

優先使用：

```text
Alpine
Slim
Minimal
```

例如：

```dockerfile
FROM node:22-alpine
```

不要安裝：

- Editor
- Browser
- Desktop
- GUI
- 無關套件

Image 越小：

- Build 越快
- Pull 越快
- Push 越快
- 安全漏洞越少

---

# Layer Design

每一個指令都會產生新的 Layer。

例如：

```dockerfile
RUN apt-get update

RUN apt-get install git
```

應改為：

```dockerfile
RUN apt-get update \
 && apt-get install -y git
```

減少 Layer 數量。

---

# Build Cache

Docker Build 依 Layer 快取。

應將：

不常變動

放前面。

例如：

```dockerfile
COPY composer.json composer.lock ./

RUN composer install

COPY . .
```

不要：

```dockerfile
COPY . .

RUN composer install
```

否則：

任何程式修改：

都會重新安裝 Composer。

---

# Multi-stage Build

正式環境建議：

使用：

```dockerfile
FROM ...

AS builder
```

再：

```dockerfile
FROM ...

COPY --from=builder ...
```

好處：

- Image 更小
- Build 更快
- 不包含 Build Tool

例如：

```text
Composer

Node

npm

Git
```

可以只存在 Builder Stage。

---

# COPY Rules

優先：

```dockerfile
COPY
```

避免：

```dockerfile
ADD
```

除非：

真的需要：

- 解壓縮
- Remote URL

否則：

使用：

```dockerfile
COPY
```

即可。

---

# .dockerignore

每個專案：

都應建立：

```text
.dockerignore
```

避免：

傳送：

- .git
- node_modules
- vendor
- storage/logs
- tmp
- IDE 設定

例如：

```text
.git
node_modules
vendor
storage/logs
.idea
.vscode
```

---

# Package Installation

Linux 套件：

建議：

```dockerfile
RUN apt-get update \
 && apt-get install -y \
        git \
        unzip \
 && rm -rf /var/lib/apt/lists/*
```

完成後：

清除：

APT Cache。

避免：

Image 過大。

---

# PHP Extensions

PHP Extension：

集中安裝。

例如：

```dockerfile
RUN docker-php-ext-install \
    pdo_mysql \
    zip \
    bcmath
```

不要：

分散：

多個：

```dockerfile
RUN docker-php-ext-install
```

---

# Composer

Composer：

應安裝：

官方版本。

例如：

```dockerfile
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
```

不要：

```text
curl

↓

下載 installer

↓

php installer
```

除非：

特殊需求。

---

# Working Directory

固定：

```dockerfile
WORKDIR /var/www/html
```

不要：

一直：

```dockerfile
cd
```

---

# User

正式開發：

建議：

建立：

非 root User。

例如：

```dockerfile
RUN useradd app
```

最後：

```dockerfile
USER app
```

避免：

整個 Container：

都使用：

```text
root
```

---

# File Permission

不要：

大量：

```dockerfile
chmod 777
```

應：

最小權限。

例如：

```dockerfile
chmod 775
```

必要時：

搭配：

```dockerfile
chown
```

---

# Environment Variables

Dockerfile：

只放：

固定設定。

例如：

```dockerfile
ENV TZ=Asia/Taipei
```

不要：

放：

```text
Database Password

API Key

Secret

Token
```

這些：

應由：

Compose

或：

環境變數提供。

---

# ENTRYPOINT

ENTRYPOINT：

只做：

初始化。

例如：

- 建立目錄
- 修正權限
- 執行前置檢查

不要：

做：

- Migration
- Seed
- 大量初始化
- 長時間等待

---

# CMD

CMD：

只負責：

預設啟動。

例如：

```dockerfile
CMD ["php-fpm"]
```

不要：

將：

複雜 Script：

全部：

寫在 CMD。

---

# Healthcheck

Healthcheck：

建議：

寫在：

Compose。

不是：

Dockerfile。

因為：

不同環境：

Healthcheck：

可能不同。

---

# Build Context

Dockerfile：

應假設：

Context：

是：

專案根目錄。

例如：

```yaml
build:
  context: .
```

不要：

依賴：

特殊目錄。

---

# Image Naming

建議：

```text
company/php

company/node

company/nginx
```

Tag：

例如：

```text
8.4

8.4-v1

8.4.1
```

不要：

全部：

只有：

```text
latest
```

---

# Security

避免：

安裝：

不必要工具。

例如：

- SSH Server
- FTP Server
- Browser
- GUI
- Debug Tool（正式環境）

不要：

把：

```text
.env
```

放入 Image。

不要：

把：

Secret

寫進 Dockerfile。

---

# Build Performance

建議：

依序：

```text
Base Image

↓

System Package

↓

Language Runtime

↓

Dependency File

↓

Install Dependency

↓

Source Code

↓

CMD
```

避免：

Source Code：

放最前面。

---

# Reproducibility

任何人：

任何電腦：

執行：

```bash
docker build
```

都應產生：

相同：

Image。

不得：

依賴：

本機：

特殊檔案。

---

# Documentation

Dockerfile：

建議加入：

註解。

例如：

```dockerfile
# Install PHP extensions

# Install Composer

# Create app user

# Switch to app user
```

不要：

留下：

大量：

失效註解。

---

# Common Mistakes

避免：

- 使用 latest
- 使用 root 執行所有程式
- 沒有 .dockerignore
- COPY 全部程式後才安裝依賴
- 沒有 Build Cache
- 沒有固定版本
- Image 過大
- 安裝過多套件
- 將密碼寫進 Dockerfile
- 使用 chmod 777
- 一個 Image 執行多個服務

---

# Checklist

建立新的 Dockerfile 前：

- [ ] Base Image 已固定版本
- [ ] 使用官方 Image
- [ ] Image 足夠精簡
- [ ] Layer 已最佳化
- [ ] Build Cache 已考量
- [ ] 建立 `.dockerignore`
- [ ] COPY 順序正確
- [ ] Dependency 安裝最佳化
- [ ] 建立非 root User
- [ ] 權限設定正確
- [ ] Secret 未寫入 Image
- [ ] CMD 僅負責啟動
- [ ] Dockerfile 已加必要註解

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