---
title: semble
type: entity
created: 2026-05-22
updated: 2026-05-22
sources:
  - https://github.com/MinishLab/semble
tags:
  - coding-agent
  - code-intelligence
  - MCP
  - code-search
  - code-indexing
---

# semble

**semble**（`MinishLab/semble`，3.6k ⭐，Python）是面向 AI Agent 的**极速精准代码搜索工具**，使用嵌入向量检索实现比 `grep + read` 少 ~98% token 的代码搜索。

许可：MIT。协议：MCP Server + 嵌入向量检索。

## 核心价值

| 指标 | 效果 |
|------|------|
| Token 节省 | **~98%** 比 grep+read |
| 搜索方式 | 嵌入向量相似度检索 |
| Agent 集成 | MCP Server 协议 |
| 部署 | 本地，无 API key |

## 技术特点

- **嵌入向量检索**：将代码符号、注释、上下文编码为向量，语义相似度搜索而非正则匹配。
- **MCP Server**：作为 MCP Server 暴露搜索能力，Claude Code / Cursor / Codex 等开箱即用。
- **轻量**：Python 实现，依赖少，pip 安装即可。
- **精准**：针对 Agent 场景优化，返回结果直接可用于上下文注入。

## 安装

```bash
pip install semble
# 或
npx @minishlab/semble
```

## 与 CodeGraph / GitNexus 对比

| 维度 | semblent | CodeGraph | GitNexus |
|------|---------|-----------|----------|
| 核心能力 | 向量搜索 | 符号图谱 | 知识图谱 |
| 解析方式 | 嵌入向量 | 多语言 AST | Tree-sitter |
| 工具调用优化 | ~98% token ↓ | 84-96% 调用 ↓ | 显著减少 |
| Web UI | ❌ | ❌ | ✅ |
| 许可 | MIT | MIT（推测） | PolyForm Noncommercial |
| Stars | 3.6k | 14.9k | 39.7k |
