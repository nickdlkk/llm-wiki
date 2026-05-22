---
title: claude-context
type: entity
created: 2026-05-22
updated: 2026-05-22
sources:
  - https://github.com/zilliztech/claude-context
  - https://marketplace.visualstudio.com/items?itemName=zilliz.semanticcodesearch
tags:
  - coding-agent
  - code-intelligence
  - MCP
  - code-search
  - code-indexing
---

# claude-context

**claude-context**（`zilliztech/claude-context`，11.5k ⭐，TypeScript）是 Zilliz 出品的 **MCP 语义代码搜索插件**，将整个代码库作为向量存入 Zilliz Cloud（Milvus）向量数据库，为 Claude Code 等 AI Coding Agent 提供深度语义搜索能力。

许可：MIT。支持的 Agent：Claude Code / Cursor / Gemini CLI / Codex。

## 核心价值

- **全代码库作为上下文**：语义搜索从百万行代码中找出所有相关内容，无需多轮发现。
- **成本可控**：整目录加载到 Claude 上下文非常昂贵，claude-context 只在上下文中注入相关代码。
- **多 Agent 支持**：Claude Code / Cursor / Gemini CLI / Codex，通过 MCP 协议集成。
- **VS Code 插件**：独立 VS Code 插件可直接在编辑器内使用。

## 架构

```
代码文件
    ↓
代码分块 + 嵌入（OpenAI embedding）
    ↓
Zilliz Cloud / Milvus 向量数据库
    ↓
MCP Server
    ↓
Claude Code / Cursor / Codex（语义检索 → 上下文注入）
```

## 依赖

- **向量数据库**：Zilliz Cloud（Milvus）— 需要注册获取 API key
- **嵌入模型**：OpenAI API key（`text-embedding-3-small` 等）

## 安装

```bash
# Claude Code MCP 配置
claude mcp add claude-context \
  -e OPENAI_API_KEY=your-key \
  -e MILVUS_ADDRESS=your-zilliz-public-endpoint \
  -e MILVUS_TOKEN=your-zilliz-token \
  -- npx @zilliz/claude-context-mcp@latest
```

## 与 CodeGraph / GitNexus 对比

| 维度 | claude-context | CodeGraph | GitNexus |
|------|---------------|-----------|----------|
| 核心能力 | 向量语义搜索 | 符号图谱 | 知识图谱 |
| 数据库 | Zilliz Cloud/Milvus | SQLite | LadybugDB |
| 解析方式 | OpenAI Embedding | 多语言 AST | Tree-sitter |
| 完全本地 | ❌（需 Zilliz Cloud） | ✅ | ✅ |
| Web UI | ✅ VS Code 插件 | ❌ | ✅ |
| 许可 | MIT | MIT（推测） | PolyForm Noncommercial |
| Stars | 11.5k | 14.9k | 39.7k |
