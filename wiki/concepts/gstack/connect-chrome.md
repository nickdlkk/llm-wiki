---
title: gstack /connect-chrome
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/connect-chrome/SKILL.md"]
tags: [gstack, browser, chromium, automation, side-panel]
---

# /connect-chrome

Launch GStack Browser — AI-controlled Chromium with the sidebar extension baked in. Opens a visible browser window where you can watch every action in real time. The sidebar shows a live activity feed and chat. Anti-bot stealth built in.

## 核心職責

**何時觸發**：
- 用戶說 "open gstack browser"、"launch browser"、"connect chrome"、"open chrome"、"real browser"、"launch chrome"、"side panel"、"control my browser"
- 需要可視化瀏覽器進行操作或監控

**功能特點**：
- AI-controlled Chromium
- 實時可視化操作
- Sidebar extension（活動 feed + chat）
- Anti-bot stealth 內建

## Voice Triggers

- "show me the browser"

## 與 /browse 的區別

| 維度 | /connect-chrome | /browse |
|------|-----------------|---------|
| 瀏覽器模式 | 可視化（可觀看） | Headless（幕後） |
| 用途 | 調試、演示、用戶引導 | QA 測試、自動化 |
| 互動 | 實時觀看 AI 操作 | ~100ms 快速執行 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/browse]] — headless browser 自動化
