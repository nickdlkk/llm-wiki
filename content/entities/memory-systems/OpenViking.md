---
title: OpenViking
type: entity
created: 2026-05-10
updated: 2026-05-19
sources:
  - https://github.com/volcengine/OpenViking
  - https://openviking.ai
  - https://docs.openviking.ai
tags:
  - memory
  - context-database
  - volcengine
  - self-hosted
  - multi-agent
  - code-agent
---

# OpenViking

**OpenViking** 是字节跳动（Volcengine）Viking 团队开源的 AI Agent **上下文数据库（Context Database）**，许可：AGPLv3。核心理念：把上下文从"碎片化向量堆砌"转变为**文件系统范式**，通过层级目录结构统一管理记忆、资源和技能。

## 核心定位

| 维度 | 传统向量数据库 | OpenViking |
|------|--------------|-------------|
| 存储模型 | 扁平向量 | 层级文件系统（AGFS） |
| 检索方式 | 单次向量相似度 | 目录递归检索 + Intent 分析 + Rerank |
| 输出格式 | 原始文本块 | 结构化上下文（L0/L1/L2） |
| 记忆能力 | 不支持 | 内置 6 类记忆自动提取 |
| 可观测性 | 黑盒 | 全链路检索轨迹可追溯 |
| 上下文类型 | 仅文档 | 资源 + 记忆 + 技能 |

## 分层上下文架构

OpenViking 实现**三级渐进加载机制**：

| 层级 | 名称 | Token 量 | 用途 |
|------|------|---------|------|
| **L0** | Abstract | ~100 tokens | 向量检索召回、快速过滤 |
| **L1** | Overview | ~2k tokens | Rerank、内容导航 |
| **L2** | Details | 无限制 | 按需加载完整原始内容 |

**层级聚合**：每个目录维护对应 L0/L1 层，L0 从子目录向上聚合为父目录 L1，实现**自底向上**的抽象。

## 技术架构

```
用户数据（会话/文件/URL）
    ↓
解析器（Parser）— 物理搬运，无 LLM 调用，快速返回
    ↓
异步语义处理（Semantic Processor）
    ↓
┌──────────────┬──────────────────┐
│ AGFS         │ Vector Index     │
│ 内容存储      │ 语义检索         │
│ (L0/L1/L2    │ (URI + 向量      │
│  多媒体)     │  + 元数据)       │
└──────────────┴──────────────────┘
    ↓
分层检索（find/search）
    ↓
上下文输出（L0/L1/L2）
```

### VikingFS：统一 URI 抽象层

所有上下文通过 `viking://` URI 操作，像文件系统一样管理：
- `viking://resources/...` — 外部资源
- `viking://memory/...` — 记忆
- `viking://skill/...` — 技能

### 存储后端

| 后端 | 说明 |
|------|------|
| **VikingDB** | 字节内部向量数据库 |
| **Milvus** | 开源向量数据库 |
| **Qdrant** | 开源向量数据库 |
| **pgvector** | PostgreSQL 向量扩展 |
| **LanceDB** | 本地向量数据库 |
| **Local/HTTP** | 通用适配器 |

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

- **双层 API Key**：Root keys（`ov.conf`） + User keys（`/{account_id}/_system/users.json`）
- **数据隔离**：AGFS 路径前缀 `account_id`，VectorDB 查询过滤 `account_id + owner_space`
- **RBAC**：目录遍历过滤实现 User/Agent 级隔离
- **RequestContext 注入**：Auth Middleware → RBAC Guard → Service Layer → VikingFS/VectorDB 全链路身份传递

## 基准测试

**LoCoMo 10 长期对话数据集（1,540 案例）**：

| 配置 | 任务完成率 | 输入 Token 成本 |
|------|-----------|----------------|
| OpenClaw（裸） | 35.65% | 24,611,530 |
| OpenClaw + LanceDB | 44.55% | 51,574,530 |
| OpenClaw + OpenViking（无原生记忆） | **52.08%** | 4,264,396 |
| OpenClaw + OpenViking（原生记忆） | 51.23% | **2,099,622** |

**结论**：+43% 任务完成率，+91% Token 成本降低（原生记忆模式）。

## 生态集成

| 集成 | 说明 |
|------|------|
| **OpenCode** | Vikingbot 提供代码上下文注入 |
| **Claude Code** | MCP 或直接集成 |
| **OpenClaw** | 官方 Plugin 2.0，auto-capture + recall + inject |
| **LangChain** | Context backend 替换 |
| **狼人杀 Demo** | 多 Agent 游戏场景演示 |

## 部署

```bash
# pip 安装
pip install openviking
openviking-server --config ~/.openviking/ov.conf

# Docker Compose
docker-compose up -d
```

配置文件 `ov.conf` 支持 embedding provider、VLM provider、存储后端灵活配置。

## 关键特性

- **文件系统式 URI** — `viking://resources/...` 像操作文件一样操作上下文
- **代码仓库解析** — 保留目录结构，AST 感知，不拆分文件
- **可观测性** — QueueObserver、VikingDBObserver 实时监控
- **实时会话记忆** — 自动记录 Agent 会话，支持 `memcommit` 显式提交
- **检索可追溯** — 明确知道哪条记忆来自哪里

---

## 记忆系统横向对比

> 以下对比基于各系统 GitHub 仓库、官网和文档的公开信息。Token 节省率数字来自各自论文或 benchmark 自述。

### 综合对比表

