---
title: Honcho
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://github.com/plastic-labs/honcho
  - https://github.com/elkimek/honcho-self-hosted
tags:
  - memory
  - agent
  - hermes
  - plastic-labs
---

# Honcho

**Honcho** 是 [Plastic Labs](https://plasticlabs.ai) 开发的记忆层（Memory Layer），为 AI Agent 提供跨会话持久记忆。Hermes Agent 的 L4 记忆层由 Honcho 驱动。

## 核心概念

### Neuromancer 模型

Honcho 默认使用 Plastic Labs 自研的 **Neuromancer XR** 模型（8B 参数，从 Qwen3-8B 微调），专门用于从对话中提取逻辑结论：

- **LoCoMo 基准**: 86.9%（vs Qwen3-8B 基线 69.6%，vs Claude 4 Sonnet 80.0%）
- 训练数据：~10,000 条精选社会推理轨迹
- 专精逻辑：从对话中既提取显式事实（如"用户说他们喜欢 Python"），也提取演绎结论（如"用户很可能是一名开发者"）

### 架构

```
会话 → Honcho Extraction → 记忆存储 → 检索 → 注入上下文
```

- **提取（Extraction）**: 从对话中提取 observations（观察）
- **记忆深化（Deepening）**: 随着时间推移累积对用户的建模
- **一致性（Consolidation）**: 合并相关记忆，消解矛盾

## 部署模式

| 模式 | 隐私 | 数据位置 | LLM | 难度 |
|------|------|----------|-----|------|
| **托管云（默认）** | 低 | Plastic Labs 服务器 | Neuromancer | 无需配置 |
| **自托管 + API** | 中 | 你的机器 | 任意 OpenAI 兼容 API | ~3 分钟 |
| **自托管 + 本地模型** | 高 | 完全本地 | Ollama / vLLM | 更多配置 |

### 自托管版本（Hermes 专用）

[elkimek/honcho-self-hosted](https://github.com/elkimek/honcho-self-hosted) 提供了在自有服务器上部署 Honcho 的配置，无需 fork 上游代码，只需 3 个配置文件即可与 Hermes Agent 无缝集成。

## 与 Hermes 的集成

Hermes Agent 的 4 层记忆系统中，L4（跨会话记忆）由 Honcho 提供。默认使用 Plastic Labs 托管云 + Neuromancer 模型，换用自托管可获得数据主权。

## 关键指标

| 指标 | 值 |
|------|-----|
| LoCoMo 基准 | 86.9%（Neuromancer XR）|
| 基模型 | Qwen3-8B 微调 |
| 训练数据 | ~10,000 社会推理轨迹 |
| 专注方向 | 记忆深化、逻辑结论提取 |

## 相关项目

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) — Honcho 的主要集成目标
- [Neuromancer XR](https://blog.plasticlabs.ai/research/Introducing-Neuromancer-XR) — 专用 8B 记忆推理模型
