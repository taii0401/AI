# {{PROJECT_NAME}}

{{PROJECT_DESCRIPTION}}

---

## 1. Technology Stack

### Backend

- PHP {{PHP_VERSION}}
- Laravel {{LARAVEL_VERSION}}
- Composer
- MySQL {{MYSQL_VERSION}}
- Redis {{REDIS_VERSION}}
- Nginx
- Mailpit

### Frontend

- React {{REACT_VERSION}}
- TypeScript
- Vite
- Node.js {{NODE_VERSION}}
- npm

### Development

- Docker
- Docker Compose
- Git
- VS Code

---

## 2. Project Structure

```text
.
├── api/                 # Laravel API
├── web/                 # React SPA
├── docker/              # Docker 設定
├── docs/                # 專案與模組文件
├── compose.yaml
├── PROJECT.md
├── AGENTS.md
├── instructions.md
├── .env.example
└── README.md
```

主要文件：

| 文件 | 用途 |
|---|---|
| `PROJECT.md` | 專案目標、範圍、版本與架構 |
| `AGENTS.md` | AI Agent 與開發流程規範 |
| `instructions.md` | 專案細部開發規則 |
| `docs/modules/` | 各功能模組規格 |
| `docs/decisions/` | 架構與技術決策 |

---

## 3. Prerequisites

使用 Docker 開發時，需要：

- Docker Desktop
- WSL2
- Ubuntu
- Git
- VS Code

確認環境：

```bash
docker version
docker compose version
```

---

## 4. First-Time Setup

### 4.1 Clone Repository

```bash
git clone {{REPOSITORY_URL}}
cd {{PROJECT_DIRECTORY}}
```

### 4.2 Create Environment Files

```bash
cp .env.example .env
cp api/.env.example api/.env
cp web/.env.example web/.env
```

請依本機環境調整：

```text
.env
api/.env
web/.env
```

---

## 5. Root Environment Configuration

根目錄 `.env` 提供 Docker Compose 使用。

範例：

```dotenv
PHP_IMAGE={{PHP_IMAGE}}

APP_UID=1000
APP_GID=1000

API_PORT=8080
WEB_PORT=5173

DB_DATABASE={{DB_DATABASE}}
DB_USERNAME={{DB_USERNAME}}
DB_PASSWORD={{DB_PASSWORD}}
DB_ROOT_PASSWORD={{DB_ROOT_PASSWORD}}
MYSQL_FORWARD_PORT=3307

REDIS_FORWARD_PORT=6380

MAILPIT_WEB_PORT=8025
MAILPIT_SMTP_PORT=1025
```

查看目前 WSL UID、GID：

```bash
id -u
id -g
```

---

## 6. Laravel Environment Configuration

`api/.env` 範例：

```dotenv
APP_NAME="{{PROJECT_NAME}}"
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8080

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE={{DB_DATABASE}}
DB_USERNAME={{DB_USERNAME}}
DB_PASSWORD={{DB_PASSWORD}}

SESSION_DRIVER=redis
CACHE_STORE=redis
QUEUE_CONNECTION=redis

REDIS_CLIENT=phpredis
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="system@example.test"
MAIL_FROM_NAME="${APP_NAME}"

FRONTEND_URL=http://localhost:5173
```

Container 內部連線必須使用 Service Name：

```text
mysql
redis
mailpit
```

不得使用：

```text
localhost
127.0.0.1
```

---

## 7. React Environment Configuration

`web/.env` 範例：

```dotenv
VITE_APP_NAME="{{PROJECT_NAME}}"
VITE_API_BASE_URL=http://localhost:8080
```

---

## 8. Validate Docker Compose

```bash
docker compose config
```

查看全部服務：

```bash
docker compose --profile application config --services
```

預期包含：

```text
api
api-nginx
web
mysql
redis
mailpit
queue
scheduler
```

---

## 9. Start Infrastructure

先啟動 MySQL、Redis、Mailpit：

```bash
docker compose up -d mysql redis mailpit
```

查看狀態：

```bash
docker compose ps
```

預期：

```text
mysql    healthy
redis    healthy
mailpit  running
```

---

## 10. Start Laravel API

```bash
docker compose --profile application up -d api
```

確認 PHP：

```bash
docker compose exec api php -v
```

---

## 11. Install Backend Dependencies

先確保 Laravel 可寫目錄：

```bash
docker compose exec --user root api sh -lc '
  mkdir -p \
    /var/www/html/vendor \
    /var/www/html/bootstrap/cache \
    /var/www/html/storage/framework/cache/data \
    /var/www/html/storage/framework/sessions \
    /var/www/html/storage/framework/views \
    /var/www/html/storage/logs

  chown -R app:app \
    /var/www/html/vendor \
    /var/www/html/bootstrap/cache \
    /var/www/html/storage

  chmod -R ug+rwX \
    /var/www/html/vendor \
    /var/www/html/bootstrap/cache \
    /var/www/html/storage
'
```

