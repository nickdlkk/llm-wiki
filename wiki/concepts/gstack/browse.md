---
title: gstack /browse
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/browse/SKILL.md"]
tags: [gstack, headless-browser, qa-testing, automation, screenshot]
---

# /browse

Fast headless browser for QA testing and site dogfooding. Navigate any URL, interact with elements, verify page state, diff before/after actions, take annotated screenshots, check responsive layouts, test forms and uploads, handle dialogs, and assert element states. ~100ms per command.

## 核心職責

**何時觸發**：
- 用戶說 "open in browser"、"test the site"、"take a screenshot"、"dogfood this"
- 需要驗證功能、部署、或 filing bug with evidence

**功能範圍**：
- 導航任意 URL
- 與元素互動
- 驗證頁面狀態
- Before/After 差異比對
- 截圖（含標註）
- 響應式佈局測試
- 表單和上傳測試
- 對話框處理
- 元素狀態斷言

## 性能特點

~100ms per command — 快速執行，適合大量 QA 測試場景。

## 與 /canary 的區別

| 維度 | /browse | /canary |
|------|---------|---------|
| 用途 | 通用瀏覽器自動化 | Post-deploy 監控 |
| 觸發 | 按需執行 | 部署後自動觸發 |
| 重點 | 功能驗證、截圖、狀態斷言 | 錯誤監控、性能回歸 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/canary]] — post-deploy 監控（使用 browse daemon）
- [[concepts/gstack/benchmark]] — 性能回歸檢測（使用 browse daemon）
- [[concepts/gstack/qa]] — QA gate
