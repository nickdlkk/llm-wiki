---
title: cass-memory
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/dicklesworthstone-cass-memory-system.md
tags:
  - skill
  - memory
  - session-extraction
  - open-source
confidence: high
---
# cass-memory（Dicklesworthstone）

> **411 ⭐ / TypeScript / Bun / MIT / alpha**。**Procedural memory for AI coding agents**——把散落的 agent session 转成持久、**跨 agent** 的记忆，让每个 agent 都能从其他 agent 的经验中学习。

## 核心主张：跨 agent 学习

多数项目是「让 Claude 记住 Claude 的事」。cass-memory 的目标是**让 Codex 学到 Claude 踩过的坑**——记忆作为 agent 之间的共享层。

## Agent-first 的 CLI 设计

README 有专门的「🤖 Agent Quickstart (JSON)」章节，规定 **agent 上下文中必须用 `--json`**：stdout = data，stderr = diagnostics，exit 0 = success。

```bash
# 1) 开工前拿到任务相关的记忆
cm context "implement auth rate limiting" --json

# 2) 看最小可行 workflow
cm quickstart --json

# 3) 构建 playbook（记忆 onboarding）
cm onboard status --json
cm onboard sample --fill-gaps --json
cm onboard read /path/to/session.jsonl --template --json
cm onboard mark-done /path/to/session.jsonl
```

注意 `cm onboard read <session.jsonl>`——**直接吃 session JSONL 做 onboarding**，这正是 [[Session-Extraction-Pipeline]] Step 1 的标准形态。

## 值得借鉴的工程细节

| 细节 | 说明 |
|------|------|
| **`--json` 契约** | stdout/stderr/exit code 三者语义分离，agent 可安全解析 |
| **onboard 有 status/mark-done** | 增量幂等——已处理的 session 不重复吃 |
| **`--fill-gaps`** | 主动找记忆空洞，而非被动等 session |
| **多渠道安装** | curl one-liner / Homebrew / Scoop 三平台 |

## 状态提醒

仓库自标 **alpha**，License 为 NOASSERTION（README 里写 MIT，元数据未识别）。生产使用前需自行确认许可。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[claude-mem-thedotmack]] — 同吃 JSONL 但单 agent
- [[code-session-memory]] — 另一个跨 harness 路线
