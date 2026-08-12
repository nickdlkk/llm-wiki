---
title: ccflash
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - https://github.com/polyphilz/ccflash
  - raw/articles/polyphilz-ccflash.md
tags:
  - memory
  - session-extraction
  - anki
  - flashcards
  - claude-code
---

# ccflash

**ccflash** 是 [polyphilz](https://github.com/polyphilz) 出的 Claude Code skill（7 ⭐，2026-08）。**核心理念：把完整 chat session 提取为关键概念 → 生成 atomic Anki flashcards**。**走 spaced repetition 路线**而不是 wiki/skill 路线。

## 核心能力

- 读取完整 session
- 提取**关键概念**（key concepts）
- 生成 **atomic**（最小独立单位）Anki 卡片
- 推送到 Anki deck

## 范式对比

| 项目 | 输出目标 | 范式 |
|------|---------|------|
| [[ccflash]] | **Anki 卡片**（个人 SRS）| Spaced repetition |
| [[reflect-skill-claude]] | skill 文件 + CLAUDE.md | Agent instruction |
| [[cc-analyst]] | patch 提议 | Agent instruction |
| [[Open-Amnesia|Open Amnesia]] | 日期 Markdown + DB | Knowledge base |

> 这是一条**完全不同的方向**——把 session 喂给人脑的 SRS 而非 agent 上下文。适合个人学习，不适合做 agent 记忆。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式
- [[reflect-skill-claude]] — 同形式（单文件 skill）
