# Architecture Application Architecture

## Purpose

定義應用程式內部結構。

---

## Typical Layers

Presentation Layer

↓

Application Layer

↓

Domain Layer

↓

Data Access Layer

↓

Infrastructure Layer

---

## Laravel Mapping

Controller

↓

Service

↓

Repository

↓

Model

↓

Database

---

## Common Styles

### Monolith

適合：

* MVP
* 小型團隊

### Modular Monolith

適合：

* 中大型專案

### Event Driven

適合：

* 非同步流程

### Microservice

適合：

* 大型團隊
* 獨立部署需求
