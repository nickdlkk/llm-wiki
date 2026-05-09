---
title: CodeFlow
type: entity
created: 2026-04-17
updated: 2026-04-17
sources:
  - https://github.com/braedonsaunders/codeflow
tags:
  - code-visualization
  - dependency-analysis
  - security-scanning
  - browser-tool
  - github-integration
---

# CodeFlow

**代碼庫架構可視化工具**。粘貼 GitHub URL，秒級生成交互式架構圖。零安裝、零構建、純瀏覽器運行。

## 核心定位

CodeFlow 解決的是「拿到新代碼庫一臉懵」的問題。它是一個**單 HTML 文件**（4718 行 JSX/JS），通過 CDN 加載所有依賴，聲稱「粘貼 URL → 立即看到架構」。

## 技術架構

```
CodeFlow (index.html)
├── React 18        — UI 組件層（CDN）
├── D3.js 7         — 依賴圖可視化
├── D3-Sankey       — 流量/層級圖
├── Babel 7         — 瀏覽器端 JSX 編譯
├── tree-sitter WASM — 多語言語法解析（35+ 語言）
├── Acorn           — JavaScript 輕量解析
├── jsrsasign       — JWT/Token 處理
└── GitHub API      — 直接從瀏覽器調用
```

**零後端、零數據收集**。代碼只存在於瀏覽器內，GitHub API 調用從瀏覽器直連，token 只存瀏覽器內存。

## 核心功能

### 依賴圖可視化
- D3 力導向圖展示文件級依賴關係
- 點擊任意節點高亮其傳入/傳出依賴
- 支持拖拽、縮放

### Blast Radius 分析
- 選擇任意文件，立即顯示「改動這個文件會影響哪些模塊」
- 基於函數級調用關係追蹤影響範圍

### 安全掃描
- 硬編碼 API Key / Secret 檢測
- SQL 注入漏洞模式識別
- `eval()` 危險使用檢測
- Debug Statement 生產環境殘留檢測

### 設計模式識別
- Singleton / Factory / Observer 模式
- React Custom Hooks 識別
- 反模式檢測：God Object、高耦合模塊

### 健康評分
- A-F 等級：死代碼比例、循環依賴、耦合度、安全問題
- 彩色熱力圖：按提交頻率定位熱點文件

### PR 影響分析
- 粘貼 PR URL，分析該 PR 影響了哪些文件
- 計算變更的 blast radius

### 代碼所有權
- 根據 Git History 計算每個文件的 Top Contributors
- 適用於 Code Review 和找人場景

### 本地文件分析
- 離線運行，無需上傳
- 拖拽文件/文件夾到瀏覽器
- 隱私敏感項目適用

## 視圖模式

| 模式 | 說明 |
|------|------|
| Folder | 按目錄結構著色 |
| Layer | 按架構層（UI/Service/Utils）著色 |
| Churn | 按提交頻率著色（熱點定位） |
| Blast | 按選定文件的影響範圍著色 |

## 支持語言（35+）

JavaScript/TypeScript/JSX/TSX、Python、Java、Go、Rust、C/C++/C#、Swift、Kotlin、Scala、Ruby、PHP、Vue、Svelte、Elixir、Erlang、Haskell、Lua、R、Julia、Dart、Perl、Shell、PowerShell、F#、OCaml、Clojure、Elm、VBA 等。

## 局限性

- **依賴分析基於函數名匹配**，無法處理動態 import / rename import，精度有限
- **每個文件單獨發請求到 GitHub API**，大倉庫速度慢（建議加 PAT）
- **無後端持久化**，分析結果不保存
- **tree-sitter WASM 解析**比原生 parser 慢，首次加載約 2-3 秒

## 與 Hermes 集成

CodeFlow 是純瀏覽器工具，理論上可通過以下方式為 Agent 提供代碼庫概覽：
1. **Agent 爬取目標倉庫** → 整理文件結構 → 粘貼到 CodeFlow
2. **本地模式**：Agent 把需要分析的代碼片段 dump 成文件 → 用 CodeFlow 本地模式分析
3. **PR Impact**：CodeFlow 支持粘貼 PR URL，Agent 可在 Code Review 時快速評估變更範圍

但**不是 Harness Engineering 核心工具**，更偏向人類開發者的快速探索工具。

## 與 SDD 工具對比

| 維度 | CodeFlow | architecture-diagram-generator |
|------|----------|-------------------------------|
| 用途 | 現有代碼庫探索 | 生成新架構圖 |
| 輸出 | 動態圖 + 指標 | 靜態 SVG/HTML |
| 部署 | 瀏覽器打開即用 | Claude Code Skill |
| 隱私 | 瀏覽器直連 GitHub | 本地運行 |
| 核心價值 | 快速理解陌生代碼庫 | 清晰表達設計意圖 |

## 總結

CodeFlow 解決的是**「拿到陌生代碼庫如何快速上手」**的問題。作為 Agent，**它更像是給人類開發者用的瑞士軍刀**，而不是 SDD 流程中的核心組件。如果要讓 Agent 可靠地「理解並表達」一個代碼庫的架構，仍需結合代碼解析 + 文檔生成的工具鏈。

**Stars**: _（未獲取，見 GitHub）_
**License**: MIT
**作者**: braedonsaunders
**地址**: https://github.com/braedonsaunders/codeflow
