---
title: MemRL
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/memtensor-memrl.md
tags:
  - memory
  - skill
  - open-source
  - optimization
confidence: high
---
# MemRL（MemTensor）

> **164 ⭐ / MIT**。论文《MEMRL: Self-Evolving Agents via **Runtime Reinforcement Learning on Episodic Memory**》的开源实现。出自 [[MemOS]] 同一团队。

## 核心思想

把 **episodic memory 当作 RL 的作用对象**：不微调模型权重，而是在**运行时**用强化学习优化记忆的使用策略。

这在 [[Skill-Distillation-Research]] 的谱系里属于「memory-skill 联合演化」分支——与 [[MemSkill]] 同类，区别是 MemRL 明确用 RL 框架。

## 为什么值得关注

多数 session-mining 工具的提取策略是**固定的**（写死的 prompt + 阈值）。MemRL 这条路线让「**记什么、怎么用**」本身成为可学习的策略——对应 [[Session-Extraction-Pipeline]] 里「skill 提取门槛怎么定」这个开放问题的学术答案。

## 相关页面

- [[MemOS]] — 同团队的记忆 OS
- [[MemSkill]] — 同类方法
- [[Skill-Distillation-Research]] — 学术层总览
