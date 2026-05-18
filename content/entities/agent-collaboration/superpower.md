---
title: Superpowers
type: entity
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - skills
  - TDD
  - workflow
  - subagent
  - planning
  - brainstorming
  - AI-coding
  - SDLC
---

# Superpowers

> GitHub: [obra/superpowers](https://github.com/obra/superpowers) · 195,609 ⭐ · MIT License
> 作者：[Jesse Vincent](https://blog.fsck.com)（[Prime Radiant](https://primeradiant.com)）

Superpowers 是一套完整的 **AI Coding Agent 软件开发方法论**，通过 14 个可组合的技能（Skills）让 Agent 在动手编码前先理解需求、制定计划、验证结果。核心理念：**Spec First + TDD + 双阶段审查**。

## 核心工作流

```
brainstorming → writing-plans → subagent-driven-development / executing-plans
                → test-driven-development → requesting-code-review
                → finishing-a-development-branch
```

**Phase 1（用户授权前不动代码）：**
1. `brainstorming` — 通过苏格拉底式追问澄清需求，产出设计文档
2. `writing-plans` — 将设计拆解为 2-5 分钟粒度的可执行任务

**Phase 2（批准后执行）：**
3. `subagent-driven-development` / `executing-plans` — 按任务批次执行，TDD 小步推进
4. `finishing-a-development-branch` — 验证测试后提交 merge/PR/keep 选项

## 14 个技能总览

| # | Skill | 职责 | 触发时机 | 类型 |
|---|-------|------|---------|------|
| 1 | [[concepts/superpowers/brainstorming]] | 苏格拉底式需求澄清，产出设计文档 | **编码前必须**（HARD-GATE） | 流程规范 |
| 2 | [[concepts/superpowers/writing-plans]] | 将设计拆解为分钟级可执行任务 | 设计批准后 | 执行技能 |
| 3 | [[concepts/superpowers/subagent-driven-development]] | 每个任务派发 fresh subagent + 两阶段审查 | 计划就绪后 | 执行技能 |
| 4 | [[concepts/superpowers/executing-plans]] | 批次执行 + 人工检查点（无 subagent 时备选） | 计划就绪后 | 执行技能 |
| 5 | [[concepts/superpowers/test-driven-development]] | RED-GREEN-REFACTOR 循环，铁律：无不测试代码 | 实现过程中 | 流程规范 |
| 6 | [[concepts/superpowers/systematic-debugging]] | 4 阶段根因分析 → 最小修复 | Bug 修复前 | 流程规范 |
| 7 | [[concepts/superpowers/verification-before-completion]] | 证据门禁：未验证不声称通过 | 任务完成前 | 流程规范 |
| 8 | [[concepts/superpowers/dispatching-parallel-agents]] | 独立问题域并行调查 | 多独立失败时 | 协调技能 |
| 9 | [[concepts/superpowers/requesting-code-review]] | 任务间审查检查表 | 任务间 | 流程规范 |
| 10 | [[concepts/superpowers/receiving-code-review]] | 技术核实后实施，非表演性同意 | 收到反馈时 | 流程规范 |
| 11 | [[concepts/superpowers/finishing-a-development-branch]] | 测试验证 → 四选项 → 清理 worktree | 任务完成后 | 流程规范 |
| 12 | [[concepts/superpowers/using-git-worktrees]] | 新分支独立工作区创建 | 设计批准后 | 工程原语 |
| 13 | [[concepts/superpowers/writing-skills]] | TDD 方式编写新技能（RED-GREEN-REFACTOR for docs） | 创建/修改技能时 | 元技能 |
| 14 | [[concepts/superpowers/using-superpowers]] | 技能系统介绍和使用规范 | Agent 启动时 | 流程规范 |

## 设计哲学

| 原则 | 说明 |
|------|------|
| **Test-Driven Development** | 测试先行，无 failing test 不得写生产代码 |
| **Systematic over ad-hoc** | 流程优于猜测，不接受"太简单不需要设计" |
| **Complexity reduction** | 简洁性为首要目标，YAGNI 严格 |
| **Evidence over claims** | 验证通过前不声称完成 |
| **Spec First** | 编码前必须产出设计文档并获用户批准 |

## 与 superpower-with-files 的区别

| 维度 | Superpowers（原版） | superpower-with-files（融合版） |
|------|---------------------|-------------------------------|
| 技能数量 | **14 个** | 15 个（多了 planning-with-files） |
| 记忆机制 | **无持久记忆** | 融合了 planning-with-files |
| 记忆文件 | — | task_plan.md / progress.md / findings.md |
| 平台支持 | Claude Code / Codex / Cursor / OpenCode / Gemini CLI / GitHub Copilot | 同上 |
| TDD 循环 | 原始 RED-GREEN-REFACTOR | 同上 |

## 快速开始

```bash
# Claude Code 官方市场安装
/plugin install superpowers@claude-plugins-official

# 或通过 Superpowers 市场
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace

# Cursor
/add-plugin superpowers

# OpenCode
Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.opencode/INSTALL.md
```

## 社区资源

- **Discord**: [Join](https://discord.gg/35wsABTejz)
- **Issues**: [GitHub Issues](https://github.com/obra/superpowers/issues)
- **Release announcements**: [Subscribe](https://primeradiant.com/superpowers/)
- **Blog**: [Original announcement](https://blog.fsck.com/2025/10/09/superpowers/)
