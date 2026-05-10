---
title: AI Agent 记忆系统对比
tags:
  - memory
  - comparison
  - benchmarks
---

# AI Agent 记忆系统对比

主流 AI Agent 记忆系统一览（2026-05 更新）。对比维度包括：核心定位、基准成绩、部署方式、技术栈、集成生态。

## 总览表

| 系统 | 定位 | ★Stars | 许可 | 架构 |
|------|------|--------|------|------|
| [[Supermemory]] | 通用记忆引擎（#1 基准）| ~22k | MIT | Vector + Semantic |
| [[Mem0]] | 通用记忆层（行业标杆）| 55k | Apache 2.0 | Vector + Graph (hybrid) |
| [[Letta]] | LLM-as-OS（模型自管理记忆）| 22k | Apache 2.0 | Block-based self-editing |
| [[Graphiti]] | 时序知识图谱 | 26k | Apache 2.0 | Temporal KG (Neo4j) |
| [[Cognee]] | 认知 pipeline（企业级）| 17k | Apache 2.0 | Graph + Vector + Cognitive |
| [[Memori]] | 行为记忆（不只是对话）| 14k | Apache 2.0 | SQL-native |
| [[MemMachine]] | 通用记忆适配器 | 3k | Apache 2.0 | Client-server multi-backend |
| [[Honcho]] | Hermes 专用记忆层 | — | — | Neuromancer XR |
| [[Hindsight]] | 向量记忆存储 | — | MIT | Multi-strategy hybrid |
| [[Holographic]] | 全息记忆范式 | — | — | — |
| [[RetainDB]] | Postgres 自托管记忆 | 13 | Apache 2.0/BSL | pgvector + KG |
| [[ByteRover]] | 编程 Agent 记忆 CLI | 5k | Elastic 2.0 | Context tree + vector |
| [[OpenViking]] | 上下文数据库 | 23k | AGPLv3 | L0/L1/L2 分层 |
| [[Claude-Mem]] | Claude Code 插件 | 74 | Apache 2.0 | 会话压缩 |

## 基准详细对比

| 系统 | LoCoMo | LongMemEval | ConvoMem | 备注 |
|------|--------|-------------|----------|------|
| **Supermemory** | #1 | #1 | #1 | 三大基准全部第一 |
| **ByteRover** | **96.1%** | 92.8% | — | 编程场景专精 |
| **Honcho** | 86.9% | — | — | Neuromancer XR 模型 |
| **OpenViking** | ~85% | — | — | 字节内部评估 |
| **Letta** | 竞争级 | — | — | MemGPT 基础 |
| **Graphiti** | — | 94.8% | — | Deep Memory Retrieval |

> 注：LoCoMo（Stanford）测试 ~20K tokens、35 会话的多轮对话记忆。LongMemEval-S 测试知识更新、时序推理、多会话综合能力。ConvoMem 测试会话上下文记忆。

## 核心维度对比

### 1. 部署方式

| 系统 | 完全自托管 | 云优先 | Agent 专用 |
|------|-----------|--------|-----------|
| Supermemory | ❌（数据在云）| ✅ | ❌（通用）|
| Mem0 | ✅（Docker）| ✅ | ❌（通用）|
| Letta | ✅（SQLite/PG）| ✅ | ❌ |
| Graphiti | ✅（Neo4j）| ✅ | ❌ |
| Cognee | ✅ | ✅ | ❌ |
| Memori | ✅ | ✅ | ❌ |
| MemMachine | ✅（Docker）| ✅ | ❌ |
| Honcho | ✅ | ✅ | ✅（Hermes）|
| Hindsight | ✅ | ❌ | ❌ |
| RetainDB | ✅ | ❌ | ❌ |
| ByteRover | 部分 | ✅ | ✅（编程）|
| OpenViking | ✅ | ✅ | ❌ |
| Claude-Mem | ✅ | ❌ | ✅（Claude Code）|

### 2. 存储技术

