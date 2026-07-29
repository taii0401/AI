# Architecture Database Design

## Purpose

這份文件定義通用 database design 原則。

目標是讓資料模型正確、可查詢、可演進，避免未來資料不一致。

---

## Core Principles

- 資料正確性優先於短期方便
- schema 命名要清楚
- constraint 要放在 database
- index 根據查詢設計
- migration 要可回溯與可部署
- 不要把所有資料塞進 JSON

---

## Relational Database First

多數產品預設使用 relational database。

適合：

- transaction
- relationship
- reporting
- data integrity
- ad-hoc query

NoSQL 應解決明確問題，而不是預設選項。

---

## Constraints

建議使用：

- primary key
- foreign key
- unique constraint
- not null
- check constraint when available

不要只依賴 application validation 維護資料正確性。

---

## Indexes

Index 應根據查詢設計。

適合加 index：

- foreign key
- filter 欄位
- sort 欄位
- unique lookup
- frequently joined columns

注意：

- index 會增加寫入成本
- 複合 index 順序很重要
- 不要盲目為所有欄位加 index

---

## Migrations

Production migration 原則：

- 避免長時間 lock table
- 大表 schema change 要分階段
- backfill 要批次處理
- destructive change 要延後
- deploy 與 rollback 要考慮相容性

---

## Checklist

檢查 database design：

- constraint 是否足夠？
- nullable 是否合理？
- index 是否符合查詢？
- 是否有資料 ownership？
- migration 是否可安全部署？
- 是否避免過度使用 JSON 欄位？
