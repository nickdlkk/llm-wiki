---
title: Canopy
description: RAG framework and context engine powered by Pinecone — 开源RAG框架，提供ChatEngine/ContextEngine/KnowledgeBase核心类
github: https://github.com/pinecone-io/canopy
stars: 1031
tags: [rag, context-engine, vector-search, llm, pinecone, open-source]
created: 2026-05-16
---

# Canopy

## 概述

**Canopy** 是 Pinecone 开源的检索增强生成 (RAG) 框架和上下文引擎，基于 Pinecone 向量数据库构建。Canopy 提供完整的 RAG 工作流：文档分块、嵌入、聊天历史管理、查询优化、上下文检索和增强生成。

> ⚠️ **项目状态：不再维护 (No longer maintained)**
> Canopy 团队已停止维护此仓库。如需高质量托管RAG解决方案，请查看 [Pinecone Assistant](https://docs.pinecone.io/guides/assistant/understanding-assistant)。

**GitHub**: [pinecone-io/canopy](https://github.com/pinecone-io/canopy)  
**Language**: Python  
**License**: Apache-2.0  
**Stars**: 1,031 ⭐  

## 核心组件

### 1. ChatEngine

对话接口，用于与数据交互。给定聊天消息历史，ChatEngine 向 ContextEngine 制定相关查询，然后使用 LLM 生成有知识的响应。

### 2. ContextEngine

执行 RAG 的"检索"部分。ContextEngine 利用底层 KnowledgeBase 检索最相关文档，然后制定连贯的文本上下文作为 LLM 的提示。

### 3. KnowledgeBase

管理 RAG 工作流的数据。自动分块并将文本数据转换为文本嵌入，存储在 Pinecone (默认) 或 Qdrant 向量数据库中。

## RAG 工作流

```
知识库创建流程:
  用户上传文档 → 分块 → 嵌入 → 存储到Pinecone

聊天流程:
  用户查询 + 聊天历史 → 查询优化 → 检索相关文档 → 生成上下文 → LLM回答
```

## 技术特点

| 特性 | 描述 |
|------|------|
| 完整RAG实现 | 防止幻觉，用自有数据增强LLM |
| 可配置服务器 | 内置FastAPI服务器，易于部署 |
| CLI工具 | 命令行聊天测试，对比RAG vs 非RAG |
| 多后端支持 | Pinecone (默认) / Qdrant |
| 嵌入选项 | sentence-transformers, Cohere reranker |

## 安装

```bash
# 基本安装
pip install canopy-sdk

# 完整安装 (含torch, transformers, cohere, qdrant)
pip install canopy-sdk[torch,transformers,cohere,qdrant]
```

## 环境变量

| 变量 | 描述 |
|------|------|
| `PINECONE_API_KEY` | Pinecone API密钥 |
| `OPENAI_API_KEY` | OpenAI API密钥 |
| `INDEX_NAME` | 索引名称 |

## 与其他工具对比

| 工具 | 定位 | 核心能力 |
|------|------|----------|
| Canopy | RAG框架 | 文档检索 + LLM增强 |
| AgentVerse | 多Agent协作 | 团队讨论 + 角色扮演 |
| ClawPort | Agent仪表盘 | 监控 + 协作管理 |
| Aperant | 自主编码 | 端到端代码生成 |

## 相关链接

- [GitHub Repository](https://github.com/pinecone-io/canopy)
- [Pinecone Assistant](https://docs.pinecone.io/guides/assistant/understanding-assistant)
- [Blog Post](https://pinecone.io/blog/canopy-rag-framework)
- [Tutorial Video](https://www.youtube.com/watch?v=dVGPglKh80Y)