| 系统 | 向量数据库 | 图结构 | 时序建模 | SQL 原生 |
|------|-----------|--------|---------|---------|
| Supermemory | ✅ | ✅（Memory Graph）| ✅ | ❌ |
| Mem0 | ✅ | ✅（Graph KG）| ❌ | ❌ |
| Letta | ❌ | ❌ | ❌ | ❌（block-based）|
| Graphiti | ✅ | ✅（Temporal KG）| ✅ 核心特性 | ❌ |
| Cognee | ✅ | ✅（六阶段）| 扩展支持 | ❌ |
| Memori | ❌ | ✅（结构化）| 行为跟踪 | ✅ 核心特性 |
| MemMachine | ✅ | ❌ | ❌ | ✅（Profile）|
| Honcho | — | — | ✅ | — |
| RetainDB | ✅（pgvector）| ✅（关系图）| ✅ | ❌ |
| ByteRover | ✅ | ✅（上下文树）| ✅ | ❌ |
| OpenViking | ✅（多后端）| ✅（Viking URI）| ✅ | ❌ |
| Claude-Mem | — | — | ✅ | — |

### 3. 集成生态

| 系统 | MCP | Vercel AI | LangChain | Claude | Hermes |
|------|-----|-----------|---------|--------|--------|
| Supermemory | ✅ | ✅ | ✅ | ✅ | ✅ |
| Mem0 | ✅ | ✅ | ✅ | ❌ | ❌ |
| Letta | ✅ | ❌ | ❌ | ❌ | ❌ |
| Graphiti | ✅ | ❌ | ❌ | ✅ | ❌ |
| Cognee | ❌ | ✅ | ✅ | ❌ | ❌ |
| Memori | ❌ | ✅ | ✅ | ❌ | ❌ |
| MemMachine | ❌ | ❌ | ❌ | ❌ | ❌ |
| Honcho | — | — | — | — | ✅ |
| RetainDB | ✅ | ✅ | ✅ | ✅ | — |
| ByteRover | ✅ | — | — | — | — |
| OpenViking | ✅ | — | — | ✅ | ❌ |
| Claude-Mem | — | — | — | ✅ | — |

### 4. 检索能力

| 系统 | 语义搜索 | BM25 | 混合检索 | Rerank | 分层摘要 |
|------|---------|------|---------|--------|---------|
| Supermemory | ✅ | ✅ | ✅ | ✅ | ✅ |
| Mem0 | ✅ | — | ✅ | ✅ | ✅ |
| Letta | — | — | — | — | — |
| Graphiti | ✅ | ✅ | ✅（语义+KG）| ❌ | ❌ |
| Cognee | ✅ | — | ✅ | ✅ | ✅ |
| RetainDB | ✅ | ✅ | ✅ | ✅ | — |
| ByteRover | ✅ | — | — | — | ✅ |
| OpenViking | ✅ | ✅ | ✅ | ✅ | ✅（L0/L1/L2）|

## 记忆哲学对比

| 哲学 | 代表系统 | 核心观点 |
|------|---------|---------|
| **LLM-as-OS** | Letta | 模型自己管理记忆，像人一样决定记什么 |
| **Temporal KG** | Graphiti | 记忆是带时间戳的事件流，事实随时间变化 |
| **Cognitive Pipeline** | Cognee | 记忆需要通过认知阶段提取，不能直接灌入 |
| **Action-based** | Memori | 记忆来自 Agent 行为，不只是对话文本 |
| **SQL-native** | Memori, MemMachine | 向量搜索不够，结构化 SQL 才是长期记忆的归宿 |
| **Hybrid Universal** | Mem0, Supermemory | 向量 + 图谱 + 摘要，全都要 |

## 选型指南

### 选 [[Supermemory]] 当：
- 需要在多个基准上追求 SOTA
- 需要多 Agent 共享记忆
- 需要丰富的 SDK 和连接器生态（MCP、Vercel AI）

### 选 [[Mem0]] 当：
- 需要最成熟、社区最大的开源记忆系统（55k stars）
- 需要混合向量 + 图谱检索
- 接受云端管理或 Docker 自托管

