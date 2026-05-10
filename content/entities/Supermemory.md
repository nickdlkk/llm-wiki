---
title: Supermemory (mem0)
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://github.com/supermemoryai/supermemory
  - https://supermemory.ai
tags:
  - memory
  - research
  - RAG
  - benchmarks
---

# Supermemory (mem0)

**Supermemory** 是 [supermemory.ai](https://supermemory.ai) 研究实验室构建的 SOTA 记忆与上下文引擎。在 **LongMemEval**、**LoCoMo**、**ConvoMem** 三大 AI 记忆基准上均排名第一。

> 注：Supermemory 早期名为 **mem0**，GitHub 仓库仍保留 `supermemoryai/supermemory` 名称，npm 包名为 `supermemory`。

## 核心能力

| 能力 | 说明 |
|------|------|
| 🧠 **记忆提取** | 从对话中提取事实，处理时序变化、矛盾和自动遗忘 |
| 👤 **用户画像** | 自动维护用户上下文 — 稳定事实 + 最近活动，单次调用 ~50ms |
| 🔍 **混合搜索** | RAG + Memory 一体化查询，知识库文档与个性化上下文同时返回 |
| 🔌 **连接器** | Google Drive · Gmail · Notion · OneDrive · GitHub — 实时 webhook 同步 |
| 📄 **多模态提取** | PDF、图片（OCR）、视频（转录）、代码（AST 感知分块）|

## 技术架构

```
用户对话 / 文档
    ↓
提取层（Extractor）→ 事实 + 关系
    ↓
记忆图（Memory Graph）→ 事实版本链、矛盾关系
    ↓
混合检索（Hybrid Search）→ 向量 + BM25 + Rerank
    ↓
上下文注入 → LLM
```

## 基准成绩

| 基准 | 成绩 | 说明 |
|------|------|------|
| LongMemEval | #1 | 长对话记忆评测 |
| LoCoMo | #1 | Stanford 基准，~20K tokens，35 会话 |
| ConvoMem | #1 | Salesforce 对话记忆基准 |

## SDK 与集成

Supermemory 提供多语言 SDK 和 Agent 框架集成：

- **TypeScript/JS**: `@supermemory/tools`（AI SDK、OpenAI、Claude Memory、Mastra、Voltagent）
- **Python**: `supermemory` 包（OpenAI SDK、Pipecat、Cartesia Voice、Mastra）
- **MCP Server**: 通过 Model Context Protocol 与 Claude Desktop 无缝集成
- **连接器生态**: Notion、Google Drive、Gmail、GitHub 等

## 应用场景

- **个人记忆**: 跨对话记住用户偏好、项目背景、历史讨论
- **团队知识管理**: 共享记忆，Agent 学会团队上下文
- **RAG 增强**: 将个性化记忆与知识库检索融合

## 关键指标

| 指标 | 值 |
|------|-----|
| LoCoMo 排名 | #1（State-of-the-art）|
| 内存查询延迟 | ~50ms |
| 支持连接器 | Notion / Drive / Gmail / GitHub / OneDrive |
| SDK 语言 | TypeScript、Python |
