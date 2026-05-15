---
title: gstack /office-hours
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/office-hours/SKILL.md"]
tags: [gstack, product-planning, yc, forcing-questions]
---

# /office-hours

YC Office Hours skill — 两个模式：Startup mode（6 个强制问题暴露需求现实） + Builder mode（设计思维头脑风暴）。保存设计文档。在写任何代码之前使用。

## 核心职责

**何时触发**：
- 用户说 "brainstorm this"、"I have an idea"、"help me think through"、"office hours"、"is this worth building"
- 用户描述新产品想法、问某事是否值得做、探索还不存在的设计决策

**使用顺序**：`/office-hours` → `/plan-ceo-review` 或 `/plan-eng-review` 之前运行

## 6 个强制问题（Startup Mode）

每个问题都是强制门（forcing function），暴露需求现实：

| # | 问题类型 | 目的 |
|---|---------|------|
| 1 | **需求现实** | 用户是否真正知道目标用户是谁？ |
| 2 | **Status Quo** | 为什么用户现在不用竞品？ |
| 3 | **极度具体** | 想法是否足够窄/具体？ |
| 4 | **最窄楔子** | 能否找到最小的可验证切入点？ |
| 5 | **观察** | 用户见过真实用户使用吗？ |
| 6 | **未来适配** | 产品方向在大规模时还能保持吗？ |

## Builder Mode

面向 side projects、hackathons、学习、开源的头脑风暴模式。不强制 6 个问题，而是：
- 设计思维发散
- 快速原型探索
- 生成 `*-design.md` 设计文档

## 与 /plan-ceo-review 的区别

| 维度 | /office-hours | /plan-ceo-review |
|------|--------------|-----------------|
| 目的 | 重新审视产品方向 | 找到 10 星产品 |
| 时机 | 想法验证早期 | office-hours 后深入 |
| 输出 | 6 个问题的答案 | 4 种模式决策（Expansion/SelExp/Hold/Reduction） |

## 关键设计

- **Proactive 触发**：skill 会主动介入，不回答直接问题，而是引导用户完成 6 个问题
- **GBrain 集成**：从 `~/.gstack/analytics/eureka.jsonl` 提取 prior eureka moments
- **学习文件**：`~/.gstack/projects/{repo}/learnings.jsonl` 记录决策历史

## 相关技能

- [[concepts/gstack/plan-ceo-review]] — CEO 层评审，找到 10 星产品
- [[concepts/gstack/autoplan]] — CEO → Design → Eng 审查链
- [[entities/gstack]] — gstack 完整 entity 入口
