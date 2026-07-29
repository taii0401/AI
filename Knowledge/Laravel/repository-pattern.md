# Laravel Repository Pattern

## Purpose

本文件記錄 Repository Pattern 的通用設計原則、使用時機與常見注意事項。

Repository Pattern 的目的不是讓架構變複雜，而是將資料存取邏輯集中管理，讓商業流程與資料查詢細節分離。

本文件不包含特定專案規範。

---

# What Is Repository Pattern

Repository Pattern 是一種將資料存取邏輯封裝起來的設計方式。

它通常位於：

```text
app/
└── Repositories/
```

Repository 負責：

* 查詢資料
* 新增資料
* 更新資料
* 刪除資料
* 封裝複雜 Query
* 隱藏資料來源細節

---

# Basic Flow

建議流程：

```text
Controller
↓
Service
↓
Repository
↓
Model / Query Builder
↓
Database
```

Controller 負責 HTTP Request / Response。

Service 負責商業流程。

Repository 負責資料存取。

Model 負責資料表對應與 Eloquent 關聯。

---

# When To Use Repository

適合使用 Repository 的情境：

* Query 重複出現在多個地方
* Query 條件複雜
* 需要封裝 Join、Filter、Sorting
* 同一個資料來源被多個 Service 使用
* 未來可能更換資料來源
* 需要讓 Service 保持乾淨
* 需要更容易測試資料存取邏輯

---

# When Not To Use Repository

不一定需要 Repository 的情境：

* 單純 CRUD
* 只有一兩行 Eloquent Query
* Query 不會重複使用
* 建立 Repository 反而讓程式更難懂
* 專案規模很小且沒有明顯資料存取複雜度

原則：

不要為了套架構而建立 Repository。

---

# Responsibility

Repository 應該負責「資料怎麼取得」與「資料怎麼保存」。

Repository 適合負責：

* find
* create
* update
* delete
* search
* filter
* pagination
* complex query
* query condition composition

Repository 不應該負責：

* HTTP Request
* HTTP Response
* 商業流程控制
* 第三方 API 流程
* Queue 派送決策
* 權限判斷流程
* 複雜商業規則

---

# Naming

Repository 命名應對應資料或聚合概念。

建議：

```text
UserRepository
TaskRepository
OrderRepository
AppointmentRepository
LineMessageRepository
```

避免：

```text
DataRepository
CommonRepository
BaseRepositoryForEverything
QueryRepository
```

如果 Repository 名稱太模糊，通常代表職責不清楚。

---

# Method Naming

Method 命名應表達資料查詢目的。

建議：

```php
findById()
findActiveByStoreId()
getPendingTasks()
paginateByFilters()
createFromData()
updateStatus()
```

避免：

```php
getData()
query()
handle()
process()
doSomething()
```

若 Method 名稱無法清楚描述用途，通常表示查詢目的不明確。

---

# Return Type

Repository 回傳值應保持一致。

常見回傳：

```php
?Model
Collection
LengthAwarePaginator
bool
int
array
```

建議明確標示 Return Type。

例如：

```php
public function findById(int $id): ?User
{
    return User::find($id);
}
```

---

# Query Scope

簡單且高度綁定 Model 的查詢，可以考慮放在 Eloquent Scope。

例如：

```php
User::active()->verified()->get();
```

Repository 適合封裝：

* 跨多個條件的查詢
* 多個表關聯查詢
* 給 Service 使用的查詢方法
* 查詢目的具有明確業務意義的查詢

---

# Repository vs Service

Repository 負責資料存取。

Service 負責商業流程。

簡單區分：

```text
Repository：資料要怎麼查、怎麼存
Service：這個功能要怎麼完成
```

錯誤做法：

* Repository 裡面處理完整商業流程
* Service 裡面塞滿複雜 Query
* Repository 直接處理 HTTP Request
* Repository 決定 API Response 格式

---

# Repository vs Model

Model 負責：

* 資料表對應
* Relationship
* Attribute Casting
* Local Scope
* Accessor / Mutator

Repository 負責：

* 封裝查詢用途
* 組合複雜查詢
* 提供 Service 需要的資料存取方法

不要讓 Model 變成所有查詢邏輯的集中地。

---

# Interface

是否建立 Interface 依專案需求決定。

