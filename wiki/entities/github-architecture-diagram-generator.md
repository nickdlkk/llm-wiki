---
title: Architecture Diagram Generator
type: entity
created: 2026-04-17
updated: 2026-04-17
sources:
  - https://github.com/Cocoon-AI/architecture-diagram-generator
tags:
  - claude-skill
  - diagrams
  - svg
  - visualization
  - claude-ai
---

# Architecture Diagram Generator

> AI 生成專業架構圖的工具，輸出為獨立的暗色調 HTML 文件（內嵌 SVG）
> MIT License · Cocoon AI

## 概述

用戶描述系統架構（純文字），Claude 生成一個精美的暗色調架構圖 HTML 文件。

- **輸入**：文字描述架構（前端、後端、數據庫、雲服務等）
- **輸出**：單個 HTML 文件，無需任何軟體即可在瀏覽器打開
- **風格**：Slate-950 背景 + 網格圖案 + JetBrains Mono 字體

## 與 LLM Wiki 的關係

LLM Wiki 的實體頁（如架構複雜的系統）可以用這個 skill 生成視覺化架構圖。具體場景：
- 在 entity 頁頂部嵌入該系統的架構圖
- 用於生成 Hermes Gateway、OpenClaw 等項目的架構圖

## 語義色彩系統

| 組件類型 | 填充色 | 邊框色 |
|----------|--------|--------|
| Frontend | Cyan `#22d3ee` | rgba(8, 51, 68, 0.4) |
| Backend | Emerald `#34d399` | rgba(6, 78, 59, 0.4) |
| Database | Violet `#a78bfa` | rgba(76, 29, 149, 0.4) |
| AWS/Cloud | Amber `#fbbf24` | rgba(120, 53, 15, 0.3) |
| Security | Rose `#fb7185` | rgba(136, 19, 55, 0.4) |
| Message Bus | Orange `#fb923c` | rgba(251, 146, 60, 0.3) |
| External | Slate `#94a3b8` | rgba(30, 41, 59, 0.5) |

## 輸出結構

每個生成的 HTML 文件包含：
1. **Header** — 標題 + 動畫狀態指示點
2. **Main SVG diagram** — 所有組件和連接箭頭
3. **Summary cards** — 3 張信息卡片
4. **Footer** — 項目元數據

## 使用方式

1. 在 Claude.ai 安裝 `architecture-diagram.zip` Skill
2. 描述架構：`Create an architecture diagram for...`
3. Claude 生成 HTML 文件 → 瀏覽器直接打開

## 關鍵設計細節

- **箭頭 z-order**：先繪製連接箭頭，再繪製組件盒子（SVG 文檔順序）
- **遮罩技巧**：半透明填充上蓋一層 opaque rect 來遮住背後的箭頭
- **間距規則**：組件豎向間距 ≥ 40px，Message Bus 放在間距正中央
- **圖例位置**：必須放在所有邊界框外部

## 連結

- GitHub：https://github.com/Cocoon-AI/architecture-diagram-generator