安裝 Composer 套件：

```bash
docker compose exec \
  -e COMPOSER_PROCESS_TIMEOUT=1800 \
  api \
  composer install \
  --prefer-dist \
  --no-interaction \
  --no-progress
```

確認：

```bash
docker compose exec api php artisan --version
```

---

## 12. Initialize Laravel

產生 Application Key：

```bash
docker compose exec api php artisan key:generate
```

清除快取：

```bash
docker compose exec api php artisan optimize:clear
```

執行 Migration：

```bash
docker compose exec api php artisan migrate
```

查看 Migration：

```bash
docker compose exec api php artisan migrate:status
```

---

## 13. Install Frontend Dependencies

```bash
docker compose --profile application run --rm \
  --user "$(id -u):$(id -g)" \
  -e HOME=/tmp \
  -e NPM_CONFIG_FETCH_RETRIES=5 \
  -e NPM_CONFIG_FETCH_TIMEOUT=600000 \
  web \
  npm install
```

確認：

```bash
ls -la web/node_modules | head
```

如曾出現 Vite 快取權限問題：

```bash
rm -rf web/node_modules/.vite
```

---

## 14. Start Website

啟動 Laravel Nginx 與 React：

```bash
docker compose --profile application up -d api-nginx web
```

查看：

```bash
docker compose --profile application ps
```

---

## 15. Local URLs

| Service | URL |
|---|---|
| React SPA | `http://localhost:5173` |
| Laravel API | `http://localhost:8080` |
| Mailpit | `http://localhost:8025` |
| MySQL Host Port | `localhost:3307` |
| Redis Host Port | `localhost:6380` |

---

## 16. Daily Startup

重開機後先開啟 Docker Desktop，再執行：

```bash
cd {{PROJECT_PATH}}

docker compose --profile application up -d \
  mysql \
  redis \
  mailpit \
  api \
  api-nginx \
  web
```

查看狀態：

```bash
docker compose --profile application ps
```

---

## 17. Common Commands

### Laravel

```bash
docker compose exec api php artisan about
docker compose exec api php artisan route:list
docker compose exec api php artisan migrate
docker compose exec api php artisan migrate:status
docker compose exec api php artisan optimize:clear
docker compose exec api php artisan test
```

### Composer

```bash
docker compose exec api composer install
docker compose exec api composer require vendor/package
docker compose exec api composer check-platform-reqs
```

不要任意執行：

```bash
composer update
```

### React

```bash
docker compose exec web npm run dev
docker compose exec web npm run build
docker compose exec web npm run lint
```

### MySQL

```bash
docker compose exec mysql \
  mysql \
  -u{{DB_USERNAME}} \
  -p{{DB_PASSWORD}} \
  {{DB_DATABASE}}
```

### Redis

```bash
docker compose exec redis redis-cli ping
```

預期：

```text
PONG
```

---

## 18. Logs

```bash
docker compose logs -f api
docker compose logs -f api-nginx
docker compose logs -f web
docker compose logs -f mysql
docker compose logs -f redis
docker compose logs -f mailpit
```

最近 100 行：

```bash
docker compose logs --tail=100 api
```

---

## 19. Queue and Scheduler

啟動 Queue：

```bash
docker compose --profile application up -d queue
```

啟動 Scheduler：

```bash
docker compose --profile application up -d scheduler
```

查看：

```bash
docker compose logs -f queue
docker compose logs -f scheduler
```

---

## 20. Testing

Backend：

```bash
docker compose exec api php artisan test
```

Frontend Build：

```bash
docker compose exec web npm run build
```

Frontend Lint：

```bash
docker compose exec web npm run lint
```

---

## 21. Stop Environment

停止 Container：

```bash
docker compose --profile application stop
```

移除 Container，但保留 MySQL、Redis 資料：

```bash
docker compose --profile application down
```

不得任意執行：

```bash
docker compose down -v
```

因為 `-v` 會刪除 MySQL 與 Redis Volume。

---

## 22. Troubleshooting

### Laravel 找不到 `vendor/autoload.php`

```bash
docker compose exec api composer install
```

### Laravel 設定未更新

```bash
docker compose exec api php artisan optimize:clear
```

### React 白畫面

```bash
docker compose logs -f web
rm -rf web/node_modules/.vite
docker compose --profile application up -d --force-recreate web
```

### MySQL Access Denied

確認：

```dotenv
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE={{DB_DATABASE}}
DB_USERNAME={{DB_USERNAME}}
DB_PASSWORD={{DB_PASSWORD}}
```

### Redis 連線

```bash
docker compose exec redis redis-cli ping
```

---

## 23. Documentation

開發前閱讀：

```text
PROJECT.md
AGENTS.md
instructions.md
```

功能規格：

```text
docs/modules/<module-slug>/
```

---

## 24. License

{{LICENSE}}