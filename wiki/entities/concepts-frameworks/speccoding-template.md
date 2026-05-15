---
title: speccoding-template
type: entity
created: 2026-04-28
updated: 2026-04-28
sources:
  - https://github.com/beautifulSoup/speccoding-template
tags:
  - SDD
  - template
  - openspec
  - superpowers
  - fullstack
  - AI-coding
---

# speccoding-template

**beautifulSoup/speccoding-template** — 基于 Claude Code + OpenSpec + Superpowers 三件套的**全栈 AI 开发模板**。让 AI 稳定交付全栈项目，告别"AI 改崩代码 / 失忆 / 跑偏"。MIT License。

## 核心定位

可直接 Clone 即用的项目骨架，实现文章《让 AI 稳定交付全栈项目》里的完整工作流。包含两级 Spec 体系 + 七阶段工作流 + 三工具协同。

## 两级 Spec 体系

| 层级 | 位置 | 作用 |
|------|------|------|
| 项目级 | `spec/` | 整体需求、设计、任务清单、开发日志 |
| 需求级 | `openspec/changes/<name>/` | 单个变更的提案、设计、规格、任务 |

### spec/ 项目级文档

| 文件 | 作用 |
|------|------|
| `requirements.md` | 项目整体需求 |
| `design.md` | 技术栈、模块划分、数据模型、关键接口 |
| `tasks.md` | 10~30 个里程碑任务 |
| `devlog.md` | 开发日志（每次 PR 合并后追加） |
| `structure.md` | 项目目录结构 |

## 七阶段工作流

```
git branch → openspec scaffold → brainstorming → writing-plans → executing-plans → openspec archive → git merge
```

| 阶段 | 命令 | 说明 |
|------|------|------|
| 1. 创建分支 | `git checkout -b feature/<name>` | 每个变更独立分支 |
| 2. 脚手架 | `openspec-cn new change "<name>"` | 创建变更目录 |
| 3. 头脑风暴 | `/superpowers:brainstorming` | 产出 proposal/design/specs/tasks |
| 4. 制定计划 | `/superpowers:writing-plans` | 产出 plan.md |
| 5. 执行 | `/superpowers:executing-plans` | 按计划执行 |
| 6. 归档 | `/opsx:archive` | 变更移入 archive |
| 7. 合并 | `git merge` | 合并回主分支 |

## 三工具协同

- **Claude Code** — 执行编码
- **OpenSpec** — 规格管理
- **Superpowers** — 流程规范（brainstorming / writing-plans / executing-plans）

## 项目结构

```
backend/          # 后端服务（技术栈自选）
frontend/         # 前端（Web / H5 / App）
prototype/        # 原型设计稿
spec/             # 项目级 spec
openspec/changes/ # 需求级 spec
```
