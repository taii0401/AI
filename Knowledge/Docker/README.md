# Docker Knowledge

## Purpose

本目錄記錄 Docker 在軟體開發中的通用知識、設計原則、最佳實踐、開發規範與問題排除方式。

本知識庫不屬於任何特定專案，可重複使用於不同的 Laravel、React、Node.js、PHP 或其他專案。

Docker Template 則放置於：

```text
Templates/
```

Docker Project 則放置於：

```text
Projects/
```

---

# Scope

目前主要適用於：

- Docker Desktop
- Docker Engine
- Docker Compose
- WSL2
- PHP
- Laravel
- React
- Node.js
- MySQL
- Redis
- Mailpit
- Queue Worker
- Scheduler

---

# Knowledge Structure

```text
Docker/
├── README.md
├── architecture.md
├── compose-standards.md
├── dockerfile-standards.md
├── development-environment.md
├── image-versioning.md
├── permissions.md
├── volumes.md
└── troubleshooting.md
```

---

# File Description

## architecture.md

Docker 開發環境整體架構。

包含：

- Docker Architecture
- Container Responsibility
- Service Responsibility
- Network
- Runtime Flow
- Development vs Production

適用：

- 新專案規劃
- Docker 架構設計
- 系統架構討論

---

## compose-standards.md

Docker Compose 撰寫規範。

包含：

- Service Naming
- Network
- Volumes
- Environment
- Profiles
- Healthcheck
- Depends On
- Restart Policy

適用：

- compose.yaml
- compose.override.yaml
- docker compose

---

## dockerfile-standards.md

Dockerfile 撰寫規範。

包含：

- Base Image
- Layer
- Cache
- PHP Extension
- Composer
- User
- Security
- Build Strategy

適用：

- Docker Image
- PHP Image
- Node Image

---

## development-environment.md

本機開發環境流程。

包含：

- 第一次建立
- 啟動
- 停止
- Daily Startup
- Composer
- npm
- Migration
- Testing

適用：

- Local Development
- Docker Desktop
- WSL2

---

## image-versioning.md

Docker Image 管理方式。

包含：

- Docker Hub
- Repository
- Tag
- Version
- Digest
- Push
- Pull

適用：

- Image Release
- CI/CD

---

## permissions.md

Docker 權限管理。

包含：

- UID
- GID
- root
- app User
- Composer Permission
- npm Permission
- Vite Permission
- Laravel Permission

適用：

- Linux
- WSL2
- Docker Desktop

---

## volumes.md

Volume 使用原則。

包含：

- Bind Mount
- Named Volume
- Anonymous Volume
- Data Persistence
- Source Code
- MySQL
- Redis

適用：

- Compose
- Docker Desktop

---

## troubleshooting.md

Docker 問題排除。

包含：

- Container
- Image
- Compose
- Network
- Permission
- Composer
- npm
- Vite
- Laravel
- MySQL
- Redis

適用：

- 日常開發
- 問題診斷

---

# Design Principles

Docker Knowledge 應遵循以下原則：

## 1. Project Independent

不得包含任何特定專案資訊。

例如：

```text
Hopkins Helper
AI Task Manager
```

不應出現在本知識庫。

---

## 2. Version Independent

除非必要，不綁定版本。

例如：

```text
PHP 8.4
Laravel 12
```

若涉及版本差異，應描述：

```text
Laravel 10+
Laravel 11+
Laravel 12+
```

而不是固定單一版本。

---

## 3. Reusable

所有內容都應可重複使用。

例如：

```text
Docker Compose 命名

Dockerfile

Volume

Network

Permission
```

都屬於通用知識。

---

## 4. Best Practice

優先紀錄：

- Best Practice
- Design Pattern
- Standard
- Checklist

而不是：

```text
今天遇到什麼 Bug
```

Bug 應整理成：

```text
Problem

↓

Root Cause

↓

Solution

↓

Prevention
```

---

## 5. Evolution

Knowledge 可以持續增加。

例如：

今天學到：

```text
Docker Build Cache
```

新增：

```text
docker-build-cache.md
```

不用修改既有文件。

---

# Relationship

Docker Knowledge 與其他 Workspace 的關係：

```text
Knowledge
        │
        ├── Docker
        ├── Laravel
        ├── React
        ├── Redis
        └── Architecture
                │
                ▼
Templates
        │
        ▼
Projects
```

Knowledge：

說明

↓

Template：

提供可直接使用

↓

Project：

真正開發

---

# Usage

建立新專案時：

```text
閱讀 Docker Knowledge

↓

閱讀 Template

↓

建立 Project
```

修改 Docker：

```text
先確認 Docker Knowledge

↓

確認是否符合標準

↓

修改 compose

↓

更新 Template
```

---

# Maintenance

新增知識：

請建立新的 Markdown。

避免：

```text
README.md
5000 行
```

建議：

每一個主題一個 Markdown。

例如：

```text
permissions.md

volumes.md

healthcheck.md

network.md
```

---

# References

官方文件：

Docker

https://docs.docker.com/

Docker Compose

https://docs.docker.com/compose/

Docker Hub

https://hub.docker.com/