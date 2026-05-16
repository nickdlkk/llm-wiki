---
title: Langcli
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/LangcliTeam/langcli
tags:
  - CodingAgent
  - 命令行终端
---

# Langcli

Langcli 是 100% 兼容 Claude Code 的终端 AI 编程助手，基于 Claude Code 最佳实践构建。

## 核心特性

- **100% Claude Code 兼容**：现有 `.claude` 配置和 Skills 可直接复用
- **多模型切换**：通过 LangRouter 深度集成，可在会话内无缝切换主流 LLM（Claude OPUS 4.6、DeepSeek v4、GLM 5.1、Kimi K2.6、MiniMax M2.5 等）
- **全模型支持**：Anthropic Direct、AWS Bedrock、Google Vertex、Azure Foundry

## 技术架构

- **运行时**：Node.js 20+（或 Bun）
- **REPL**：Ink 终端渲染，支持 5000+ 行历史
- **流式对话**：query.ts 1700+ 行，含 auto-compaction 和 token tracking
- **安装**：`npm i -g langcli-com` 或 `bash <(curl ...) install-langcli.sh`

## 与同类对比

| 特性 | Langcli | Claude Code | OpenCode |
|------|---------|-------------|----------|
| Claude Code 兼容 | ✅ | — | ❌ |
| 多模型切换 | ✅ (LangRouter) | ❌ | 部分 |
| 自托管 | ✅ | ❌ | ✅ |

## 链接

- GitHub: https://github.com/LangcliTeam/langcli
- 官网: https://langcli.com
- LangRouter: https://langrouter.ai/
