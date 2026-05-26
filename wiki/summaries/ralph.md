---
title: ralph
type: summary
created: 2026-05-26
updated: 2026-05-26
sources: ["raw/refs/ralph/README.md"]
tags: ["autonomous-loop", "agent-loop", "prd-driven"]
---

# Summary — snarktank/ralph

自主 Agent 循环：每轮新 AI 实例 + 干净上下文，靠 prd.json / progress.txt / Git 历史跨迭代持久化，直到 PRD 全部完成。

## 核心要点

1. **新鲜上下文防污染**：每轮迭代是全新 AI 实例，避免前几轮错误上下文累积
2. **三文件记忆**：prd.json（任务状态）+ progress.txt（学习笔记）+ Git 历史（代码变更）
3. **PRD 驱动**：先生成 PRD → 转 JSON → 循环执行，粒度控制在单个上下文窗口内
4. **反馈闭环**：类型检查 + 测试必须过才提交，AGENTS.md 每轮更新

## 与 wiki 其他实体的关联

- [[entities/concepts-frameworks/ralphy]] — 增强版 CLI，8 种引擎 + 并行
- [[entities/concepts-frameworks/ralph-claude-code]] — 早期实现
- [[concepts/Harness Engineering]] — 分解 + 隔离理念的实践

## 相关链接

- GitHub: https://github.com/snarktank/ralph
- 灵感来源: https://ghuntley.com/ralph/
