---
title: LLM Wiki
type: concept
created: 2026-04-17
updated: 2026-04-17
sources:
  - https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
  - https://www.youtube.com/watch?v=kcim-tGKHLE
  - https://www.youtube.com/watch?v=b6ygsbflbX4
tags:
  - knowledge-base
  - wiki
  - memory
  - rag
---

# LLM Wiki

> 讓 AI 幫你維護一個持久、可累積、會自己生長的個人知識庫

## 核心命題

**RAG 每次查詢都要重新檢索；LLM Wiki 讓 AI 預先整理好知識，下次查詢直接回答。**

Karpathy 的方法：把大量 Raw Data（文章、論文、會議記錄）交給 AI，自動整理成一個**結構化且相互連結的 Markdown Wiki**，位於用戶和原始資料之間。

效果：383 個散亂文件整理成 Wiki 後，用 AI 查詢的 Token 消耗**降了 95%**。

---

## 三層架構

```
┌─────────────────────────────────────┐
│  第三層：應用層                       │
│  查詢、回答、任務執行                 │
├─────────────────────────────────────┤
│  第二層：LLM Wiki（持久層）           │
│  AI 維護的 Markdown 維基             │
│  結構化 · 相互連結 · 摘要層           │
├─────────────────────────────────────┤
│  第一層：Raw Data                    │
│  原始資料（PDF/網頁/文獻/Notebook）   │
└─────────────────────────────────────┘
```

**與 RAG 的關鍵區別：**

| 維度 | RAG | LLM Wiki |
|------|-----|---------|
| 查詢方式 | 每次重新檢索 | 直接查 Wiki（已預先維護） |
| 結果穩定性 | 因 embedding 質量有差異 | 每次結果一致 |
| 跨文件引用 | 準度下降 | 精準（引用同一 Wiki） |
| 知識累積 | 無 | 有（持久化累積） |
| 平台綁定 | 有 | 無（純文字檔，任何模型可用） |

---

## 實作要點

### Index（索引）是給 AI 看的，不是給人看的

Index 是**整個系統最關鍵的層**。假設知識庫有幾十萬字，直接丟給 AI 問問題要把所有文章從頭讀一遍——昂貴且慢。

AI 每次先掃一眼每篇文章的一句摘要，判斷哪幾篇跟問題有關，再精準跳進去讀。這就是 Index 的作用。

> **Index 不是給人看的目錄，是給 AI 看的「一一句話摘要索引」。**

### Hot Cache：讓查詢越來越快

知識庫用久了會越來越大，AI 每次從頭爬整個 Wiki 會越來越慢。

**Hot Cache 技巧：**
- 建立一個 `hot.md` 文件
- 只放約 **500 字**
- 記錄：最近發生了什麼、最新加進來的內容、最近問過什麼問題 / 結論是什麼
- AI 每次先讀這個文件，多數情況下 500 字就夠定位到答案
- 相當於「知識庫的最近使用記錄」

### 知識溯源：避免「AI 化」

Obsidian 創始人提出的警示：知識庫全是 AI 整理的內容，久而久之會全是 AI 的邏輯，失去你自己的聲音。

**解決方法：每篇筆記加 `origin` 字段**

```
origin: self written    # 自己寫的想法、判斷、感受
origin: agent compiled  # AI 幫忙整理的摘要、分析、素材
```

> 「我希望 AI 是我的圖書館管理員，但**書是我選的，筆記也是我寫的**。」

---

## 實作工具鏈

- **Obsidian** + **Claudian（Claude in Obsidian）**：本地維基管理
- **NotebookLM**：自動抓取 Raw Source，建立 Entity / Concept / Reference
- **Claude Code**：用 Background Agent 並行閱讀 sources，自動建立 Wiki 頁面
- **Web Clipper**：把網頁文章存進 `raw/` 目錄

---

## 與 Harness Engineering 的關係

LLM Wiki 是 Harness Engineering 的**記憶層**。

Harness Engineering 解決的是「單次會話中 Agent 可靠工作」的問題；LLM Wiki 解決的是「跨會話、跨時間尺度知識持久化」的問題。

| 層次 | Harness Engineering | LLM Wiki |
|------|---------------------|---------|
| 單次會話 | `progress.md` 追蹤狀態 | Wiki 層持久累積知識 |
| 知識來源 | `progress.md` + 代碼 | Raw Data（文章/文獻/視頻） |
| 查詢方式 | 從 `progress.md` 恢復上下文 | 從 Wiki 層直接回答 |
| 更新頻率 | 每個功能完成後 | 持續迭代、有新資料時 |

---

## 參與討論

- **Karpathy 原始 GitHub Gist**：https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- **HC AI 說人話（33分鐘深入版）**：https://www.youtube.com/watch?v=kcim-tGKHLE
- **Dannie（5分鐘快速上手）**：https://www.youtube.com/watch?v=b6ygsbflbX4

## 參見

- [[Harness Engineering]] — 讓 Agent 可靠工作的五大子系统
- [[Three Engineering Primitives]] — 隔離 / 分解 / 協調
