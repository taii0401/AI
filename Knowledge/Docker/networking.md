# Docker Networking

## Purpose

本文件定義 Docker Networking 的設計原則、網路模型、最佳實踐與常見問題。

Docker Network 的主要目的是：

- 建立 Container 間的通訊
- 提供 Service Discovery（DNS）
- 隔離不同專案
- 控制網路存取
- 降低環境差異

本文件不綁定任何特定專案。

---

# Goals

Docker Network 應具備：

- Container 可互相通訊
- Host 可存取必要服務
- Browser 可存取 Web Service
- Service 不需知道 Container IP
- Network 可獨立管理
- 不同專案互不影響

---

# Networking Overview

```text
                    Browser
                       │
               http://localhost:5173
                       │
             ┌─────────┴─────────┐
             │                   │
             ▼                   ▼
         Host OS            Docker Engine
                                  │
                           Docker Network
                                  │
      ┌──────────┬──────────┬──────────┬──────────┐
      ▼          ▼          ▼          ▼
    web         api       mysql      redis
      │
      ▼
 api-nginx
```

---

# Three Networking Layers

Docker 開發環境可分成三個層級：

```text
Browser

↓

Host

↓

Docker Network

↓

Container
```

理解這三層是排查問題的基礎。

---

# Browser

Browser 永遠只能透過：

```text
localhost

或

IP:Port
```

例如：

```text
http://localhost:5173

http://localhost:8080
```

Browser **不能直接連 Container Name**。

錯誤示例：

```text
http://api
```

---

# Host

Host 是：

- Windows
- macOS
- Linux

Host 可以透過：

```text
localhost:PORT
```

存取 Docker 暴露的 Port。

例如：

```text
localhost:3307
```

代表：

```text
Host

↓

Docker Port Mapping

↓

mysql:3306
```

---

# Docker Network

Docker Compose 建立的 Network：

```yaml
networks:

  app_network:
```

所有 Container：

加入同一個 Network。

例如：

```text
api

mysql

redis

web

mailpit
```

彼此可直接通訊。

---

# Container

Container 之間：

不需要：

```text
IP Address
```

而是使用：

```text
Service Name
```

例如：

```dotenv
DB_HOST=mysql

REDIS_HOST=redis

MAIL_HOST=mailpit
```

---

# Service Discovery

Docker 內建 DNS。

例如：

```text
mysql
```

Docker 會自動解析：

```text
172.x.x.x
```

因此：

不要：

```dotenv
DB_HOST=172.20.0.5
```

因為：

IP 可能改變。

---

# localhost

這是 Docker 最容易誤解的地方。

假設：

Laravel Container：

```text
api
```

裡面：

```dotenv
DB_HOST=localhost
```

代表：

Laravel 會連：

```text
api Container 自己
```

不是：

```text
mysql Container
```

因此：

```text
Connection Refused
```

---

# Correct Connection

Container：

互相連線：

應使用：

```dotenv
DB_HOST=mysql

REDIS_HOST=redis

MAIL_HOST=mailpit
```

不要：

```text
localhost

127.0.0.1
```

---

# Port Mapping

Compose：

例如：

```yaml
ports:

- "3307:3306"
```

意思：

```text
Host

3307

↓

Container

3306
```

Container：

仍然：

使用：

```text
3306
```

不是：

3307。

---

# Container to Container

例如：

Laravel：

```text
api
```

連：

MySQL：

```text
mysql:3306
```

Redis：

```text
redis:6379
```

Mail：

```text
mailpit:1025
```

完全：

不用：

Port Mapping。

---

# Host to Container

Host：

例如：

HeidiSQL：

連：

```text
localhost

3307
```

即可。

不是：

```text
mysql
```

因為：

Host：

不認識：

Docker DNS。

---

# Browser to Container

Browser：

只能：

```text
localhost

↓

Port Mapping

↓

Container
```

例如：

```text
http://localhost:5173
```

---

# Container to Host

有時：

Container：

需要：

連：

Host。

例如：

Redis：

在 Host。

可使用：

```text
host.docker.internal
```

例如：

```text
DB_HOST=host.docker.internal
```

注意：

不同平台支援略有差異。

---

# DNS

Docker Compose：

會建立：

DNS。

例如：

```text
api

↓

172.xx.xx.xx
```

完全：

自動。

因此：

不要：

寫死：

IP。

---

# Multiple Projects

假設：

```text
project-a

project-b
```

各自：

Compose。

Docker：

會建立：

```text
project-a_default

project-b_default
```

Network：

互相隔離。

因此：

```text
mysql
```

在：

Project A：

看不到：

Project B。

---

# Shared Network

若：

需要：

不同 Compose：

互通。

可：

建立：

External Network。

例如：

```yaml
networks:

  shared:

    external: true
```

所有：

Compose：

加入：

```text
shared
```

即可。

---

# Bridge Network

Compose：

預設：

使用：

```text
Bridge
```

適用：

- Local Development
- Testing
- Single Machine

大部分專案：

都足夠。

---

# Host Network

Linux：

可：

```yaml
network_mode: host
```

Container：

直接：

使用：

Host Network。

一般：

開發：

不建議。

---

# None Network

若：

Container：

完全：

不需要：

Network。

可：

```yaml
network_mode: none
```

通常：

安全需求：

才會使用。

---

# Overlay Network

Docker Swarm：

使用：

Overlay。

提供：

跨主機：

Network。

一般：

Compose：

不用。

---

# Expose vs Ports

## expose

```yaml
expose:

- "9000"
```

只有：

Container：

可連。

Host：

不能。

---

## ports

```yaml
ports:

- "8080:80"
```

Host：

可以。

Browser：

可以。

---

# Network Aliases

可：

建立：

Alias。

例如：

```yaml
aliases:

- database
```

Laravel：

可：

使用：

```text
database
```

連線。

通常：

直接：

使用：

Service Name：

即可。

---

# Debug

查看：

Network：

```bash
docker network ls
```

查看：

詳細：

```bash
docker network inspect app_network
```

查看：

Container：

IP：

```bash
docker inspect container_name
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

測試：

Redis：

```bash
docker compose exec api nc -z redis 6379
```

---

# Common Mistakes

避免：

- DB_HOST=localhost
- REDIS_HOST=127.0.0.1
- 寫死 Container IP
- Host 使用 mysql 當 Host Name
- Browser 使用 api 當網址
- 不理解 Port Mapping
- Container 使用 Host Port
- 不使用 Docker DNS

---

# Checklist

建立新的 Docker Network 前：

- [ ] 使用 Service Name
- [ ] 不寫死 IP
- [ ] Host 與 Container Port 分清楚
- [ ] Browser 使用 localhost
- [ ] Container 使用 Service Name
- [ ] Network 已命名
- [ ] Network 已隔離
- [ ] Port Mapping 正確
- [ ] DNS 可解析
- [ ] 已完成連線測試

---

# Best Practices

建議遵循：

1. 一個 Compose Project 一個 Network。
2. Container 間一律使用 Service Name。
3. Browser 永遠透過 `localhost:PORT` 存取。
4. 不在程式碼或設定檔中寫死 Container IP。
5. 僅對需要對外提供服務的 Container 開放 `ports`，內部服務可優先使用 `expose`。
6. 將不同環境（開發、測試、正式）使用不同 Network，避免互相干擾。

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
permissions.md
    ↓
volumes.md
    ↓
image-versioning.md
    ↓
troubleshooting.md
```