# Laravel Query Performance

## Purpose

本文件記錄 Laravel 與 MySQL 查詢效能相關的最佳實務、常見問題與優化策略。

目標：

* 降低資料庫負載
* 提升 API 回應速度
* 提升系統可擴充性
* 建立查詢效能思維

本文件不包含專案特定 Query。

---

# Performance Philosophy

優化前先確認：

```text
是否真的有問題？
```

避免：

* 過早優化
* 憑感覺優化
* 沒有數據的優化

---

# Optimization Priority

查詢效能優化優先順序：

```text
1. Query Correctness
2. Index
3. Query Structure
4. Data Volume
5. Cache
6. Infrastructure
```

不要一開始就加 Redis。

---

# Measure Before Optimize

優化前先確認：

* 執行時間
* Query 次數
* 資料量
* CPU 使用率
* Memory 使用率

常用工具：

```text
Laravel Debugbar
Laravel Telescope
EXPLAIN
Slow Query Log
```

---

# N+1 Query

## Problem

常見：

```php
$users = User::all();

foreach ($users as $user) {
    echo $user->profile->name;
}
```

產生：

```text
1 + N
```

次 Query。

---

## Solution

使用：

```php
User::with('profile')->get();
```

---

## Principle

查詢關聯資料時：

優先思考：

```text
with()
load()
loadMissing()
```

---

# Index

## Purpose

Index 是最有效的 Query Optimization。

通常：

```text
加 Index
>>
加 Redis
```

---

## Suitable Columns

常用於：

```text
WHERE
JOIN
ORDER BY
GROUP BY
```

欄位。

---

## Common Example

```sql
member_id
created_at
status
store_id
```

---

## Verification

使用：

```sql
EXPLAIN
```

確認是否使用 Index。

---

# Select Only What You Need

避免：

```php
User::all();
```

如果只需要：

```php
User::select('id')->get();
```

---

## Principle

不要查：

```text
100 個欄位
```

只為了使用：

```text
1 個欄位
```

---

# Pagination

避免：

```php
Model::all();
```

大量資料。

---

優先：

```php
paginate()
simplePaginate()
cursorPaginate()
```

---

# Count Optimization

注意：

```php
Model::count()
```

在大表上可能很慢。

---

常見優化：

* 快取
* 統計表
* 非同步計算

---

# Exists vs Count

判斷資料是否存在：

避免：

```php
$count > 0
```

優先：

```php
exists()
```

原因：

```text
找到第一筆即可停止
```

---

# Eloquent vs Query Builder

Eloquent：

優點：

* 可讀性高
* 開發快速

缺點：

* 額外 Model 成本

---

大量資料：

可考慮：

```php
DB::table(...)
```

---

# Chunk Processing

大量資料：

避免：

```php
Model::all()
```

---

優先：

```php
chunk()
chunkById()
lazy()
cursor()
```

---

# Batch Update

避免：

```php
foreach (...)
{
    save();
}
```

---

優先：

```php
update()
upsert()
bulk insert
```

---

# Join Optimization

原則：

* 必要才 Join
* Join 欄位要有 Index
* 避免不必要 LEFT JOIN

---

檢查：

```sql
EXPLAIN
```

是否出現：

```text
Using temporary
Using filesort
```

---

# Group By

注意：

```sql
GROUP BY
```

通常是報表效能殺手。

---

檢查：

* 是否有 Index
* 是否能預先彙總
* 是否能建立統計表

---

# Report Query

報表查詢：

優先：

```text
背景計算
↓
統計表
↓
前端讀取
```

避免：

```text
每次打開頁面即時計算
```

---

# Cache Strategy

適合 Cache：

```text
排行榜
統計數據
首頁資料
固定設定
```

---

不適合 Cache：

```text
即時交易資料
付款狀態
庫存數量
```

---

# Redis

Redis 是：

```text
Optimization
```

不是：

```text
Problem Solver
```

先優化 Query。

再考慮 Redis。

---

# Query Review Checklist

新增 Query 前：

請確認：

* 是否有 N+1 Query？
* 是否有 Index？
* 是否查太多欄位？
* 是否需要 Pagination？
* 是否可以 Cache？
* 是否有 EXPLAIN？
* 是否會造成 Table Scan？

---

# Common Mistakes

常見錯誤：

1. 沒有 Index
2. SELECT *
3. N+1 Query
4. 過度使用 Eloquent
5. 大量迴圈查詢
6. 沒有 Pagination
7. 報表即時計算
8. 還沒分析就加 Redis
9. 沒有使用 EXPLAIN
10. Query 可以優化卻先升級機器

---

# Long-Term Goal

建立查詢效能思維：

```text
寫得出 Query
↓
看得懂 EXPLAIN
↓
會優化 Query
↓
能設計高效能資料流
```

最終目標：

設計可維護且可擴充的系統，而不只是讓 Query 能執行成功。