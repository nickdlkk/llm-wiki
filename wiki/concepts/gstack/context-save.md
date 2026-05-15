---
title: gstack /context-save
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/context-save/SKILL.md"]
tags: [gstack, context, checkpoint, save-state, persistence]
---

# /context-save

Save working context. Captures git state, decisions made, and remaining work so any future session can pick up without losing a beat.

## 核心職責

**何時觸發**：
- 用戶說 "save progress"、"save state"、"context save"、"save my work"

**保存內容**：
- Git 狀態
- 已做的決策
- 剩餘工作

## 與 /context-restore 的關係

/context-save 是 /context-restore 的逆操作：

| 維度 | /context-save | /context-restore |
|------|---------------|------------------|
| 操作 | 保存 | 恢復 |
| 時機 | 工作中斷前 | 繼續工作時 |
| 數據 | Git 狀態、決策、剩餘工作 | 之前保存的狀態 |

## 歷史背景

Formerly /checkpoint — renamed because Claude Code treats /checkpoint as a native rewind alias in current environments, which was shadowing this skill.

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/context-restore]] — 恢復保存的上下文
- [[concepts/gstack/learn]] — 跨會話記憶
