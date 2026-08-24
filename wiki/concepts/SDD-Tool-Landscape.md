---
title: SDD 工具全景
type: concept
created: 2026-04-17
updated: 2026-08-24
sources:
  - github:Fission-AI/OpenSpec
  - github:github/spec-kit
  - github:potpie-ai/potpie
  - github:gsd-build/get-shit-done
  - github:sighup/claude-workflow
  - github:spec2ship/spec2ship
  - github:deepeshBodh/human-in-loop
  - github:fntune/swarm
  - github:mindfold-ai/Trellis
  - github:SYZ-Coder/superpowers-openspec-team-skills
  - github:zxzvsdcj/spec-first-superpowers
tags:
  - 规范驱动开发
  - sdd
  - AI 编程 Agent
  - 工具全景
---

# SDD 工具全景

AI 编程 Agent 生态中"规范驱动开发"（Spec-Driven Development, SDD）工具盘点（2026）。这些工具在人类意图与代码生成之间增加了一层规范（spec）—— 降低幻觉、保持一致性、并支持可追溯的开发流程。（共调研 14 款工具）

## 对比矩阵

| 工具 | 焦点 | 语言 | 工作流模型 | 多 Agent | 核心创新 |
|------|------|------|------------|----------|----------|
| **OpenSpec** | 规范 + Delta 合并 | TypeScript | 基于模板的工件驱动 | 26 个工具适配器 | Delta 格式的规范变更 |
| **Spec-Kit** | 工作流 + 集成 | Python | YAML 可编程流水线 | 22 个集成 | 可编程工作流引擎 |
| **Get Shit Done** | 上下文卫生 + 元提示 | TypeScript | 技能系统 | 多 Agent | 防止上下文腐烂 |
| **Potpie** | 从代码库构建知识图谱 | Python | Agent 编排 | 是（基于图） | 索引优先，而非规范优先 |
| **GenericAgent** | 通过技能结晶实现自进化 | Python | 9 个原子工具 + ~100 行循环 | 否 | 真实浏览器、自举启动、<30K 上下文 |
| **Claude-Workflow** | 规范 → 已校验的实现 | Python | 依赖感知任务图 | 并行子 Agent | 完整生命周期 |
| **HumanInLoop** | 规范强制 + 人类检查点 | Python | 确定性 DAG | 基于 MCP | 强制人类参与 |
| **Spec2Ship** | 多角色协商 | TypeScript | 圆桌 Agent 讨论 | 12 个专门角色 | 对抗性规范审查 |
| **Swarm** | 并行 worktree 编排 | Python | YAML 计划 + git worktree | 是（worktree 隔离） | 通过 SQLite 实现崩溃恢复 |
| **Vibe-Skills** | 340+ 技能 + 受治理的运行时 | Python | VCO 6 阶段状态机 | 是（Root/Child 通道） | 129 条治理规则、智能路由 |
| **Trellis** | 规范注入 + 任务工作流 | Node.js + Python | 斜杠命令 + git worktree | 是（14 个平台） | 跨平台规范注入、任务连续性 |
| **superpowers-openspec-team-skills** | 团队的 Superpowers + OpenSpec | 多语言（shell/ps1） | 可选的显式工作流 | 是（Codex/Cursor/Claude） | 工具专用 bundle + 项目记忆 + 跨会话 `.superpowers-memory/` |
| **spec-first-superpowers** | Cursor 的"先规范后代码" | Shell | `/super-spec` G0–G4 质量门 | 基于子 Agent | 内联自审（~30s） + MemPalace 跨会话记忆 + 复杂度分诊 |

## 关键模式

### 规范格式

| 工具 | 格式 | Delta? |
|------|------|--------|
| OpenSpec | Markdown（必须含 SHALL/MUST、场景） | 是 —— ADDED / MODIFIED / REMOVED |
| Spec-Kit | Markdown + frontmatter | 否 —— 每次变更全量规范 |
| GSD | 元提示约定 | 隐式 |
| Claude-Workflow | 结构化规范 + Gherkin | 部分 |
| HumanInLoop | constitution.md + spec.md + plan | 任务即 DAG |
| Spec2Ship | 多角色协商 | 协作起草 |

### 多 Agent 模式

1. **并行子 Agent**（claude-workflow、swarm）—— 基于依赖图的并行执行
2. **角色化协商**（Spec2Ship）—— 多个 Agent 代表不同利益方
3. **基于图的推理**（Potpie）—— Agent 查询知识图谱
4. **工具适配器注册表**（OpenSpec、Spec-Kit）—— 跨多种 AI 工具的统一规范格式
5. **Worktree 隔离**（swarm）—— 用 git worktree 避免并行 Agent 的文件冲突

## 研究空白

- 工具之间没有标准的规范交换格式
- Delta 规范格式（OpenSpec）除 Markdown 约定外没有正式 schema
- 人类参与的强制机制差异很大
- 规范版本控制 / 分支管理未得到妥善处理
- 与形式化验证（TLA+）的集成缺失