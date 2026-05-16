---
title: "Agent框架"
type: concept
created: 2026-05-03
updated: 2026-05-03
sources:
  - "[[raw/articles/luo-fuli-3h-interview-zhangxiaojun-2026]]"
tags: [Agent, 框架, OpenClaw, Claude_Code]
---

# Agent框架

## 定义

Agent 框架是位于**人和模型之间的"厚中间层"**，负责：
- 定义交互层
- 定义怎么跟模型沟通
- 感知模型能力的长板短板
- 成本优化调度

前端 UI 是最薄的一层，已经不关键了。

## OpenClaw vs Claude Code

| | Claude Code | OpenClaw |
|---|---|---|
| 设计目标 | For 软件工程 | For 端到端所有任务 |
| 源码 | 黑盒 | **开源可改** |
| 记忆系统 | Session级压缩 | **分层分级持久化** |
| 多模型协作 | 需手动配 | **自动调度** |
| 适用场景 | 严肃编程 | 非严肃编程、日常生活、研究 |

**核心机制**：
- **System MD**：每轮 Context 前拼当前时间，让模型感知时间
- **分层记忆**：Memory 分层分级，比 Claude Code 更持久
- **多模型编排**：自动找最好的模型处理不同任务

## 框架弥补模型短板

> "一个非常复杂的 Agent 框架设计，能够弥补非常多模型能力的短板。"
> — 罗福莉

中层模型 + OpenClaw 框架 → 在 85% 场景达到 Cloud Sonnet 水准。

## 关键设计原则

1. **Context 编排**：精心设计的 Context 输入比模型本身更重要
2. **可操控性**：开源框架可以被改（Memory 系统、Agent 架构）
3. **群体智能**：开源激发社区创造力，想象力是沉积的
4. **自学习**：模型进步的同时，Agent 框架也在同步演进

## 相关页面

- [[entities/OpenClaw]]
- [[entities/罗福莉]]
