# AGENTS.md

## 1. Project Identity

本專案名稱：

```text
{{PROJECT_NAME}}
```

專案總覽請先閱讀：

```text
PROJECT.md
```

本文件定義所有 AI Agent、Coding Agent 與自動化工具在本專案中的工作規則。

---

## 2. Instruction Priority

當不同文件出現衝突時，依以下優先順序執行：

```text
1. 使用者當前明確指令
2. 本專案 AGENTS.md
3. 功能模組規格
4. 本專案 PROJECT.md
5. 本專案 instructions.md
6. Workspace 根目錄 AGENTS.md
7. Workspace 根目錄 instructions.md
8. Knowledge
9. 通用最佳實踐
```

不得以通用最佳實踐覆蓋專案既有明確規則。

---

## 3. Required Reading

任何修改前，至少閱讀：

```text
PROJECT.md
AGENTS.md
instructions.md
```

若涉及特定模組，必須再閱讀：

```text
docs/modules/<module-slug>/
```

若涉及架構、Laravel、React、Redis 或 Docker，依需求參考：

```text
{{KNOWLEDGE_BASE_PATH}}/Architecture
{{KNOWLEDGE_BASE_PATH}}/Laravel
{{KNOWLEDGE_BASE_PATH}}/React
{{KNOWLEDGE_BASE_PATH}}/Redis
{{KNOWLEDGE_BASE_PATH}}/Docker
{{KNOWLEDGE_BASE_PATH}}/Security
{{KNOWLEDGE_BASE_PATH}}/Testing
```

Knowledge 是參考基準，不得不經判斷直接套用。

---

## 4. Standard Workflow

所有開發任務應依序執行：

```text
理解需求
    ↓
定位相關程式與文件
    ↓
確認現有版本與架構
    ↓
分析影響範圍
    ↓
確認或建立規格
    ↓
提出實作計畫
    ↓
小範圍實作
    ↓
執行測試
    ↓
檢查風險
    ↓
更新文件
```

不得跳過分析直接大量修改程式。

---

## 5. Existing Project First

在既有專案中，必須優先遵循現況。

修改前先確認：

- PHP 與 Laravel 版本
- React、Node.js 與 Vite 版本
- 現有資料夾結構
- 現有命名方式
- 現有 Service、Repository、Action 或其他分層
- 現有 Authentication 與 Authorization
- 現有測試框架
- 現有資料表與欄位
- 現有 API Response 格式

不得因模板或 Knowledge 不同，就自行重構整個專案。

---

## 6. Specification-Driven Development

新功能開發前，應確認存在：

```text
docs/modules/<module-slug>/
```

建議文件：

```text
README.md
requirements.md
flow.md
data-model.md
api.md
permissions.md
tasks.md
test-plan.md
decisions.md
```

規格未完成時，可以進行：

- 現況分析
- 問題整理
- 技術調查
- 建立 Open Questions
- 建立實作計畫

規格未完成時，不應進行：

- 大量建立正式業務程式
- 修改核心資料結構
- 大範圍重構
- 建立不可逆 Migration
- 假設尚未確認的業務規則

---

## 7. Backend Rules

Backend 技術基準：

```text
PHP {{PHP_VERSION}}
Laravel {{LARAVEL_VERSION}}
```

### 7.1 Recommended Flow

```text
Route
    ↓
Controller
    ↓
Form Request
    ↓
Service
    ↓
Repository / Eloquent
    ↓
Model
```

此流程為原則，不得為非常簡單的 CRUD 強制建立無意義抽象。

### 7.2 Controller

Controller 應負責：

- 接收 Request
- 呼叫驗證後的資料
- 呼叫 Application Service
- 回傳 Response

Controller 不應負責：

- 複雜業務規則
- 大量 SQL
- 多步驟 Transaction
- 外部 API 完整流程
- 複雜資料轉換

### 7.3 Validation

輸入驗證優先使用：

```text
Form Request
```

必須處理：

- 必填欄位
- 型別
- 格式
- 長度
- 唯一性
- 資料存在性
- 業務限制

不得只依賴前端驗證。

### 7.4 Service

Service 用於：

- 複雜業務流程
- 多個 Model 協作
- Transaction
- 外部服務整合
- Queue Dispatch
- 權限以外的業務規則

不得建立只包一行 Model 呼叫、且沒有任何語意價值的 Service。

### 7.5 Repository

Repository 適用於：

- 複雜查詢
- 多處共用查詢
- 查詢規則封裝
- 需要替換資料來源
- 專案既有架構已採 Repository

簡單 Eloquent 查詢不一定需要 Repository。

### 7.6 Database

資料庫異動必須使用 Migration。

不得直接修改正式資料表結構而不留下 Migration。

建立 Migration 前必須確認：

- 既有資料量
- Nullable
- Default
- Index
- Foreign Key
- Unique Constraint
- Rollback
- 舊資料相容性

### 7.7 Query

必須注意：

- N+1 Query
- 大量資料載入
- 缺少 Index
- 不必要的 `select *`
- 不可分頁的列表
- 在迴圈內執行 Query
- Transaction 範圍過大

### 7.8 API Response

API Response 應遵循專案既有格式。

若專案尚無規範，建議：

```json
{
  "data": {},
  "message": "Success",
  "meta": {}
}
```

錯誤格式應一致，不得每支 API 使用不同結構。

---

## 8. Frontend Rules

Frontend 技術基準：

```text
React {{REACT_VERSION}}
TypeScript
Vite
```

### 8.1 Folder Structure

優先使用 Feature-Based Structure：

