# {{PROJECT_NAME}}

## 1. Project Overview

### 1.1 專案名稱

```text
{{PROJECT_NAME}}
```

### 1.2 專案說明

{{PROJECT_DESCRIPTION}}

### 1.3 專案目標

- {{PROJECT_GOAL_1}}
- {{PROJECT_GOAL_2}}
- {{PROJECT_GOAL_3}}

### 1.4 專案現況

```text
{{PROJECT_STATUS}}
```

可使用的狀態：

- Planning
- Initializing
- In Development
- Testing
- Maintenance
- Archived

---

## 2. Product Scope

### 2.1 主要使用者

本系統主要使用者可能包含：

- {{USER_ROLE_1}}
- {{USER_ROLE_2}}
- {{USER_ROLE_3}}
- {{USER_ROLE_4}}

### 2.2 系統預計涵蓋

- {{MODULE_1}}
- {{MODULE_2}}
- {{MODULE_3}}
- {{MODULE_4}}

### 2.3 暫不涵蓋

目前階段不包含：

- {{OUT_OF_SCOPE_1}}
- {{OUT_OF_SCOPE_2}}
- {{OUT_OF_SCOPE_3}}

### 2.4 未來規劃

- {{FUTURE_PLAN_1}}
- {{FUTURE_PLAN_2}}
- {{FUTURE_PLAN_3}}

---

## 3. Technology Stack

### 3.1 Backend

| 項目 | 技術／版本 |
|---|---|
| Language | PHP {{PHP_VERSION}} |
| Framework | Laravel {{LARAVEL_VERSION}} |
| Dependency Manager | Composer {{COMPOSER_VERSION}} |
| Database | MySQL {{MYSQL_VERSION}} |
| Cache / Queue | Redis {{REDIS_VERSION}} |
| Web Server | Nginx |
| Mail Testing | Mailpit |

### 3.2 Frontend

| 項目 | 技術／版本 |
|---|---|
| Framework | React {{REACT_VERSION}} |
| Language | TypeScript |
| Build Tool | Vite {{VITE_VERSION}} |
| Runtime | Node.js {{NODE_VERSION}} |
| Package Manager | npm |

### 3.3 Development Environment

| 項目 | 設定 |
|---|---|
| Docker | {{USE_DOCKER}} |
| Docker Compose | {{USE_DOCKER_COMPOSE}} |
| Local OS | {{LOCAL_OS}} |
| Version Control | Git |
| Repository | {{REPOSITORY_URL}} |

---

## 4. Runtime Baseline

本節記錄專案實際執行版本，Agent 不得自行假設版本。

```text
PHP: {{PHP_VERSION}}
Laravel: {{LARAVEL_VERSION}}
Composer: {{COMPOSER_VERSION}}
Node.js: {{NODE_VERSION}}
npm: {{NPM_VERSION}}
React: {{REACT_VERSION}}
Vite: {{VITE_VERSION}}
MySQL: {{MYSQL_VERSION}}
Redis: {{REDIS_VERSION}}
Environment: {{ENVIRONMENT_TYPE}}
```

版本確認指令：

```bash
php -v
php artisan --version
composer --version
node -v
npm -v
mysql --version
redis-server --version
```

若使用 Docker：

```bash
docker compose exec api php -v
docker compose exec api php artisan --version
docker compose exec api composer --version
docker compose exec web node -v
docker compose exec web npm -v
```

---

## 5. System Architecture

### 5.1 High-Level Architecture

```text
User Browser
    │
    ▼
React SPA
    │
    │ HTTP / JSON
    ▼
Laravel API
    │
    ├── MySQL
    ├── Redis
    ├── Queue Worker
    ├── Scheduler
    └── Mail Service
```

### 5.2 Backend Flow

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
    ↓
MySQL / Redis
```

此流程為建議基準，不代表每一個簡單功能都必須建立所有分層。

### 5.3 Frontend Flow

```text
Page
    ↓
Feature Component
    ↓
Custom Hook
    ↓
Feature API
    ↓
Shared API Client
    ↓
Laravel API
```

---

## 6. Project Structure

```text
{{PROJECT_NAME}}/
├── api/
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   ├── database/
│   ├── public/
│   ├── routes/
│   ├── storage/
│   ├── tests/
│   ├── composer.json
│   └── artisan
│
├── web/
│   ├── public/
│   ├── src/
│   ├── package.json
│   └── vite.config.ts
│
├── docker/
├── docs/
│   ├── architecture/
│   ├── decisions/
│   └── modules/
│
├── compose.yaml
├── PROJECT.md
├── AGENTS.md
├── instructions.md
└── README.md
```

若本專案未使用 Docker，允許不存在：

```text
docker/
compose.yaml
```

若本專案不是前後端分離，允許依現況調整目錄，不得為了符合模板強制搬移既有程式。

---

## 7. Modules

功能細節不得直接大量寫入本文件。

每一個功能模組應建立於：

```text
docs/modules/<module-slug>/
```

### 7.1 Module Status

| 模組 | 狀態 | 規格位置 | 備註 |
|---|---|---|---|
| Authentication | {{STATUS}} | `docs/modules/authentication/` | |
| Admin Management | {{STATUS}} | `docs/modules/admin-management/` | |
| {{MODULE_NAME}} | {{STATUS}} | `docs/modules/{{MODULE_SLUG}}/` | |

可使用的狀態：

- Planned
- Analyzing
- Specifying
- Ready
- In Development
- Testing
- Completed
- Deferred

---

## 8. Development Approach

本專案採用：

```text
Specification-Driven Development
```

標準流程：

```text
需求提出
    ↓
