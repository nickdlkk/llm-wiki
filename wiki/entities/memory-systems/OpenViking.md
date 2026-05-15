---
title: OpenViking
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://github.com/volcengine/OpenViking
  - https://openviking.ai
tags:
  - memory
  - context-database
  - volcengine
  - self-hosted
  - multi-agent
---

# OpenViking

**OpenViking** 是字节跳动（Volcengine）开源的 AI Agent **上下文数据库（Context Database）**，定位为记忆与上下文管理的完整解决方案。许可协议：AGPLv3。

## 核心问题与解法

| 挑战 | OpenViking 解法 |
|------|----------------|
| 上下文碎片化 | 统一记忆/资源/技能管理 |
| 上下文需求激增 | 分层存储 + 按需按层级注入 |
| 检索效果差 | 图结构 + 层级摘要（L0/L1/L2）|
| 上下文黑盒 | 可观测的检索链 |
| 记忆迭代困难 | 记忆图支持增量化 |

## 分层上下文架构

OpenViking 实现了 **三级上下文层级**：

| 层级 | 名称 | 说明 |
|------|------|------|
| **L0** | Abstract | 目录/文件的功能摘要 |
| **L1** | Overview | 详细概览，含文件结构和关键实体索引 |
| **L2** | Detail | 原始内容（代码/文档），不拆分 |

## 技术架构

```
用户数据（会话/文件/URL）
    ↓
解析器（Parser）— 物理搬运，无 LLM 调用，快速返回
    ↓
异步语义处理（Semantic Processor）
    ↓
向量存储（VikingDB / Milvus / Qdrant / pgvector）
    ↓
分层检索（find/search）
    ↓
上下文输出（L0/L1/L2）
```

### 存储后端

支持多种向量数据库适配器：
- **VikingDB**（字节内部）
- **Milvus**
- **Qdrant**
- **pgvector**
- **Local** / **HTTP** 通用适配器

### 多租户架构

```
ROOT API Key（管理员）
    ↓
Account（租户）
    ↓
User（用户）— ADMIN 或 USER 角色
    ↓
Agent（代理）
```

每个层级都有独立的认证与数据隔离。

## 基准测试

OpenViking 在 LoCoMo 基准上运行了竞品对比测试：

| 系统 | LoCoMo 成绩 | 说明 |
|------|-------------|------|
| **OpenViking** | ~85%（参考 benchmark 脚本）| 字节内部评估 |
| Supermemory | #1 | 第三方基准 |
| mem0 | Baseline | 第三方基准 |

> 实际 benchmark 在 `benchmark/` 目录下，包含 LoCoMo 数据集上的 mem0、Supermemory 对比测试。

## 集成生态

| 集成 | 说明 |
|------|------|
| **OpenCode** | Vikingbot 提供代码上下文注入 |
| **Claude Code** | 通过 MCP 或直接集成 |
| **Vikingbot** | 字节自研 AI 编程 Bot |
| **狼人杀 Demo** | 多 Agent 游戏场景演示 |

## 部署方式

```bash
# Docker 一键部署
pip install openviking
openviking-server --config ~/.openviking/ov.conf

# Docker Compose
docker-compose up -d
```

配置文件 `ov.conf` 支持 embedding provider、VLM provider、存储后端等灵活配置。

## 关键特性

- **文件系统式 URI** (`viking://resources/...`) — 像操作文件一样操作上下文
- **代码仓库解析** — 保留目录结构，AST 感知，不拆分文件
- **可观测性** — QueueObserver、VikingDBObserver 实时监控
- **实时会话记忆** — 自动记录 Agent 会话，支持 `memcommit` 显式提交
- **检索可追溯** — 明确知道哪条记忆来自哪里

## 相关项目

- [[Memory Systems]] — 记忆系统总体对比
- [[Supermemory]] — 基准排名第一
- [[ByteRover]] — 专注编程 Agent 的记忆系统
- [[RetainDB]] — Postgres/pgvector 自托管方案
