---
title: "ruflo"
type: entity
created: 2026-05-09
updated: 2026-05-15
sources: ["raw/refs/ruflo/README.md", "raw/refs/ruflo/package.json", "raw/refs/ruflo/CLAUDE.md"]
tags: [multi-agent, Claude-Code, MCP, swarm, orchestration, vector-memory, federation]
---

# ruflo

> GitHub: [ruvnet/ruflo](https://github.com/ruvnet/ruflo)
> 原名 Claude Flow，v3.5 (2026-02) 更名为 Ruflo。MIT License。

**Multi-agent AI orchestration for Claude Code** — 让 100+ 专业 agents 跨机器、跨团队、跨信任边界协作的编排框架。

## 基本信息

- **作者**: RuvNet（[@rUv](https://ruv.io)）
- **最新版本**: 3.7.0-alpha.18
- **License**: MIT
- **入口**: `npx ruflo@latest init` 或 Claude Code 插件市场安装
- **核心依赖**: Node.js ≥20，@claude-flow/cli、@claude-flow/mcp、@claude-flow/neural、@claude-flow/shared

## 核心架构

```
User --> CLI/MCP/Plugin --> Router --> Swarm --> 100+ Agents
                                ^                |
                                +--- Memory <----+
                                           |
                    AgentDB (HNSW) <--+    +--> Federation
                           SONA     <-------+
```

**三层模型路由（ADR-026）:**

| Tier | Handler | Latency | Cost | 用途 |
|------|---------|---------|------|------|
| 1 | Agent Booster (WASM) | <1ms | $0 | 简单 transform（var→const、加类型等）|
| 2 | Haiku | ~500ms | $0.0002 | 低复杂度任务（<30%）|
| 3 | Sonnet/Opus | 2-5s | $0.003-0.015 | 复杂推理/架构/安全（>30%）|

## 核心子系统

### 1. Swarm Orchestration（蜂群协调）
- **拓扑**: hierarchical / mesh / adaptive
- **共识算法**: Raft（leader 维护权威状态）
- **策略**: specialized（角色边界清晰）、development
- **Anti-drift**: 频繁 checkpoint + post-task hooks

### 2. Vector Memory（向量记忆）
- **引擎**: AgentDB + HNSW 索引（150x-12,500x 快于 brute force）
- **SONA**: 自学习神经模式，从轨迹中提取成功 pattern
- **命名空间**: patterns / results / knowledge-graph

### 3. MCP Server（~210 工具）
5 个工具服务器组：Core、Intelligence、Agents、Memory、DevTools

关键 MCP 工具：
- `memory_store` / `memory_search` — 持久记忆读写
- `swarm_init` / `swarm_start` / `agent_spawn` — 蜂群生命周期
- `agentic_router_*` — 模型路由
- `ruv-swarm_*` — RuVocal 特有工具

### 4. Agent Federation（跨节点协作）
Zero-trust 安全架构：
- mTLS + ed25519 身份认证（无 API key 共享）
- 14 类型 PII 检测 → 4 级策略（BLOCK/REDACT/HASH/PASS）
- 行为信任评分：`0.4×success + 0.2×uptime + 0.2×threat + 0.2×integrity`
- 合规模式：HIPAA / SOC2 / GDPR

### 5. RuVocal Web UI
- 自托管多模型聊天 UI（flo.ruv.io 托管演示）
- ~210 MCP 工具并行调用（一个 model response 可触发 4-6+ 工具）
- 支持自建 MCP 服务器（HTTP/SSE/stdio）
- Docker 一键部署（含嵌入式 MongoDB）

### 6. GOAP Goal Planner
- goal.ruv.io — 目标导向动作规划（GOAP）UI
- A* 搜索状态空间 → 可视化计划树
- 失败时从当前状态 replan，不从头开始

## 32 插件生态

| 类别 | 插件 |
|------|------|
| **Core** | ruflo-core, ruflo-swarm, ruflo-autopilot, ruflo-loop-workers, ruflo-workflows, ruflo-federation |
| **Memory** | ruflo-agentdb, ruflo-rag-memory, ruflo-rvf, ruflo-ruvector, ruflo-knowledge-graph |
| **Intelligence** | ruflo-intelligence, ruflo-daa, ruflo-ruvllm, ruflo-goals |
| **Code Quality** | ruflo-testgen, ruflo-browser, ruflo-jujutsu, ruflo-docs |
| **Security** | ruflo-security-audit, ruflo-aidefence |
| **Architecture** | ruflo-adr, ruflo-ddd, ruflo-sparc |
| **DevOps** | ruflo-migrations, ruflo-observability, ruflo-cost-tracker |
| **Extensibility** | ruflo-wasm, ruflo-plugin-creator |
| **Domain** | ruflo-iot-cognitum, ruflo-neural-trader, ruflo-market-data |

## 关键文件

```
raw/refs/ruflo/
  CLAUDE.md          — Agent 行为规则（6,000+ commits 总结）
  AGENTS.md          — Swarm 命令速查表
  CHANGELOG.md       — 版本演进历史
  package.json       — npm 包定义（claude-flow@3.7.0-alpha.18）
  bin/cli.js         — CLI 入口
  v3/@claude-flow/  — monorepo 核心包
    cli/             — 26 命令 CLI
    codex/           — Claude + Codex 双引擎协作
    guidance/        — 治理控制平面
    hooks/           — 17 hooks + 12 workers
    memory/          — AgentDB + HNSW
    security/        — 输入验证 / CVE 修复
    swarm/           — 蜂群协调
  plugins/           — 32 插件目录
  ruflo/src/ruvocal/ — Web UI（Svelte）
  v3/goal_ui/        — GOAP Planner UI（Vite + Supabase）
```

## vs 类似工具

| 维度 | ruflo | Hermes |
|------|-------|--------|
| 定位 | Claude Code 扩展包 | 独立 Agent 框架 |
| 插件生态 | 32 专用插件 | 自定义 Skill |
| 联邦 | 原生 zero-trust federation | 无内置 |
| 记忆 | AgentDB + HNSW + SONA | Agent Memory |
| Web UI | RuVocal + GOAP Planner | 无 |
| License | MIT | Nous Research |

## 安装方式

```bash
# Path A: Claude Code 插件（仅 slash commands，零文件侵入）
/plugin marketplace add ruvnet/ruflo
/plugin install ruflo-core@ruflo
/plugin install ruflo-swarm@ruflo

# Path B: CLI 完整安装
npx ruflo@latest init
# 或
curl -fsSL https://cdn.jsdelivr.net/gh/ruvnet/ruflo@main/scripts/install.sh | bash
```

## MCP 接入

```bash
# 作为 MCP server 接入 Claude Code
claude mcp add ruflo -- npx ruflo@latest mcp start
```
