# Docker Security

## Purpose

本文件定義 Docker 開發與部署環境的安全原則、最佳實踐與安全基線（Security Baseline）。

目的：

- 建立安全的 Docker 開發流程
- 降低 Container 被攻擊的風險
- 保護 Secrets 與敏感資料
- 建立可持續維護的安全策略
- 建立團隊共同遵循的安全標準

本文件適用於：

- Docker Desktop
- Docker Engine
- Docker Compose
- Docker Hub
- GitHub Container Registry
- AWS ECR
- Laravel
- React
- Node.js

---

# Security Goals

Docker Security 應符合：

- Least Privilege
- Defense in Depth
- Immutable Infrastructure
- Secure by Default
- Principle of Separation
- Traceability

---

# Security Layers

Docker 安全可分成：

```text
Source Code

↓

Dockerfile

↓

Docker Image

↓

Registry

↓

Container

↓

Network

↓

Host
```

每一層都需要安全措施。

---

# Least Privilege

所有 Container：

應只擁有完成工作所需的最小權限。

例如：

不要：

```text
root
```

建議：

```dockerfile
USER app
```

---

# Root User

避免：

```dockerfile
USER root
```

整個 Runtime。

Root：

應只在：

Build：

階段：

使用。

Application：

應：

切換：

```dockerfile
USER app
```

---

# Official Images

優先：

使用：

官方 Image。

例如：

```text
php

mysql

redis

nginx

node
```

避免：

來源：

不明：

Image。

---

# Pin Image Version

固定：

版本。

例如：

```dockerfile
FROM php:8.4-fpm
```

不要：

```dockerfile
FROM php:latest
```

---

# Verify Image Source

Image：

應：

來自：

可信 Registry。

例如：

- Docker Hub Official
- GitHub Container Registry
- AWS ECR
- Azure ACR
- Google Artifact Registry

---

# Image Scanning

Image：

應：

定期：

掃描。

例如：

- Docker Scout
- Trivy
- Grype
- Snyk

確認：

是否：

存在：

CVE。

---

# Update Base Image

Base Image：

應：

定期：

更新。

例如：

```text
每月

↓

重新 Build

↓

重新 Scan
```

避免：

使用：

數年前：

Image。

---

# Secrets

不要：

將：

```text
Password

API Key

Token

Private Key

Secret
```

寫入：

Dockerfile。

---

# .env

不要：

將：

```text
.env
```

Build：

進：

Image。

避免：

```dockerfile
COPY . .
```

包含：

`.env`

---

# .dockerignore

必須：

建立：

```text
.dockerignore
```

至少：

```text
.git

.env

node_modules

vendor

storage/logs

.idea

.vscode
```

避免：

敏感資料：

進入：

Build Context。

---

# Environment Variables

Secrets：

建議：

由：

Compose：

或：

Secret Manager：

提供。

不要：

Hard Code。

---

# Docker Secrets

正式環境：

建議：

使用：

Docker Secrets。

不要：

直接：

Environment Variables：

存放：

高敏感資料。

---

# Read Only Filesystem

若：

Application：

不需：

寫入。

可：

設定：

```yaml
read_only: true
```

降低：

被修改：

風險。

---

# Writable Directories

只有：

必要：

目錄：

可寫。

例如：

Laravel：

```text
storage/

bootstrap/cache/
```

其它：

保持：

Read Only。

---

# Linux Capabilities

Container：

不要：

擁有：

全部：

Linux Capability。

例如：

移除：

```yaml
cap_drop:

- ALL
```

需要：

再：

加入：

```yaml
cap_add:
```

---

# Privileged Mode

避免：

```yaml
privileged: true
```

除非：

特殊需求。

因為：

Container：

幾乎：

取得：

Host：

全部權限。

---

# Docker Socket

不要：

掛載：

```text
/var/run/docker.sock
```

除非：

真的：

需要。

因為：

等同：

控制：

Docker Host。

---

# SSH

不要：

Container：

內：

安裝：

SSH Server。

Container：

應：

單一用途。

Debug：

使用：

```bash
docker exec
```

即可。

---

# Package Installation

不要：

安裝：

無關：

套件。

例如：

- FTP
- Browser
- Desktop
- SSH
- Telnet

降低：

攻擊面。

---

# Network

只有：

需要：

對外：

提供：

服務：

才：

開：

```yaml
ports
```

Database：

通常：

不用：

Expose：

給：

Host。

---

# Internal Communication

Container：

互相：

使用：

Docker Network。

不要：

公開：

所有：

Port。

---

# File Permissions

不要：

使用：

```bash
chmod -R 777
```

建議：

最小：

權限。

---

# Logs

Log：

不要：

包含：

- Password
- Token
- Secret
- Access Key
- Session ID
- Credit Card
- 個人敏感資料

---

# Build Context

Build：

只：

包含：

必要：

檔案。

避免：

將：

Git History

測試資料

憑證

一起：

Build。

---

# Supply Chain Security

Image：

來源：

必須：

可信。

Dependency：

固定：

版本。

避免：

下載：

未知：

Script。

例如：

不要：

```bash
curl xxx | sh
```

除非：

可信：

來源。

---

# Registry Security

Registry：

建議：

- Private Repository
- MFA
- Access Control
- Audit Log

Production：

不要：

使用：

公開：

Registry。

---

# CI/CD

CI：

應：

- Build
- Scan
- Test

全部：

成功：

才：

Deploy。

不要：

直接：

Deploy：

未掃描：

Image。

---

# Runtime Security

Container：

建議：

- Non-root User
- Read Only FS
- Health Check
- Resource Limit
- Restart Policy

---

# Resource Limits

建議：

限制：

CPU：

Memory。

例如：

```yaml
deploy:

  resources:
```

避免：

Container：

耗盡：

Host：

資源。

---

# Security Checklist

建立新的 Docker 專案前：

- [ ] 使用官方 Image
- [ ] 固定版本
- [ ] 非 root User
- [ ] 建立 `.dockerignore`
- [ ] Secret 未寫入 Image
- [ ] `.env` 未加入 Image
- [ ] Image 已掃描
- [ ] Build Context 已檢查
- [ ] Network 已隔離
- [ ] Port 最小化
- [ ] Log 不包含敏感資訊
- [ ] Read Only Directory 已確認
- [ ] Resource Limit 已設定
- [ ] Registry 已啟用權限控管

---

# Common Mistakes

避免：

- 使用 latest
- 使用 root
- COPY `.env`
- Commit Secret
- Image 太大
- 未掃描 CVE
- 安裝 SSH
- 開放全部 Port
- 掛載 docker.sock
- privileged=true
- chmod 777

---

# Best Practices

建議：

1. 使用官方 Base Image。
2. 固定 Image Version。
3. 定期更新 Base Image。
4. 每次 Build 都進行漏洞掃描。
5. 使用非 root User。
6. Secrets 不寫入 Image。
7. Build Context 保持最小。
8. 只開放必要 Port。
9. 定期清理不用的 Image。
10. 建立 Security Review 流程。

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