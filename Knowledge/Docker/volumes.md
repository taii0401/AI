# Docker Volumes

## Purpose

本文件定義 Docker Volume 的設計原則、生命週期、使用策略與最佳實踐。

目的：

- 正確管理資料持久化（Persistence）
- 保護重要資料
- 提高開發效率
- 降低跨平台差異
- 建立一致的 Storage Strategy

本文件適用於：

- Docker Engine
- Docker Compose
- Laravel
- React
- MySQL
- Redis
- PostgreSQL
- Node.js

---

# Why Volumes

Container：

本質上：

```text
Stateless
```

Container：

被刪除：

裡面的資料：

通常也會消失。

例如：

```text
docker rm

↓

Container 消失
```

因此：

重要資料：

不能放在：

Container。

---

# Storage Types

Docker：

主要有：

三種：

Storage。

```text
Bind Mount

↓

Named Volume

↓

Anonymous Volume
```

---

# Lifecycle

```text
Docker Image

↓

Container

↓

Volume
```

Image：

不保存：

資料。

Container：

不建議：

保存：

資料。

Volume：

保存：

Persistent Data。

---

# Bind Mount

Bind Mount：

直接：

掛載：

Host：

目錄。

例如：

```yaml
volumes:

- ./api:/var/www/html
```

Container：

修改：

立即：

同步：

Host。

---

# Bind Mount Advantages

適合：

- Source Code
- Configuration
- Markdown
- Templates
- Logs（開發）

優點：

- IDE 可直接修改
- Git 可追蹤
- 即時同步
- AI 可讀取

---

# Bind Mount Disadvantages

缺點：

- Windows 效能較差
- 權限容易衝突
- Host 與 Container 耦合
- 容易受到 Host 影響

---

# Named Volume

Docker：

自行：

管理。

例如：

```yaml
volumes:

  mysql_data:

  redis_data:
```

使用：

```yaml
services:

  mysql:

    volumes:

      - mysql_data:/var/lib/mysql
```

---

# Named Volume Advantages

優點：

- 效能佳
- Docker 管理
- 不受 Host 目錄影響
- 適合 Database
- 適合 Cache Persistence

---

# Named Volume Disadvantages

缺點：

- Host 不容易直接查看
- 不適合 Source Code
- 不適合 Git 管理

---

# Anonymous Volume

例如：

```yaml
volumes:

- /var/lib/mysql
```

Docker：

自動：

建立。

名稱：

隨機。

通常：

不建議：

長期使用。

---

# Volume Selection

建議：

| 資料 | 建議 |
|------|------|
| Source Code | Bind Mount |
| Markdown | Bind Mount |
| `.env.example` | Bind Mount |
| Dockerfile | Bind Mount |
| MySQL | Named Volume |
| PostgreSQL | Named Volume |
| Redis Persistence | Named Volume |
| Elasticsearch | Named Volume |
| MinIO Data | Named Volume |

---

# Laravel

Laravel：

Source：

建議：

```yaml
./api:/var/www/html
```

不要：

把：

整個：

Laravel：

放：

Named Volume。

---

# React

React：

建議：

```yaml
./web:/app
```

Source：

使用：

Bind Mount。

---

# node_modules

有：

兩種：

策略。

## Strategy A

Host：

管理。

```text
web/node_modules
```

適合：

開發。

---

## Strategy B

Named Volume。

例如：

```yaml
node_modules:
```

適合：

大型：

CI。

---

# vendor

同樣：

兩種：

策略。

通常：

開發：

建議：

Bind Mount。

CI：

可：

Named Volume。

---

# Database

MySQL：

永遠：

建議：

Named Volume。

例如：

```yaml
mysql_data:/var/lib/mysql
```

不要：

```yaml
./mysql:/var/lib/mysql
```

尤其：

Windows。

---

# Redis

Redis：

若：

需要：

Persistence。

建議：

Named Volume。

若：

只是：

Cache。

可：

不用：

保存。

---

# Logs

正式：

建議：

集中：

Logging。

不要：

大量：

Bind Mount。

開發：

可：

Bind Mount。

---

# Configuration

例如：

```text
nginx.conf

php.ini

supervisord.conf
```

建議：

Bind Mount。

方便：

修改。

---

# Backup

Named Volume：

定期：

Backup。

不要：

依賴：

Container。

---

# Restore

Restore：

直接：

還原：

Volume。

不用：

重新：

Build：

Image。

---

# Performance

Linux：

Bind Mount：

通常：

速度：

很好。

Windows：

Bind Mount：

速度：

較慢。

Database：

建議：

Named Volume。

---

# Volume Permissions

Bind Mount：

容易：

受：

Host：

UID/GID：

影響。

Named Volume：

通常：

Docker：

管理。

權限：

較單純。

---

# Remove Volume

查看：

```bash
docker volume ls
```

刪除：

```bash
docker volume rm volume_name
```

全部：

未使用：

```bash
docker volume prune
```

注意：

會：

刪除：

資料。

---

# Inspect Volume

查看：

```bash
docker volume inspect volume_name
```

可看到：

- Mount Point
- Driver
- Labels

---

# Volume Sharing

多個：

Container：

可：

共用：

同一：

Volume。

例如：

```text
Laravel

↓

storage
```

注意：

避免：

同時：

大量：

寫入。

---

# Read Only Mount

若：

不需要：

寫入。

建議：

```yaml
:ro
```

例如：

```yaml
./config:/config:ro
```

提高：

安全性。

---

# Common Mistakes

避免：

- MySQL 使用 Bind Mount（Windows）
- Redis Cache 長期保存不必要資料
- 使用 Anonymous Volume
- 刪除 Volume 未備份
- Source Code 放 Named Volume
- Commit Volume 資料
- Container 保存重要資料

---

# Best Practices

建議：

1. Source Code 使用 Bind Mount。
2. Database 使用 Named Volume。
3. Configuration 使用 Bind Mount。
4. Secret 不放 Volume。
5. 定期備份 Database Volume。
6. Read Only 設定可唯讀的 Mount。
7. 不依賴 Container 保存資料。
8. 定期清理未使用的 Volume。

---

# Volume Lifecycle

```text
Create Volume

↓

Mount

↓

Use

↓

Backup

↓

Restore

↓

Remove
```

Volume：

生命週期：

通常：

比：

Container：

更長。

---

# Checklist

建立新的 Docker 專案前：

- [ ] Source Code 使用 Bind Mount
- [ ] Database 使用 Named Volume
- [ ] Redis Persistence 已確認
- [ ] Configuration 掛載方式正確
- [ ] Volume 權限正常
- [ ] 已規劃 Backup
- [ ] 已規劃 Restore
- [ ] 已確認是否需要 Read Only
- [ ] 無 Anonymous Volume
- [ ] Volume 命名一致

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