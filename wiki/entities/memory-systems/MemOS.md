---
title: MemOS
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/memtensor-memos.md
tags:
  - memory
  - open-source
  - model
  - optimization
confidence: high
---
# MemOS（MemTensor）

> **10,686 ⭐ / Apache-2.0 / arXiv [2507.03724](https://arxiv.org/abs/2507.03724)**。"Memory Operating System for LLM & AI Agents"，2.0 版代号 Stardust（星尘）。**本调研中除 [[Hindsight]] 外 star 最高的记忆系统。**

## 定位

统一 **store / retrieve / manage** 的长期记忆操作系统，含 KB、多模态、tool memory 和企业级优化。声称 **35.24% token 节省**。

## 关键特性

| 特性 | 说明 |
|------|------|
| **Unified Memory API** | 单一 API 做 add/retrieve/edit/delete；结构是**图**，设计上可检视可编辑，**不是黑箱 embedding store** |
| **Multi-Modal Memory** | 原生支持 text / images / **tool traces** / personas，统一检索推理 |
| **Multi-Cube KB** | 多知识库作为可组合的 memory cube，支持隔离、受控共享、跨 user/project/agent 动态组合 |
| **MemScheduler** | 异步摄入，毫秒级延迟，高并发下稳定 |
| **Memory Feedback** | 用**自然语言反馈**修正记忆——纠正、补充、替换 |

## 为什么与本主题相关

`tool traces` 作为一等公民的多模态记忆——这正是 session mining 的原料。加上 **MemScheduler 异步摄入**，架构上支持「session 结束后台消化」的模式（与 [[Letta]] 的 sleep-time agent 同思路）。

## 生态

同团队还出了 [[MemRL]]（runtime RL on episodic memory）和 [Awesome-AI-Memory](https://github.com/IAAR-Shanghai/Awesome-AI-Memory) 资源库。

## 与本 wiki 已收录记忆系统对比

| 项目 | ⭐ | 结构 | 特色 |
|------|-----|------|------|
| [[Hindsight]] | 19,693 | 三层仿生 | Nick 当前部署 |
| **MemOS** | 10,686 | 图 + cube | 可编辑非黑箱、tool trace |
| [[Supermemory]] | — | 图 | 基准 SOTA |
| [[Mem0]] | — | 向量+图 | Claude Code 集成最深 |

## 相关页面

- [[Memory-Systems]] — 总览
- [[MemRL]] — 同团队的 RL 路线
- [[Skill-Distillation-Research]] — 学术层
