---
title: superpowers-openspec-team-skills
type: entity
subtype: AI-coding-workflow
created: 2026-04-28
updated: 2026-04-28
sources:
  - github:SYZ-Coder/superpowers-openspec-team-skills
tags:
  - workflow
  - superpowers
  - openspec
  - multi-agent
  - team-workflow
  - skill-pack
---

# superpowers-openspec-team-skills

SYZ-Coder 维护的团队协作 skill 库，提供 superpowers + OpenSpec 组合的预构建工作流包，支持 Codex / Cursor / Claude Code 三种工具。

## 核心定位

将 superpowers 和 OpenSpec 的方法论封装成**显式 opt-in 工作流**，团队可以按需组合使用。核心原则：**先澄清再实现，先锁定再动手**。

## 与标准 superpowers 的差异

| 维度 | 标准 superpowers | 本仓库 |
|------|-----------------|--------|
| 工具适配 | 无 | `dist/` 下预构建 Codex/Cursor/Claude Code 三个工具的 bundle |
| 工作流 | 单 skill | `team-skills/` 源码 + `dist/` 发行层分离 |
| 团队记忆 | 无 | `.superpowers-memory/` 跨会话记忆模式 |
| 安装方式 | 手动 copy | `scripts/install-*.sh` 脚本自动安装 |

## 五个核心工作流

### 1. openspec-superpowers（推荐入口）
完整全流程：clarification → OpenSpec 锁定 → verification

### 2. superpowers-openspec-execution（四步固定路径）
Superpowers 探索 → OpenSpec 锁定 → Superpowers 执行 → OpenSpec 归档

### 3. superpowers-feature（无 OpenSpec 产物）
只做 superpowers 工程规范，不生成 OpenSpec change artifacts

### 4. openspec-feature（无实现）
只创建和完成 OpenSpec 变更规范，不进入实现

### 5. superpowers-learning（收尾）
沉淀会话成果、经验教训到项目记忆中

## 仓库结构

```
team-skills/   ← 源码工作流定义（维护者用）
dist/          ← 预构建工具适配包
  ├── codex/bundles/
  ├── cursor/bundles/
  └── claude-code/bundles/
scripts/       ← 安装脚本
```

## 显式激活模式

默认不激活，用户必须**显式命名工作流**才会触发：

```
Use $openspec-superpowers-workflow to run this feature.
```

## 跨会话记忆模式

当项目含 `.superpowers-memory/` 时，工作流会：
- 读取 `PROJECT_CONTEXT.md` 了解稳定项目事实
- 读取 `CURRENT_STATE.md` 了解最新工作上下文
- 读取 `DECISIONS.md` 和 `KNOWN_FAILURES.md`
- 在会话结束前更新相关记忆文件

## 依赖

- OpenSpec CLI（使用 OpenSpec 相关工作流时）
- 目标项目 git 仓库（可写入 design docs / plans / OpenSpec changes / code）

## 安装

```bash
# Codex
sh scripts/install-codex.sh --bundle openspec-superpowers --codex-home "$HOME/.codex"

# Cursor
sh scripts/install-cursor.sh --bundle openspec-superpowers --project-root <project>

# Claude Code
sh scripts/install-claude-code.sh --bundle openspec-superpowers --project-root <project>
```

## 与其他仓库的关系

- 依赖 [tielei60/superpowers-openspec] 提供中文 OpenSpec 路由
- 依赖 [beautifulSoup/speccoding-template] 提供完整项目模板
- 与 [pas-workflow-SD-OpenSpec] 的关系：后者是 PAS-AI 的定制落地版，增加了 SD 文档强制生成流程

[pas-workflow-SD-OpenSpec]: ../concepts/pas-workflow-SD-OpenSpec.md
