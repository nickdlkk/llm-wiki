---
title: "superpower-with-files"
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
tags:
  - skills
  - TDD
  - workflow
  - subagent
  - memory
  - planning
  - AI-coding
---

# superpower-with-files

> GitHub: [mok888/superpower-with-files](https://github.com/mok888/superpower-with-files)

superpower-with-files 将 **superpowers**（obra/superpowers，spec-first + TDD 执行框架）与 **planning-with-files**（Manus 风格持久化记忆格式）融合，形成统一的工作流。v2.20.0，MIT License。

## 核心定位

| 组件 | 职责 | 产出 |
|------|------|------|
| **superpowers** | TDD 高效执行框架 | 代码 + 测试循环 |
| **planning-with-files** | 持久化记忆循环 | task_plan.md / findings.md / progress.md |
| **统一框架** | 两者融合 | `.superpower-with-files/` 目录下的完整工作流 |

**设计哲学：**
- **持久 AI 记忆**：文件即工作内存，AI 从不"断片"
- **动态 TDD 循环**：测试先行，最小代码通过，逐步迭代
- **工作区整洁**：所有 AI 日志路由到统一目录，项目根目录保持干净
- **阶段严格分离**：Planning（构思/设计）→ Execution（执行/编码），用户未给"Execute"命令前不动代码

## 15 个子技能总览

| # | Skill | 职责 | 类型 |
|---|-------|------|------|
| 1 | [[superpower-with-files/using-superpowers]] | 技能发现与使用规范 | 流程规范 |
| 2 | [[superpower-with-files/planning-with-files]] | Manus 风格持久化记忆循环 | 流程规范 |
| 3 | [[superpower-with-files/brainstorming]] | 创意 → 设计，需求澄清 | 流程规范 |
| 4 | [[superpower-with-files/spf-write-plan]] | 规格 → 可执行实现计划 | 执行技能 |
| 5 | [[superpower-with-files/spf-exec-plan]] | 按计划批次执行，TDD 小步 | 执行技能 |
| 6 | [[superpower-with-files/test-driven-development]] | 红 → 绿 → 重构 TDD 循环 | 流程规范 |
| 7 | [[superpower-with-files/systematic-debugging]] | 根因分析 → 最小修复 | 流程规范 |
| 8 | [[superpower-with-files/verification-before-completion]] | 证据门禁，伪状态预防 | 流程规范 |
| 9 | [[superpower-with-files/dispatching-parallel-agents]] | 多独立问题并行调查 | 协调技能 |
| 10 | [[superpower-with-files/subagent-driven-development]] | 子 agent 驱动开发，两阶段审查 | 执行技能 |
| 11 | [[superpower-with-files/requesting-code-review]] | 发起代码审查请求 | 流程规范 |
| 12 | [[superpower-with-files/receiving-code-review]] | 处理审查反馈，技术核实 | 流程规范 |
| 13 | [[superpower-with-files/finishing-a-development-branch]] | 完成开发分支，合并/PR/清理选项 | 流程规范 |
| 14 | [[superpower-with-files/using-git-worktrees]] | Git worktree 隔离工作区创建 | 工程原语 |
| 15 | [[superpower-with-files/writing-skills]] | TDD 方式编写新技能 | 元技能 |

## 架构：统一记忆路径

默认保存路径：`<project-root>/.superpower-with-files/`

| 文件 | 用途 | 更新时机 |
|------|------|---------|
| `task_plan.md` | 高层阶段检查清单 + 目标追踪 | 每个阶段后 |
| `active_tdd_plan.md` | 粒度化分钟级执行步骤 | 计划时 |
| `progress.md` | 完整 session 日志、测试结果、错误追踪 | 全程 |
| `findings.md` | 研究、架构决策、关键约束 | 任何发现后 |
| `guides/task-N.md` | 中/复杂项目的详细实现指南 | 计划时 |

**用户可 Override**：直接在 prompt 中指定路径，AI 必须遵守。

## 两阶段工作流

```
Phase 1: Planning（用户授权前不动代码）
  brainstorming → spf-write-plan → 等待用户批准

Phase 2: Execution（批准后）
  spf-exec-plan / subagent-driven-development → finishing-a-development-branch
```

## 核心原则

### Iron Law of TDD
> "没有 failing test 不得写 production code"

### Iron Law of Debugging
> "未完成根因调查不得尝试修复"

### Iron Law of Verification
> "未运行验证命令不得声称通过"

### Superplanner Memory Integration
每次修改记忆文件必须追加时间戳：
```markdown
---
*Last Updated: YYYY-MM-DD HH:MM UTC*
```

## 与 superpowers-zh 的区别

| 维度 | superpower-with-files | superpowers-zh |
|------|---------------------|----------------|
| 子 skill 数量 | **15 个（全部详细定义）** | 6 个中文新增 |
| 记忆机制 | **planning-with-files 融合** | 无持久记忆 |
| TDD 执行 | **SPF 系列（spf-write/exec-plan）** | 原始 executing-plans |
| 子 agent | **两阶段审查（spec → code quality）** | 无 |
| 并行调查 | **dispatching-parallel-agents** | 无 |
| 平台支持 | Claude Code / Cursor / OpenCode / OpenClaw / Codex / Gemini CLI | 17 款工具 |

## 快速开始

```bash
git clone https://github.com/mok888/superpower-with-files.git
# 将 AI agent 指向 /skills 文件夹

# Phase 1: 规划
# 使用 spf-write-plan 创建计划
"Create a plan for the <feature>. Use medium complexity."

# Phase 2: 执行
# 批准后使用 spf-execute 执行
"Execute the plan."
```
