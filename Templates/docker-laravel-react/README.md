# Docker Laravel API + React SPA Template

這是一套共用的本機 Docker 開發環境模板。

## Services

- PHP 8.4-FPM
- Nginx
- MySQL 8.4
- Redis
- Mailpit
- Node.js
- Laravel Queue Worker
- Laravel Scheduler

## Project Structure

- `backend/`：Laravel API
- `frontend/`：React SPA
- `docker/php/`：共用 PHP Image
- `docker/nginx-api/`：Laravel API Nginx 設定

## Current Phase

目前僅建立 Docker 開發環境。

Laravel API 與 React SPA 將於後續 Phase 建立。

## Infrastructure Start

```bash
docker compose up -d