# 3層架構打造個人AI大腦：從Raw Data到持久知識庫

**來源：** https://www.youtube.com/watch?v=kcim-tG-KHLE
**頻道：** HC AI說人話
**觀看：** 2,378
**時長：** 1997秒 (~33分鐘)
**語言：** 中文（繁體）
**字幕：** 繁體中文（人工字幕，可下載）

---

## 核心主題

基於 Andrej Karpathy 的 X 發文（GitHub Gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f）

### 三層架構

```
┌─────────────────────────────────────┐
│  第三層：應用層                       │
│  查詢、回答、任務執行                 │
├─────────────────────────────────────┤
│  第二層：LLM Wiki                    │
│  AI 維護的持久化 Markdown 維基        │
│  結構化且相互連結的總結層              │
├─────────────────────────────────────┤
│  第一層：Raw Data                    │
│  原始資料來源（PDF/網頁/文獻等）       │
└─────────────────────────────────────┘
```

### Karpathy 原文核心觀點

> 「與其只在查詢時從原始文件檢索（RAG 那樣），LLM 應逐步建立並維護一個持久的 Wiki——一個結構化、相互連結的 Markdown 文件集合，位於我們和原始資料之間。」

---

## RAG vs LLM Wiki 對比

| 維度 | RAG | LLM Wiki |
|------|-----|---------|
| 查詢方式 | 每次重新檢索 | 直接查 Wiki（已維護） |
| 結果穩定性 | 因 embedding 不同有差異 | 每次結果一致 |
| 跨文件引用 | 準度下降 | 精準（引用同一個 Wiki） |
| 知識累積 | 無（每次重新拼湊） | 有（持久化累積） |
| 平台綁定 | 有（綁定某 embedding model） | 無（純文字檔，任何模型可用） |
| 即時資訊更新 | 需重新 embedding | 可標記到 Wiki 中 |

---

## 重點內容

### 1. 為什麼需要 LLM Wiki

傳統人類用資料夾整理檔案的方式（分類、命名）在有 AI 後可以改變。AI 能在短時間內從大量檔案中查找所需知識，人機協作下檔案整理方式可以更有效率。

### 2. RAG 的缺點

- 每次問問題都要重新檢索
- 向量之間沒有語意理解
- 複雜多文件問題每次都要重新拼湊
- 跨文件引用準度會下降
- 標記知識困難（新增/剔除知識複雜）
- 有平台綁定和 model 綁定問題

### 3. LLM Wiki 的優勢

- **持久性**：知識持續累積，不會每次重新來過
- **穩定性**：每次查詢結果一致（同一 Wiki）
- **精準跨文件引用**：直接引用 Wiki 內容
- **無平台綁定**：純文字檔，任何 LLM 都能用（OpenAI / Anthropic / MiniMax / 千問等）
- **知識標記**：新知識直接更新到 Wiki（如：A 發生了食安危機，標記到 Wiki）

### 4. 實際應用案例：交易策略知識庫

- 用 Claude Code + NotebookLM Skill
- 5 個 Background Agent 並行閱讀 ~69 個 sources
- 自動建立 Entity / Concept / Reference 頁面
- 存入 Obsidian 本地維基
- Demo：讓 Agent 基於 wiki 回答「10萬美金做蘋果股票交易，如何制定策略」

**有 wiki vs 沒 wiki 的差別：**
- 沒 wiki：每次答案都不一樣
- 有 wiki：答案穩定、框架完整、有憑有據

### 5. 旅行知識庫應用

- Agent 定時更新最新資訊
- 手機隨時詢問行程安排
- 有 Raw Source 可查證

### 6. Obsidian 的角色

- 本地維基管理工具
- 外掛功能豐富
- 適合搭配 AI 做第二大腦

---

## 相關連結

- [Andrej Karpathy X 發文](https://x.com/karpathy/status/2039805659525644595)
- [Karpathy GitHub Gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [HC AI LLM Wiki Skills](https://ultrclick.gumroad.com/l/LLMWikiSkills)
- [Obsidian 下載](https://obsidian.md/)
- [MiniMax Coding Plan 10% off](https://platform.minimax.io/subscribe/token-plan?code=LqabaQygTS&source=link)
