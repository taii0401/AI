# Redis Debounce Queue

## Purpose

本文件記錄使用 Redis 實作 Debounce Queue 的設計方式。

適用於：

* LINE Bot
* Chat System
* AI Assistant
* WebSocket Message
* Telegram Bot
* Facebook Messenger

目標：

避免使用者短時間內連續輸入時，每則訊息都立即觸發後續處理。

---

# Problem

使用者常在短時間內連續輸入訊息：

例如：

```text
你好

請問營業時間

今天有開嗎
```

如果每收到一則訊息就立即：

```text
呼叫 AI
查詢資料
發送通知
```

會造成：

* 成本增加
* 回覆品質下降
* 重複處理
* 系統負載增加

---

# Solution

採用 Debounce Queue。

核心概念：

```text
最後一則訊息後等待 N 秒

若期間又收到新訊息

則重新計時

直到使用者停止輸入

才進行處理
```

---

# Architecture

```text
User Message
        │
        ▼
Redis List
        │
        ▼
Delay Key (TTL)
        │
        ▼
Worker Scan
        │
        ▼
AI / Business Logic
        │
        ▼
Reply User
```

---

# Redis Key Design

## Message Queue

```text
message_line:{account}
```

儲存使用者訊息。

範例：

```text
message_line:718
```

---

## Message Metadata

```text
message_meta_line:{account}
```

儲存：

* user_id
* group_id
* source_type
* timestamp

---

## Delay Key

```text
message_delay_line:{account}
```

用途：

控制等待時間。

設定 TTL：

```text
7 秒
10 秒
15 秒
```

依實際需求決定。

---

## Processing Lock

```text
message_line:{account}:processing_line
```

避免：

* 多個 Worker 同時執行
* AI 重複回覆
* 資料重複寫入

---

# Message Flow

## Receive Message

收到訊息：

```text
你好
```

加入：

```text
message_line:718
```

並建立：

```text
message_delay_line:718
TTL = 7
```

---

## Receive Another Message

3 秒後：

```text
請問營業時間
```

再次加入 Queue。

重新建立：

```text
message_delay_line:718
TTL = 7
```

重新計時。

---

## User Stops Typing

7 秒後：

Delay Key 消失。

Worker 發現：

```text
message_line:718
```

存在且：

```text
message_delay_line:718
```

不存在。

表示：

```text
使用者已停止輸入
```

開始處理。

---

# Worker Logic

掃描：

```text
message_line:*
```

---

跳過：

```text
*_meta*
*:processing*
```

---

檢查：

```text
delay key 是否存在
```

若存在：

```text
continue
```

若不存在：

```text
開始處理
```

---

# Processing Lock

處理前建立：

```text
message_line:718:processing_line
```

---

處理完成：

```text
DEL processing key
```

---

建議：

```text
finally
{
    DEL processing key
}
```

避免 Worker 異常導致 Lock 永久存在。

---

# Advantages

## Better Context

AI 能取得完整上下文。

例如：

```text
你好

請問營業時間

今天有開嗎
```

一次送出。

---

## Lower Cost

降低：

* OpenAI API
* Claude API
* Gemini API

呼叫次數。

---

## Better User Experience

避免：

```text
每一句都回一次
```

造成洗版。

---

## Reduce System Load

降低：

* Queue 數量
* API 呼叫數
* Database 寫入數

---

# Common Issues

## Duplicate Reply

原因：

Processing Lock 未正確建立。

解法：

```text
先建立 Lock
再開始處理
```

---

## Message Never Processed

原因：

Processing Lock 未清除。

解法：

```text
finally 清除 Lock
```

並定期檢查異常 Lock。

---

## Meta Not Found

原因：

Message 與 Metadata 不一致。

解法：

```text
寫入 Message
與 Meta
必須保持原子性
```

---

# Suitable Scenarios

適合：

* Chat Bot
* AI Assistant
* Customer Service
* Notification System

不適合：

* 即時交易
* 金流處理
* 即時競價
* 需要毫秒級反應的系統

---

# Lessons Learned

不要每收到一則訊息就立即執行昂貴操作。

先收集訊息。

等待使用者停止輸入。

再統一處理。

這種模式能顯著降低成本、提升上下文品質與改善使用者體驗。
