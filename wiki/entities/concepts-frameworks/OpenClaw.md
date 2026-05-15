---
title: "OpenClaw"
type: entity
created: 2026-05-03
updated: 2026-05-03
sources:
  - "[[raw/articles/luo-fuli-3h-interview-zhangxiaojun-2026]]"
tags: [开源, Agent框架, Skills, Claude, 小米]
---

# OpenClaw

## 概述

开源 Agent 框架，由罗福莉定义为"划时代"。核心逻辑是通过复杂的系统编排（分层记忆体系）弥补模型能力的不足，而非单纯依赖模型本身。

## 关键特性

| 特性 | 说明 |
|---|---|
| **System MD** | 每轮 Context 前拼当前时间，让模型感知时间，有"温度" |
| **分层记忆** | Memory 分层分级，比 Claude Code 更持久 |
| **多模型编排** | 自动调度最好的模型处理不同任务 |
| **可改源码** | 开源，可以改 Memory 系统和整个 Agent 架构 |
| **Skill Hub** | 社区贡献 Skills，激发创造力 |

## 与 Claude Code 的关键区别

- Claude Code = For 软件工程（黑盒，改不了）
- OpenClaw = For 端到端所有任务（开源可改）

## 罗福莉三天认知转变

1. **Day 1**：凌晨2点装上用到6点，感受到"有灵魂"——会提醒睡觉
2. **Day 2**：变成数字分身——团队管理、筛选人才用它
3. **Day 3**：两小时做出 User Agent——用于 SFT/RL 数据生成

## 生态影响

- 大量开发者共同改进，框架以小时计迭代
- 已被 Claude Code 吸纳部分设计（持久化记忆等）
- 激发了中层模型的上限（85% 场景达到 Sonnet 水准）

## 相关页面

- [[entities/罗福莉]]
- [[concepts/Agent框架/index|Agent框架]]
