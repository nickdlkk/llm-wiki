---
title: EverOS
type: entity
created: 2026-05-31
updated: 2026-05-31
sources:
  - https://github.com/EverMind-AI/EverOS
  - https://evermind.ai
  - https://docs.evermind.ai
tags:
  - memory
  - agent
  - long-term-memory
  - coding-agent
  - benchmark
  - self-evolving
---

# EverOS

**EverOS** 是 **EverMind-AI** 开源的 AI Agent 长期记忆操作系统，核心产品是 **EverCore**（⭐6,299，640 forks）。定位：构建、评估和整合自进化 Agent 的长期记忆。

## 核心定位

**EverCore** = AI Agent 的长期记忆操作系统。对话输入 → 自动提取 MemCell 原子记忆 → 多层分类存储 → 灵活检索。

解决的问题：LLM 在多会话间无状态，RAG 无法捕获用户偏好和会话演化。

## 两大认知-track

### 1. Memory Construction（记忆构建）

```
对话输入 → MemCell 提取 → 记忆类型分类 → MongoDB 存储 → 索引（ES + Milvus）
```

- **MemCell**：最小记忆单元，一次提取一个原子信息
- **记忆类型**：Episodes（主题会话）/ Profiles（用户画像）/ Preferences（偏好）/ Relationships（关系）/ Semantic Knowledge / Core Memories

### 2. Memory Perception（记忆感知/检索）

两种检索模式：

| 模式 | 原理 |
|------|------|
| **Lightweight** | BM25（ES）+ 向量语义（Milvus）+ RRF 融合 |
| **Agentic** | LLM 扩展查询 → 多轮检索 → 智能融合 |

## 六层架构

```
Agentic Layer    ← 顶层：协调提取/检索/重排
Memory Layer     ← MemCell 提取 + 分类
Retrieval Layer  ← 向量 + 关键词 + RRF融合
Business Layer   ← 业务逻辑 + API
Infra Layer      ← MongoDB/Redis/ES/Milvus 适配
Core Framework   ← DI + 生命周期 + 中间件
```

技术栈：**FastAPI + Python 3.10+ + MongoDB 7.0 + Redis 7.x + Elasticsearch 8.x + Milvus 2.4+ + Docker**

## 组件结构

| 组件 | 位置 | 说明 |
|------|------|------|
| **EverCore** | `methods/EverCore/` | 核心记忆操作系统 |
| **HyperMem** | `methods/HyperMem/` | 超图记忆架构 |
| **EverMemBench** | `benchmarks/EverMemBench/` | 记忆质量评估基准 |
| **EvoAgentBench** | `benchmarks/EvoAgentBench/` | Agent 自进化能力评估 |

## 基准测试

EverOS 内置两大 Benchmark：

- **EverMemBench**：评估记忆质量（提取准确率、检索召回、记忆持久性）
- **EvoAgentBench**：测量 Agent 自我改进能力（闭环评估）

> 对比：Hindsight 无 Benchmark；OpenViking 仅靠字节内部评估；Letta 无公开基准。

## 生态集成

Use Cases 覆盖 30+ 场景：

- **AI Coding Assistants**：Claude Code Plugin、OpenClaw Agent Memory、EverMem Sync
- **多 Agent 协作**：Hive Orchestrator、MCO、Memori
- **可穿戴/助手**：Rokid Glasses、Mobi Companion、AI Wearable
- **游戏 NPC**：Memory-Driven NPC、Live2D Character with Memory
- **专业工具**：MemoCare（阿尔茨海默症助手）、AI Data Technician、Study Buddy

## 与 Hindsight 核心差异

| 维度 | EverOS | Hindsight |
|------|--------|-----------|
| **定位** | 通用记忆操作系统（多场景） | Agent 会话内记忆管理 |
| **记忆单元** | MemCell（原子提取） | Structured facts（实体关系图） |
| **存储** | MongoDB + Redis + ES + Milvus | SQLite（FTS5） |
| **检索** | 向量 + BM25 + RRF + LLM Agentic | 语义检索 + FTS + 图遍历 |
| **Benchmark** | EverMemBench + EvoAgentBench | 无 |
| **外部依赖** | 6个组件（高门槛） | 仅 SQLite（低门槛） |
| **MCP 支持** | 是 | 否 |
| **Stars** | 6,299 | — |

**EverOS 本质上是 Hindsight 的企业级放大版**：检索层多了 Milvus + ES + RRF + LLM 扩展，记忆层多了多类型分类，Benchmark 形成闭环评估体系。

## 相关链接

- GitHub: https://github.com/EverMind-AI/EverOS
- 官网: https://evermind.ai
- 文档: https://docs.evermind.ai