---
title: cocoindex
type: entity
created: 2026-05-22
updated: 2026-05-22
sources:
  - https://github.com/cocoindex-io/cocoindex-code
tags:
  - coding-agent
  - code-intelligence
  - MCP
  - code-search
  - code-indexing
---

# cocoindex

**cocoindex**（`cocoindex-io/cocoindex-code`，1.7k ⭐，Python）是基于 **CocoIndex**（Rust 高性能数据转换引擎）的**轻量 AST 语义代码搜索工具**，1 分钟安装，零配置，即装即用。

Token 节省 70%，支持 CLI、MCP Server 和 Claude Code Skill 多种接入方式。

许可：Apache-2.0。

## 核心价值

| 指标 | 效果 |
|------|------|
| Token 节省 | **70%** |
| 安装时间 | **1 分钟**，零配置 |
| 解析方式 | AST + Tree-sitter |
| 接入方式 | CLI / MCP Server / Claude Code Skill |

## 安装

```bash
# pipx 安装（推荐，含本地嵌入向量）
pipx install 'cocoindex-code[full]'

# uv 安装
uv tool install --upgrade 'cocoindex-code[full]'

# Skill 方式（Claude Code 自动使用）
npx skills add cocoindex-io/cocoindex-code
```

`[full]` 变体含本地嵌入向量（`sentence-transformers`），默认用 Snowflake/arctic-embed-xs，无需 API key。

`slim` 变体需要云端嵌入服务（LiteLLM + API key）。

## Claude Code Skill 集成

安装 Skill 后，Claude Code 会自动在需要时使用语义搜索，无需手动 `ccc init` 或 `ccc index`，Skill 会自动处理初始化、索引和搜索。

```bash
npx skills add cocoindex-io/cocoindex-code
```

## 与 CodeGraph / GitNexus 对比

| 维度 | cocoindex | CodeGraph | GitNexus |
|------|-----------|-----------|----------|
| 核心能力 | AST 语义搜索 | 符号图谱 | 知识图谱 |
| Token 节省 | 70% | 84-96% 调用 ↓ | 显著减少 |
| 解析方式 | Tree-sitter AST | 多语言 AST | Tree-sitter |
| 安装 | pipx 1 分钟 | npx 交互 | npm 全家桶 |
| Web UI | ❌ | ❌ | ✅ |
| 许可 | Apache-2.0 | MIT（推测） | PolyForm Noncommercial |
| Stars | 1.7k | 14.9k | 39.7k |
