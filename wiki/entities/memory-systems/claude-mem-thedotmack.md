---
title: claude-mem
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/thedotmack-claude-mem.md
tags:
  - memory
  - skill
  - session-extraction
  - open-source
  - model
confidence: high
---
# claude-mem（thedotmack）

> **规模之王**：90,459 ⭐ / JavaScript / 活跃（2026-08-12 push）。Claude Code + OpenClaw 生态里装机量最大的「会话 → 长期记忆」插件。

## 定位

`claude-mem` 是 **hook 驱动的 session 压缩器**：不做 wiki，不做 skill 提取，专注把每次 session 的 tool output 压成语义化「观测（observation）」存进本地 SQLite，下次 session 自动注入。

| 维度 | 值 |
|------|-----|
| Stars | 90,459 ⭐（本调研中最大） |
| 语言 | JavaScript |
| 平台 | Claude Code / OpenClaw（一行安装） |
| 存储 | 本地 SQLite + FTS5 |
| i18n | 30+ 语言 README（含简中/繁中） |
| 赞助 | Vercel OSS Program |

## 与 [[Session-Extraction-Pipeline]] 的映射

| Step | claude-mem 的做法 |
|------|-------------------|
| 1 采集 | 6 个生命周期 hook 捕获 tool output |
| 2 标准化 | Claude Agent SDK 压成 ~500 token 语义观测 |
| 3 聚类 | 按 decision / bugfix / feature / refactor / discovery / change 分类 |
| 4 提取 | 语义观测本身即产物（**不提取 skill**）|
| 5 策展 | FTS5 SQLite + web viewer UI + `mem-search` skill |

## 关键设计

- **压缩优先而非提取优先**：不试图归纳 skill，只把原始 tool output 压缩到可注入的体积——工程上更稳，避免 LLM 归纳出伪 skill
- **6 hook 覆盖全生命周期**：比只挂 SessionEnd 的方案（[[claude-memory-compiler]]）覆盖更全，能防 auto-compact 丢数据
- **附带 web viewer**：记忆可视化是留存关键，和 [[claude-code-trace]] 是同一思路

## 与本 wiki 已有项目对比

| 项目 | 提取 skill? | 出 wiki? | Stars |
|------|-------------|----------|-------|
| **claude-mem** | ❌ 只压缩观测 | ❌ | 90,459 |
| [[claude-memory-compiler]] | ⚠️ 出 concept 文章 | ✅ Karpathy wiki | 1,275 |
| [[cc-analyst]] | ✅ patch skill 文件 | ❌ | 0 |
| [[reflect-skill-claude]] | ✅ 保守提升 | ❌ | 10 |
| [[Open-Amnesia]] | ✅ facts+skills | ✅ 日期 MD | 30 |

## 评价

**如果只想「不再重复解释项目」**：claude-mem 是最省事、最经过验证的选择。
**如果想要 memory→skill→wiki 全链路**：它只解决第一段，需要配 [[claude-memory-compiler]] 或 [[Open-Amnesia]]。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[claude-memory-compiler]] — 同为 hook 驱动，但产出 wiki
- [[Claude-Mem]] — ⚠️ **注意重名**：本 wiki 早前的 `Claude-Mem` 页面指的是另一项目，勿混淆
- [[Memory-Systems]] — 记忆系统总览
