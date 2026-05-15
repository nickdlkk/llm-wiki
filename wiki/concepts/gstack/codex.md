---
title: gstack /codex
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/codex/SKILL.md"]
tags: [gstack, openai, codex, second-opinion, adversarial-testing]
---

# /codex

OpenAI Codex CLI wrapper — three modes. Code review: independent diff review via codex review with pass/fail gate. Challenge: adversarial mode that tries to break your code. Consult: ask codex anything with session continuity for follow-ups. The "200 IQ autistic developer" second opinion.

## 核心職責

**何時觸發**：
- 用戶說 "codex review"、"second opinion"、"outside voice challenge"、"ask codex"
- 主動建議：需要第二意見、對抗性測試、獨立審查

**三種模式**：

### 1. Review Mode
獨立 Diff 審查，附帶 pass/fail gate。用於代碼審查的客觀第二意見。

### 2. Challenge Mode
對抗性模式，嘗試打破你的代碼。用於發現邊界條件和潛在漏洞。

### 3. Consult Mode
向 Codex 提問，支援 session continuity 進行 follow-ups。用於 Consult 任何問題。

## Voice Triggers

- "code x"
- "code ex"
- "get another opinion"

## 與 /review 的區別

| 維度 | /codex | /review |
|------|--------|---------|
| 視角 | OpenAI Codex（外部） | gstack 內部審查 |
| 重點 | 對抗性、challenge | 結構性 bug、N+1、安全 |
| 獨立性 | 外部模型視角 | 內部工具視角 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/review]] — 內部代碼審查
- [[concepts/gstack/benchmark-models]] — 模型橫向對比（使用 Codex CLI）
