# Redis Patterns

## Purpose

這份文件整理 Redis 常見實務模式。

重點是判斷何時適合使用，而不是把 Redis 加到所有功能。

---

## Distributed Lock

適合：

- 避免同一資源被重複處理
- 防止重複 checkout
- 控制排程任務同時執行

原則：

- lock 必須有 TTL
- lock value 要唯一
- release 時確認 value
- lock timeout 要小於可接受等待時間

---

## Rate Limiting

適合：

- login
- register
- password reset
- public API
- expensive endpoint

常見 key：

```text
rate_limit:login:ip:{ip}
rate_limit:api:user:{user_id}
```

---

## Counter

適合：

- view count
- like count
- realtime metric
- temporary count

注意：

- 是否需要同步回 database
- 是否能接受延遲一致
- counter key 是否會過期

---

## Leaderboard

使用 sorted set。

適合：

- score ranking
- activity ranking
- top products

注意：

- score 更新規則要明確
- ranking 資料要定期修剪

---

## Session Storage

Redis 適合存 session。

注意：

- TTL 要正確
- Redis 掛掉會影響登入狀態
- production 要有 persistence 或可接受重新登入

---

## Temporary Token

適合：

- OTP
- email verification
- password reset temporary state

原則：

- TTL 必須短
- token 不應明文可推測
- 成功使用後刪除
