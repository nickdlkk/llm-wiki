---
title: AI Agent 记忆系统对比
tags:
  - memory
  - comparison
  - benchmarks
---

# AI Agent 记忆系统对比

主流 AI Agent 记忆系统一览。对比维度包括：核心定位、基准成绩、部署方式、技术栈、集成生态。

## 总览表

| 系统 | 定位 | LoCoMo | 部署 | 许可 |
|------|------|--------|------|------|
| [[Supermemory]] | 通用记忆引擎（#1 基准）| #1（SOTA）| 云优先 | MIT |
| [[Honcho]] | Hermes 专用记忆层 | 86.9% | 自托管/云 | — |
| [[Hindsight]] | 向量记忆存储 | — | Docker 本地/云 | — |
| [[Holographic]] | 全息记忆范式 | — | — | — |
| [[RetainDB]] | Postgres 自托管记忆 | — | Docker（自托管）| Apache 2.0/BSL |
| [[ByteRover]] | 编程 Agent 记忆 CLI | **96.1%** | CLI + 云 | Elastic 2.0 |
| [[OpenViking]] | 上下文数据库 | ~85%（内部）| 自托管/云 | AGPLv3 |
| [[Claude-Mem]] | Claude Code 插件 | — | 本地插件 | Apache 2.0 |

## 基准详细对比

| 系统 | LoCoMo | LongMemEval | ConvoMem | 备注 |
|------|--------|-------------|----------|------|
| **Supermemory** | #1 | #1 | #1 | 三大基准全部第一 |
| **ByteRover** | **96.1%** | 92.8% | — | 编程场景专精 |
| **Honcho** | 86.9% | — | — | Neuromancer XR 模型 |
| **OpenViking** | ~85% | — | — | 字节内部评估 |

> 注：LoCoMo（Stanford）测试 ~20K tokens、35 会话的多轮对话记忆。LongMemEval-S 测试知识更新、时序推理、多会话综合能力。

## 核心维度对比

### 1. 部署方式

| 系统 | 完全自托管 | 云优先 | Agent 专用 |
|------|-----------|--------|-----------|
| Supermemory | ❌（数据在云）| ✅ | ❌（通用）|
| Honcho | ✅（via 自托管版）| ✅ | ✅（Hermes）|
| Hindsight | ✅ | ❌ | ❌ |
| RetainDB | ✅ | ❌ | ❌ |
| ByteRover | 部分 | ✅ | ✅（编程）|
| OpenViking | ✅ | ✅ | ❌ |
| Claude-Mem | ✅ | ❌ | ✅（Claude Code）|

### 2. 存储技术

| 系统 | 向量数据库 | 图结构 | 时序记忆 | 版本控制 |
|------|-----------|--------|---------|---------|
| Supermemory | ✅ | ✅（Memory Graph）| ✅ | ✅ |
| Honcho | — | — | ✅ | ✅ |
| Hindsight | ✅ | — | — | — |
| RetainDB | ✅（pgvector）| ✅（关系图）| ✅ | ✅ |
| ByteRover | ✅ | ✅（上下文树）| ✅ | ✅（Git 式）|
| OpenViking | ✅（多后端）| ✅（Viking URI）| ✅ | ✅ |
| Claude-Mem | — | — | ✅ | ✅ |

### 3. 集成生态

| 系统 | MCP | Vercel AI | LangChain | Claude Desktop | OpenCode |
|------|-----|-----------|---------|--------------|---------|
| Supermemory | ✅ | ✅ | ✅ | ✅ | ✅ |
| Honcho | — | — | — | — | — |
| Hindsight | — | — | — | — | — |
| RetainDB | ✅ | ✅ | ✅ | ✅ | — |
| ByteRover | ✅ | — | — | — | ✅ |
| OpenViking | ✅ | — | — | ✅ | ✅ |
| Claude-Mem | — | — | — | — | — |

### 4. 检索能力

| 系统 | 语义搜索 | BM25 | 混合检索 | Rerank | 分层摘要 |
|------|---------|------|---------|--------|---------|
| Supermemory | ✅ | ✅ | ✅ | ✅ | ✅ |
| RetainDB | ✅ | ✅ | ✅ | ✅ | — |
| ByteRover | ✅ | — | — | — | — |
| OpenViking | ✅ | ✅ | ✅ | ✅ | ✅（L0/L1/L2）|

## 选型指南

### 选 Supermemory 当：
- 需要在多个基准上追求 SOTA
- 需要多 Agent 共享记忆
- 需要丰富的 SDK 和连接器生态
- 需要 MCP 集成

### 选 Honcho 当：
- 使用 Hermes Agent
- 需要 Neuromancer 专用推理模型
- 数据主权要求高（自托管）

### 选 RetainDB 当：
- 需要完全自主的 Postgres 基础设施
- 需要显式建模记忆关系（contradicts/updates/supports）
- 不希望依赖任何第三方服务

### 选 ByteRover 当：
- 主要场景是 AI 编程（Cursor/Windsurf/Claude Code）
- 需要 Git 式版本控制管理记忆演进
- 需要 CLI 优先的工作流

### 选 OpenViking 当：
- 需要多租户、团队级记忆管理
- 需要代码仓库的深层语义理解（L0/L1/L2 分层）
- 在字节/火山引擎生态内

### 选 Claude-Mem 当：
- 只使用 Claude Code
- 只需要简单的会话压缩记忆
- 不需要跨 Agent 或跨工具的互操作性

### 选 Hindsight 当：
- 只需要向量存储作为记忆后端
- 愿意自己处理 LLM 提取逻辑
- Nick 当前已部署的方案

## 架构哲学差异

### 全息派（Holographic 理念）
记忆不是孤立的原子事实，而是分布式编码——每个记忆片段都包含与其他片段的关系信息。检索时通过"相干性检测"重建上下文。

代表：OpenViking 的 L0/L1/L2 分层（每个层级都包含整体信息）、ByteRover 的上下文树。

### 原子派（Atomic Memory）
记忆是最小独立的原子事实，具有明确的类型、置信度和有效期。事实之间通过显式关系图连接。

代表：Supermemory、RetainDB、Honcho。

### 两者并非互斥
现代系统往往结合两者：底层用原子事实存储，上层用分层摘要和关系图增强检索效果。

## 相关页面

- [[Supermemory]] — 基准第一的记忆系统
- [[Honcho]] — Hermes 专用记忆层
- [[Hindsight]] — Nick 当前部署的本地记忆方案
- [[OpenViking]] — 字节跳动上下文数据库
- [[ByteRover]] — 编程 Agent 记忆 CLI，LoCoMo 96.1%
- [[RetainDB]] — Postgres 自托管方案
- [[Claude-Mem]] — Claude Code 专用插件
- [[Holographic]] — 全息记忆范式
