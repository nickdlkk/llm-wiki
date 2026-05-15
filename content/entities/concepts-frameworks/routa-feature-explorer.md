---
title: Routa Feature Explorer — 从 Session 历史重建需求全景
date: 2026-04-20
tags:
  - AI-Coding
  - Spec-Driven
  - Routa
  - Phodal
  - Engineering-Workflow
---

# Routa Feature Explorer — 从 Session 历史重建需求全景

> 原文：[从写清 Spec 到看懂功能：在 Session 历史中使用 Routa 重建需求全景](https://www.phodal.com/blog/routa-feature-explorer/) by Phodal

## 核心问题

当 AI Coding 速度被 Agent 拉高之后，Spec 仍然负责定义需求入口（边界、约束、验收条件），但**真正的实现过程已经散落到 Session 历史里**：

- 某次失败的尝试
- 某个被临时收掉的交互
- 一次 API 结构调整
- 一组被反复读写的文件
- 某个最终没有进入正式实现的方向

这些问题导致：第二天回看功能时，最难回答的不是"做完没有"，而是**"这段演化是怎么发生的"**。

## Spec 与 Session 的分工

| 层次 | 职责 | 局限 |
|------|------|------|
| **Spec** | 定义起点：需求入口、边界、约束、验收条件 | 只保留干净的最终版本，不保存演化过程 |
| **Session** | 记录过程：读过的文件、跑过的命令、阶段性判断、本轮结论 | 按"某次会话"组织，不是按"某个功能"组织 |

Session 天然按会话组织，而非按产品能力组织。追踪一个 Feature 的长期演化时，Session 是不够用的。

## Feature Explorer 的解法

不是 Session 管理页面，而是**先把恢复单位从"某次会话"切换成"当前功能涉及的一组页面、API 和文件"**。

两层设计：

**第一层（系统）：上下文整理**
- 确定当前功能的边界（页面 + API + 文件）
- 把相关 Session 历史收拢成证据
- 把历史从"能保存"推进到"能归因"再到"能检查"

**第二层（Agent）：分析推理**
- Agent 不做第一层整理，系统先完成
- Agent 站在第二层：解释证据说明了什么，下一轮该怎么继续

## Feature Explorer 的三层结构

### 1. 确定当前功能边界
系统返回一组相近的数据结构（Next.js + Rust/Axum 两端），围绕 Feature 收拢：
- 涉及的页面
- 涉及的 API
- 涉及的文件

### 2. 接口层归并
- 分析 API 的变更历史
- 追踪参数和返回结构的演化
- 把散落在多个 Session 里的 API 调整串联起来

### 3. 历史证据收拢
把与当前功能有关的读写、修改、尝试、失败和收敛过程重新组织成一份可检查的证据：
- 哪些文件被持续触达
- 哪些操作被反复尝试
- 哪些问题反复出现
- 哪些改动最终真正沉淀在功能上

## 与 Vibe Kanban 的对比

| 维度 | Vibe Kanban | Feature Explorer |
|------|-------------|-----------------|
| **解决的问题** | 多 Agent 并行调度，人成为瓶颈 | Spec 之外的演化过程丢失 |
| **组织单位** | Issue / Task | Feature（页面 + API + 文件） |
| **核心机制** | Kanban 看板 + Git Worktree | Session 历史收拢 + 功能边界恢复 |
| **Agent 角色** | 执行者（Plan Mode 追问） | 第二层分析者（基于证据推理） |

两者解决的是 AI Coding 工作流的不同环节：Vibe Kanban 管**执行侧并发**，Feature Explorer 管**需求侧回溯**。

## 核心结论

> 功能演化现场还在，只是它不再集中保存在最初的 Spec 文档里。Feature Explorer 解决的是：让系统先围绕当前功能把两层历史重新组织成可操作的入口，再交给 Agent 做分析推理。
