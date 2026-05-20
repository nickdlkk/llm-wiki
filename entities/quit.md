---
title: QUIT
type: entity
created: 2026-05-20
updated: 2026-05-20
sources:
  - https://github.com/Mr-XcHan/QUIT
tags:
  - ai-research
  - human-in-the-loop
  - agent-pipeline
  - paper-writing
  - multi-agent
---

# QUIT

**QUIT: A Human-in-the-Loop Platform for AI Research Automation**
*Query · Understand · Implement · Tell*

GitHub: [Mr-XcHan/QUIT](https://github.com/Mr-XcHan/QUIT) · ⭐ 34 · Python · Apache 2.0

## 一句话定位

不是黑盒，是一个**透明 Pipeline**，研究者全程掌控每个环节。End-to-end 全自动跑完一篇论文约 ¥10 (DeepSeek-V4-Pro)。

## 四阶段流水线

| 阶段 | Agent | 职责 |
|:---:|---|---|
| 🔍 Query | ResearchAgent | 搜索论文、仓库、本地文献 |
| 💡 Understand | ResearchAgent | 提取 Evidence Card，聚类洞察，生成 Idea |
| 🔧 Implement | BuilderAgent | 把想法转成代码 → 跑实验 → 审计结果 |
| 📝 Tell | ReviewerAgent | 从实际输出起草并 review 论文 |

## 系统架构

4 个专业 Agent 由中央 **Orchestrator / State Machine** 协调：

- 🗺️ **PlannerAgent** → 将用户 topic 转为 validated `ResearchBrief`
- 🔬 **ResearchAgent** → 检索论文、提取证据、合成想法
- 🏗️ **BuilderAgent** → 生成实验代码、运行、写论文
- 🔍 **ReviewerAgent** → 审计想法/代码/论文草稿

**Artifact 驱动设计**：所有协调通过磁盘上的命名文件，不共享对话上下文 → 可追溯、可复现、可从任意状态恢复继续。

## 人类控制点

研究者在任意状态后可停止，检查 artifact（evidence card、BuildSpec、生成代码、结果），然后从选定状态恢复继续。

## 安装与配置

```bash
git clone https://github.com/Mr-XcHan/QUIT.git
cd QUIT
bash setup.sh
```

主配置文件：`Quit_v0_3/config.json`。只需改 topic 和 LLM 凭证即可启动。

```json
{
  "project": { "topic": "Flow Matching for Offline Reinforcement Learning" },
  "llm": { "provider": "deepseek", "model": "deepseek-v4-pro", "api_key_env": "DEEPSEEK_API_KEY" }
}
```

支持 provider: `anthropic`, `openai`, `deepseek`, `local-vllm` 等。

## 关键参数

| 字段 | 默认值 | 说明 |
|---|---|---|
| `runtime.stop_after` | `null` | 在特定状态后停止（如 `"CODE_EVAL"`） |
| `run_budget.experiment_timeout_seconds` | `3600` | 生成实验的最大时间 |
| `retrieval.sources` | `["arxiv"]` | 论文搜索来源 |
| `write.expected_main_pages` | `8` | 目标论文长度 |

## 依赖

- Python 3.11+
- Git
- LaTeX (`texlive` + `latexmk`)

## 与 Coding Agent 的区别

Coding Agent（如 Claude Code、Cua、Devin）目标是**写代码完成任务**。QUIT 目标是**从想法到可发表论文**。QUIT 的 Implement 阶段会写代码跑实验，但核心价值在于端到端的 Research Pipeline + 论文写作能力。
