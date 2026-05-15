---
title: gstack /design-shotgun
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/design-shotgun/SKILL.md"]
tags: [gstack, design, variants, brainstorm, exploration]
---

# /design-shotgun

Design shotgun: generate multiple AI design variants, open a comparison board, collect structured feedback, and iterate. Standalone design exploration you can run anytime.

## 核心職責

**何時觸發**：
- 用戶說 "explore designs"、"show me options"、"design variants"、"visual brainstorm"、"I don't like how this looks"
- 主動建議：用戶描述 UI 功能但還沒看到可能的樣子

**工作流程**：
1. 生成多個 AI 設計變體
2. 打開 comparison board
3. 收集結構化反饋
4. 迭代

## 與 /design-html 的關係

| 維度 | /design-shotgun | /design-html |
|------|-----------------|--------------|
| 階段 | 探索、多變體 | 最終實現 |
| 輸入 | 設計想法 | 批准的 mockups |
| 輸出 | 多個設計選項 | 單一生產代碼 |

## 與 /design-consultation 的區別

| 維度 | /design-shotgun | /design-consultation |
|------|-----------------|---------------------|
| 目的 | 多變體探索 | 完整系統提案 |
| 輸出 | 多個獨立選項 | 統一的設計語言 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/design-html]] — 將批准的設計轉為 HTML
- [[concepts/gstack/design-review]] — 視覺 QA
- [[concepts/gstack/design-consultation]] — 設計系統提案
