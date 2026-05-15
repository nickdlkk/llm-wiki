---
title: gstack /context-restore
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/context-restore/SKILL.md"]
tags: [gstack, context, checkpoint, resume, handoff]
---

# /context-restore

Restore working context saved earlier by /context-save. Loads the most recent saved state (across all branches by default) so you can pick up where you left off — even across Conductor workspace handoffs.

## 核心職責

**何時觸發**：
- 用戶說 "resume"、"restore context"、"where was I"、"pick up where I left off"

**功能**：
- 恢復之前保存的工作上下文
- 跨所有 branch 默認恢復最新狀態
- 支援 Conductor workspace handoffs

## 與 /context-save 的關係

/context-restore 是 /context-save 的逆操作：

| 維度 | /context-save | /context-restore |
|------|---------------|------------------|
| 操作 | 保存 | 恢復 |
| 時機 | 工作中斷前 | 繼續工作時 |
| 數據 | Git 狀態、決策、剩餘工作 | 之前保存的狀態 |

## 歷史背景

Formerly /checkpoint resume — renamed because Claude Code treats /checkpoint as a native rewind alias in current environments.

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/context-save]] — 保存工作上下文
- [[concepts/gstack/learn]] — 跨會話記憶
