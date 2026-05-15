---
title: gstack /design-review
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/design-review/SKILL.md"]
tags: [gstack, design, qa, visual-audit, polish]
---

# /design-review

Designer's eye QA: finds visual inconsistency, spacing issues, hierarchy problems, AI slop patterns, and slow interactions — then fixes them. Iteratively fixes issues in source code, committing each fix atomically and re-verifying with before/after screenshots.

## 核心職責

**何時觸發**：
- 用戶說 "audit the design"、"visual QA"、"check if it looks good"、"design polish"
- 主動建議：用戶提到視覺不一致或想要潤色 live site

**檢查維度**：
- Visual Inconsistency（視覺不一致）
- Spacing Issues（間距問題）
- Hierarchy Problems（層級問題）
- AI Slop Patterns（AI 敷衍模式）
- Slow Interactions（慢交互）

**工作方式**：
1. 發現問題
2. 在源碼中迭代修復
3. 每個修復 atomic commit
4. 用 before/after 截圖重新驗證

## 與 /plan-design-review 的區別

| 維度 | /design-review | /plan-design-review |
|------|----------------|---------------------|
| 時機 | 實現後（代碼級修復） | 實現前（plan 階段） |
| 重點 | 視覺 QA + 修復 | 設計審查（不動代碼） |
| 輸出 | 修復後的代碼 | 設計建議 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/design-html]] — 設計實現
- [[concepts/gstack/design-shotgun]] — 設計變體探索
- [[concepts/gstack/qa]] — 功能 QA
