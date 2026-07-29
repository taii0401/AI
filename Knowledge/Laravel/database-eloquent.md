# Laravel Database And Eloquent

## Purpose

這份文件定義 Laravel database、migration、Model 與 Eloquent 的基本規範。

目標是讓資料結構清楚、約束明確，並避免資料層在專案成長後變得難以維護。

---

## Migration Principles

- 欄位名稱要表達業務語意
- foreign key 要明確
- 常用查詢欄位要加 index
- nullable 欄位要有理由
- 狀態時間使用明確欄位，例如 `published_at`

範例：

```php
$table->foreignId('user_id')->constrained()->cascadeOnDelete();
$table->string('status')->index();
$table->timestamp('published_at')->nullable()->index();
```

---

## Model Responsibility

Model 適合放：

- relationship
- cast
- local scope
- accessor / mutator
- 簡單 domain behavior

Model 不適合放：

- 外部 API
- 付款流程
- 寄信
- 複雜跨 model transaction

---

## Relationships

Relationship 命名要反映 domain。

推薦：

```php
public function author(): BelongsTo
{
    return $this->belongsTo(User::class, 'user_id');
}

public function comments(): HasMany
{
    return $this->hasMany(Comment::class);
}
```

查詢列表時要主動考慮 eager loading：

```php
Post::query()->with(['author', 'comments'])->paginate();
```

---

## Casts

Model 欄位型別應使用 casts。

推薦：

```php
protected function casts(): array
{
    return [
        'is_active' => 'boolean',
        'published_at' => 'datetime',
        'settings' => 'array',
    ];
}
```

---

## Scopes

重複查詢條件使用 local scope。

```php
public function scopePublished(Builder $query): Builder
{
    return $query->where('status', 'published');
}
```

Scope 適合單一 Model 的可組合條件，不適合處理複雜報表。

---

## Factories And Seeders

Factory 用於測試資料與開發資料。

建議：

- 測試依賴 Factory，不依賴固定 seed data
- Seeder 用於本機開發或必要初始化資料
- production seeder 要小心 idempotent

---

## Data Integrity

資料一致性優先靠 database constraint 與 transaction。

建議：

- foreign key
- unique index
- transaction
- reasonable nullable policy
- 明確狀態欄位

不要只依賴 application validation 維護資料正確性。

---

## Checklist

檢查資料層時確認：

- foreign key 是否存在？
- index 是否符合常用查詢？
- nullable 是否合理？
- Model 是否過胖？
- relationship 是否清楚？
- 是否有 N+1 query 風險？
- Factory 是否足夠支援測試？
