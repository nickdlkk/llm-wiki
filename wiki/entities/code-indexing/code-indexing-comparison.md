---
title: code-indexing-comparison
type: entity
created: 2026-05-22
updated: 2026-05-23
sources:
  - https://github.com/colbymchenry/codegraph
  - https://github.com/abhigyanpatwari/GitNexus
  - https://github.com/MinishLab/semble
  - https://github.com/cocoindex-io/cocoindex-code
  - https://github.com/zilliztech/claude-context
  - https://github.com/Tencent/TencentDB-Agent-Memory
tags:
  - coding-agent
  - code-intelligence
  - MCP
  - code-indexing
  - comparison
---

# 代码索引工具横向对比

## 总览

|| 工具 | Stars | 语言 | 许可 | 核心能力 | Embedding模型 |
||------|-------|------|------|----------|---------------|
|| **GitNexus** | 39.7k | TypeScript | PolyForm Noncommercial | 知识图谱 + Web UI + MCP | ❌ |
|| **CodeGraph** | 14.9k | TypeScript | MIT（推测） | 符号图谱 + 框架感知 | ❌ |
|| **claude-context** | 11.5k | TypeScript | MIT | 向量语义搜索 + Milvus | ✅ OpenAI |
|| **TencentDB Agent Memory** | 1.2k | TypeScript | MIT | 四层记忆 + Mermaid 符号压缩 | ✅ 混合（BM25 + 向量 + RRF） |
|| **semble** | 3.6k | Python | MIT | 嵌入向量搜索 | ✅ |
|| **cocoindex** | 1.7k | Python | Apache-2.0 | AST 语义搜索 + Skill | ❌ |

## 能力矩阵

|| 维度 | CodeGraph | GitNexus | TencentDB Mem | semblent | cocoindex | claude-context |
||------|-----------|----------|----------------|---------|-----------|----------------|
|| **工具调用优化** | 84-96% ↓ | 显著 | 61% ↓ | ~98% token ↓ | 70% ↓ | 显著 |
|| **解析方式** | 多语言 AST | Tree-sitter | 混合（BM25+向量+RRF） | 嵌入向量 | Tree-sitter AST | OpenAI Embedding |
|| **索引存储** | SQLite | LadybugDB | SQLite+sqlite-vec / TCVDB | 向量 | 本地文件 | Zilliz/Milvus |
|| **100% 本地** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌（需 Zilliz Cloud） |
|| **MCP Server** | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |
|| **CLI** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
|| **Web UI** | ❌ | ✅ | ❌ | ❌ | ❌ | ❌（VS Code 插件） |
|| **框架感知路由** | ✅ 13种 | ❌ | ❌ | ❌ | ❌ | ❌ |
|| **跨语言调用链** | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
|| **无需 API Key** | ✅ | ✅ | ❌ | ✅ | ✅（full版）| ❌ |
|| **PR Review** | ❌ | ✅（企业版） | ❌ | ❌ | ❌ | ❌ |
|| **安装便捷性** | npx 交互 | npm 全家桶 | npm 插件 | pip | pipx 1分钟 | npm + Zilliz 注册 |
|| **多 Agent 记忆** | ❌ | ✅ | ✅（四层 L0-L3） | ❌ | ❌ | ✅ |
|| **Mermaid 符号压缩** | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ |

## 选型决策树

```
你的场景
│
├─ 追求极致工具调用减少 + 框架感知
│   └─ CodeGraph（84-96%，13种框架，100%本地）
│
├─ 需要浏览器内探索 + Web UI + PR Review
│   └─ GitNexus（Web UI 内置，39.7k⭐ 最大社区）
│
├─ 多 Agent 长程记忆 + 上下文压缩 + Token 节省
│   └─ TencentDB Agent Memory（61% 降低，四层 L0-L3，Mermaid 符号压缩）
│
├─ 已有 Zilliz Cloud + 需要深度语义搜索
│   └─ claude-context（向量检索，多 Agent 支持）
│
├─ 轻量快速 + 不想折腾
│   ├─ Token 节省优先 → semblent（~98%，MCP）
│   └─ 1分钟极速上手 → cocoindex（pipx，零配置）
│
└─ 不想依赖外部服务 + 追求本地化
    └─ CodeGraph 或 GitNexus
```

## 技术路线分类

### 路线一：符号图谱（Symbol Graph）

**代表**：CodeGraph、GitNexus

解析代码 AST，构建符号（函数/类/变量）节点和调用关系边。优点：精确的调用链追踪、影响分析、跨语言支持。缺点：依赖代码解析器，语言覆盖受限于解析器实现。

### 路线二：向量语义搜索（Vector Embedding）

**代表**：semblé、claude-context

将代码块编码为向量，用相似度搜索召回相关代码。优点：语义理解能力强，无需语法解析。缺点：精确调用链弱，embedding 模型质量影响大。

### 路线三：AST 结构搜索（AST Search）

**代表**：cocoindex

在 AST 层面做结构化查询，兼顾语法精确性和语义相关性。介于图谱和向量之间。

### 路线四：混合召回（Hybrid Retrieval）

**代表**：TencentDB Agent Memory

BM25 关键词检索 + 向量相似度 + RRF 融合排序，兼顾关键词精确性和语义相关性。支持 SQLite+sqlite-vec 本地部署或腾讯云 TCVDB。

## 各工具适合的场景

|| 场景 | 推荐工具 |
|------|---------|
| 大型 monorepo（万级文件） | CodeGraph（框架感知 + SQLite 高性能） |
| 快速原型 / 小团队 | GitNexus（Web UI 上手快） |
| 多 Agent 协作（Cursor + Claude Code） | GitNexus 或 CodeGraph（多 Editor 支持） |
| 预算有限，不想注册外部服务 | CodeGraph 或 cocoindex（100%本地） |
| 已有 Milvus / 向量数据库基础设施 | claude-context |
| 追求极简，1 分钟上手 | cocoindex（pipx 1分钟，零配置） |
| 多 Agent 长程记忆 + Token 成本敏感 | TencentDB Agent Memory（四层 L0-L3，Mermaid 符号压缩，61% Token 节省） |

## 相关概念

- [[Harness Engineering]] — 让 Agent 可靠工作的工程化方法论
- [[Claude-Code]] — 代码索引工具的主要使用方
- [[Memory Systems]] — 记忆系统总体分类
- [[TencentDB Agent Memory]] — 腾讯四层记忆系统，混合召回 + Mermaid 符号压缩
