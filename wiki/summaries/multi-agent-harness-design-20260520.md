---
title: "从零设计生产级 Multi-Agent Harness：架构、评估、记忆、成本与 MCP 工具接入全拆解"
type: summary
created: 2026-05-20
updated: 2026-05-20
sources:
  - https://mp.weixin.qq.com/s/JPhcyDc4JwRmnMQ-76A-FQ
  - https://www.bestblogs.dev/article/878057b5?entry=email_weekly_item
tags:
  - harness-engineering
  - multi-agent
  - agent-orchestration
  - tool-registry
  - memory-system
  - evaluation
  - cost-control
  - mcp
  - production
author: 李伟山
organization: 腾讯云开发者
summary: 一篇系统讲解生产级 Multi-Agent Harness 五大核心模块的长文：架构编排（Orchestrator 独占五项决策权）、工具治理（Tool Registry 九项元信息）、状态与记忆（分层记忆 + 遗忘机制）、评估体系（四层 Eval Pipeline）、成本控制（Token Budget 实时调度）。核心观点：Agent 负责局部智能，Harness 负责全局控制；工具不是函数调用而是生产资源授权点；记忆不是仓库而是花园需要定期修剪。
---

# 从零设计生产级 Multi-Agent Harness

**一句话总结：** 生产级 Multi-Agent Harness 的核心竞争力不在于"拼了多少个 Agent"，而在于 Harness 本身对编排、治理、记忆、评估、成本的全局控制能力。

## 核心观点

### 1. Harness 的本质定位

| 类比 | 角色 |
|------|------|
| Prompt | 台词 |
| Agent | 演员 |
| 工具 | 道具 |
| 模型 | 大脑 |
| **Harness** | **导演 + 灯光 + 调度 + 安全规章 + 票务** |

Harness 不是 Prompt 拼盘，不是 Orchestrator（只解决顺序），也不是 LangGraph/AutoGen（只是积木）。它是把积木拼成生产建筑的**工程方案**。

### 2. 架构编排：Agent 负责局部智能，Harness 拿决定

**根本原则：** 大模型本质上不是可靠的调度器——它没有成本意识、并发意识、权限意识、全局一致性意识。

Orchestrator 必须独占五项决策权：
1. **任务生命周期** — 明确状态机（创建→规划→执行→审查→完成/失败）
2. **执行计划裁决** — 计划可来自 Planner，但必须由 Orchestrator 接管
3. **Agent 路由** — 结合任务类型、Agent 能力、权限、历史质量评分
4. **失败处理** — 重试/降级/跳过/终止不能由出错 Agent 自己决定
5. **硬终止条件** — max_steps、max_tokens、max_duration、max_tool_calls 四道硬闸

**Planner 应输出声明式计划而非命令式调用：**
```json
{ "step": 1, "intent": "research", "agent": "researcher", "input": "..." }
```
声明式的好处：Harness 可以重排顺序、并行优化、拒绝步骤、做安全审查。

### 3. 工具治理：Tool Registry 是安全边界

工具不是函数调用，是**生产资源的对外授权点**。每个工具至少登记九项元信息：

| 元信息 | 说明 |
|--------|------|
| 工具名称 | 唯一标识 |
| 工具描述 | 给 LLM 看的说明 |
| 输入参数 JSON Schema | 用于校验 |
| 允许调用的 Agent 列表 | RBAC |
| 调用超时与速率限制 | — |
| 风险等级 | 低/中/高 |
| 是否需要人工确认 | — |
| 输出结果结构 | — |
| 审计日志策略 | 保存什么、保留多久、谁能看 |

**关键思维转变：** 工具不是函数调用，而是生产资源的对外授权点。哪怕只有 3 个工具，也从第一天起强制走 Tool Registry。

### 4. 状态与记忆：让 Agent 记住该记的，忘掉该忘的

记忆的四种失败方式：
- **记得太少** — 每次像第一次，无法复用经验
- **记得太多** — 上下文膨胀，成本爆炸
- **不分层** — 临时数据和长期知识混在一起
- **不遗忘** — 过期信息长期污染决策

**状态分三层：**
- Working State — 当前步骤临时上下文，任务结束即丢
- Session State — 一次会话多个 Agent 共享，放 Redis，设 TTL
- Execution Log — 不可变执行日志，用于审计、回放、评估

**记忆分两类：**
- Episodic Memory — 踩过的坑、用户偏好、处理经验
- Semantic Memory — 领域概念、业务规则、工具约束

