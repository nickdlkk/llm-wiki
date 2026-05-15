---
title: gstack /freeze
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/freeze/SKILL.md"]
tags: [gstack, safety, file-editing, restriction, scope]
---

# /freeze

Restrict file edits to a specific directory for the session. Blocks Edit and Write outside the allowed path. Use when debugging to prevent accidentally "fixing" unrelated code, or when you want to scope changes to one module.

## 核心職責

**何時觸發**：
- 用戶說 "freeze"、"restrict edits"、"only edit this folder"、"lock down edits"

**機制**：
- 限制文件編輯到特定目錄
- 阻止 Edit 和 Write 到允許路徑之外
- 會話範圍內有效

## 典型用途

1. **調試時**：防止意外「修復」不相關的代碼
2. **限定範圍**：只修改一個模塊

## 與 /careful 的區別

| 維度 | /freeze | /careful |
|------|---------|----------|
| 防護機制 | 阻止編輯 | 警告（可覆蓋） |
| 作用範圍 | 目錄範圍 | 危險命令 |
| 組合 | 可與 /careful 組合為 /guard | 可與 /freeze 組合為 /guard |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/careful]] — 危險命令警告
- [[concepts/gstack/guard]] — /careful + /freeze 組合
- [[concepts/gstack/investigate]] — 調試場景（/freeze 綁定在 investigate）
