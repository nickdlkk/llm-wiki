---
title: 代码索引工具
type: folder
created: 2026-05-22
updated: 2026-05-22
tags:
  - coding-agent
  - code-intelligence
  - code-indexing
---

# 代码索引工具

面向 AI Coding Agent 的代码索引 / 语义搜索工具集合。通过预索引代码库、构建符号图谱或向量索引，让 Agent 在执行任务时减少工具调用次数、节省 token、提升准确性。

## 目录

- [[CodeGraph]] — 语义符号图谱，84-96% 工具调用减少，100% 本地 SQLite
- [[GitNexus]] — 零服务端知识图谱，Tree-sitter + MCP + Web UI
- [[semble]] — 嵌入向量搜索，比 grep+read 节省 ~98% token
- [[cocoindex]] — 轻量 AST 搜索 CLI，70% token 节省，1 分钟安装
- [[claude-context]] — Zilliz/Milvus 向量数据库驱动，深度语义搜索
- [[code-indexing-comparison]] — 横向对比与选型建议
