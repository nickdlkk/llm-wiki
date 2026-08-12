---
title: total-recall
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/davegoldblatt-total-recall.md
tags:
  - memory
  - open-source
  - session-extraction
  - concept
confidence: high
---
# Total Recall（davegoldblatt）

> **200 ⭐ / Shell**。Claude Code 的分层记忆插件。**核心贡献不是架构，而是「写入门控（write gate）」这个反向设计**：不是想办法多记，而是想办法**少记**。

## 核心问题意识

> 它给 Claude Code 一个跨 session 的记事本，但是一个**挑剔的**记事本。不是什么都能进。任何东西要永久保存前，必须过一个简单测试：**这件事明天还重要吗？**

## 四层存储（厨房比喻）

| 层 | 路径 | 定位 |
|----|------|------|
| **Counter（台面）** | `CLAUDE.local.md` | 每天都要用的。**每 session 自动加载**，故意保持小——约一页 |
| **Pantry（储藏柜）** | `memory/registers/` | 按类目组织：preferences / decisions / project details / people。Claude 需要时才查 |
| **Daily notebook（日记本）** | `memory/daily/` | 每日带时间戳的草稿。**所有东西先写这里** |
| **Storage closet（储物间）** | `memory/archive/` | 过期/完成的。仍可搜，但**永不自动加载** |

## 关键设计：人类掌握晋升权

> 笔记先进日记本，**然后由你提升**那些值得留的。Claude **不能自行决定**什么重要到值得永久记住。你决定。

这是与本 wiki 已收录项目的根本分歧点：

| 项目 | 晋升决策者 |
|------|-----------|
| **Total Recall** | **人类**（显式 promote）|
| [[open-second-brain]] | **确定性算法**（evidence count 计数器）|
| [[reflect-skill-claude]] | LLM 提议 + 人类逐项批准 |
| [[claude-mem-thedotmack]] | LLM 全自动 |
| [[cc-analyst]] | LLM 提议 + dry-run + rollback |

## 为什么这条设计重要

绝大多数 session-mining 项目的失败模式是「**记忆变垃圾场**」——记的越多，检索质量越差，注入的 context 越噪。Total Recall 用写入门控直接从源头解决，代价是需要人参与。

对本 wiki 的 MEMORY.md（2200 字符硬上限）而言，这是同一问题的另一种解法：**上限逼迫淘汰** vs **门控阻止进入**。

## 安装

```bash
/plugin marketplace add davegoldblatt/recall-marketplace
/plugin install recall@recall-marketplace
```

## 同名项目注意

另有 `strvmarv/total-recall`（13 ⭐ / C#），是 TUI coding assistant 的多层记忆 + KB 插件，支持 Claude Code / Copilot CLI / OpenCode / Cline / Cursor，本地 sqlite-vec + BM25 融合排序，含 hot→warm→cold 自动 compact。**两者无关联，勿混淆。**

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览（设计权衡一节）
- [[open-second-brain]] — 确定性晋升的对照实现
- [[reflect-skill-claude]] — 保守晋升 + 人工批准
