---
title: Harness Engineering
type: concept
created: 2026-04-17
updated: 2026-08-06
sources:
  - https://www.youtube.com/watch?v=3DlXq9nsQOE
  - https://openai.com/index/harness-engineering/
  - https://www.anthropic.com/engineering/managed-agents
  - https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA
tags:
  - engineering
  - llm-ops
  - coding-agents
  - paradigm-shift
  - deterministic-shell
---

# Harness Engineering

> **AI 工程領域的第三次重心遷移**
> Prompt Engineering → Context Engineering → **Harness Engineering**

## 核心定義

**Harness Engineering** 是 AI 編碼 Agent 領域提出的工程師角色，專注於：
- 構建、開發和維護能指導 AI Agent 行為的軟體系統
- 設計反饋迴圈、記憶架構、工具生態
- 讓 AI Agent 在複雜任務中可靠地執行

類似傳統軟體工程師讓程序可靠執行的方式，Harness Engineer 讓 AI Agent 可靠執行。

## 視頻來源

- **標題**：最近爆火的 Harness Engineering 到底是个啥？
- **頻道**：code秘密花园
- **鏈接**：https://www.youtube.com/watch?v=3DlXq9nsQOE
- **觀看**：145,803 次

## 三次重心遷移

| 階段 | 時間 | 核心問題 | 關鍵技能 |
|------|------|----------|----------|
| Prompt Engineering | 早期 | 如何讓模型聽話？ | 提示詞編寫 |
| Context Engineering | 中期 | 如何給模型最好的上下文？ | RAG、上下文壓縮、prompt engineering |
| Harness Engineering | 現在 | 如何讓 AI Agent **靠譜地工作**？ | 軟體工程 + AI 協同 |

## 與 LLM Wiki 的關係

LLM Wiki 是 Harness Engineering 的基礎設施之一：
- **記憶架構**：LLM Wiki 是 Agent 的持久化知識庫
- **工具生態**：Wiki 本身是一種工具，幫助組織和檢索知識
- **反饋迴圈**：Wiki 的內容持續更新，來自 Agent 的工作產出

從這個角度，構建 LLM Wiki 也是 Harness Engineering 的一種實踐。

## 與 Spec-Driven Development 的關係

Harness Engineering 和 SDD 共享同一個目標：讓 AI Agent 的行為更可靠、可預測、可維護。

但切入角度不同：
- **SDD**：從「規格文檔」切入，用 spec 來約束和引導 Agent
- **Harness Engineering**：從「工程系統」切入，用軟體架構來支撐 Agent

兩者是互補的：SDD 產出 spec，Harness Engineering 構建執行 spec 的基礎設施。

## OpenAI 正式定義（2026）

[OpenAI Harness Engineering](https://openai.com/index/harness-engineering/) 不再只優化模型「看到什麼」，而是讓工程師**設計環境、表達意圖、建設反饋環路**，使 Agent 能可靠工作 ^[lencx 2026](https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA)。

## Anthropic Managed Agents 拆分

[Anthropic Managed Agents](https://www.anthropic.com/engineering/managed-agents) 給出更接近系統實現的拆分 ^[lencx 2026^]：

- **Session** = 追加寫的事件日誌
- **Harness** = 調用模型並路由工具的循環
- **Sandbox** = 執行代碼與文件動作的環境

## 與上下文/Loop/Graph/Skill 的關係

> Context 是每次採樣的視野，Loop 是時間軸，Graph 是任務拓撲，Skill 是可加載的程序性模組；**Harness 持有目標**，並把它們組織成一次可恢復執行。 ^[lencx 2026](https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA)

## 刪減型 Harness（Claude 5 復盤）

[Claude 5 context engineering 復盤](https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models) 披露：Claude Code 為 Opus 5 / Fable 5 刪掉 **>80%** system prompt，coding eval 無可測量損失。每條長期指令都應有退出條件；**模型升級時 Harness 應刪除過時腳手架** ^[lencx 2026^]。

## 延伸閱讀

- [[LLM Wiki]] — 持久化知識庫，Harness Engineering 的記憶層
- [[Spec-Driven Development]] — 另一種讓 AI Agent 靠譜工作的方法論
- [[Coding Agents]] — 這個概念的主要應用場景
- [[Agent Native Stack]] — lencx 2026 蒸馏的 11 节框架，含本文相关的完整推理
- [lencx 蒸馏全文](summaries/harness-engineering/lencx-agent-dev-guide-20260806.md)
