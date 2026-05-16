---
title: "Harness Engineering 核心概念"
type: summary
created: 2026-04-21
updated: 2026-04-21
sources:
  - raw/articles/harness-core-concept-perplexity-20260421.md
tags:
  - harness-engineering
  - agent
  - concept
  - feedback-loop
  - constraints
---

# Harness Engineering 核心概念

> **来源**: Perplexity AI Search · 2026-04-21  
> **一句话定义**: 模型是"脑子"，harness 是围绕模型的一整套「约束 + 工具 + 状态 + 反馈闭环」，把不稳定的智能变成可控的生产力系统。

## 公式

```
Agent = Model + Harness
```

Harness = 状态管理 + 工具调用 + 权限 + 安全策略 + 日志 + 恢复机制（所有不是模型本身的部分）

## 核心职责

在"意图"和"行动"之间拦截、校验、路由、记录——防止模型乱出手，并沉淀每步结果。

## 三层架构

| 层级 | 作用 | 例子 |
|------|------|------|
| **约束 Constraints** | 收紧搜索空间，减少错误空间 | 架构 linter、类型系统、权限边界 |
| **反馈环 Feedback Loops** | 自动发现错误并反馈给 agent | 自动测试、shadow evaluation |
| **质量闸门 Quality Gates** | 关键节点卡住不合规的输出 | PR 合并前检查、自动测试 + 审批 |

## 关键区分

- **Prompt / Context** = 怎么跟模型说话
- **Harness** = 给它一个什么样的世界去工作（规则、工具、信息、后果）

核心模式：**约束 → 告知 → 验证 → 纠正**（简单但闭环）

## 长期 + 多 Agent

- **"项目大脑"角色**：初始化环境 + 任务清单 → 每次 session 做一小步 → 写回文件/仓库/日志 → 下一个 agent 无需记忆历史也能接着干
- **中枢调度**：harness 接收高层目标 → 分发给专长 agent → 控制整体节奏和质量
