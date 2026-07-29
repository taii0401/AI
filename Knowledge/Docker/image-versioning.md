# Docker Image Versioning

## Purpose

本文件定義 Docker Image 的版本管理、命名規範、發布流程與生命週期管理。

目的：

- 建立一致的 Image 命名方式
- 建立可追蹤的版本
- 提供 Rollback 能力
- 提高 CI/CD 可維護性
- 降低部署風險
- 避免使用不可預測的 Image

本文件不綁定任何特定專案。

---

# Goals

Image Versioning 應符合：

- 可追蹤 (Traceable)
- 可重建 (Reproducible)
- 可回滾 (Rollback)
- 可維護 (Maintainable)
- 可自動化 (Automatable)

---

# Image Lifecycle

Docker Image 建議生命週期：

```text
Dockerfile

↓

Build

↓

Local Test

↓

Tag

↓

Push

↓

Registry

↓

Deploy

↓

Production

↓

Archive

↓

Remove
```

每一個階段都應可追蹤。

---

# Repository Naming

Repository 應能直接反映用途。

例如：

```text
company/php

company/nginx

company/node

company/laravel-runtime

company/react-runtime
```

避免：

```text
image1

docker

myimage

project

test
```

---

# Image Tag

Tag 應代表：

- Runtime
- Version
- Release

例如：

```text
php:8.4-fpm

node:22-alpine

mysql:8.4

redis:7-alpine
```

---

# Never Use latest

避免：

```text
latest
```

原因：

- 無法知道版本
- 無法 Rollback
- 每次 Pull 結果可能不同
- CI/CD 不可預測

建議：

```text
8.4

8.4.1

8.4.1-r1
```

---

# Semantic Versioning

建議遵循：

```text
MAJOR.MINOR.PATCH
```

例如：

```text
1.0.0

1.0.1

1.1.0

2.0.0
```

---

## Major

代表：

重大變更。

例如：

```text
PHP 8.3

↓

PHP 8.4
```

可能：

不相容。

---

## Minor

代表：

新增功能。

例如：

```text
增加 GD

增加 Imagick

增加 Extension
```

通常：

向下相容。

---

## Patch

代表：

修正：

例如：

- Security Fix
- Bug Fix
- Package Update

不影響：

API。

---

# Runtime Version

若 Runtime 很重要：

可直接加入：

Tag。

例如：

```text
laravel-php:8.4

laravel-php:8.4-v2

laravel-php:8.4.1
```

容易辨識。

---

# Build Version

CI：

可加入：

Build Number。

例如：

```text
1.2.0-build35
```

或：

```text
20260728
```

方便：

追蹤。

---

# Git Commit Tag

建議：

每個 Image：

都有：

Commit。

例如：

```text
1.2.0-a34fe21
```

即可知道：

哪一次：

Commit。

---

# Git Branch

不要：

直接：

使用：

```text
master

main

develop
```

當：

正式：

Image Tag。

可：

作為：

Temporary Tag。

例如：

```text
develop

feature-login

release
```

---

# Stable Tag

可：

建立：

```text
stable
```

例如：

```text
stable

↓

1.5.3
```

方便：

Production。

---

# Development Tag

開發：

可：

```text
dev
```

例如：

```text
dev

↓

每天更新
```

不要：

部署：

Production。

---

# Registry

Image：

應：

集中：

Registry。

例如：

```text
Docker Hub

GitHub Container Registry

GitLab Registry

AWS ECR

Azure ACR

Google Artifact Registry
```

避免：

每位工程師：

自己：

Build。

---

# Immutable Image

Image：

發布後：

不要：

覆蓋。

例如：

```text
1.5.0
```

永遠：

代表：

同一個 Image。

不要：

重新：

Push：

```text
1.5.0
```

不同內容。

---

# Rollback

Production：

應：

保留：

舊版本。

例如：

```text
1.4.8

1.4.9

1.5.0
```

若：

1.5.0：

有問題：

立即：

Rollback：

```text
1.4.9
```

---

# Build Once

Image：

建議：

Build：

一次。

流程：

```text
Build

↓

Test

↓

Push

↓

Deploy
```

不要：

Production：

重新：

Build。

避免：

產生：

不同：

Image。

---

# Digest

Docker：

每個 Image：

都有：

Digest。

例如：

```text
sha256:xxxxxx
```

Digest：

代表：

真正：

Image。

Tag：

只是：

Alias。

---

# Verify Image

查看：

Image：

```bash
docker images
```

查看：

Digest：

```bash
docker image inspect image_name
```

---

# Pull Policy

Production：

建議：

Pull：

固定版本。

例如：

```text
php:8.4-fpm
```

不要：

```text
latest
```

---

# Image Cleanup

定期：

清理：

未使用：

Image。

例如：

```bash
docker image prune
```

全部：

未使用：

```bash
docker system prune
```

注意：

Production：

避免：

隨意：

清除。

---

# Image Size

Image：

越小：

越好。

建議：

- Alpine
- Multi-stage Build
- 清除 Cache
- 清除 Package Cache
- 不安裝不必要工具

---

# Security

Image：

應：

- 使用官方 Image
- 固定版本
- 定期更新
- 定期掃描
- 不包含 Secret
- 不包含 `.env`
- 不包含測試資料

---

# CI/CD

建議流程：

```text
Git Push

↓

CI

↓

Build Image

↓

Run Test

↓

Push Registry

↓

Deploy

↓

Production
```

Production：

不要：

重新：

Build。

---

# Common Mistakes

避免：

- 使用 latest
- 覆蓋既有 Tag
- 沒有 Rollback
- 每台機器自己 Build
- Image 太大
- Image 包含 Secret
- Image 沒有固定版本
- Production 使用 develop

---

# Best Practices

建議：

1. 每個版本都有唯一 Tag。
2. 使用 Semantic Version。
3. Production 永遠使用固定版本。
4. 保留最近幾個可 Rollback 版本。
5. CI Build 一次，部署重複使用同一 Image。
6. 定期更新 Base Image。
7. 定期掃描安全漏洞。
8. Image 不包含敏感資訊。
9. Image 不依賴本機環境。
10. 建立完整發布紀錄。

---

# Checklist

發布 Image 前：

- [ ] Base Image 已更新
- [ ] Tag 已確認
- [ ] Build 成功
- [ ] 測試通過
- [ ] 無 Secret
- [ ] 無 `.env`
- [ ] Image 大小合理
- [ ] 已 Push Registry
- [ ] 已建立 Release Note
- [ ] 可 Rollback

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
image-versioning.md
    ↓
permissions.md
    ↓
volumes.md
    ↓
troubleshooting.md
```