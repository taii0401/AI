# Docker Permissions

## Purpose

本文件定義 Docker 開發環境中的權限管理原則、檔案擁有者（Ownership）、使用者（User）、群組（Group）與最佳實踐。

目的：

- 避免權限錯誤
- 避免 root 建立檔案
- 提高跨平台一致性
- 降低 Windows、WSL2、Linux 差異
- 建立可維護的權限策略

本文件適用於：

- Docker Desktop
- Docker Engine
- WSL2
- Linux
- Laravel
- React
- Composer
- npm
- Vite

---

# Why Permissions Matter

Docker Container：

與：

Host：

屬於：

不同：

Linux Environment。

因此：

Container：

建立：

```text
vendor/

node_modules/

storage/
```

Host：

不一定：

有權限。

反之：

Host：

建立：

檔案。

Container：

也可能：

無法修改。

---

# Permission Model

Docker：

主要：

包含：

```text
User

↓

Group

↓

Permission
```

每個：

檔案：

都有：

```text
Owner

Group

Mode
```

例如：

```text
-rw-r--r--
```

---

# UID

Linux：

真正：

辨識：

使用者：

不是：

名稱。

而是：

```text
UID
```

例如：

```text
1000
```

---

# GID

群組：

也是：

```text
GID
```

例如：

```text
1000
```

---

# User Name

例如：

```text
app

www-data

root
```

其實：

只是：

UID：

的：

名稱。

真正：

權限：

由：

UID/GID：

決定。

---

# Root

Root：

UID：

永遠：

```text
0
```

Root：

擁有：

所有權限。

但是：

開發：

不建議：

全部：

使用：

Root。

---

# Recommended User

Application：

建議：

建立：

```text
app
```

例如：

```dockerfile
RUN useradd -u 1000 app
```

最後：

```dockerfile
USER app
```

不要：

一直：

使用：

```text
root
```

---

# Host User

WSL：

查看：

```bash
id
```

例如：

```text
uid=1000

gid=1000
```

Docker：

建議：

Container：

也：

使用：

```text
1000
```

保持一致。

---

# APP_UID

Compose：

建議：

```yaml
user:

"${APP_UID}:${APP_GID}"
```

Root：

`.env`

例如：

```dotenv
APP_UID=1000

APP_GID=1000
```

避免：

Container：

建立：

Root：

檔案。

---

# Bind Mount

例如：

```yaml
./api:/var/www/html
```

Container：

修改：

Host：

檔案。

因此：

UID：

必須：

一致。

---

# Named Volume

例如：

```yaml
mysql_data
```

Docker：

自行：

管理。

通常：

較少：

Permission：

問題。

---

# File Ownership

查看：

```bash
ls -l
```

查看：

UID：

```bash
ls -ln
```

例如：

```text
1000

1000
```

代表：

Owner：

UID。

---

# Change Owner

修改：

Owner：

```bash
chown -R app:app storage
```

或：

```bash
chown -R 1000:1000 storage
```

---

# chmod

修改：

Permission。

例如：

```bash
chmod 775 storage
```

不要：

```bash
chmod 777
```

除非：

除錯。

---

# Laravel

Laravel：

需要：

可寫：

```text
storage/

bootstrap/cache/
```

建議：

```bash
chmod -R ug+rwX storage

chmod -R ug+rwX bootstrap/cache
```

---

# Composer

Composer：

不要：

使用：

Root。

建議：

```bash
docker compose exec api composer install
```

Container：

User：

應：

為：

```text
app
```

---

# npm

Node：

同樣：

不要：

Root。

避免：

```text
node_modules
```

全部：

Owner：

```text
root
```

---

# Vite

Vite：

建立：

```text
.vite
```

若：

Root：

建立。

Host：

容易：

無法：

修改。

因此：

建議：

User：

一致。

---

# Cache Directory

例如：

```text
storage/framework

bootstrap/cache

node_modules/.vite
```

都是：

容易：

發生：

Permission：

問題。

---

# Root-Owned Files

若：

曾：

使用：

Root：

可能：

看到：

```text
root root
```

解決：

```bash
sudo chown -R $USER:$USER .
```

或：

指定：

UID。

---

# Windows + WSL2

建議：

所有：

Docker：

操作：

都：

在：

WSL：

執行。

不要：

PowerShell：

與：

WSL：

混用。

避免：

不同：

Permission。

---

# Git

Git：

應：

Host：

執行。

不要：

Container：

Commit。

避免：

Owner：

混亂。

---

# Temporary Fix

若：

只：

為了：

除錯：

可：

```bash
chmod -R 777 storage
```

完成：

後：

應：

恢復：

正常：

權限。

---

# Security Principle

遵循：

Least Privilege。

每個：

Process：

只：

擁有：

必要：

權限。

不要：

全部：

Root。

---

# Debug

查看：

User：

```bash
whoami
```

查看：

UID：

```bash
id
```

查看：

Owner：

```bash
ls -ln
```

查看：

Permission：

```bash
stat filename
```

---

# Common Mistakes

避免：

- 使用 root 開發
- chmod 777
- Container 與 Host UID 不一致
- Root 建立 vendor
- Root 建立 node_modules
- Root 建立 storage
- PowerShell 與 WSL 混用
- Composer 使用 sudo
- npm 使用 sudo

---

# Best Practices

建議：

1. Container User 與 Host UID 一致。
2. 使用非 root User。
3. Bind Mount 保持 UID/GID 一致。
4. Laravel 只開放必要目錄可寫。
5. Composer、npm 不使用 sudo。
6. Named Volume 優先保存資料。
7. 發生權限問題先檢查 Owner，再檢查 Mode。
8. 避免以 `chmod 777` 作為永久解法。

---

# Checklist

建立新的 Docker 環境前：

- [ ] Host UID 已確認
- [ ] Host GID 已確認
- [ ] APP_UID 已設定
- [ ] APP_GID 已設定
- [ ] Container 使用非 root User
- [ ] storage 可寫
- [ ] bootstrap/cache 可寫
- [ ] vendor 權限正常
- [ ] node_modules 權限正常
- [ ] .vite 權限正常
- [ ] 無 root 擁有的專案檔案

---

# Related Documents

建議閱讀順序：

```text
README.md
    ↓
architecture.md
    ↓
compose-standards.md
    ↓
networking.md
    ↓
healthcheck.md
    ↓
dockerfile-standards.md
    ↓
development-environment.md
    ↓
permissions.md
    ↓
volumes.md
    ↓
image-versioning.md
    ↓
troubleshooting.md
```