### 选 [[Letta]] 当：
- 想要让 LLM 自己管理记忆（LLM-as-OS 范式）
- 需要构建自主 agent，有自我改进能力
- 研究 agent 架构，不介意每次记忆操作消耗 token

### 选 [[Graphiti]] 当：
- 在时间敏感领域（客服、医疗、法律、金融）
- 需要追踪事实如何随时间变化
- 需要 SOC 2 / HIPAA 企业合规

### 选 [[Cognee]] 当：
- 企业场景，需要处理大量异构数据源（38+ 连接器）
- 需要六阶段认知 pipeline 确保记忆质量
- 已有复杂的数据基础设施，需要 memory control plane

### 选 [[Memori]] 当：
- Agent 行为本身也是记忆（不只是对话）
- 偏好 SQL 原生架构，不想引入向量数据库
- 需要跨 agent 共享行为模式

### 选 [[MemMachine]] 当：
- 需要通用记忆适配器，支持多种后端存储
- 需要 SQL 结构化的 Profile Memory
- 偏好 client-server 架构分离记忆逻辑

### 选 [[Honcho]] 当：
- 使用 Hermes Agent
- 需要 Neuromancer 专用推理模型
- 数据主权要求高（自托管）

### 选 [[Hindsight]] 当：
- 只需要向量存储作为记忆后端
- 愿意自己处理 LLM 提取逻辑
- Nick 当前已部署的方案

### 选 [[RetainDB]] 当：
- 需要完全自主的 Postgres 基础设施
- 需要显式建模记忆关系（contradicts/updates/supports）
- 不希望依赖任何第三方服务

### 选 [[ByteRover]] 当：
- 主要场景是 AI 编程（Cursor/Windsurf/Claude Code）
- 需要 Git 式版本控制管理记忆演进
- 需要 CLI 优先的工作流

### 选 [[OpenViking]] 当：
- 需要多租户、团队级记忆管理
- 需要代码仓库的深层语义理解（L0/L1/L2 分层）
- 在字节/火山引擎生态内

### 选 [[Claude-Mem]] 当：
- 只使用 Claude Code
- 只需要简单的会话压缩记忆
- 不需要跨 Agent 或跨工具的互操作性

## 架构哲学差异

### 全息派（Holographic 理念）
记忆不是孤立的原子事实，而是分布式编码——每个记忆片段都包含与其他片段的关系信息。检索时通过"相干性检测"重建上下文。

代表：OpenViking 的 L0/L1/L2 分层（每个层级都包含整体信息）、ByteRover 的上下文树。

### 原子派（Atomic Memory）
记忆是最小独立的原子事实，具有明确的类型、置信度和有效期。事实之间通过显式关系图连接。

代表：Supermemory、RetainDB、Honcho、Graphiti（时间维度）。

### LLM 自管理派
不给 LLM 检索结果，而是让 LLM 决定自己记什么、什么时候归档、什么时候检索。像人类管理自己的笔记本。

代表：Letta。

### 两者并非互斥
现代系统往往结合两者：底层用原子事实存储，上层用分层摘要和关系图增强检索效果。

## 相关页面

### 新增系统（2026-05）
- [[Mem0]] — 55k stars，行业标杆，hybrid vector + graph
- [[Letta]] — LLM-as-OS，模型自管理记忆
- [[Graphiti]] — Zep 时序知识图谱，SOC 2 + HIPAA
- [[Cognee]] — 企业级六阶段 cognitive pipeline
- [[Memori]] — 行为记忆，SQL-native，action-based
- [[MemMachine]] — 通用记忆适配器，client-server 架构

### 原有系统
- [[Supermemory]] — 基准第一的记忆系统
- [[Honcho]] — Hermes 专用记忆层
- [[Hindsight]] — Nick 当前部署的本地记忆方案
- [[OpenViking]] — 字节跳动上下文数据库
- [[ByteRover]] — 编程 Agent 记忆 CLI，LoCoMo 96.1%
- [[RetainDB]] — Postgres 自托管方案
- [[Claude-Mem]] — Claude Code 专用插件
- [[Holographic]] — 全息记忆范式
