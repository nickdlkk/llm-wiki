---
title: RetainDB
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://github.com/RetainDB/RetainDB
  - https://retaindb.com
tags:
  - memory
  - postgres
  - MCP
  - self-hosted
---

# RetainDB

**RetainDB** 是开源的 AI Agent 记忆与上下文基础设施，可自托管，通过单个 `docker compose up` 即可运行。

## 核心功能

- **记忆存储**: 从对话中提取记忆 — 带类型、版本、置信度评分
- **记忆检索**: 语义搜索 + BM25 + Rerank，一次调用返回可直接注入 LLM 的上下文字符串
- **记忆图**: 记忆之间可建立关系（`updates`/`contradicts`/`supports`/`derives`）
- **时序有效性**: 事实带 `validFrom`/`validUntil`，过期知识被新知识取代而非丢失
- **MCP Server**: 开箱即用，支持 Claude Desktop 和任何 MCP Host
- **框架适配器**: Vercel AI SDK、LangChain、LangGraph

## 快速部署

```bash
git clone https://github.com/retaindb/retaindb
cd retaindb
docker compose up
# 服务就绪于 http://localhost:3000
```

无配置文件要求，无需 API Key（本地使用）。

可选：启用 OpenAI Embeddings（默认回退到本地模型）：

```bash
OPENAI_API_KEY=*** docker compose up
```

## 技术栈

| 组件 | 技术 |
|------|------|
| 数据库 | PostgreSQL + pgvector |
| SDK | TypeScript/Node.js |
| 协议 | MCP（Model Context Protocol）|
| 许可 | Apache 2.0 / BSL 1.1 |

## 记忆关系图

```json
{
  "fact": "用户住在深圳",
  "type": "user.location",
  "confidence": 0.9,
  "validFrom": "2024-01-01",
  "validUntil": "2025-06-01",
  "supersedes": ["fact_id_previous"]
}
```

| 关系类型 | 说明 |
|---------|------|
| `updates` | 新记忆更新旧记忆 |
| `contradicts` | 新记忆与旧记忆矛盾 |
| `supports` | 新记忆支持旧记忆 |
| `derives` | 新记忆从旧记忆衍生 |

## 与其他系统的关键区别

- **完全自托管**: 无需任何第三方云服务
- **基于 PostgreSQL**: 成熟稳定，数据完全自主
- **关系型记忆模型**: 显式建模记忆间的时序和逻辑关系
- **无 LLM 推理绑定**: Embedding 模型可替换（默认本地）

## 相关项目

- [[Memory Systems]] — 记忆系统总体对比
- [[Supermemory]] — 云优先记忆系统
- [[OpenViking]] — 上下文数据库，字节跳动出品
