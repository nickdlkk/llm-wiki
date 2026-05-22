---
title: CodeGraph
type: entity
created: 2026-05-22
updated: 2026-05-22
sources:
  - https://github.com/colbymchenry/codegraph
  - https://www.npmjs.com/package/@colbymchenry/codegraph
tags:
  - coding-agent
  - code-intelligence
  - MCP
  - Claude-Code
  - Cursor
  - Codex
  - opencode
  - code-indexing
---

# CodeGraph

**CodeGraph**（`colbymchenry/codegraph`，14.9k ⭐，TypeScript）是给 AI Coding Agent 用的**语义代码图谱工具**。在项目目录初始化后，预索引整个代码库构建符号关系图谱（调用图、符号关系、代码结构），Agent 用 `codegraph_explore` 工具查询图谱代替扫描文件。

100% 本地，SQLite 存储，无 API key，无外部服务。

## 核心价值

| 指标 | 基准数据 |
|------|---------|
| 工具调用减少 | **84-96%**（平均 92%）|
| 速度提升 | **43-82%**（平均 71%）|
| 文件读取 | **0**（从不读文件，只查图谱）|
| 上下文窗口节省 | Agent 查询图谱返回精确上下文，无需海读 |

## Benchmark（Claude Code Explore Agent 对比）

| Codebase | 工具调用 | 时间 | 改进 |
|----------|---------|------|------|
| VS Code (TS, 4002 文件) | 52→**3** | 1m37s→**17s** | 94% ↓ · 82% ↓ |
| Excalidraw (TS) | 47→**3** | 1m45s→**29s** | 94% ↓ · 72% ↓ |
| Claude Code (Python+Rust) | 40→**3** | 1m8s→**39s** | 93% ↓ · 43% ↓ |
| Claude Code (Java) | 26→**1** | 1m22s→**19s** | 96% ↓ · 77% ↓ |
| Alamofire (Swift) | 32→**3** | 1m39s→**22s** | 91% ↓ · 78% ↓ |
| Swift Compiler (Swift/C++, 25874 文件) | 37→**6** | 2m8s→**35s** | 84% ↓ · 73% ↓ |

## 技术架构

```
代码文件
    ↓
代码解析器（多语言 AST 感知）
    ↓
符号图谱（SQLite）
  - 函数/类/变量定义节点
  - 调用关系边（references）
  - 类型关系边
  - 框架路由边
    ↓
codegraph_explore（Agent 工具）
  - 入口点查询
  - 调用链追踪
  - 影响半径分析
  - 跨语言调用链
```

## 核心功能

| 功能 | 说明 |
|------|------|
| **智能上下文构建** | 一次工具调用返回入口点 + 关联符号 + 代码片段 |
| **全文搜索** | FTS5 驱动，跨整个代码库按名称查找 |
| **影响分析** | 追踪任意符号的所有调用者/被调用者 |
| **文件监控** | FSEvents/inotify/ReadDirectoryChangesW 实时同步，零配置 |
| **框架感知路由** | 13 种 Web 框架自动识别 URL → Handler 映射 |
| **跨语言调用链** | Python+Rust 等混合语言项目无缝追踪 |
| **100% 本地** | SQLite，无 API key，无外部服务 |

### 支持的 Web 框架

Django / Flask / FastAPI / Express / Laravel / Rails / Spring / Gin / Axum / ASP.NET / React Router / SvelteKit 等。

### 支持的编程语言

TypeScript / JavaScript / Python / Go / Rust / Java / C# / PHP / Ruby / C / C++ / Swift / Kotlin / Dart / Svelte / Liquid / Pascal 等 19+ 种。

## 安装

```bash
# 交互式安装（自动检测 Agent）
npx @colbymchenry/codegraph

# 非交互式
codegraph install --target=auto --location=local --yes

# 初始化项目
cd your-project
codegraph init -i
```

安装后会自动写入对应 Agent 的 MCP 配置 + instructions 文件。

## 相关项目

- [[GitNexus]] — 浏览器内知识图谱，零服务端
- [[semble]] — ~98% 更少 token 的代码搜索
- [[cocoindex]] — 轻量 AST 代码搜索 CLI
- [[kit]] — AI devtools 上下文工程工具包
- [[claude-context]] — Zilliz 向量数据库驱动的代码搜索 MCP
- [[probe]] — ripgrep 速度 + tree-sitter AST 语义搜索
