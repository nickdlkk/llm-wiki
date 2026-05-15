---
title: gstack /document-release
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/document-release/SKILL.md"]
tags: [gstack, documentation, post-ship, changelog, diataxis]
---

# /document-release

Post-ship documentation update. Reads all project docs, cross-references the diff, builds a Diataxis coverage map (reference/how-to/tutorial/explanation), updates README/ARCHITECTURE/CONTRIBUTING/CLAUDE.md to match what shipped, detects architecture diagram drift, polishes CHANGELOG voice with a sell-test rubric, cleans up TODOs, and optionally bumps VERSION.

## 核心職責

**何時觸發**：
- 用戶說 "update the docs"、"sync documentation"、"post-ship docs"
- 主動建議：PR 合併或代碼交付後

**工作內容**：
1. 讀取所有項目文檔
2. 交叉引用 diff
3. 構建 Diataxis coverage map
4. 更新 README/ARCHITECTURE/CONTRIBUTING/CLAUDE.md
5. 檢測架構圖漂移
6. 用 sell-test rubric 潤色 CHANGELOG
7. 清理 TODOs
8. 可選：bump VERSION

**輸出**：
- PR body 中的文檔債務列表

## 與 /document-generate 的關係

| 維度 | /document-release | /document-generate |
|------|-------------------|-------------------|
| 觸發 | Post-ship | 按需 |
| 輸入 | Diff + 現有文檔 | Feature/模塊 |
| 重點 | 同步更新 | 從零生成 |
| 調用 | - | 發現 coverage gaps 時 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/document-generate]] — 生成缺失文檔
- [[concepts/gstack/ship]] — 觸發 post-ship 文檔更新
