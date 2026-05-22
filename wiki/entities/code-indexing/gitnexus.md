---
title: GitNexus
type: entity
created: 2026-05-22
updated: 2026-05-22
sources:
  - https://github.com/abhigyanpatwari/GitNexus
  - https://www.npmjs.com/package/gitnexus
  - https://gitnexus.vercel.app
tags:
  - coding-agent
  - code-intelligence
  - MCP
  - Claude-Code
  - Cursor
  - codex
  - code-indexing
---

# GitNexus

**GitNexus**（`abhigyanpatwari/GitNexus`，39.7k ⭐，TypeScript）是零服务端的**代码智能知识图谱引擎**。为任意代码库建立符号依赖图谱、调用链、聚类和执行流，再通过智能工具暴露给 AI Agent，让 Agent 永远不会遗漏关键代码关系。

许可：PolyForm Noncommercial（禁止商用）；企业版：akonlabs.com

## 两种使用模式

| | **CLI + MCP** | **Web UI** |
|---|---|---|
| **场景** | 日常开发，接入 Cursor / Claude Code / Codex | 快速探索、演示、一次性分析 |
| **规模** | 任意大小仓库 | 浏览器内存限制（~5k 文件），或无限制（后端模式） |
| **安装** | `npm install -g gitnexus` | 无需安装：[gitnexus.vercel.app](https://gitnexus.vercel.app) |
| **存储** | LadybugDB native（快速持久化） | LadybugDB WASM（内存，按会话） |
| **解析** | Tree-sitter 原生绑定 | Tree-sitter WASM |
| **隐私** | 100% 本地，无网络 | 100% 浏览器内，无服务器 |

> **Bridge 模式**：`gitnexus serve` 打通两者 — Web UI 自动发现本地服务，直接浏览已索引仓库，无需重新上传或索引。

## 核心价值

- **Building nervous system for agent context**：比 DeepWiki 更深 — DeepWiki 帮助**理解**代码，GitNexus 帮助**分析**代码，知识图谱追踪每一条关系而非仅仅描述。
- **零服务端**：Web UI 直接在浏览器运行，CLI 100% 本地，无外部依赖。
- **自动 Hook**：PreToolUse + PostToolUse 自动注入，Claude Code 开箱即用。
- **PR Review**（企业版）：PR 提交时自动 blast radius 分析。

## 快速上手

```bash
# 安装 CLI
npm install -g gitnexus

# 索引仓库（从仓库根目录运行）
npx gitnexus analyze
# 一次性完成：索引 + 安装 Agent skills + 注册 Claude Code hooks + 创建 AGENTS.md/CLAUDE.md

# 配置 MCP（编辑器自动检测）
npx gitnexus setup
```

## 支持的编辑器

| 编辑器 | MCP | Skills | Hooks | 支持度 |
|--------|-----|--------|-------|--------|
| **Claude Code** | ✅ | ✅ | ✅ PreToolUse + PostToolUse | **完整** |
| **Cursor** | ✅ | ✅ | ✅ postToolUse（需手动安装） | **完整** |
| **Codex / Windsurf / OpenCode** | ✅ | — | — | 基础 |

## 与 CodeGraph 对比

| 维度 | GitNexus | CodeGraph |
|------|----------|-----------|
| 架构 | CLI+MCP + Web UI 双模 | MCP Server + SQLite |
| 存储 | LadybugDB native | SQLite |
| 解析 | Tree-sitter 原生 + WASM | 多语言 AST 感知 |
| Web UI | ✅ 内置 | ❌ |
| PR Review | ✅ 企业版 | ❌ |
| 许可 | PolyForm Noncommercial | MIT（推测） |
| 规模 | 任意大小仓库 | 任意大小仓库 |
| Stars | 39.7k | 14.9k |