```text
web/src/
├── api/
├── app/
├── components/
├── features/
├── hooks/
├── lib/
├── pages/
├── styles/
└── types/
```

功能模組建議：

```text
features/<module-slug>/
├── api/
├── components/
├── hooks/
├── pages/
├── schemas/
└── types/
```

### 8.2 TypeScript

- 避免使用 `any`
- API Response 必須定義型別
- 共用型別不得重複散落
- Nullable 與 Optional 必須明確
- 不得以 Type Assertion 隱藏實際型別問題

### 8.3 Components

Component 應保持單一責任。

大型頁面應拆分：

- Layout
- Filter
- Table
- Form
- Modal
- Detail
- Loading State
- Empty State
- Error State

### 8.4 API

Component 不應直接散落大量 `fetch()`。

建議流程：

```text
Component
    ↓
Hook
    ↓
Feature API
    ↓
Shared API Client
```

禁止硬編碼：

```text
http://localhost:8080
```

應使用：

```text
VITE_API_BASE_URL
```

### 8.5 UI States

畫面必須視需求處理：

- Initial
- Loading
- Empty
- Success
- Validation Error
- API Error
- Forbidden
- Not Found

不得只完成成功狀態。

---

## 9. Redis, Queue and Background Jobs

涉及下列功能時：

- Queue
- Webhook
- Debounce
- AI 回覆
- 批次工作
- 第三方訊息
- 長時間任務
- 排程

必須優先參考：

```text
{{KNOWLEDGE_BASE_PATH}}/Redis/debounce-queue.md
{{KNOWLEDGE_BASE_PATH}}/Redis/processing-lock.md
{{KNOWLEDGE_BASE_PATH}}/Redis/queue.md
{{KNOWLEDGE_BASE_PATH}}/Redis/persistence.md
{{KNOWLEDGE_BASE_PATH}}/Redis/security.md
```

必須考慮：

- Idempotency
- Duplicate Delivery
- Processing Lock
- Lock Expiration
- Retry
- Backoff
- Timeout
- Failed Jobs
- Dead Letter
- Observability
- Manual Recovery

---

## 10. Docker Rules

若本專案使用 Docker，Laravel 指令優先透過：

```bash
docker compose exec api php artisan
docker compose exec api composer
```

React 指令優先透過：

```bash
docker compose exec web npm
```

第一次安裝 React 套件可使用：

```bash
docker compose --profile application run --rm \
  --user "$(id -u):$(id -g)" \
  -e HOME=/tmp \
  web \
  npm install
```

不得假設 Host 已安裝：

- PHP
- Composer
- Node.js
- npm
- MySQL
- Redis

---

## 11. Testing Rules

每次實作後必須執行適當測試。

### 11.1 Backend

至少依需求涵蓋：

- Happy Path
- Validation Failure
- Unauthenticated
- Forbidden
- Not Found
- Conflict
- Transaction Rollback
- Queue Failure
- External Service Failure

### 11.2 Frontend

至少依需求涵蓋：

- Component Render
- Loading State
- Empty State
- Error State
- Validation Error
- API Success
- API Failure
- Permission Restriction

### 11.3 Regression

修正 Bug 時，應優先建立可重現該 Bug 的測試。

---

## 12. Security Rules

不得：

- 提交 `.env`
- 提交 Token、Password、API Key
- 在 Log 中輸出敏感資料
- 只靠前端隱藏按鈕控制權限
- 信任第三方 Webhook Payload
- 直接拼接 SQL
- 未驗證就使用使用者輸入的檔案路徑
- 將正式憑證放入 Dockerfile

涉及權限的功能必須同時檢查：

```text
Authentication
Authorization
Ownership
Scope
```

---

## 13. Documentation Rules

完成實作後，依影響範圍更新：

```text
PROJECT.md
README.md
docs/modules/<module-slug>/
docs/decisions/
.env.example
api/.env.example
web/.env.example
```

文件必須區分：

- Current Behavior
- New Behavior
- Assumption
- Open Question
- Decision

不得將推測寫成既有事實。

---

## 14. Multi-Agent Collaboration

多 Agent 可分工，但每個 Agent 必須有明確邊界。

建議角色：

```text
Analysis Agent
Specification Agent
Backend Agent
Frontend Agent
Database Agent
Testing Agent
Review Agent
Documentation Agent
```

所有 Agent 共用：

```text
PROJECT.md
AGENTS.md
docs/modules/<module-slug>/
```

任務拆分應寫在：

```text
docs/modules/<module-slug>/tasks.md
```

Agent 不得修改不屬於任務範圍的模組，除非發現必要相依性並明確說明。

---

## 15. Prohibited Actions

未經使用者明確要求，不得：

- 執行 `docker compose down -v`
- 刪除資料庫
- 刪除 Docker Volume
- 執行破壞性 Migration
- 大量刪除檔案
- 強制覆蓋既有程式
- 執行 `composer update`
- 執行大版本升級
- 修改正式環境
- 重寫整個模組
- 變更 Authentication 架構
- 提交 Secrets
- 建立與需求無關的抽象層

---

## 16. Completion Checklist

任務完成前確認：

- [ ] 已閱讀相關規格
- [ ] 已確認版本與現有架構
- [ ] 修改範圍符合任務
- [ ] 驗證已完成
- [ ] 權限檢查已完成
- [ ] 錯誤處理已完成
- [ ] 測試已完成
- [ ] 無明顯 N+1
- [ ] 無敏感資料外洩
- [ ] 文件已更新
- [ ] 未執行破壞性操作
- [ ] 已列出尚未完成事項