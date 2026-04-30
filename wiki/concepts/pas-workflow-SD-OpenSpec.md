---
title: PAS-AI SD + OpenSpec 工作流
type: concept
created: 2026-04-28
updated: 2026-04-28
sources:
  - local:D:\PAS-AI\.claude\skills\pas-workflow-SD-OpenSpec
tags:
  - workflow
  - openspec
  - sd
  - pas-ai
  - superpowers
  - skill-chain
---

# PAS-AI SD + OpenSpec 工作流

PAS-AI 项目专属的 SD 文档 + OpenSpec 端到端工作流，是 [superpowers-openspec-workflow] 的 PAS-AI 落地实现版本。

## 与标准 superpowers-openspec 的差异

| 维度 | 标准 superpowers-openspec | PAS-AI 落地版 |
|------|--------------------------|---------------|
| SD 文档 | 无专门流程 | Phase 2 强制生成 SD 文档，人审通过后才能进 Phase 3 |
| 文档存放 | `docs/solutions/` 临时目录 | `openspec/changes/<name>/working-refs/` 受 git 管理 |
| 变更管理 | OpenSpec 标准流程 | 新增需求变更管理工作流（轻/中/重大三类型） |
| 归档内容 | OpenSpec 产物 | 归档包含完整 working-refs（SD 文档 + CodeAnalyze） |

## 六阶段链路

| Phase | 操作 | 产出位置 |
|-------|------|----------|
| 1. 需求澄清 | superpowers:brainstorming | `openspec/changes/<name>/working-refs/` |
| 2. SD 文档生成 | pas-SD-概要设计 skill | `openspec/changes/<name>/working-refs/sd-doc/` |
| 3. OpenSpec 初始化 | `openspec new change` | OpenSpec 变更骨架 + 合并 SD 到 design.md |
| 4. 制定实现计划 | superpowers:writing-plans | `tasks.md` |
| 5. subagent 执行 | superpowers:subagent-driven-development | 代码实现 |
| 6. 收尾归档 | finishing-a-development-branch + `openspec archive` | `archive/YYYY-MM-DD-<name>/` |

## 目录结构

```
openspec/changes/<name>/
├── proposal.md              <- 变更提案
├── design.md               <- 设计（头部引用 SD 文档来源）
├── tasks.md                <- 实现任务清单
├── specs/                  <- 场景规格
└── working-refs/           <- 开发过程文档（git 管理）
    ├── sd-doc/             <- SD 文档（人审通过）
    └── code-analyze/        <- CodeAnalyze 文档
```

## 需求变更管理

Phase 5 执行过程中收到新需求时，根据影响范围分三种处理方式：

| 变更类型 | 影响范围 | 处理方式 |
|----------|----------|----------|
| **轻量变更** | 只增不减，不改已有设计 | 直接插入 tasks.md，继续执行 |
| **中等变更** | 需修改已有任务产出文件 | 暂停 → 更新 design/tasks → 继续 |
| **重大变更** | 推翻核心设计决策 | 归档当前 → 新建 change → 从 Phase 2 重来 |

### 变更评估决策树

```
新需求来了
    ↓
评估：新需求修改了哪些已有设计？
    ├─ 不涉及任何已有设计文件  → 轻量变更
    ├─ 涉及已完成任务的产出文件
    │   └─ 需重做已完成任务？ → 中等变更
    └─ 推翻 spec/design 中的核心决策
        └─ 是 → 重大变更
```

## 核心约束

1. **SD 文档必须人审通过后才能进入 Phase 3** — 人给指令，AI 写文档
2. **任务必须文件级别独立** — 避免 subagent 执行时文件冲突
3. **所有工作文档在 openspec/ 下** — git 管理，不会丢失
4. **归档包含完整 working-refs** — 过程文档不丢失
5. **design.md 头部引用 SD 文档来源** — 追溯关系清晰

## 触发条件

满足任一情况时进入本工作流：

- 用户要求执行完整链路：SD 文档 → OpenSpec → 实现 → 归档
- 用户要求"先写 SD 文档，审批后再做"
- 用户在 brainstorming 后要求继续执行完整工作流
- 用户要求按本工作流执行任意阶段

## Skill 文件位置

```
D:\PAS-AI\.claude\skills\pas-workflow-SD-OpenSpec\
├── SKILL.md
└── references/
    ├── workflow-diagram.md
    ├── sd-skill-usage.md
    ├── task-split-principles.md
    ├── subagent-usage.md
    ├── doc-folder-layout.md
    └── change-management.md
```

## 相关文档

- [superpowers-openspec-workflow] — 标准 superpowers + OpenSpec 工作流
- [planning-with-files] — 规划文件技能（本工作流不使用，tasks.md 已够用）
- `D:\PAS-AI\README-TOOL.md` — PAS-AI 工具与工作流说明

[superpowers-openspec-workflow]: ./superpowers-openspec-workflow.md
[planning-with-files]: ./planning-with-files.md
