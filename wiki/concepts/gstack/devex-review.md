---
title: gstack /devex-review
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/devex-review/SKILL.md"]
tags: [gstack, devex, developer-experience, onboarding, tthw]
---

# /devex-review

Live developer experience audit. Uses the browse tool to actually TEST the developer experience: navigates docs, tries the getting started flow, times TTHW (Time To Hello World), screenshots error messages, evaluates CLI help text. Produces a DX scorecard with evidence.

## 核心職責

**何時觸發**：
- 用戶說 "test the DX"、"DX audit"、"developer experience test"、"try the onboarding"
- 主動建議：交付面向開發者的功能後

**測試維度**：
- Navigates docs（導航文檔）
- Tries getting started flow（嘗試入門流程）
- Times TTHW（測量 Hello World 時間）
- Screenshots error messages（截圖錯誤信息）
- Evaluates CLI help text（評估 CLI 幫助文本）

**輸出**：
- DX Scorecard（帶證據的開發者體驗記分卡）
- 對比 /plan-devex-review 分數（如有）「 Boomerang effect」：plan 說 3 分鐘，現實說 8 分鐘

## Voice Triggers

- "dx audit"
- "test the developer experience"
- "try the onboarding"
- "developer experience test"

## 與 /plan-devex-review 的關係

| 維度 | /plan-devex-review | /devex-review |
|------|-------------------|---------------|
| 時機 | Plan 階段（實現前） | 實現後（真實測試） |
| 方法 | 推演評估 | 實際運行測試 |
| 輸出 | 預期分數 | 實際分數 + 證據 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/browse]] — headless browser（用於實際測試）
- [[concepts/gstack/qa]] — 功能 QA
