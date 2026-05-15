---
title: GenericAgent
type: entity
source: github:lsdefine/GenericAgent
created: 2026-04-17
updated: 2026-04-17
tags:
  - autonomous-agent
  - self-evolving
  - layered-memory
  - browser-control
  - python
  - minimal
---

# GenericAgent

*"Don't preload skills — evolve them."*

**Repo:** `lsdefine/GenericAgent` | **Lang:** Python | **License:** MIT | **Status:** Active

---

## Summary

极简自进化自主 Agent 框架。核心仅 **~3K 行代码**，Agent Loop 仅 **~100 行** (`agent_loop.py`)，通过 **9 个原子工具** 赋予 LLM 对本地计算机的系统级控制能力。

**设计哲学：** 不预设技能，靠进化获得能力。每次解决新任务后自动将执行路径固化为 Skill，供后续直接调用。

---

## Architecture

```
GenericAgent/
├── agent_loop.py          # ~100行，核心执行循环
├── llmcore.py             # LLM客户端（Claude/Gemini/Kimi/MiniMax等）
├── agentmain.py           # Agent主逻辑
├── memory/                # 分层记忆系统
│   ├── L4_raw_sessions/   # L4: 会话归档
│   └── *.sop.md           # L3: Skills / SOPs
├── TMWebDriver.py         # 浏览器驱动（真实浏览器，保留登录态）
├── frontends/             # 多前端：Streamlit/Qt/微信/QQ/飞书/钉钉/企微/Telegram
└── memory/*.sop.md        # 自进化的Skill库
```

---

## Layered Memory System

| Layer | Name | Description |
|-------|------|-------------|
| L0 | Meta Rules | Agent 基础行为规则和系统约束 |
| L1 | Insight Index | 极简索引层，用于快速路由与召回 |
| L2 | Global Facts | 长期运行中积累的稳定知识 |
| L3 | Task Skills / SOPs | 可复用工作流程 |
| L4 | Session Archive | 已完成任务蒸馏出的归档记录 |

---

## 9 Atomic Tools

| Tool | Function |
|------|----------|
| `code_run` | 执行任意代码（动态安装Python包、写脚本、调用API、控制硬件） |
| `file_read` | 读取文件 |
| `file_write` | 写入文件 |
| `file_patch` | 修改文件 |
| `web_scan` | 感知网页内容 |
| `web_execute_js` | 控制浏览器行为 |
| `ask_user` | 人机协作确认 |
| `update_working_checkpoint` | 跨会话持久化上下文 |
| `start_long_term_update` | 积累经验 |

---

## Self-Evolution Mechanism

```
[New Task] → [Autonomous Exploration]
  (install deps, write scripts, debug & verify)
→ [Crystallize into Skill] → [Write to Memory Layer]
→ [Direct Recall on Next Similar Task]
```

用户说"监控股票并提醒我" → Agent 第一次：安装mootdx→构建选股流程→配置定时任务→保存Skill → 之后一句话启动。

---

## Key Differentiators

1. **Self-Bootstrap Proof** — 仓库的一切（安装Git、git init、每条commit message）均由 GenericAgent 自主完成，作者从未打开过终端
2. **极简主义** — ~3K行 vs OpenClaw ~530K行；<30K上下文 vs 其他Agent的 200K-1M
3. **真实浏览器注入** — 保留登录态，非无头/沙箱
4. **自进化技能树** — 用得越久，技能积累越多，完全属于用户个人
5. **多前端支持** — Streamlit / Qt / 微信 / QQ / 飞书 / 钉钉 / 企微 / Telegram

---

## vs Other SDD Tools

| Aspect | GenericAgent | OpenSpec | Swarm | HumanInLoop |
|--------|-------------|----------|-------|-------------|
| Focus | Self-evolution | Spec delta | Parallel worktree | Human checkpoints |
| Core size | ~3K lines | ~3K lines | ~2K lines | ~3K lines |
| Memory | L0-L4 layered | Profile/skill | SQLite state | StrategyGraph |
| Skills | Auto-crystallized | Manual conventions | YAML plan | DAG + MCP |
| Browser | Real (preserved session) | No | No | No |
| Self-bootstrap | Yes | No | No | No |

---

## Frontends

Streamlit Web UI (`stapp.py`), Qt desktop (`qtapp.py`), 微信Bot (`wechatapp.py`), QQ Bot (`qqapp.py`), 飞书Bot (`fsapp.py`), 企微Bot (`wecomapp.py`), 钉钉Bot (`dingtalkapp.py`), Telegram Bot (`tgapp.py`)

---

## Strengths

- Self-evolution: 每次任务自动沉淀 Skill，能力随使用增长
- 极小代码量，极低门槛，极低成本（<30K上下文）
- 真实浏览器保留登录态 — 实际应用场景强
- 支持所有主流模型（Claude / Gemini / Kimi / MiniMax 等）
- 自举能力已验证 — 整个仓库由自身构建

## Weaknesses

- 无 spec/specification 概念 — 依赖探索和记忆，不适合需要严格规范的项目
- 浏览器依赖（Windows/macOS/Android）— 跨平台有一定限制
- 自我进化质量依赖模型能力 — 可能沉淀出错误的 Skill
