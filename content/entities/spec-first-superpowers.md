---
title: spec-first-superpowers
type: entity
subtype: AI-coding-workflow
created: 2026-04-28
updated: 2026-04-28
sources:
  - github:zxzvsdcj/spec-first-superpowers
tags:
  - workflow
  - superpowers
  - spec-kit
  - openspec
  - cursor
  - spec-before-code
  - mempalace
  - v5
---

# spec-first-superpowers

Cursor Agent Skill，强制"规范先行"工作流，防止 AI 跳过设计直接写代码。v5 版本整合了 Superpowers + Spec-Kit + OpenSpec 三大规范体系。

## 核心定位

**"先规范再实现"** 的端到端工作流，通过 `/super-spec` 命令激活，覆盖从规范到归档的完整链路。

## 工作流链路

```
/super-spec
  → Mode Selection（Spec-Kit vs OpenSpec）
  → Complexity Triage（quick / standard / thorough）
  → Session Recovery（auto + MemPalace 可选）
  → Specification（G1，含 inline spec review）
  → Persistent Planning（G2，含 inline plan review + file structure mapping）
  → UI/UX Design（G3，conditional，v2.5.0 智能设计系统）
  → TDD Implementation（G4，含 model selection + implementer status）
  → Archive（+ MemPalace persistence）
```

## v5 新特性

| 特性 | 说明 |
|------|------|
| **Inline self-review** | 用快速 inline checklist 替代 subagent review loop（~30s vs ~25min） |
| **MemPalace 集成** | 跨会话记忆 + 知识图谱 + agent diary（可选） |
| **Spec-Kit Workflow Engine** | 自定义工作流注册与发现（v0.7.0+） |
| **OpenSpec `/opsx:refine`** | 定向 artifact review & refinement |
| **ui-ux-pro-max v2.5.0** | +6 specialist skills，Three.js stack，1923 Google Fonts DB |

## 质量门禁 G0-G4

- **G0**: 前置检查（constitution verification）
- **G1**: 规范一致性检查（spec conformance）
- **G2**: 计划有效性检查（plan review）
- **G3**: UI/UX 设计检查（conditional）
- **G4**: 代码质量检查（implementation + TDD）

## 工具选择

| 场景 | 工具 |
|------|------|
| 快速简单项目 | Spec-Kit 模式 |
| 复杂变更管理 | OpenSpec 模式（默认） |
| UI/UX 设计密集 | ui-ux-pro-max v2.5.0 |
| 跨会话记忆 | MemPalace（可选） |

## 命令

```
/super-spec                  # 全流程（auto mode + auto complexity）
/super-spec force-spec-kit   # 强制 Spec-Kit 模式
/super-spec force-openspec   # 强制 OpenSpec 模式
/super-spec reset            # 重置模式选择
/super-spec upgrade          # 检查所有集成项目更新
```

## 依赖集成

| 集成项目 | 版本 | 角色 |
|----------|------|------|
| Spec-Kit | v0.7.1 | GitHub spec-driven 框架 |
| OpenSpec | v1.2.0 | OPSX 工作流（默认） |
| Superpowers | v5.0.7 | TDD + inline review + subagent 方法论 |
| planning-with-files | v2.30.0 | 文件持久化规划 |
| ui-ux-pro-max | v2.5.0 | UI/UX 设计系统 |
| MemPalace | v3.3.0 | 跨会话记忆（可选） |

## 安装

```bash
npx skills add zxzvsdcj/spec-first-superpowers
```

验证：
```bash
npx skills ls -g -a cursor
```

## 与其他仓库的关系

- 与 [superpowers-openspec-team-skills] 同属 superpowers 生态，区别在于后者侧重多工具 bundle + 团队记忆，前者侧重 Cursor 单工具 + v5 inline review
- 与 [pas-workflow-SD-OpenSpec] 的关系：后者是 PAS-AI 定制版，增加了 SD 文档强制人审流程

[superpowers-openspec-team-skills]: ./superpowers-openspec-team-skills.md
[pas-workflow-SD-OpenSpec]: ../concepts/pas-workflow-SD-OpenSpec.md