**两个被低估的设计点：**
1. **注入时机（Retrieval Timing）** — 生产推荐混合模式：前置注入少量高置信记忆 + 提供 `memory_search` 工具供 Agent 主动调用
2. **遗忘机制（Memory Forgetting）** — 基于访问频次、创建时间、重要性、最近使用计算保留分数：低分→删除，中分→压缩摘要，高分→保留原文

> 记忆不是仓库，而是花园。需要定期修剪。

### 5. 评估体系：不要只看答案，要看轨迹

**只看最终答案会漏掉的危险信号：**
- 最终报告对了，但中间用了未授权的数据源
- 最终代码能跑，但 Agent 调用了十几次无意义工具
- 最终回答完整，但关键事实来自错误检索
- 某次结果成功，只是因为重试撞上了正确答案

**四层 Eval Pipeline：**

| 层级 | 评估内容 |
|------|---------|
| Component Eval | 单 Agent 是否选对工具、参数是否合规 |
| Trajectory Eval | 步骤是否必要、顺序是否合理、是否陷入循环 |
| Task Completion Eval | 是否满足用户目标、是否覆盖必要信息 |
| End-to-End Eval | 用户采纳率、人工返工率、处理时长、单位任务成本 |

**Eval 必须进入 CI：** Prompt 就是代码，工具 Schema 就是接口，执行轨迹就是日志，Eval 就是测试体系。

### 6. 成本控制：Token Budget 是生命线

**三个核心策略：**

**策略一：Model Routing**
- 分类、摘要、格式转换 → 小模型
- 复杂推理、最终合成 → 强模型
- 高风险审查 → 强模型 + 规则校验
- 低价值重试 → 禁止高价模型

**策略二：Context Compression**
- 保留最近几轮原文
- 更早历史压缩成结构化摘要（关键事实、决策、未解决问题、引用）

**策略三：Budget 分级降级**
| 区域 | 预算剩余 | 策略 |
|------|---------|------|
| 绿区 | >50% | 正常执行 |
| 黄区 | 20%-50% | 压缩上下文 |
| 红区 | 5%-20% | 切小模型 + 跳过 CoT |
| 熔断区 | <5% | 强制收束，返回 partial result |

### 7. MCP 工具接入：标准化是趋势，但不能裸奔

**MCP 的核心价值：**
- 快速扩展能力（文件系统、数据库、Git、Slack、Jira、内部 API）
- 生态可复用
- 解耦工具与模型

**五条最佳实践：**
1. 永远不要把 MCP Server 直接暴露给 Agent，必须经过 Tool Registry
2. 给每个 MCP Server 单独配额
3. 对工具做白名单而非黑名单
4. 高风险工具一律走 Human-in-the-Loop
5. 所有 MCP 调用都要打 Trace

> MCP 让工具接入变得便宜，Harness 让工具调用变得可信。两者必须搭配。

### 8. 落地路线：分三阶段演进

| 阶段 | 目标 | 重点 |
|------|------|------|
| Phase 1 — MVP | 跑通一条端到端业务闭环 | 最小 Orchestrator + Tool Registry + 简单状态 + 基础 Trace + 评估数据集 |
| Phase 2 — Hardening | Demo 变成可控系统 | Budget、权限、重试、压缩、轨迹评估、审计、回归测试 |
| Phase 3 — Scale | 支撑更多场景与并发 | 分布式队列、多租户隔离、动态模型路由、Agent 质量排行榜、A/B 测试 |

**技术选型建议：**
- 小团队：LangGraph 或自研轻量状态机 + FastAPI + Redis + PostgreSQL/pgvector + Langfuse/OpenTelemetry + LiteLLM 网关
- 企业团队：更重视权限、审计、多租户、成本中心、数据治理。MCP 作为接入标准，但不允许直连 Agent

---

## 关键语录

> 没有 Harness，Multi-Agent 只是热闹；有了 Harness，Agent 才可能成为生产力。

> Agent 负责局部智能，Harness 负责全局控制。

> 工具不是函数调用，而是生产资源的对外授权点。

> 记忆不是仓库，而是花园。需要定期修剪。

> MCP 让工具接入变得便宜，Harness 让工具调用变得可信。

## 相关概念

- [[harness-core-concept-20260421]] — Harness Engineering 核心概念
- [[claude-code-agentic-harness-20260421]] — Claude Code 的 Agentic Harness 架构
- [[openai-harness-engineering-codex-20260211]] — OpenAI Harness Engineering with Codex
- [[ruflo]] — Multi-agent AI orchestration（多 Agent 队友协作平台）
- [[Multica]] — 开源多 Agent 队友平台（pgvector 知识积累）
