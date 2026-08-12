---
title: cc-analyst
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - https://github.com/shayke-cohen/cc-analyst
  - raw/articles/shayke-cohen-cc-analyst.md
tags:
  - memory
  - ingest
  - session-extraction
  - claude-code
  - codex
  - claude-md
  - agents-md
  - skill-patch
  - typescript
---

# cc-analyst

**cc-analyst** 是 [shayke-cohen](https://github.com/shayke-cohen) 的 Claude Code/Codex session log 提取+分析工具。**专做一件事：把 JSONL session → agent 分析 → 提议 patch `CLAUDE.md` / `AGENTS.md` / skill 文件**，带 backup/rollback 和 insight packs 分享。

## 两个可插拔轴

| Axis | 取值 | 默认 |
|------|------|------|
| `--source`（提取什么）| `claude-code`, `codex`, `all` | `all` |
| `--engine`（谁来分析）| `claude-code`, `codex` | `claude-code` |

> source 和 engine 完全独立——可以用 Codex 引擎分析 Claude Code session，或反之。4 种组合均端到端验证。

## 工作流（6 步）

```bash
# 1. 列出发现的项目
npx tsx src/index.ts projects

# 2. 提取 session 到 JSON（纯本地，无 API 调用）
npx tsx src/index.ts extract --source all --output sessions.json

# 3. 分析某个项目（READ-ONLY，写报告不 patch）
npx tsx src/index.ts analyze --project my-api --output ./out

# 4. 预览提议的 patch
npx tsx src/index.ts analyze --project my-api --output ./out --apply --dry-run

# 5. 交互式 apply（带 backup）
npx tsx src/index.ts analyze --project my-api --output ./out --apply

# 6. 回滚最近一次 apply
npx tsx src/index.ts rollback
```

> ⚠️ **重要**：`analyze` 不带 `--apply` 时**只写报告**，**永远不会**修改 `CLAUDE.md` / `AGENTS.md` / skill 文件。

## 输出结构

```
out/
├── README.md              # 运行索引（项目表 + 所有报告链接）
├── analysis.json          # 完整结构化 pipeline 输出
├── feedback.json          # 开发者教练报告（raw）
├── feedback.md            # 开发者教练报告（rendered）
├── stats.md               # 聚合指标：top tools、peak hours、error rate
├── cross-project.json     # 共享规则 + 分歧（≥2 个项目时生成）
```

## 提取的洞察类型

- 你反复做的 pattern（"always use msw for HTTP mocks"）
- 值得 codify 成 skill 的多步 workflow
- `CLAUDE.md` / `AGENTS.md` 的精确 edit 建议
- 每条建议带 **evidence** + **confidence level** + **rollback-safe apply** 步骤

## 安装

```bash
git clone https://github.com/shayke-cohen/cc-analyst.git
cd cc-analyst
npm install --legacy-peer-deps     # Node ≥ 20
# Claude engine: ANTHROPIC_API_KEY
# Codex engine: codex CLI + codex login
```

## 与同类对比

| 维度 | cc-analyst | [[reflect-skill-claude]] | [[Open-Amnesia|Open Amnesia]] |
|------|-----------|--------------------------|------------------|
| **输入** | JSONL session | Claude Code session（in-memory）| 多源 |
| **输出** | patch 提议（带 evidence）| skill 文件 + CLAUDE.md | 日期 Markdown + DB |
| **Patch 目标** | CLAUDE.md / AGENTS.md / skill | CLAUDE.md / skill | 无（消费侧）|
| **回滚** | ✅ rollback 命令 | ❌ | ❌ |
| **多平台** | Claude Code + Codex | 仅 Claude Code | Codex + Claude + iMessage |
| **LLM 分析** | 是（独立 engine）| 是（Claude 自身）| 是（enrichment）|
| **Stars** | 0 | 10 | 30 |

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式
- [[Open-Amnesia|Open Amnesia]] — 同类但多源 + 日期导出
- [[reflect-skill-claude]] — 同类但更轻
- [[ccflash]] — 同类但导出为 Anki 卡片
