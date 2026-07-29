# Redis Processing Lock

## Purpose

本文件記錄使用 Redis Processing Lock 避免重複處理訊息的設計方式。

適用於：

* LINE Bot
* Chat Bot
* AI Assistant
* Queue Worker
* Background Job
* Webhook Processing

---

# Problem

當系統有多個 Worker 或排程同時執行時，可能發生：

```text
同一筆訊息被處理兩次
```

例如：

```text
Worker A
Worker B
```

同時掃描到：

```text
message_line:718
```

結果：

```text
AI 回覆兩次

DB 寫入兩次

LINE Push 兩次
```

---

# Solution

建立 Processing Lock。

核心概念：

```text
先上鎖

再執行

完成後解鎖
```

---

# Architecture

```text
Queue Message
      │
      ▼
Check Lock
      │
      ▼
Lock Exists ?
      │
 ┌────┴────┐
 │         │
Yes       No
 │         │
Skip    Create Lock
 │         │
 └────┬────┘
      ▼
Process
      ▼
Release Lock
```

---

# Redis Key Design

Queue：

```text
message_line:{account}
```

範例：

```text
message_line:718
```

---

Processing Lock：

```text
message_line:{account}:processing_line
```

範例：

```text
message_line:718:processing_line
```

---

# Processing Flow

## Step 1

收到 Queue：

```text
message_line:718
```

---

## Step 2

檢查：

```text
message_line:718:processing_line
```

是否存在。

---

## Step 3

如果存在：

```php
continue;
```

表示：

```text
已有 Worker 處理中
```

直接跳過。

---

## Step 4

如果不存在：

建立 Lock。

```php
$redis->set(
    $processingKey,
    time()
);
```

---

## Step 5

開始執行：

```text
呼叫 AI

寫入 DB

發送 LINE
```

---

## Step 6

完成後刪除：

```php
$redis->del($processingKey);
```

---

# Recommended Implementation

```php
try {

    $redis->set(
        $processingKey,
        time()
    );

    process();

}
finally {

    $redis->del($processingKey);

}
```

---

# Why Use finally

避免：

```text
Exception

Fatal Error

AI Timeout

Worker Crash
```

導致：

```text
Lock 永遠存在
```

---

# TTL Protection

建議增加 TTL。

例如：

```php
$redis->setex(
    $processingKey,
    300,
    time()
);
```

---

TTL：

```text
300 秒
```

---

目的：

即使 Worker 異常終止，

Lock 仍會自動釋放。

---

# Common Problems

## Duplicate Reply

現象：

```text
LINE 回覆兩次
```

原因：

```text
Lock 未建立

或建立太晚
```

解法：

```text
進入處理前立即建立 Lock
```

---

## Lock Never Released

現象：

```text
訊息永遠不被處理
```

原因：

```text
Worker Crash

Exception

未執行 DEL
```

解法：

```text
finally

TTL
```

---

## Multiple Workers

現象：

```text
Worker A

Worker B
```

同時處理。

原因：

```text
Lock 建立時間過晚
```

解法：

```text
先 Lock

後 Process
```

---

# Monitoring

定期檢查：

```text
message:*:processing*

message_line:*:processing_line*
```

---

查詢：

```bash
redis-cli --scan --pattern "*:processing*"
```

---

檢查 TTL：

```bash
redis-cli TTL key_name
```

---

TTL 判斷：

```text
-2  Key 不存在

-1  無 TTL（危險）

0~300 正常

>300 可能卡住
```

---

# Best Practices

## Always Lock First

```text
Lock

↓

Process
```

不要反過來。

---

## Always Use finally

確保：

```text
DEL Processing Lock
```

一定執行。

---

## Set TTL

避免：

```text
永久鎖死
```

---

## Log Processing

記錄：

```text
建立 Lock

刪除 Lock

處理時間
```

方便除錯。

---

# Lessons Learned

Processing Lock 是 Queue 系統中最重要的保護機制之一。

沒有 Lock：

```text
重複執行
重複回覆
重複寫入
```

有 Lock：

```text
同一時間

同一筆資料

只會被一個 Worker 處理
```

因此：

```text
Debounce Queue
+
Processing Lock
```

通常必須一起使用。