適合建立 Interface 的情境：

* 需要替換資料來源
* 需要 Mock Repository
* 團隊規範要求
* 模組邊界清楚
* 專案較大且需要明確契約

不一定需要 Interface 的情境：

* 小型專案
* Repository 很簡單
* 沒有測試替換需求
* 只會有一種實作

原則：

不要為了形式而建立 Interface。

---

# Base Repository

Base Repository 可以減少重複 CRUD，但不要過度抽象。

適合放：

* find
* all
* create
* update
* delete

不適合放：

* 過多業務查詢
* 太多魔法方法
* 難以追蹤的動態 Query

如果 Base Repository 變得太複雜，通常代表抽象過度。

---

# Pagination

分頁查詢適合封裝在 Repository。

例如：

```php
public function paginateByFilters(array $filters): LengthAwarePaginator
{
    return User::query()
        ->when($filters['keyword'] ?? null, function ($query, $keyword) {
            $query->where('name', 'like', "%{$keyword}%");
        })
        ->paginate($filters['per_page'] ?? 15);
}
```

原則：

* Filter 條件集中管理
* Service 不應處理查詢細節
* Controller 不應組 Query

---

# Transaction

Repository 通常不負責控制 Transaction。

Transaction 應多數情況放在 Service。

原因：

* Transaction 通常橫跨多個 Repository
* Service 較清楚完整業務流程
* Repository 只知道單一資料操作

例外：

若 Repository 的單一方法本身就是不可分割資料操作，可在 Repository 內部處理。

---

# Cache

Repository 是否處理 Cache 需謹慎。

可接受：

* 查詢結果 Cache
* 固定資料 Cache
* 低變動資料 Cache

需注意：

* Cache 失效時機
* 資料一致性
* 不要讓 Cache 影響業務正確性

若 Cache 邏輯複雜，可拆成專門的 Cache Service。

---

# Avoid Fat Repository

避免 Repository 變成另一種 God Class。

警訊：

* Repository 超過數百行
* Method 數量過多
* 負責多個不相關資料表
* 查詢目的不清楚
* 包含商業流程
* 包含權限與狀態流轉判斷

解法：

* 依資料概念拆分 Repository
* 將商業流程移到 Service
* 將複雜查詢拆成 Query Object
* 將統計報表查詢獨立處理

---

# Query Object

若 Repository 中出現非常複雜的查詢，可以考慮 Query Object。

適合情境：

* 報表查詢
* 多條件搜尋
* 多表 Join
* 統計查詢
* 查詢邏輯需要重複使用

範例概念：

```text
Queries/
├── AppointmentReportQuery
├── UserSearchQuery
└── PaymentSummaryQuery
```

Query Object 可以讓 Repository 維持乾淨。

---

# Testing

Repository 測試可依需求選擇：

* Unit Test
* Feature Test
* Integration Test

若 Repository 包含實際 DB Query，通常使用 Integration Test 較實際。

測試重點：

* 查詢條件是否正確
* 回傳資料是否符合預期
* Filter 是否正常
* Pagination 是否正常
* Edge Case 是否處理

---

# Common Mistakes

常見錯誤：

1. 小專案過早導入 Repository
2. Repository 裡面放商業邏輯
3. Repository 直接處理 Request
4. Repository 直接回傳 API Response
5. Method 命名太模糊
6. Base Repository 過度抽象
7. Repository 裡面塞太多不相關查詢
8. Service 仍然直接寫大量 Query
9. Interface 只為了形式存在
10. Cache 失效策略沒有設計清楚

---

# Review Checklist

建立或修改 Repository 時，請檢查：

* 這個 Repository 是否有明確資料職責？
* Method 名稱是否表達查詢目的？
* 是否避免商業流程？
* 是否避免 HTTP Request / Response？
* 是否讓 Service 更清楚？
* 是否過度抽象？
* 是否需要 Interface？
* 是否有 Cache 失效問題？
* 是否存在 N+1 Query？
* 是否需要 Index 支援？

---

# Long-Term Goal

Repository Pattern 的目標是讓資料存取邏輯更清楚，而不是增加架構負擔。

好的 Repository 應該讓人一看就知道：

* 資料從哪裡來
* 查詢條件是什麼
* 回傳資料型態是什麼
* Service 為什麼需要這些資料