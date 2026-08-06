---
title: "Dispatcher State Machine + File Handoff"
date: 2026-06-15
tags: [harness-engineering, agent-orchestration, dispatcher, state-machine]
type: concept
created: 2026-06-15
updated: 2026-06-15
sources:
  - raw/articles/harness-engineering-practice-2026.md
  - summaries/harness-engineering-ai-discipline-20260615.md
---

# Dispatcher State Machine + File Handoff

## What it is

一种**轻量级多 Agent 编排模式**：不隔离进程，通过 **dispatcher 状态机** + **文件系统交接** 实现类似"脑机分离"的效果。

核心设计：
1. **dispatcher**（流程驱动器）只负责"算下一步该谁上场"，交通警察，不管业务
2. 其他 agent 各司其职，通过文件 `phases/*.md` 交接信息
3. 主会话退化为"纯执行器"，只听 dispatcher 指令

## vs Claude Code 原生机制

| 机制 | 判定 | 硬伤 |
|------|------|------|
| **Workflow** | 不适合做控制平面 | 超时无法区分失败/被杀；无 askUser 原语；跨 session 不可续 |
| **Agent Team** | 不适合严格工序链 | 松散协调无确定性；状态散落；SendMessage 是通知不是阻断 |
| **dispatcher + 文件交接** ✅ | 适合有状态工序链 | 天然持久化 + 可审计 + 强一致性 |

## 三个硬优势

1. **天然持久化** — 进程崩了文件还在，跨天需求 `Read state.json` 即续
2. **可审计** — 每步产物都是人可读的 markdown，`git diff` 一眼看清谁在哪步写了什么
3. **强一致性** — state-keeper 单写者（hook 拦截其他写者）+ ajv schema 校验前置

## 三条铁律落地

```
1. 主会话只听 dispatcher
   dispatcher 读 state.json 返回"下一步调谁"，主会话照做
   禁止自己 Read phases/*.md / evidence.json

2. 职责隔离
   dispatcher 只管路由、orchestrator 只管合成、
   developer 只管编码、verifier 只管检查
   每个 agent 的可用工具严格受限

3. 上下文 ≤8K
   主会话只加载 CLAUDE.md + 触发规则 + 最近一条 dispatcher 指令
```

## 19 节点链路

```
需求评审→需求确认→方案设计→方案确认→Pre-Mortem→实施计划→验收标准确认
→拉变更→建分支→建 worktree→开发→编译→单测→ATDD→证据链
→部署预发→接口测试→上线确认→验收报告
```

由 **意图 × 风险** 动态裁剪——不是每个需求都走全 19 步。

## 关键研究支撑

- **arxiv 2605.29682**：验证反馈质量（Effective Feedback Compute）R²=0.94~0.99，远高于原始 token 消耗的 R²=0.33~0.42
- **sd0x-dev-flow**：四个关键词 "hook-enforced dual review, state-machine gates that survive context compaction, and fail-closed safety"
- **Apache Burr**：状态机节点 + 可插拔持久化 + 实时追踪 UI

## 代价

- 每次 agent 切换需 Read 上一步产物（~2-5K tokens IO 开销）
- 调试链路跨多个 agent 的 transcript
- 并行能力受限于文件交接的序列化特性

## 混合编排方向

三种机制正交互补：
- **Workflow** 管计算平面（高并行单阶段）
- **Team** 管协作平面（多人独立任务）
- **dispatcher + 文件交接** 管控制平面（有状态工序链 + 人工门禁 + 跨天续跑）

当前实验方向：dispatcher 管控制流，Workflow 加速三角色评审等纯计算环节。