現況分析
    ↓
建立功能規格
    ↓
確認資料結構與 API
    ↓
拆分 Tasks
    ↓
實作
    ↓
測試
    ↓
文件更新
```

不得在需求、資料結構或流程尚未確認時，直接進行大範圍實作。

---

## 9. Documentation Rules

### 9.1 專案層文件

```text
PROJECT.md
AGENTS.md
instructions.md
README.md
```

### 9.2 模組層文件

每個模組建議包含：

```text
docs/modules/<module-slug>/
├── README.md
├── requirements.md
├── flow.md
├── data-model.md
├── api.md
├── permissions.md
├── tasks.md
├── test-plan.md
└── decisions.md
```

小型功能可合併文件，但至少需要：

```text
README.md
tasks.md
test-plan.md
```

---

## 10. Knowledge References

開發前依需求參考：

```text
{{KNOWLEDGE_BASE_PATH}}/Architecture
{{KNOWLEDGE_BASE_PATH}}/Laravel
{{KNOWLEDGE_BASE_PATH}}/React
{{KNOWLEDGE_BASE_PATH}}/Redis
{{KNOWLEDGE_BASE_PATH}}/Docker
{{KNOWLEDGE_BASE_PATH}}/Security
{{KNOWLEDGE_BASE_PATH}}/Testing
```

若 Knowledge 與專案現況衝突，以以下順序判斷：

```text
1. 專案實際程式與版本
2. 專案 AGENTS.md
3. 專案規格文件
4. 專案 PROJECT.md
5. Workspace instructions.md
6. Knowledge
7. 通用最佳實踐
```

---

## 11. Non-Functional Requirements

### 11.1 Security

- 使用者輸入必須驗證。
- 敏感資料不得寫入 Log。
- 密碼、Token、Secret 不得提交 Git。
- 權限檢查不得只由前端執行。
- API 必須依需求實作 Authentication 與 Authorization。

### 11.2 Performance

- 避免 N+1 Query。
- 大量資料必須分頁。
- 複雜查詢需檢查 Index。
- 背景處理應視需求使用 Queue。
- Cache 必須有失效策略。

### 11.3 Reliability

- 多步驟資料異動應考慮 Transaction。
- Queue Job 應考慮 Retry、Timeout 與 Idempotency。
- Webhook 應避免重複處理。
- 外部服務失敗不得造成不可恢復狀態。

### 11.4 Maintainability

- 避免過大的 Controller、Service 或 Component。
- 共用邏輯應適度抽離。
- 不為了套用 Pattern 而過度設計。
- 關鍵技術決策應寫入 `docs/decisions/`。

### 11.5 Testability

核心功能至少應涵蓋：

- Happy Path
- Validation Failure
- Unauthorized
- Forbidden
- Not Found
- Conflict
- Failure Recovery

---

## 12. Environment

### 12.1 Local URLs

| 服務 | URL |
|---|---|
| Laravel API | `{{API_URL}}` |
| React SPA | `{{WEB_URL}}` |
| Mailpit | `{{MAILPIT_URL}}` |

### 12.2 Environment Files

```text
.env
api/.env
web/.env
```

實際環境檔不得提交 Git。

必須保留：

```text
.env.example
api/.env.example
web/.env.example
```

---

## 13. Current Phase

目前階段：

```text
{{CURRENT_PHASE}}
```

目前工作重點：

- {{CURRENT_FOCUS_1}}
- {{CURRENT_FOCUS_2}}
- {{CURRENT_FOCUS_3}}

---

## 14. Open Questions

尚未確認事項：

- {{OPEN_QUESTION_1}}
- {{OPEN_QUESTION_2}}
- {{OPEN_QUESTION_3}}

未確認事項不得由 Agent 自行假設為既定需求。

---

## 15. Key Decisions

| 日期 | 決策 | 原因 | 影響範圍 |
|---|---|---|---|
| {{DATE}} | {{DECISION}} | {{REASON}} | {{SCOPE}} |

詳細決策可另存於：

```text
docs/decisions/
```

---

## 16. Completion Definition

一個功能只有在以下條件皆完成時，才可標示為 Completed：

- [ ] 功能符合規格
- [ ] API 與畫面可正常使用
- [ ] 驗證與權限完成
- [ ] 資料庫異動已建立 Migration
- [ ] 自動化測試完成
- [ ] 手動驗收完成
- [ ] 錯誤處理完成
- [ ] 文件已更新
- [ ] 無已知重大安全問題
- [ ] 無已知重大效能問題