---
title: Hindsight
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://hindsight.vectorize.io
  - Memory context (2026-05-10)
tags:
  - memory
  - hosted
  - hermes
  - local
---

# Hindsight

**Hindsight** 是 [vectorize.io](https://hindsight.vectorize.io) 提供的记忆系统，提供本地部署（Embedded）和云端（Cloud）两种模式。Nick 当前在 PVE LXC（192.168.3.241）上使用 Docker 部署了 Hindsight 本地版本，已成功对接 MiniMax LLM（通过 `HINDSIGHT_API_LLM_BASE_URL=https://api.minimaxi.com/v1` 配置）。

## 部署模式

| 模式 | 说明 | 配置方式 |
|------|------|----------|
| **Cloud** | 官方托管，需注册获取 API Key | `HINDSIGHT_MODE=cloud` |
| **Embedded（本地）** | Docker 部署到自有服务器 | `HINDSIGHT_MODE=local` |

## 架构

```
会话文本 → LLM Extraction（MiniMax/其他） → 记忆存储 → 检索 → 上下文
```

Nick 的配置中，Hindsight 仅负责**存储**，不负责 LLM 解析。外部脚本调用 MiniMax API 提取关键事实，通过 REST API 存入 Hindsight。

## 已知问题

### MiniMax 对接问题

根因：MiniMax provider 的 OpenAICompatibleLLM 使用了错误端点 `/v1/chat/completions`，实际应为 `/v1/text/chatcompletion_v2`。

**修复方案**：设置 `HINDSIGHT_API_LLM_BASE_URL=https://api.minimaxi.com/v1`

### 环境变量前缀

Hindsight Docker 镜像读取的 env var 前缀是 `HINDSIGHT_API_*`（不是 `HINDSIGHT_*`）：

```
HINDSIGHT_API_LLM_API_KEY=xxx
HINDSIGHT_API_LLM_PROVIDER=minimax
HINDSIGHT_API_LLM_MODEL=MiniMax-M2
HINDSIGHT_API_LLM_BASE_URL=https://api.minimaxi.com/v1
```

注意：`MINIMAX_API_KEY` 是 litellm minimax provider 内部读取的，不是 Hindsight 前缀。

### local 模式 bug

Hindsight local Embedded 模式遇到上游 bug 无法解决（2026-05-10）。Nick 考虑切换到 Cloud 模式，需去 https://ui.hindsight.vectorize.io 注册获取 API Key。

## 服务端口

- Hindsight Test: `http://localhost:8888`
- Hindsight Local: `http://localhost:18792`

## 记忆流程

1. 外部脚本（Python）调用 MiniMax API 提取关键事实
2. 通过 REST API 存入 Hindsight
3. Hindsight 仅负责存储，不负责 LLM 解析

## 相关记忆

- 2026-05-10: Hindsight 对接 MiniMax LLM 成功，解决端点路径问题
- 根因是 minimax provider 的 OpenAICompatibleLLM 使用了错误端点
