---
title: gstack /benchmark-models
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/benchmark-models/SKILL.md"]
tags: [gstack, model-comparison, benchmark, cross-model, evaluation]
---

# /benchmark-models

Cross-model benchmark for gstack skills. Runs the same prompt through Claude, GPT (via Codex CLI), and Gemini side-by-side — compares latency, tokens, cost, and optionally quality via LLM judge. Answers "which model is actually best for this skill?" with data instead of vibes.

## 核心職責

**何時觸發**：
- 用戶說 "benchmark models"、"compare models"、"which model is best for X"、"cross-model comparison"、"model shootout"
- 主動建議在選擇模型或優化成本時

**測量維度**：
- Latency（延遲）
- Tokens（token 消耗）
- Cost（成本）
- Quality（質量，通過 LLM judge）

## 與 /benchmark 的區別

| 維度 | /benchmark | /benchmark-models |
|------|-----------|------------------|
| 測量對象 | 網頁性能 | AI 模型表現 |
| 用途 | 性能回歸檢測 | 模型橫向對比 |
| 工具 | Browse daemon、Lighthouse | Codex CLI、LLM Judge |

## 支援模型

- Claude（Anthropic）
- GPT（OpenAI，透過 Codex CLI）
- Gemini（Google）

## Voice Triggers

- "compare models"
- "model shootout"
- "which model is best"

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/codex]] — OpenAI Codex wrapper（用於 GPT 測試）
