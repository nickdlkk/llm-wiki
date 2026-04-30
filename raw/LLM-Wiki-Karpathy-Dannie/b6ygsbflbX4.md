# Karpathy LLM Wiki 快速上手：5分鐘用 Obsidian 搭起來

**來源：** https://www.youtube.com/watch?v=b6ygsbflbX4
**頻道：** Dannie（頻道名未知）
**觀看：** 6,795
**時長：** 344秒 (~5分44秒)
**語言：** 中文（繁體）

---

## 核心主題

Karpathy 的 LLM Wiki 方法——把所有資料交給 AI，自動整理成相互連結的 Wiki 知識庫。

**相關連結：**
- Karpathy 原始 GitHub 筆記：https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- 上一期｜怎麼把 Claude 裝進 Obsidian（Claudian 設定教學）：https://www.youtube.com/watch?v=ohPBYGN8a9Y

---

## 章節

- 0:00 Intro
- 0:34 為什麼整個 AI 圈都在討論這件事
- 1:09 這個方法的核心邏輯
- 2:33 實際操作：在 Obsidian 裡搭給你看
- 3:45 讓知識庫越來越聰明：Hot Cache 技巧
- 4:23 這樣會讓知識庫「AI 化」嗎？
- 5:23 結語

---

## 重點內容

### 1. 為什麼這個方法爆火

收藏夾裡存了幾百篇文章、幾百個視頻，但基本是「進收藏夾裡吃灰」。用 Obsidian + Web Clipper 已經在做的事，只是還差最後一步——自己的個人知識庫索引系統。

**一個直觀的案例：** x 用戶把 383 個散亂文件 + 100 多個會議記錄，整理成 Wiki 之後，用 AI 查東西 Token 消耗直接降了 **95%**。

### 2. 核心邏輯：讓 AI 當圖書館管理員

Karpathy 的方法是讓 AI 來當「圖書館管理員」——不僅幫你堆整齊，還會：
- 給每篇文章寫一句摘要
- 把相關內容相互連結在一起
- 建立一個索引文件叫 `index`（或 `index.md`）

### 3. Index 是什麼、為什麼關鍵

假設知識庫裡有幾十萬文字，直接丟給 AI 問問題，需要把所有文章從頭讀一遍，Token 消耗恐怖而且很慢。

有了 Index，AI 只需要先掃一眼每篇文章的一句摘要，判斷哪幾篇跟問題有關，再精準地跳進去讀。

> **Index 不是給人看的，是給 AI 看的。**

### 4. 實際操作（Claudian + Obsidian）

步驟：
1. Web Clipper 設定 → 把默認文件夾改成 `raw`
2. 在 Claudian（Claude in Obsidian）裡粘貼 Karpathy 的方法描述
3. AI 自動處理，在 Graph 圖譜裡生成節點和連線
4. 提問時 AI 會告訴你答案 + 從哪幾篇文章找到的

### 5. Hot Cache 技巧

知識庫用久了會越來越大，AI 每次要從頭爬整個 Wiki，會越來越慢。

解決方法：建立一個 `hot.md` 文件
- 只放 ~500 字
- 記錄：最近發生了什麼、最新加進來的內容、最近問過什麼問題/結論是什麼
- AI 每次先讀這個文件，大多數情況下 500 字就夠定位到答案
- 相當於「知識庫最近使用記錄」

### 6. 來自 Obsidian 創始人的提醒

Obsidian 創始人也有參與 Karpathy 的討論，提出一個值得思考的問題：

> 「如果你的知識庫裡全是 AI 幫你整理的內容，那這到底是你的知識庫還是 AI 的？」

——久而久之你的知識庫會變得很「AI」，全是 AI 的邏輯，不是你的邏輯。

**解決方法（Obsidian 創始人提出）：** 把自己個人的 vault 和 AI 創建的 vault 分開。

**Dannie 的個人解法：** 在每篇筆記的屬性裡加一個 `origin` 字段：
- `self written`：自己寫的想法、判斷、感受
- `agent compiled`：AI 幫忙整理的摘要、分析、素材

核心原則：
> 「我希望 AI 是我的圖書館管理員，但書是我選的，筆記也是我寫的。」

---

## 相關連結

- [Karpathy 原始 GitHub 筆記](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [上一期｜Claudian 設定教學](https://www.youtube.com/watch?v=ohPBYGN8a9Y)