| 系统 | 范式 | 分层架构 | 存储后端 | 基准成绩 | 许可 | Agent 自管理 | OpenClaw 集成 |
|------|------|---------|---------|---------|------|-------------|---------------|
| **OpenViking** | 文件系统范式 | L0/L1/L2 三级渐进 | VikingDB / Milvus / Qdrant / pgvector / LanceDB | LoCoMo: +43% 完成率, +91% Token 降低 | AGPLv3 | ❌ 平台管理 | ✅ Plugin 2.0 |
| **Supermemory** | 知识图谱 + RAG | 无固定层级 | 自有云存储 | LoCoMo **#1**，LongMemEval **#1**，ConvoMem **#1** | 专有（云） | ❌ 平台管理 | ✅ MCP Server |
| **Mem0** | 向量 + 知识图谱 | User/Session/Agent 三级 | Qdrant / Pinecone / pgvector / Chroma | 26% 提升 vs OpenAI baseline | Apache 2.0 | ❌ 平台管理 | ✅ MCP Server + LangChain |
| **Letta** | LLM-as-OS（LLM 自管） | Core/Archival/Recall/MovingWindow | SQLite+sqlite-vec / PG+pgvector / Pinecone / Redis | 竞品参考（非自测） | Apache 2.0 | ✅ LLM 直接读写 | ❌ |
| **Honcho** | 记忆深化 + 逻辑推理 | Session → Deepening → Consolidation | 自有云 / 自托管 | LoCoMo: **86.9%**（Neuromancer XR） | 专有 | ❌ 平台管理 | ✅ Hermes L4 |
| **TencentDB Agent Memory** | 符号化（LLM-free Mermaid） | L0 对话 → L1 原子 → L2 场景 → L3 画像 | SQLite+sqlite-vec / 腾讯云 TCVDB | Token 节省 **61%**，任务通过率 **+51%** | MIT | ❌ 平台管理 | ✅ Plugin |
| **ByteRover** | Git 式版本控制 | 无固定层级 | 自有存储 | LoCoMo: **96.1%** | 专有 | ❌ 平台管理 | ❌ |
| **Hindsight** | 向量记忆存储 | Session → Long-term | SQLite + 向量 | 未公开基准 | Apache 2.0 | ❌ 平台管理 | ✅ Hermes 内置 |
| **RetainDB** | 关系图记忆 | Entity → Relation → Session | Postgres + pgvector | 未公开基准 | Apache 2.0 / BSL | ❌ 平台管理 | ✅ MCP Server |
| **Cognee** | 结构化知识图谱 | Structured Facts + Graph | 多后端 | 未公开基准 | Apache 2.0 | ❌ 平台管理 | ✅ |
| **Graphiti** | 时序知识图谱 | Temporal Graph | Neo4j / PG | 未公开基准 | Apache 2.0 | ❌ 平台管理 | ✅ |
| **MemMachine** | 会话摘要压缩 | Session → Compressed Summary | 轻量嵌入 | 未公开基准 | MIT | ❌ 平台管理 | ✅ Claude Code |
| **Memori** | 多模态持久记忆 | 无固定层级 | 自有存储 | 未公开基准 | 专有 | ❌ 平台管理 | 部分 |
| **memorizz** | 多类型 × 多存储 | 10 种记忆类型 | Oracle / Mongo / FS | 未公开基准 | PolyForm（非商用） | ❌ 平台管理 | ❌ |

### 范式分类

#### 1. 平台管理型（主流）
Supermemory、Mem0、OpenViking、TencentDB Agent Memory、Hindsight、RetainDB 等：记忆的提取、压缩、存储、召回全部由**外部平台**管理，LLM 通过 API 注入上下文。优点是开箱即用；缺点是 LLM 无法理解记忆组织逻辑。

#### 2. LLM 自管型（激进）
Letta（MemGPT）：把**记忆读写能力作为工具**暴露给 LLM，LLM 显式决定何时读、写、压缩记忆。优点是 LLM 有完全控制权；缺点是每次记忆操作都消耗 Token，且模型可能随时间漂移。

#### 3. 符号化型（腾讯首创）
TencentDB Agent Memory：用 **Mermaid 符号图谱**替代向量压缩，Token 降低 61% 且 100% 可溯源，LLM-free 压缩不消耗推理 Token。

#### 4. 专用推理模型型
Honcho（Neuromancer XR）：用专用 8B 微调模型（Qwen3-8B → Neuromancer XR）专门做记忆深化，LoCoMo 86.9%，专注逻辑结论提取。

### 选型建议

| 场景 | 推荐 |
|------|------|
| OpenClaw 代码 Agent | **OpenViking**（官方集成，+43% 完成率，+91% Token 降低） |
| Hermes Agent | **Honcho**（内置 L4，支持自托管） |
| 多模态记忆（文档/图片/视频） | **Supermemory**（#1 基准，最全连接器） |
| Token 成本敏感 | **TencentDB Agent Memory**（61% 降低，LLM-free） |
| 自主记忆控制实验 | **Letta**（LLM-as-OS 范式） |
| 本地自托管 + 轻量 | **Hindsight**（Docker 一键，Nick 当前方案） |
| Postgres 已有 / 不想引入新存储 | **RetainDB**（pgvector 原生） |
| 预算有限 / 快速验证 | **Mem0**（开源，Apache 2.0，门槛最低） |

## 相关项目

- [[Supermemory]] — 基准三冠王，TypeScript-first
- [[Mem0]] — 记忆层定义者，开源门槛最低
- [[Letta]] — LLM-as-OS 激进范式
- [[Honcho]] — Hermes L4 内置，专用推理模型
- [[TencentDB Agent Memory]] — 符号化 Mermaid，61% Token 节省
- [[Hindsight]] — Nick 当前本地方案
- [[RetainDB]] — Postgres 原生
- [[ByteRover]] — 96.1% LoCoMo，Git 式版本控制
