---
title: gstack /document-generate
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/document-generate/SKILL.md"]
tags: [gstack, documentation, diataxis, tutorial, how-to]
---

# /document-generate

Generate missing documentation from scratch for a feature, module, or entire project. Uses the Diataxis framework (tutorial / how-to / reference / explanation) to produce complete, structured documentation.

## 核心職責

**何時觸發**：
- 用戶說 "write docs"、"generate documentation"、"document this feature"、"create a tutorial"、"explain this module"

**Diataxis 框架**：
- **Tutorial**（教程）：學習型文檔
- **How-to**（操作指南）：任務型文檔
- **Reference**（參考）：技術型文檔
- **Explanation**（解釋）：理解型文檔

## 可單獨使用或被調用

/document-generate 可被 /document-release 調用——當 /document-release 發現 coverage gaps 時會調用它來填補。

## 與 /document-release 的區別

| 維度 | /document-generate | /document-release |
|------|-------------------|------------------|
| 觸發 | 按需生成缺失文檔 | Post-ship 更新文檔 |
| 輸入 | Feature/模塊 | Diff + 現有文檔 |
| 重點 | 從零生成 | 同步更新 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/document-release]] — Post-ship 文檔更新
