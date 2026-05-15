---
title: gstack /design-html
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/design-html/SKILL.md"]
tags: [gstack, design, html, css, implementation, pretext]
---

# /design-html

Design finalization: generates production-quality Pretext-native HTML/CSS. Works with approved mockups from /design-shotgun, CEO plans from /plan-ceo-review, design review context from /plan-design-review, or from scratch with a user description.

## 核心職責

**何時觸發**：
- 用戶說 "finalize this design"、"turn this into HTML"、"build me a page"、"implement this design"
- 主動建議：用戶批准設計或有 plan ready

**特點**：
- 生產級別 Pretext-native HTML/CSS
- Text 實際可流動
- Heights 自動計算
- 佈局動態響應
- 30KB overhead，零依賴
- Smart API routing（為每種設計類型選擇正確的 Pretext pattern）

## 與 /design-shotgun 的關係

| 維度 | /design-shotgun | /design-html |
|------|-----------------|--------------|
| 階段 | 探索、多變體 | 最終實現 |
| 輸入 | 設計想法 | 批准的 mockups |
| 輸出 | 多個設計選項 | 單一生產代碼 |

## Voice Triggers

- "build the design"
- "code the mockup"
- "make it real"

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/design-shotgun]] — 生成多個設計變體
- [[concepts/gstack/design-review]] — 視覺 QA
- [[concepts/gstack/design-consultation]] — 設計系統提案
