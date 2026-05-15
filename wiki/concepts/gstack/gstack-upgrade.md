---
title: gstack /gstack-upgrade
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/gstack-upgrade/SKILL.md"]
tags: [gstack, upgrade, update, version]
---

# /gstack-upgrade

Upgrade gstack to the latest version. Detects global vs vendored install, runs the upgrade, and shows what's new.

## 核心職責

**何時觸發**：
- 用戶說 "upgrade gstack"、"update gstack"、"get latest version"

**功能**：
- 檢測 global vs vendored install
- 運行升級
- 展示新功能

## Voice Triggers

- "upgrade the tools"
- "update the tools"
- "gee stack upgrade"
- "g stack upgrade"

## 升級流程

### Inline Upgrade Flow

1. 檢測 `UPGRADE_AVAILABLE`
2. 詢問用戶或自動升級
3. 顯示新版本內容

### 配置

- 全局安裝 vs Vendored 安裝檢測
- Auto-upgrade（如果配置）

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
