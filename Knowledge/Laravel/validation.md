# Laravel Validation

## Purpose

這份文件定義 Laravel validation 的使用規範。

目標是讓輸入資料在進入 business logic 前就被清楚驗證，降低安全風險與資料錯誤。

---

## Core Principles

- 輸入資料一律不可信
- Controller 不堆大量 validation logic
- API 與表單優先使用 Form Request
- validation 負責資料格式與基本規則
- business invariant 放在 Service 或 domain logic
- validated data 才能進入 Model create / update

---

## Form Request

推薦使用 Form Request：

```text
StorePostRequest
UpdatePostRequest
SearchPostRequest
```

Form Request 負責：

- `rules()`
- `authorize()`
- input normalization
- custom messages
- custom attributes

Controller 使用：

```php
public function store(StorePostRequest $request)
{
    $post = Post::create($request->validated());

    return new PostResource($post);
}
```

---

## Rule Style

使用 array syntax，方便加入 Rule object。

推薦：

```php
public function rules(): array
{
    return [
        'title' => ['required', 'string', 'max:120'],
        'status' => ['required', Rule::in(['draft', 'published'])],
        'published_at' => ['nullable', 'date'],
    ];
}
```

避免：

```php
'title' => 'required|string|max:120'
```

字串語法可用，但大型專案中可讀性與可維護性較差。

---

## Authorization

Form Request 的 `authorize()` 可處理 request-specific 權限。

範例：

```php
public function authorize(): bool
{
    return $this->user()->can('create', Post::class);
}
```

但複雜權限仍應放在 Policy。

---

## Input Normalization

可在 `prepareForValidation()` 處理格式整理。

範例：

```php
protected function prepareForValidation(): void
{
    $this->merge([
        'email' => strtolower((string) $this->email),
    ]);
}
```

適合：

- trim string
- lowercase email
- normalize boolean
- normalize date format

不適合：

- 執行 business workflow
- 寫入 database
- 呼叫外部 API

---

## Validated Data

只使用 validated data 建立或更新資料。

推薦：

```php
User::create($request->validated());
```

避免：

```php
User::create($request->all());
```

原因：

- 避免 mass assignment 風險
- 避免未預期欄位進入系統
- 讓資料來源清楚可控

---

## Validation Vs Business Rule

Validation 適合：

- required
- type
- max length
- format
- enum values
- exists / unique

Business rule 適合：

- 訂單是否可取消
- 文章是否可發布
- 使用者是否能升級方案
- 庫存是否足夠
- 狀態轉換是否合法

不要把所有 business logic 塞進 validation。

---

## Common Rules

常用規範：

- string 欄位要有 `max`
- array 欄位要驗證子欄位
- id 欄位使用 `exists`
- enum-like 欄位使用 `Rule::in()` 或 Enum rule
- file upload 要限制 type 與 size
- password 要使用 Laravel password rule

---

## Checklist

檢查 validation 時確認：

- 是否使用 Form Request？
- 是否只使用 validated data？
- string 是否有 max length？
- array 子欄位是否有驗證？
- enum-like 欄位是否有限制？
- id 是否有 exists 檢查？
- 權限是否有放在 authorize 或 Policy？
- business rule 是否放在正確位置？
