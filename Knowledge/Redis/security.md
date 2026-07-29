# Redis Security

## Purpose

這份文件定義 Redis 的基本安全規範。

Redis 常位於內部網路，但不能因此忽略存取控制與敏感資料風險。

---

## Core Principles

- Redis 不應公開在 Internet
- 啟用 authentication
- 限制 network access
- 不在 key 或 value 中存敏感明文
- production 禁用危險操作或限制權限
- log 與 monitoring 不洩漏敏感資訊

---

## Network

Production Redis 應：

- 僅允許 application network 存取
- 使用 firewall 或 security group
- 不對外開放 port
- 需要時使用 TLS

---

## Authentication

使用：

- password
- ACL user
- managed Redis access control

不要使用無密碼 Redis。

---

## Sensitive Data

避免存：

- password
- raw token
- credit card data
- personal sensitive data

若必須存 token 類資料：

- TTL 要短
- value 不可被猜測
- 必要時存 hash 後的 token

---

## Dangerous Commands

Production 應限制：

```text
FLUSHALL
FLUSHDB
CONFIG
KEYS
SHUTDOWN
```

尤其是多人共用環境或 managed Redis。

---

## Checklist

安全檢查：

- Redis 是否未公開到 Internet？
- 是否啟用 authentication？
- 是否限制來源 IP 或 network？
- 是否避免敏感明文？
- 是否有 ACL？
- 是否限制危險命令？
- backup 是否加密或受保護？
