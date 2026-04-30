---
kind: article
title: "Harness Engineering 核心概念：模型是脑子，Harness 是生产力系统"
source: https://www.perplexity.ai/search/harness-UVbZYGcJRh6Js1B1BbqfyA
tags: [harness-engineering, agent, concept, feedback-loop, constraints]
---

# Harness Engineering 核心概念

> **来源**: Perplexity AI Search  
> **标签**: Harness Engineering 核心概念

## 一句话定义

> **模型是"脑子"，harness 是围绕模型的一整套「约束 + 工具 + 状态 + 反馈闭环」，把不稳定的智能变成可控的生产力系统。**

---

## 1. Agent = Model + Harness

很多文章直接给出公式：

```
Agent = Model + Harness
```

意思是"光有大模型不叫 agent，要加上一整套 harness 才能变成可工作的智能体"。

Harness 包括代码、配置、执行逻辑中所有"不是模型本身"的部分：
- 状态管理
- 工具调用
- 权限
- 安全策略
- 日志
- 恢复机制

---

## 2. Harness 的职责：在"意图"和"行动"之间挡刀

Harness 是管理 LLM 生命周期的完整架构系统，从：

```
意图捕获 → 规格化 → 执行 → 验证 → 持久化
```

覆盖除了模型本身之外的所有东西。

它在每一步做四件事：

| 动作 | 说明 |
|------|------|
| **拦截** | 阻止不合规的输出 |
| **校验** | 验证是否符合约束 |
| **路由** | 决定下一步走向 |
| **记录** | 沉淀每一步的结果 |

防止模型"乱出手"，并把每一步的结果沉淀下来，方便长期运行和审计。

---

## 3. 三层核心：约束、反馈环、质量闸门

### 第一层：约束（Constraints）

在生成前就收紧搜索空间：
- 规则文件
- 架构 linter
- 类型系统
- 权限边界

目标是"减少错误空间"，而不是限制创造力，让 agent 更快收敛到合规方案。

### 第二层：反馈环（Feedback Loops）

让系统自动发现错误并把信息反馈给 agent 或 harness 自身：
- 自动测试
- Shadow evaluation
- 观察数据驱动的回路

关键点：**验证要快且自动化**，才能支撑高频迭代，而不靠人力 code review 扛住所有风险。

### 第三层：质量闸门（Quality Gates）

在关键节点设置"闸门"：不满足某些质量/安全条件就不能进入下一步（比如合并 PR、推生产）。

这些闸门通常用以下组合实现：
- 确定性规则
- 自动测试
- 审批流程

---

## 4. Harness 是"环境设计学"，不是"更花哨的 prompt"

**Prompt / context** 更像"怎么跟模型说话"。

**Harness** 更像"给它一个什么样的世界去工作，包括规则、工具、信息和后果"。

Harness Engineering 的重点不是堆复杂基础设施，而是做成一个**简单但闭环的系统**：

```
约束 → 告知 → 验证 → 纠正
```

---

## 5. 长期项目中的"记忆 + 多 Agent 调度层"

在长期任务里，harness 还承担"项目大脑"的角色：

- **初始化时**：搭好环境和任务清单
- **每次 session**：只做一小步
- **写回**：把进度写回文件、仓库和日志
- **下一个 session/agent**：无需记住全部历史也能接着干

对**多 Agent 系统**，harness 作为**中枢调度**：
- 接收高层目标
- 把子任务分发给不同专长的 agent（写代码、写文档、评审、安全审计等）
- 在上层控制整体节奏和质量
