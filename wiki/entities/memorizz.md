---
title: Memorizz
type: entity
created: 2026-05-12
updated: 2026-05-12
sources:
- https://github.com/RichmondAlake/memorizz
tags:
- memory-systems
- python
- agent-framework
---

# Memorizz

> RichmondAlake/memorizz · 723 ⭐ · Python · PolyForm Noncommercial 1.0.0

MemoRizz 是一个 Python 记忆层框架，为 AI 应用提供多种记忆系统和可插拔存储后端。定位为**实验性/教育性**框架，API 可能变更，未经过生产级安全加固。

## 核心架构

**记忆类型（10种）：**

| 类型 | 用途 |
|------|------|
| `LONG_TERM_MEMORY` | 语义知识库事实 |
| `ENTITY_MEMORY` | 结构化实体属性（profile-style facts） |
| `TOOLBOX` | 可执行工具和元数据 |
| `WORKFLOW_MEMORY` | 流程/任务状态 |
| `CONVERSATION_MEMORY` | 对话时间线 |
| `SUMMARIES` | 压缩对话摘要 |
| `SHORT_TERM_MEMORY` | 活跃上下文便签 |
| `SEMANTIC_CACHE` | 相似查询响应缓存 |
| `SHARED_MEMORY` | 多 Agent 黑板/会话协调 |
| `MEMAGENT` | Agent 配置/状态持久化 |

**存储后端（3种，均实现 MemoryProvider 接口）：**
- **Filesystem** — 本地文件系统 + FAISS 向量检索（`pip install "memorizz[filesystem]"`）
- **Oracle** — Oracle Database 23ai（`pip install "memorizz[oracle]"`）
- **MongoDB** — MongoDB（`pip install "memorizz[mongodb]"`）

存储抽象层使得切换后端只需更换 provider，Agent 代码无需改动。

## 应用模式（ApplicationMode）

预设记忆栈组合：

| 模式 | 默认启用记忆类型 |
|------|----------------|
| `assistant` | conversation, long-term, personas, entity, short-term, summaries |
| `workflow` | workflow, toolbox, long-term, short-term, summaries |
| `deep_research` | toolbox, shared, long-term, short-term, summaries |

## 关键能力

- **Semantic Cache** — 相似查询命中缓存，减少重复 LLM 调用（threshold 可配置）
- **Entity Memory** — `entity_memory_lookup` / `entity_memory_upsert` 工具，Profile 风格事实存取
- **Context Window Telemetry** — `get_context_window_stats()` 观测 token 使用
- **Multi-Agent Orchestration** — `DeepResearchWorkflow` + `SharedMemory` 黑板协调
- **Sandbox Code Execution** — 支持 E2B、Daytona、GraalPy 沙箱
- **Automation / Scheduling** — Cron/Interval/One-shot 定时任务，结果存对话记忆，可选 WhatsApp 投递
- **Internet Access** — Deep Research Agent 可挂载 Tavily 等搜索 provider

## 与同类对比

| 特性 | Memorizz | Honcho | Hindsight | Supermemory | RetainDB | ByteRover | OpenViking |
|------|----------|--------|-----------|-------------|----------|-----------|------------|
| 多存储后端 | ✅ Oracle/Mongo/FS | ❌ | ❌ | ❌ | ✅ Postgres/pgvector | ❌ | ❌ |
| 多记忆类型 | ✅ 10种 | ✅ | ✅ | ✅ | ✅ 关系图 | ✅ | ✅ |
| Semantic Cache | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Automation | ✅ Cron/WhatsApp | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Multi-Agent | ✅ Shared Memory | ❌ | ❌ | ✅ | ✅ MCP Server | ❌ | ✅ |
| License | PolyForm Noncommercial | ? | ? | ? | Apache 2.0/BSL | ? | AGPLv3 |
| 生产可用 | ❌ 实验性 | ? | ✅ | ✅ | ✅ | ? | ✅ |

## 局限性

- **License 限制** — PolyForm Noncommercial 1.0.0，明确禁止商业使用
- **实验性软件** — README 明确声明 APIs may change，未经生产安全加固
- **Oracle 依赖** — 完整功能需要 Oracle Database 23ai，部署门槛高
- **非 Hermes 原生** — 无 Hermes Agent 深度集成（对比 Honcho）

## 适用场景

适合**学习和原型验证**场景：快速搭建多记忆类型 Agent、理解记忆抽象层设计。生产环境慎用（License + 实验性双重风险）。
