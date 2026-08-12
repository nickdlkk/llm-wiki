---
title: prism
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/prosusai-prism.md
tags:
  - skill
  - memory
  - session-extraction
  - open-source
confidence: high
---
# Prism（ProsusAI）

> **19 ⭐ / Python / Apache-2.0 / stdlib-only**。Prosus 出品的 Claude Code + Cursor 知识层。**本调研中唯一把「先验证再入库」做成一等公民的实现。**

## 定位

> Your work, refracted into knowledge AI coding tools remember.

Prism 通过 hook 观察你的 session（只收 `tool_start` 观测），当提取管线发现**反复出现的 pattern** 时，提议 **engram**——一个「活的知识单元」。

## engram 生命周期（核心创新）

engram 的 **confidence 会升也会降**：

| 事件 | 对 confidence 的影响 |
|------|---------------------|
| MCP retrieval（被检索用到）| ↑ use event |
| session-review overlap | ↑ use event |
| validator 输出 | ↑ / ↓ |
| extraction merge | ↑ |
| 长期未被使用 | ↓ decay |

这解决了 [[Session-Extraction-Pipeline]] 的一个未解问题：**提取出来的 skill/fact 如何知道它是否真的有用？** Prism 的答案是「看它有没有被真的检索到」——把使用率当反馈信号。

## 团队维度

- 把最好的 engram **promote 成可发布的 skill**
- slash command 可挖掘 **codebase + git history** 找架构 pattern
- 发布到 **team registry**，全员受益

## 与本 wiki 已收录项目的差异

| 维度 | Prism | 其他多数项目 |
|------|-------|-------------|
| 入库前 | **quality gate 验证** | 直接写 |
| 置信度 | 动态升降 + decay | 静态或无 |
| 拒绝原因 | 显式记录 rejection reasons | 无 |
| 依赖 | stdlib-only | 常需向量库 |
| 团队 | registry 分发 | 多为单机 |

**「先验证再写库」能在生产中防止 AI 编造的伪 skill 污染 skill library**——这是把 session-mining 从玩具推向生产的关键一步。

## 安装

```bash
git clone https://github.com/ProsusAI/prism.git && cd prism
./install.sh
```
要求 Python 3.12+。装完 `prism dashboard` 起本地 Web UI 看 projects 和 engrams。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[cc-analyst]] — 同为 patch 提议路线但无 confidence decay
- [[open-second-brain]] — 同为确定性晋升（evidence count）
